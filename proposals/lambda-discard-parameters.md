---
ms.openlocfilehash: 6695664c3d5ca73f66e792b7ce2ec9993aceea05
ms.sourcegitcommit: 42ef673ecc883009c865f8384249881a546df216
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2019
ms.locfileid: "79485154"
---
# <a name="lambda-discard-parameters"></a><span data-ttu-id="66f7d-101">Parametri di eliminazione lambda</span><span class="sxs-lookup"><span data-stu-id="66f7d-101">Lambda discard parameters</span></span>

## <a name="summary"></a><span data-ttu-id="66f7d-102">Summary</span><span class="sxs-lookup"><span data-stu-id="66f7d-102">Summary</span></span>

<span data-ttu-id="66f7d-103">Consenti le eliminazioni (`_`) da utilizzare come parametri di espressioni lambda e metodi anonimi.</span><span class="sxs-lookup"><span data-stu-id="66f7d-103">Allow discards (`_`) to be used as parameters of lambdas and anonymous methods.</span></span>
<span data-ttu-id="66f7d-104">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="66f7d-104">For example:</span></span>
- <span data-ttu-id="66f7d-105">espressioni lambda: `(_, _) => 0`, `(int _, int _) => 0`</span><span class="sxs-lookup"><span data-stu-id="66f7d-105">lambdas: `(_, _) => 0`, `(int _, int _) => 0`</span></span>
- <span data-ttu-id="66f7d-106">metodi anonimi: `delegate(int _, int _) { return 0; }`</span><span class="sxs-lookup"><span data-stu-id="66f7d-106">anonymous methods: `delegate(int _, int _) { return 0; }`</span></span>

## <a name="motivation"></a><span data-ttu-id="66f7d-107">Motivazione</span><span class="sxs-lookup"><span data-stu-id="66f7d-107">Motivation</span></span>

<span data-ttu-id="66f7d-108">Non è necessario denominare i parametri inutilizzati.</span><span class="sxs-lookup"><span data-stu-id="66f7d-108">Unused parameters do not need to be named.</span></span> <span data-ttu-id="66f7d-109">Lo scopo delle eliminazioni è chiaro, ovvero non sono usate/scartate.</span><span class="sxs-lookup"><span data-stu-id="66f7d-109">The intent of discards is clear, i.e. they are unused/discarded.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="66f7d-110">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="66f7d-110">Detailed design</span></span>

<span data-ttu-id="66f7d-111">[Parametri del metodo](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) Nell'elenco di parametri di un metodo lambda o anonimo con più di un parametro denominato `_`, tali parametri sono parametri di scarto.</span><span class="sxs-lookup"><span data-stu-id="66f7d-111">[Method parameters](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) In the parameter list of a lambda or anonymous method with more than one parameter named `_`, such parameters are discard parameters.</span></span>
<span data-ttu-id="66f7d-112">Nota: se un singolo parametro è denominato `_`, si tratta di un parametro normale per motivi di compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="66f7d-112">Note: if a single parameter is named `_` then it is a regular parameter for backwards compatibility reasons.</span></span>

<span data-ttu-id="66f7d-113">Ignora parametri non introduce alcun nome in alcun ambito.</span><span class="sxs-lookup"><span data-stu-id="66f7d-113">Discard parameters do not introduce any names to any scopes.</span></span>
<span data-ttu-id="66f7d-114">Si noti che ciò implica che non vengono nascosti i nomi di `_` (carattere di sottolineatura).</span><span class="sxs-lookup"><span data-stu-id="66f7d-114">Note this implies they do not cause any `_` (underscore) names to be hidden.</span></span>

<span data-ttu-id="66f7d-115">[Nomi semplici](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#simple-names) Se `K` è zero e il *simple_name* viene visualizzato all'interno di un *blocco* e se il *blocco*(o un *blocco*di inclusione) spazio di dichiarazione della variabile locale ([dichiarazioni](basic-concepts.md#declarations)) contiene una variabile locale, un parametro (ad eccezione dei parametri di scarto) o una costante con nome `I`, il *simple_name* fa riferimento a tale variabile locale, parametro o costante ed è classificato come una variabile o un valore.</span><span class="sxs-lookup"><span data-stu-id="66f7d-115">[Simple names](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#simple-names) If `K` is zero and the *simple_name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space ([Declarations](basic-concepts.md#declarations)) contains a local variable, parameter (with the exception of discard parameters) or constant with name `I`, then the *simple_name* refers to that local variable, parameter or constant and is classified as a variable or value.</span></span>

<span data-ttu-id="66f7d-116">[Ambiti](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#scopes) Ad eccezione dei parametri di scarto, l'ambito di un parametro dichiarato in una *lambda_expression* ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) è il *anonymous_function_body* di tale *lambda_expression* ad eccezione dei parametri di scarto, l'ambito di un parametro dichiarato in una *anonymous_method_expression* ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) è il *blocco* di tale *anonymous_method_expression*.</span><span class="sxs-lookup"><span data-stu-id="66f7d-116">[Scopes](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#scopes) With the exception of discard parameters, the scope of a parameter declared in a *lambda_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)) is the *anonymous_function_body* of that *lambda_expression* With the exception of discard parameters, the scope of a parameter declared in an *anonymous_method_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)) is the *block* of that *anonymous_method_expression*.</span></span>

## <a name="related-spec-sections"></a><span data-ttu-id="66f7d-117">Sezioni specifiche correlate</span><span class="sxs-lookup"><span data-stu-id="66f7d-117">Related spec sections</span></span>
- [<span data-ttu-id="66f7d-118">Parametri corrispondenti</span><span class="sxs-lookup"><span data-stu-id="66f7d-118">Corresponding parameters</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#corresponding-parameters)
