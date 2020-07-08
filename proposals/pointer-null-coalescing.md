---
ms.openlocfilehash: 5584f6a67ab21ed92d0b8c98dd0bd18ca7a6bc3d
ms.sourcegitcommit: 1aaa2ce9416639485fd0ccbbd32f68e53be6d3ea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86074297"
---
# <a name="extend-null-coalescing--and-null-coalescing-assignment--operators-to-pointers"></a><span data-ttu-id="23a02-101">Estendere l'assegnazione di valori null (??) e l'assegnazione di Unione null (?? =) per gli operatori di puntatori</span><span class="sxs-lookup"><span data-stu-id="23a02-101">Extend null-coalescing (??) and null coalescing assignment (??=) operators to pointers</span></span>

* <span data-ttu-id="23a02-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="23a02-102">[x] Proposed</span></span>
* <span data-ttu-id="23a02-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="23a02-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="23a02-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="23a02-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="23a02-105">[] Specifica: in corso</span><span class="sxs-lookup"><span data-stu-id="23a02-105">[ ] Specification: In Progress</span></span>

## <a name="summary"></a><span data-ttu-id="23a02-106">Summary</span><span class="sxs-lookup"><span data-stu-id="23a02-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="23a02-107">Questa proposta estende il supporto di una funzionalità di uso comune in C# (??</span><span class="sxs-lookup"><span data-stu-id="23a02-107">This proposal extends support of a commonly used feature in C# (??</span></span> <span data-ttu-id="23a02-108">e? =) per i tipi di puntatore e codice unsafe specifcally.</span><span class="sxs-lookup"><span data-stu-id="23a02-108">and ??=) to unsafe code and pointer types specifcally.</span></span> 

## <a name="motivation"></a><span data-ttu-id="23a02-109">Motivazione</span><span class="sxs-lookup"><span data-stu-id="23a02-109">Motivation</span></span>
[motivation]: #motivation


<span data-ttu-id="23a02-110">Non esiste alcun motivo per escludere i tipi di puntatore da questa funzionalità perché si adattano semanticamente al caso d'uso della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="23a02-110">There is no reason for pointer types to be excluded from this feature as they semantically fit the feature's use case.</span></span> <span data-ttu-id="23a02-111">Il supporto di questo consente di estendere l'ambito della funzionalità a quello che si prevede di supportare logicamente.</span><span class="sxs-lookup"><span data-stu-id="23a02-111">Supporting this extends the feature's scope to what one would expect it to logically support.</span></span> <span data-ttu-id="23a02-112">Questo concetto è già supportato nelle espressioni ternarie, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="23a02-112">This concept is already supported in ternary expressions EG:</span></span>

 `T *foo = bar != null ? bar : baz;`

<span data-ttu-id="23a02-113">Pertanto, le stesse opzioni sintattiche offerte ai tipi non puntatore devono essere estese ai tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="23a02-113">So the same syntactic options that are offered to non-pointer types should be extended to pointer types.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="23a02-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="23a02-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="23a02-115">Per questa aggiunta al linguaggio, non sono necessarie modifiche grammaticali.</span><span class="sxs-lookup"><span data-stu-id="23a02-115">For this addition to the language, no grammar changes are required.</span></span> <span data-ttu-id="23a02-116">I tipi supportati sono semplicemente aggiunti a un operatore esistente.</span><span class="sxs-lookup"><span data-stu-id="23a02-116">We are merely adding supported types to an existing operator.</span></span> <span data-ttu-id="23a02-117">Le regole di conversione correnti determineranno quindi il tipo risultante di?</span><span class="sxs-lookup"><span data-stu-id="23a02-117">The current conversion rules will then determine the resulting type of the ??</span></span> <span data-ttu-id="23a02-118">expression.</span><span class="sxs-lookup"><span data-stu-id="23a02-118">expression.</span></span>
<span data-ttu-id="23a02-119">Le altre regole rimarranno invariate, ad eccezione delle regole di tipo rilassato che dovranno essere modificate.</span><span class="sxs-lookup"><span data-stu-id="23a02-119">Other rules will remain the same with the exception of the relaxed type rules which will need to be modified.</span></span>

