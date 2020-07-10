---
ms.openlocfilehash: c40d143a933969b80f8902938a6243c33ca09432
ms.sourcegitcommit: 9cf2f666477775bacc56ed5915bc00081d4fcb8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208395"
---
# <a name="repeated-attributes-in-partial-members"></a><span data-ttu-id="2cdef-101">Attributi ripetuti in membri parziali</span><span class="sxs-lookup"><span data-stu-id="2cdef-101">Repeated Attributes in Partial Members</span></span>

## <a name="summary"></a><span data-ttu-id="2cdef-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="2cdef-102">Summary</span></span>

<span data-ttu-id="2cdef-103">Consentire a ogni dichiarazione di un membro parziale di applicare in modo indipendente un attributo non contrassegnato con `[AttributeUsage(AllowMultiple = true)]` , purché gli argomenti dell'attributo siano identici in tutte le applicazioni.</span><span class="sxs-lookup"><span data-stu-id="2cdef-103">Allow each declaration of a partial member to independently apply an attribute not marked with `[AttributeUsage(AllowMultiple = true)]`, as long as the attribute arguments are identical in all applications.</span></span>

## <a name="motivation"></a><span data-ttu-id="2cdef-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="2cdef-104">Motivation</span></span>

<span data-ttu-id="2cdef-105">Quando si considerano gli attributi presenti in un metodo "parziale", le unioni di linguaggio uniscono tutti gli attributi in tutte le posizioni corrispondenti in entrambe le dichiarazioni.</span><span class="sxs-lookup"><span data-stu-id="2cdef-105">When considering what attributes are present on a 'partial' method, the language unions together all the attributes in all corresponding positions on both declarations.</span></span> <span data-ttu-id="2cdef-106">Il metodo seguente, ad esempio, `M` presenta gli attributi `A` e `B` .</span><span class="sxs-lookup"><span data-stu-id="2cdef-106">For example, the method `M` below has attributes `A` and `B`.</span></span>

```cs
[A]
partial void M();

[B]
partial void M() { }
```

<span data-ttu-id="2cdef-107">Ciò significa che gli attributi che non sono contrassegnati non `[AttributeUsage(AllowMultiple = true)]` possono essere presenti in entrambe le parti:</span><span class="sxs-lookup"><span data-stu-id="2cdef-107">This means that attributes which are not marked `[AttributeUsage(AllowMultiple = true)]` cannot be present across both parts:</span></span>

```cs
[A]
partial void M();

[A] // error: duplicate attribute!
partial void M() { }
```

