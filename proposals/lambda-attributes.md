---
ms.openlocfilehash: 9647fff40a1e45bef917f140612ae4e91abea958
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484573"
---
# <a name="lambda-attributes"></a><span data-ttu-id="b3b4a-101">Attributi lambda</span><span class="sxs-lookup"><span data-stu-id="b3b4a-101">Lambda Attributes</span></span>

* <span data-ttu-id="b3b4a-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="b3b4a-102">[x] Proposed</span></span>
* <span data-ttu-id="b3b4a-103">[] Prototype</span><span class="sxs-lookup"><span data-stu-id="b3b4a-103">[ ] Prototype</span></span>
* <span data-ttu-id="b3b4a-104">[] Implementazione</span><span class="sxs-lookup"><span data-stu-id="b3b4a-104">[ ] Implementation</span></span>
* <span data-ttu-id="b3b4a-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="b3b4a-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="b3b4a-106">Summary</span><span class="sxs-lookup"><span data-stu-id="b3b4a-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="b3b4a-107">Consente di applicare gli attributi alle espressioni lambda (e ai metodi anonimi) e ai parametri del metodo lambda/Anonimo, in quanto possono trovarsi nei metodi normali.</span><span class="sxs-lookup"><span data-stu-id="b3b4a-107">Allow attributes to be applied to lambdas (and anonymous methods) and to lambda / anonymous method parameters, as they can be on regular methods.</span></span>

## <a name="motivation"></a><span data-ttu-id="b3b4a-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="b3b4a-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="b3b4a-109">Due motivazioni principali:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-109">Two primary motivations:</span></span>

1. <span data-ttu-id="b3b4a-110">Per fornire i metadati visibili agli analizzatori in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="b3b4a-110">To provide metadata visible to analyzers at compile-time.</span></span>
2. <span data-ttu-id="b3b4a-111">Per fornire i metadati visibili per la reflection e gli strumenti in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b3b4a-111">To provide metadata visible to reflection and tooling at run-time.</span></span>

<span data-ttu-id="b3b4a-112">Come esempio di (1): per il codice sensibile alle prestazioni, è utile poter avere un analizzatore che contrassegna quando le chiusure e i delegati vengono allocati per le espressioni lambda che chiudono lo stato.</span><span class="sxs-lookup"><span data-stu-id="b3b4a-112">As an example of (1): For performance-sensitive code, it is helpful to be able to have an analyzer that flags when closures and delegates are being allocated for lambdas that close over state.</span></span>  <span data-ttu-id="b3b4a-113">Spesso uno sviluppatore di tale codice non potrà più acquisire alcuno stato, in modo che il compilatore possa generare un metodo statico e un delegato memorizzabile nella cache per il metodo. in caso contrario, lo sviluppatore assicurerà che l'unico stato chiuso venga `this`, consentendo al compilatore almeno di evitare l'allocazione di un oggetto di chiusura.</span><span class="sxs-lookup"><span data-stu-id="b3b4a-113">Often a developer of such code will go out of his or her way to avoid capturing any state, so that the compiler can generate a static method and a cacheable delegate for the method, or the developer will ensure that the only state being closed over is `this`, allowing the compiler at least to avoid allocating a closure object.</span></span>  <span data-ttu-id="b3b4a-114">Tuttavia, senza il supporto del linguaggio per limitare ciò che può essere acquisito, è troppo facile per chiudere accidentalmente lo stato.</span><span class="sxs-lookup"><span data-stu-id="b3b4a-114">But, without language support for limiting what may be captured, it is all too easy to accidentally close over state.</span></span>  <span data-ttu-id="b3b4a-115">Sarebbe utile se uno sviluppatore potesse annotare le espressioni lambda con attributi per indicare lo stato di cui è consentita la chiusura, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-115">It would be valuable if a developer could annotate lambdas with attributes to indicate what state they're allowed to close over, for example:</span></span>

```csharp
[CaptureNone] // can't close over any instance state
[CaptureThis] // can only capture `this` and no other instance state
[CaptureAny] // can close over any instance state
```

<span data-ttu-id="b3b4a-116">Un analizzatore può quindi essere scritto per contrassegnare quando lo stato viene acquisito in modo non corretto, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-116">Then an analyzer can be written to flag when state is captured incorrectly, for example:</span></span>

```csharp
var results = collection.Select([CaptureNone](i) => Process(item)); // Analyzer error: [CaptureNone] lambdas captures `this`
...
private U Process(T item) { ... }
```

## <a name="detailed-design"></a><span data-ttu-id="b3b4a-117">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="b3b4a-117">Detailed design</span></span>
[design]: #detailed-design

