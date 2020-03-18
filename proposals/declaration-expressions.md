---
ms.openlocfilehash: d2064ec1637e50962262c9380281abd5e1711402
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484580"
---
# <a name="declaration-expressions"></a><span data-ttu-id="c0581-101">Espressioni di dichiarazione</span><span class="sxs-lookup"><span data-stu-id="c0581-101">Declaration expressions</span></span>

<span data-ttu-id="c0581-102">Supportare le assegnazioni di dichiarazione come espressioni.</span><span class="sxs-lookup"><span data-stu-id="c0581-102">Support declaration assignments as expressions.</span></span>

## <a name="motivation"></a><span data-ttu-id="c0581-103">Motivazione</span><span class="sxs-lookup"><span data-stu-id="c0581-103">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="c0581-104">Consente l'inizializzazione nel punto della dichiarazione in più casi, semplificando il codice e consentendo l'uso di `var`.</span><span class="sxs-lookup"><span data-stu-id="c0581-104">Allow initialization at the point of declaration in more cases, simplifying code, and allowing `var` to be used.</span></span>

```csharp
SpecialType ReferenceType =>
    (var st = _type.SpecialType).IsValueType() ? SpecialType.None : st;
```

<span data-ttu-id="c0581-105">Consente le dichiarazioni per `ref` argomenti, in modo analogo a `out var`.</span><span class="sxs-lookup"><span data-stu-id="c0581-105">Allow declarations for `ref` arguments, similar to `out var`.</span></span>

```csharp
Convert(source, destination, ref List<Diagnostic> diagnostics = null);
```

## <a name="detailed-design"></a><span data-ttu-id="c0581-106">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="c0581-106">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="c0581-107">Le espressioni vengono estese per includere l'assegnazione di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="c0581-107">Expressions are extended to include declaration assignment.</span></span> <span data-ttu-id="c0581-108">La precedenza è uguale a quella dell'assegnazione.</span><span class="sxs-lookup"><span data-stu-id="c0581-108">Precedence is the same as assignment.</span></span>

```antlr
expression
    : non_assignment_expression
    | assignment
    | declaration_assignment_expression // new
    ;
declaration_assignment_expression // new
    : declaration_expression '=' local_variable_initializer
    ;
declaration_expression // C# 7.0
    | type variable_designation
    ;
```

<span data-ttu-id="c0581-109">L'assegnazione della dichiarazione è di un singolo oggetto locale.</span><span class="sxs-lookup"><span data-stu-id="c0581-109">The declaration assignment is of a single local.</span></span>

<span data-ttu-id="c0581-110">Il tipo di un'espressione di assegnazione di dichiarazione è il tipo della dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="c0581-110">The type of a declaration assignment expression is the type of the declaration.</span></span>
<span data-ttu-id="c0581-111">Se il tipo è `var`, il tipo dedotto è il tipo dell'espressione di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="c0581-111">If the type is `var`, the inferred type is the type of the initializing expression.</span></span> 

<span data-ttu-id="c0581-112">L'espressione di assegnazione di dichiarazione può essere un l-value, per `ref` valori di argomento in particolare.</span><span class="sxs-lookup"><span data-stu-id="c0581-112">The declaration assignment expression may be an l-value, for `ref` argument values in particular.</span></span>

<span data-ttu-id="c0581-113">Se l'espressione di assegnazione di dichiarazione dichiara un tipo di valore e l'espressione è un r-value, il valore dell'espressione è una copia.</span><span class="sxs-lookup"><span data-stu-id="c0581-113">If the declaration assignment expression declares a value type, and the expression is an r-value, the value of the expression is a copy.</span></span>

<span data-ttu-id="c0581-114">L'espressione di assegnazione di dichiarazione può dichiarare un `ref` local.</span><span class="sxs-lookup"><span data-stu-id="c0581-114">The declaration assignment expression may declare a `ref` local.</span></span>
<span data-ttu-id="c0581-115">Si verifica un'ambiguità quando `ref` viene usato per un'espressione di dichiarazione in un argomento `ref`.</span><span class="sxs-lookup"><span data-stu-id="c0581-115">There is an ambiguity when `ref` is used for a declaration expression in a `ref` argument.</span></span>
<span data-ttu-id="c0581-116">L'inizializzatore di variabile locale determina se la dichiarazione è un `ref` local.</span><span class="sxs-lookup"><span data-stu-id="c0581-116">The local variable initializer determines whether the declaration is a `ref` local.</span></span>

```csharp
F(ref int x = IntFunc());    // int x;
F(ref int y = RefIntFunc()); // ref int y;
```

<span data-ttu-id="c0581-117">L'ambito di variabili locali dichiarate nelle espressioni di assegnazione di dichiarazione è lo stesso ambito delle espressioni di dichiarazione corrispondenti da C # 7.0.</span><span class="sxs-lookup"><span data-stu-id="c0581-117">The scope of locals declared in declaration assignment expressions is the same the scope of corresponding declaration expressions from C#7.0.</span></span>

<span data-ttu-id="c0581-118">Si tratta di un errore in fase di compilazione per fare riferimento a un oggetto locale nel testo che precede l'espressione di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="c0581-118">It is a compile time error to refer to a local in text preceding the declaration expression.</span></span>

## <a name="alternatives"></a><span data-ttu-id="c0581-119">Alternativi</span><span class="sxs-lookup"><span data-stu-id="c0581-119">Alternatives</span></span>
[alternatives]: #alternatives
<span data-ttu-id="c0581-120">Nessuna modifica.</span><span class="sxs-lookup"><span data-stu-id="c0581-120">No change.</span></span> <span data-ttu-id="c0581-121">Questa funzionalità è solo una forma abbreviata sintattica.</span><span class="sxs-lookup"><span data-stu-id="c0581-121">This feature is just syntactic shorthand after all.</span></span>

<span data-ttu-id="c0581-122">Espressioni di sequenza più generali: vedere [#377](https://github.com/dotnet/csharplang/issues/377).</span><span class="sxs-lookup"><span data-stu-id="c0581-122">More general sequence expressions: see [#377](https://github.com/dotnet/csharplang/issues/377).</span></span>

<span data-ttu-id="c0581-123">Per consentire l'uso di `var` in più casi, consentire la dichiarazione e l'assegnazione separate di `var` variabili locali e dedurre il tipo dalle assegnazioni da tutti i percorsi del codice.</span><span class="sxs-lookup"><span data-stu-id="c0581-123">To allow use of `var` in more cases, allow separate declaration and assignment of `var` locals, and infer the type from assignments from all code paths.</span></span>

## <a name="see-also"></a><span data-ttu-id="c0581-124">Vedere anche</span><span class="sxs-lookup"><span data-stu-id="c0581-124">See also</span></span>
[see-also]: #see-also
<span data-ttu-id="c0581-125">Vedere espressione di dichiarazione di base in [#595](https://github.com/dotnet/csharplang/issues/595).</span><span class="sxs-lookup"><span data-stu-id="c0581-125">See Basic Declaration Expression in [#595](https://github.com/dotnet/csharplang/issues/595).</span></span>

<span data-ttu-id="c0581-126">Vedere Dichiarazione di decostruzione nella funzionalità di [decostruzione](https://github.com/dotnet/roslyn/blob/master/docs/features/deconstruction.md) .</span><span class="sxs-lookup"><span data-stu-id="c0581-126">See Deconstruction Declaration in the [deconstruction](https://github.com/dotnet/roslyn/blob/master/docs/features/deconstruction.md) feature.</span></span>