<span data-ttu-id="2cdef-108">Si tratta di un problema di usabilità/leggibilità, perché alcuni attributi sono progettati per informare l'utente e/o il gestore del metodo di quali pre/postcondizioni o invarianti il metodo richiede.</span><span class="sxs-lookup"><span data-stu-id="2cdef-108">This presents a usability/readability issue, because some attributes are designed to inform the user and/or maintainer of the method of what pre/postconditions or invariants the method requires.</span></span> <span data-ttu-id="2cdef-109">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2cdef-109">For example:</span></span>

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue(out object? value) { ... }
```

<span data-ttu-id="2cdef-110">Un membro parziale facilita in genere la relazione tra un generatore di codice e un utente finale. ogni entità fornisce una delle dichiarazioni del membro parziale affinché un generatore di codice fornisca funzionalità all'utente o per consentire all'utente di accedere a un punto di estensione nel codice generato.</span><span class="sxs-lookup"><span data-stu-id="2cdef-110">A partial member typically facilitates the relationship between a code generator and an end user--each party provides one of the declarations of the partial member in order for a code generator to provide functionality to the user, or for the user to access an extension point in generated code.</span></span> <span data-ttu-id="2cdef-111">Nel caso in cui una sola dichiarazione possa avere questi attributi di applicazione singola, il generatore e l'utente non possono comunicare efficacemente i loro requisiti.</span><span class="sxs-lookup"><span data-stu-id="2cdef-111">In the situation where only one declaration is allowed to have these single-application attributes, the generator and the user can't effectively communicate their requirements to each other.</span></span> <span data-ttu-id="2cdef-112">Se un generatore produce una dichiarazione di definizione con un `NotNullWhen` attributo, ad esempio, l'utente non può scrivere una dichiarazione di implementazione con lo stesso attributo, anche se la postcondizione è applicabile all'implementazione e controllata dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="2cdef-112">If a generator produces a defining declaration with a `NotNullWhen` attribute, for instance, the user cannot write an implementing declaration with that same attribute, even though the postcondition is applicable to the implementation, and checked by the compiler.</span></span> <span data-ttu-id="2cdef-113">Ciò consente di creare confusione per gli utenti quando si verificano le cause principali degli avvisi o quando si tenta di comprendere i comportamenti di un metodo.</span><span class="sxs-lookup"><span data-stu-id="2cdef-113">This creates confusion for users when tracking down the root causes of warnings or when trying to understand the behaviors of a method.</span></span>

## <a name="solution"></a><span data-ttu-id="2cdef-114">Soluzione</span><span class="sxs-lookup"><span data-stu-id="2cdef-114">Solution</span></span>

<span data-ttu-id="2cdef-115">Consente di utilizzare un attributo non AllowMultiple una volta per ogni simbolo (membro, valore restituito, parametro e così via) in ogni dichiarazione parziale, purché gli argomenti dell'attributo siano identici.</span><span class="sxs-lookup"><span data-stu-id="2cdef-115">Allow a non-AllowMultiple attribute to be used once on each symbol (member, return, parameter, etc.) in each partial declaration, as long as the attribute arguments are identical.</span></span> <span data-ttu-id="2cdef-116">Poiché gli argomenti di attributo sono tutte costanti, il compilatore può verificare questa operazione.</span><span class="sxs-lookup"><span data-stu-id="2cdef-116">Since attribute arguments are all constants, the compiler can verify this.</span></span> <span data-ttu-id="2cdef-117">Quando gli attributi vengono unificati tra le dichiarazioni, ogni attributo non AllowMultiple verrà deduplicato e verrà emessa solo un'istanza dell'attributo.</span><span class="sxs-lookup"><span data-stu-id="2cdef-117">When attributes are unioned across declarations, each non-AllowMultiple attribute will be de-duplicated and only one instance of the attribute will be emitted.</span></span>

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(true)] out object? value) { ... } // ok

// equivalent to:
public bool TryGetValue([NotNullWhen(true)] out object value) { ... }

// error when attribute arguments do not match
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(false)] out object? value) { ... } // error
```

### <a name="open-questions"></a><span data-ttu-id="2cdef-118">Domande aperte</span><span class="sxs-lookup"><span data-stu-id="2cdef-118">Open questions</span></span>

1. <span data-ttu-id="2cdef-119">Questa ripetizione degli attributi deve essere consentita per le dichiarazioni di tipo ' Partial ' o solo per i membri non di tipo (ad esempio metodi)?</span><span class="sxs-lookup"><span data-stu-id="2cdef-119">Should such repetition of attributes be permitted on 'partial' type declarations or only on non-type members (e.g. methods)?</span></span>
2. <span data-ttu-id="2cdef-120">Gli attributi che *consentono l'* uso di più utilizzi su un simbolo possono "acconsentire" alla deduplicazione degli utilizzi equivalenti di un attributo?</span><span class="sxs-lookup"><span data-stu-id="2cdef-120">Should attributes which *do* allow multiple usages on a symbol be permitted to "opt in" to de-duplication of equivalent usages of an attribute?</span></span>

### <a name="design-meetings"></a><span data-ttu-id="2cdef-121">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="2cdef-121">Design meetings</span></span>
#### <a name="6th-july-2020"></a>[<span data-ttu-id="2cdef-122">6 luglio 2020</span><span class="sxs-lookup"><span data-stu-id="2cdef-122">6th July 2020</span></span>](/meetings/2020/LDM-2020-07-06.md#repeated-attributes-on-partial-members)
<span data-ttu-id="2cdef-123">La proposta viene accettata.</span><span class="sxs-lookup"><span data-stu-id="2cdef-123">The proposal is accepted.</span></span>
  - <span data-ttu-id="2cdef-124">La ripetizione degli attributi non AllowMultiple sarà consentita tra le dichiarazioni di tipi parziali (aprire la domanda 1).</span><span class="sxs-lookup"><span data-stu-id="2cdef-124">Repetition of non-AllowMultiple attributes will be permitted across partial type declarations (open question 1).</span></span>
  - <span data-ttu-id="2cdef-125">L'applicazione ripetuta di attributi AllowMultiple non cambierà nel comportamento e un meccanismo di "consenso esplicito" per la deduplicazione può essere considerato in una proposta futura (aprire la domanda 2).</span><span class="sxs-lookup"><span data-stu-id="2cdef-125">Repeated application of AllowMultiple attributes will not change in behavior, and an "opt in" mechanism for de-duplication may be considered in a future proposal (open question 2).</span></span>