- <span data-ttu-id="b3b4a-118">Utilizzando la stessa sintassi di attributo dei metodi normali, gli attributi possono essere applicati all'inizio di un metodo lambda o anonimo, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-118">Using the same attribute syntax as on normal methods, attributes may be applied at the beginning of a lambda or anonymous method, for example:</span></span>

```csharp
[SomeAttribute(...)] () => { ... }
[SomeAttribute(...)] delegate (int i) { ... }
```

- <span data-ttu-id="b3b4a-119">Per evitare ambiguità in merito al fatto che un attributo venga applicato al metodo lambda o a uno degli argomenti, gli attributi possono essere utilizzati solo quando le parentesi sono utilizzate intorno a qualsiasi argomento, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-119">To avoid ambiguity as to whether an attribute applies to the lambda method or to one of the arguments, attributes may only be used when parens are used around any arguments, for example:</span></span>

```csharp
[SomeAttribute] i => { ... } // ERROR
[SomeAttribute] (i) => { ... } // Ok
[SomeAttribute] (int i) => { ... } // Ok
```

- <span data-ttu-id="b3b4a-120">Con i metodi anonimi, le parentesi non sono necessarie per applicare un attributo al metodo prima della parola chiave `delegate`, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-120">With anonymous methods, parens are not needed in order to apply an attribute to the method before the `delegate` keyword, for example:</span></span>

```csharp
[SomeAttribute] delegate { ... } // Ok
[SomeAttribute] delegate (int i) => { ... } // Ok
```

- <span data-ttu-id="b3b4a-121">È possibile applicare più attributi, tramite la sintassi standard delimitato da virgole o tramite la sintassi dell'attributo completo, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-121">Multiple attributes may be applied, either via standard comma-delimited syntax or via full-attribute syntax, for example:</span></span>

```csharp
[FirstAttribute, SecondAttribute] (i) => { ... } // Ok
[FirstAttribute] [SecondAttribute] (i) => { .... } // Ok
```

- <span data-ttu-id="b3b4a-122">Gli attributi possono essere applicati ai parametri a un metodo anonimo o a un'espressione lambda, ma solo quando le parentesi vengono usate intorno a qualsiasi argomento, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-122">Attributes may be applied to the parameters to an anonymous method or lambda, but only when parens are used around any arguments, for example:</span></span>

```csharp
[SomeAttribute] i => { ... } // ERROR
([SomeAttribute] i) => { .... } // Ok
([SomeAttribute] int i) => { ... } // Ok
([SomeAttribute] i, [SomeOtherAttribute] j) => { ... } // Ok
```

- <span data-ttu-id="b3b4a-123">È possibile applicare più attributi ai parametri di un metodo anonimo o di un'espressione lambda, usando la sintassi delimitata da virgole o con attributi completi, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-123">Multiple attributes may be applied to the parameters of an anonymous method or lambda, using either the comma-delimited or full-attribute syntax, for example:</span></span>

```csharp
([FirstAttribute, SecondAttribute] i) => { ... } // Ok
([FirstAttribute] [SecondAttribute] i) => { ... } // Ok
```

- <span data-ttu-id="b3b4a-124">gli attributi di destinazione `return`possono essere usati anche nelle espressioni lambda, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b3b4a-124">`return`-targeted attributes may also be used on lambdas, for example:</span></span>

```csharp
([return: SomeAttribute] (i) => { ... }) // Ok
```

- <span data-ttu-id="b3b4a-125">Il compilatore genera gli attributi nel metodo e negli argomenti generati come per qualsiasi altro metodo.</span><span class="sxs-lookup"><span data-stu-id="b3b4a-125">The compiler outputs the attributes onto the generated method and arguments to those methods as it would for any other method.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="b3b4a-126">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="b3b4a-126">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="b3b4a-127">n/d</span><span class="sxs-lookup"><span data-stu-id="b3b4a-127">n/a</span></span>

## <a name="alternatives"></a><span data-ttu-id="b3b4a-128">Alternativi</span><span class="sxs-lookup"><span data-stu-id="b3b4a-128">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="b3b4a-129">n/d</span><span class="sxs-lookup"><span data-stu-id="b3b4a-129">n/a</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="b3b4a-130">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="b3b4a-130">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="b3b4a-131">n/d</span><span class="sxs-lookup"><span data-stu-id="b3b4a-131">n/a</span></span>

## <a name="design-meetings"></a><span data-ttu-id="b3b4a-132">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="b3b4a-132">Design meetings</span></span>

<span data-ttu-id="b3b4a-133">n/d</span><span class="sxs-lookup"><span data-stu-id="b3b4a-133">n/a</span></span>