<span data-ttu-id="23a02-120">La specifica di C# dovrà essere aggiornata per riflettere questa aggiunta con la modifica della riga.</span><span class="sxs-lookup"><span data-stu-id="23a02-120">The  C# spec will need to be updated to reflect this addition with the change of the line.</span></span>
> <span data-ttu-id="23a02-121">Espressione di Unione null del form `a` ?</span><span class="sxs-lookup"><span data-stu-id="23a02-121">A null coalescing expression of the form `a` ??</span></span> <span data-ttu-id="23a02-122">`b`deve `a` essere di un tipo nullable o un tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="23a02-122">`b` requires `a` to be of a nullable type or reference type.</span></span>

<span data-ttu-id="23a02-123">to</span><span class="sxs-lookup"><span data-stu-id="23a02-123">to</span></span>

> <span data-ttu-id="23a02-124">Espressione di Unione null del form `a` ?</span><span class="sxs-lookup"><span data-stu-id="23a02-124">A null coalescing expression of the form `a` ??</span></span> <span data-ttu-id="23a02-125">`b`deve `a` essere di un tipo nullable, un tipo di riferimento o un tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="23a02-125">`b` requires `a` to be of a nullable type, reference type or pointer type.</span></span>

<span data-ttu-id="23a02-126">e</span><span class="sxs-lookup"><span data-stu-id="23a02-126">as well as</span></span> 
> <span data-ttu-id="23a02-127">Se `A` Exists e non è un tipo nullable o un tipo riferimento, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="23a02-127">If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>

<span data-ttu-id="23a02-128">to</span><span class="sxs-lookup"><span data-stu-id="23a02-128">to</span></span>

> <span data-ttu-id="23a02-129">Se `A` esiste e non è un tipo nullable, un tipo di riferimento o un tipo di puntatore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="23a02-129">If `A` exists and is not a nullable type, reference type or pointer type, a compile-time error occurs.</span></span>

<span data-ttu-id="23a02-130">Attualmente le regole per non `??=` proibiscono i tipi di puntatore. Tuttavia, l'operatore non è stato implementato in questo modo.</span><span class="sxs-lookup"><span data-stu-id="23a02-130">Currently the rules for `??=` do not prohibit pointer types; however, the operator was not implemented like that.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="23a02-131">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="23a02-131">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="23a02-132">Il codice unsafe può essere considerato confuso e un vettore per i bug che non si verificheranno diversamente.</span><span class="sxs-lookup"><span data-stu-id="23a02-132">Unsafe code can be considered confusing and a vector for bugs that would not happen otherwise.</span></span> <span data-ttu-id="23a02-133">L'aumento delle opzioni sintattiche disponibili per i tipi di puntatore potrebbe causare bug in cui lo sviluppatore non è a conoscenza del significato semantico di ciò che è stato scritto.</span><span class="sxs-lookup"><span data-stu-id="23a02-133">Increasing the available syntactic options for pointer types could possibly lead to bugs where the developer was unaware of the semantic meaning of what was written.</span></span> 

## <a name="alternatives"></a><span data-ttu-id="23a02-134">Alternativi</span><span class="sxs-lookup"><span data-stu-id="23a02-134">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="23a02-135">Non sono state considerate altre progettazioni perché questa non è una nuova funzionalità, ma è piuttosto un'estensione di una funzionalità già implementata.</span><span class="sxs-lookup"><span data-stu-id="23a02-135">No other designs have been considered as this is not a new feature, but it is rather an extension of an already implemented feature.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="23a02-136">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="23a02-136">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="23a02-137">Una possibile domanda è se è necessario estendere il supporto alla dereferenziazione implicita nello stesso modo in cui sono supportate le conversioni implicite per `Nullable<T>` .</span><span class="sxs-lookup"><span data-stu-id="23a02-137">A possible question is if support should be extended to implicit dereferencing in the same vein as implicit conversions for `Nullable<T>` are supported.</span></span>

<span data-ttu-id="23a02-138">ES</span><span class="sxs-lookup"><span data-stu-id="23a02-138">EG:</span></span>

    int* foo = null;
    int bar = foo ?? 3;

## <a name="design-meetings"></a><span data-ttu-id="23a02-139">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="23a02-139">Design meetings</span></span>

https://github.com/dotnet/csharplang/issues/418



