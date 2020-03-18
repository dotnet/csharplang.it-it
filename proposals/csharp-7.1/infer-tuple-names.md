---
ms.openlocfilehash: 25e95b3ab8c384a7e66e59a7f9422cc9699074d7
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484720"
---
# <a name="infer-tuple-names-aka-tuple-projection-initializers"></a><span data-ttu-id="7e369-101">Deduce i nomi di tupla (noto anche come.</span><span class="sxs-lookup"><span data-stu-id="7e369-101">Infer tuple names (aka.</span></span> <span data-ttu-id="7e369-102">inizializzatori di proiezione di tupla)</span><span class="sxs-lookup"><span data-stu-id="7e369-102">tuple projection initializers)</span></span>

## <a name="summary"></a><span data-ttu-id="7e369-103">Summary</span><span class="sxs-lookup"><span data-stu-id="7e369-103">Summary</span></span>
[summary]: #summary

<span data-ttu-id="7e369-104">In molti casi comuni, questa funzionalità consente di omettere i nomi degli elementi di tupla e di inferirli.</span><span class="sxs-lookup"><span data-stu-id="7e369-104">In a number of common cases, this feature allows the tuple element names to be omitted and instead be inferred.</span></span> <span data-ttu-id="7e369-105">Invece di digitare `(f1: x.f1, f2: x?.f2)`, ad esempio, i nomi degli elementi "F1" e "F2" possono essere dedotti da `(x.f1, x?.f2)`.</span><span class="sxs-lookup"><span data-stu-id="7e369-105">For instance, instead of typing `(f1: x.f1, f2: x?.f2)`, the element names "f1" and "f2" can be inferred from `(x.f1, x?.f2)`.</span></span>

<span data-ttu-id="7e369-106">Questo comportamento è parallelo al comportamento dei tipi anonimi, che consentono di dedurre i nomi dei membri durante la creazione.</span><span class="sxs-lookup"><span data-stu-id="7e369-106">This parallels the behavior of  anonymous types, which allow inferring member names during creation.</span></span> <span data-ttu-id="7e369-107">Ad esempio, `new { x.f1, y?.f2 }` dichiara i membri "F1" e "F2".</span><span class="sxs-lookup"><span data-stu-id="7e369-107">For instance, `new { x.f1, y?.f2 }` declares members "f1" and "f2".</span></span>

<span data-ttu-id="7e369-108">Questa operazione è particolarmente utile quando si utilizzano le tuple in LINQ:</span><span class="sxs-lookup"><span data-stu-id="7e369-108">This is particularly handy when using tuples in LINQ:</span></span>

```csharp
// "c" and "result" have element names "f1" and "f2"
var result = list.Select(c => (c.f1, c.f2)).Where(t => t.f2 == 1); 
```

## <a name="detailed-design"></a><span data-ttu-id="7e369-109">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="7e369-109">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="7e369-110">La modifica presenta due parti:</span><span class="sxs-lookup"><span data-stu-id="7e369-110">There are two parts to the change:</span></span>

1.  <span data-ttu-id="7e369-111">Provare a dedurre un nome candidato per ogni elemento della tupla che non ha un nome esplicito:</span><span class="sxs-lookup"><span data-stu-id="7e369-111">Try to infer a candidate name for each tuple element which does not have an explicit name:</span></span>
    -   <span data-ttu-id="7e369-112">Uso delle stesse regole dell'inferenza dei nomi per i tipi anonimi.</span><span class="sxs-lookup"><span data-stu-id="7e369-112">Using same rules as name inference for anonymous types.</span></span>
        - <span data-ttu-id="7e369-113">In C#, questo consente tre casi: `y` (identificatore), `x.y` (accesso semplice ai membri) e `x?.y` (accesso condizionale).</span><span class="sxs-lookup"><span data-stu-id="7e369-113">In C#, this allows three cases: `y` (identifier), `x.y` (simple member access) and `x?.y` (conditional access).</span></span>
        - <span data-ttu-id="7e369-114">In VB Questo consente altri casi, ad esempio `x.y()`.</span><span class="sxs-lookup"><span data-stu-id="7e369-114">In VB, this allows for additional cases, such as `x.y()`.</span></span>
    -   <span data-ttu-id="7e369-115">Rifiuto dei nomi delle tuple riservate (maiuscole/minuscole in C#, senza distinzione tra maiuscole e minuscole in VB) perché non sono consentiti o sono già impliciti.</span><span class="sxs-lookup"><span data-stu-id="7e369-115">Rejecting reserved tuple names (case-sensitive in C#, case-insensitive in VB), as they are either forbidden or already implicit.</span></span> <span data-ttu-id="7e369-116">Ad esempio `ItemN`, `Rest`e `ToString`.</span><span class="sxs-lookup"><span data-stu-id="7e369-116">For instance, such as `ItemN`, `Rest`, and `ToString`.</span></span>
    -   <span data-ttu-id="7e369-117">Se i nomi dei candidati sono duplicati (senza distinzione tra C#maiuscole e minuscole in VB) all'interno dell'intera tupla, i candidati verranno eliminati.</span><span class="sxs-lookup"><span data-stu-id="7e369-117">If any candidate names are duplicates (case-sensitive in C#, case-insensitive in VB) within the entire tuple, we drop those candidates,</span></span>
2.  <span data-ttu-id="7e369-118">Durante le conversioni (che controllano e avvisano l'eliminazione dei nomi dai valori letterali di tupla), i nomi derivati non producono alcun avviso.</span><span class="sxs-lookup"><span data-stu-id="7e369-118">During conversions (which check and warn about dropping names from tuple literals), inferred names would not produce any warnings.</span></span> <span data-ttu-id="7e369-119">In questo modo si evita di suddividere il codice tupla esistente.</span><span class="sxs-lookup"><span data-stu-id="7e369-119">This avoids breaking existing tuple code.</span></span>

<span data-ttu-id="7e369-120">Si noti che la regola per la gestione dei duplicati è diversa da quella per i tipi anonimi.</span><span class="sxs-lookup"><span data-stu-id="7e369-120">Note that the rule for handling duplicates is different than that for anonymous types.</span></span> <span data-ttu-id="7e369-121">Ad esempio, `new { x.f1, x.f1 }` genera un errore, ma `(x.f1, x.f1)` comunque consentiti (solo senza nomi dedotti).</span><span class="sxs-lookup"><span data-stu-id="7e369-121">For instance, `new { x.f1, x.f1 }` produces an error, but `(x.f1, x.f1)` would still be allowed (just without any inferred names).</span></span> <span data-ttu-id="7e369-122">In questo modo si evita di suddividere il codice tupla esistente.</span><span class="sxs-lookup"><span data-stu-id="7e369-122">This avoids breaking existing tuple code.</span></span>

<span data-ttu-id="7e369-123">Per coerenza, lo stesso vale per le tuple generate da decostruzione-assegnazioni ( C#in):</span><span class="sxs-lookup"><span data-stu-id="7e369-123">For consistency, the same would apply to tuples produced by deconstruction-assignments (in C#):</span></span>

```csharp
// tuple has element names "f1" and "f2" 
var tuple = ((x.f1, x?.f2) = (1, 2));
```

<span data-ttu-id="7e369-124">Lo stesso vale anche per le tuple VB, usando le regole specifiche di VB per dedurre il nome dall'espressione e i confronti dei nomi senza distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="7e369-124">The same would also apply to VB tuples, using the VB-specific rules for inferring name from expression and case-insensitive name comparisons.</span></span>

<span data-ttu-id="7e369-125">Quando si usa C# il compilatore 7,1 (o versioni successive) con la versione del linguaggio "7,0", i nomi degli elementi vengono dedotti (nonostante la funzionalità non sia disponibile), ma è presente un errore di utilizzo del sito per il tentativo di accesso.</span><span class="sxs-lookup"><span data-stu-id="7e369-125">When using the C# 7.1 compiler (or later) with language version "7.0", the element names will be inferred (despite the feature not being available), but there will be a use-site error for trying to access them.</span></span> <span data-ttu-id="7e369-126">In questo modo si limiteranno le aggiunte del nuovo codice che in seguito si affronterà al problema di compatibilità (descritto di seguito).</span><span class="sxs-lookup"><span data-stu-id="7e369-126">This will limit additions of new code that would later face the compatibility issue (described below).</span></span>

## <a name="drawbacks"></a><span data-ttu-id="7e369-127">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="7e369-127">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="7e369-128">Lo svantaggio principale è che questa operazione introduce un break di C# compatibilità da 7,0:</span><span class="sxs-lookup"><span data-stu-id="7e369-128">The main drawback is that this introduces a compatibility break from C# 7.0:</span></span>

```csharp
Action y = () => M();
var t = (x: x, y);
t.y(); // this might have previously picked up an extension method called “y”, but would now call the lambda.
```

<span data-ttu-id="7e369-129">Questo break accettabile è stato rilevato dal Consiglio di compatibilità, dato che è limitato e l'intervallo di tempo dalle Tuple C# spedite (in 7,0) è breve.</span><span class="sxs-lookup"><span data-stu-id="7e369-129">The compatibility council found this break acceptable, given that it is limited and the time window since tuples shipped (in C# 7.0) is short.</span></span>

## <a name="references"></a><span data-ttu-id="7e369-130">Riferimenti</span><span class="sxs-lookup"><span data-stu-id="7e369-130">References</span></span>
- [<span data-ttu-id="7e369-131">LDM del 4 aprile 2017</span><span class="sxs-lookup"><span data-stu-id="7e369-131">LDM April 4th 2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md#tuple-names)
- <span data-ttu-id="7e369-132">[Discussione su GitHub](https://github.com/dotnet/csharplang/issues/370) (grazie @alrz per la presentazione di questo problema)</span><span class="sxs-lookup"><span data-stu-id="7e369-132">[Github discussion](https://github.com/dotnet/csharplang/issues/370) (thanks @alrz for bringing this issue up)</span></span>
- [<span data-ttu-id="7e369-133">Progettazione di Tuple</span><span class="sxs-lookup"><span data-stu-id="7e369-133">Tuples design</span></span>](https://github.com/dotnet/roslyn/blob/master/docs/features/tuples.md)
