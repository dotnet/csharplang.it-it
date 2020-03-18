---
ms.openlocfilehash: a0d80afc47e9f0073237db9b8d7a4f0b045c1b0b
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484510"
---
# <a name="out-variable-declarations"></a><span data-ttu-id="7a1c1-101">Dichiarazioni di variabili out</span><span class="sxs-lookup"><span data-stu-id="7a1c1-101">Out variable declarations</span></span>

<span data-ttu-id="7a1c1-102">La funzionalità di *dichiarazione della variabile out* consente di dichiarare una variabile nel percorso in cui viene passata come argomento `out`.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-102">The *out variable declaration* feature enables a variable to be declared at the location that it is being passed as an `out` argument.</span></span>

```antlr
argument_value
    : 'out' type identifier
    | ...
    ;
```

<span data-ttu-id="7a1c1-103">Una variabile dichiarata in questo modo viene chiamata *variabile out*.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-103">A variable declared this way is called an *out variable*.</span></span> <span data-ttu-id="7a1c1-104">È possibile usare la parola chiave contestuale `var` per il tipo della variabile.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-104">You may use the contextual keyword `var` for the variable's type.</span></span> <span data-ttu-id="7a1c1-105">L'ambito sarà identico a quello di una *variabile di tipo pattern* introdotta tramite criteri di ricerca.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-105">The scope will be the same as for a *pattern-variable* introduced via pattern-matching.</span></span>

<span data-ttu-id="7a1c1-106">In base alla specifica del linguaggio (sezione accesso agli elementi 7.6.7), l'elenco di argomenti di un accesso a elementi (espressione di indicizzazione) non contiene argomenti ref o out.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-106">According to Language Specification (section 7.6.7 Element access) the argument-list of an element-access (indexing expression) does not contain ref or out arguments.</span></span> <span data-ttu-id="7a1c1-107">Tuttavia, sono consentiti dal compilatore per diversi scenari, ad esempio gli indicizzatori dichiarati nei metadati che accettano `out`.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-107">However, they are permitted by the compiler for various scenarios, for example indexers declared in metadata that accept `out`.</span></span>

<span data-ttu-id="7a1c1-108">Nell'ambito di una variabile locale introdotta da un argument_value, si tratta di un errore in fase di compilazione per fare riferimento a tale variabile locale in una posizione testuale che precede la relativa dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-108">Within the scope of a local variable introduced by an argument_value, it is a compile-time error to refer to that local variable in a textual position that precedes its declaration.</span></span>

<span data-ttu-id="7a1c1-109">È anche un errore fare riferimento a una variabile out tipizzata in modo implicito (§ 8.5.1) nello stesso elenco di argomenti che contiene immediatamente la relativa dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-109">It is also an error to reference an implicitly-typed (§8.5.1) out variable in the same argument list that immediately contains its declaration.</span></span>

<span data-ttu-id="7a1c1-110">La risoluzione dell'overload viene modificata come segue:</span><span class="sxs-lookup"><span data-stu-id="7a1c1-110">Overload resolution is modified as follows:</span></span>

<span data-ttu-id="7a1c1-111">Viene aggiunta una nuova conversione:</span><span class="sxs-lookup"><span data-stu-id="7a1c1-111">We add a new conversion:</span></span>

> <span data-ttu-id="7a1c1-112">Viene eseguita una *conversione da un'espressione* da una dichiarazione di variabile out tipizzata in modo implicito a ogni tipo.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-112">There is a *conversion from expression* from an implicitly-typed out variable declaration to every type.</span></span>

<span data-ttu-id="7a1c1-113">Anche</span><span class="sxs-lookup"><span data-stu-id="7a1c1-113">Also</span></span>

> <span data-ttu-id="7a1c1-114">Il tipo di un argomento della variabile out tipizzato in modo esplicito è il tipo dichiarato.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-114">The type of an explicitly-typed out variable argument is the declared type.</span></span>

<span data-ttu-id="7a1c1-115">e</span><span class="sxs-lookup"><span data-stu-id="7a1c1-115">and</span></span>

> <span data-ttu-id="7a1c1-116">Un argomento della variabile out tipizzata in modo implicito non dispone di alcun tipo.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-116">An implicitly-typed out variable argument has no type.</span></span>

<span data-ttu-id="7a1c1-117">La *conversione da espressione* da una dichiarazione di variabile out tipizzata in modo implicito non è considerata migliore rispetto a qualsiasi altra *conversione da Expression*.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-117">The *conversion from expression* from an implicitly-typed out variable declaration is not considered better than any other *conversion from expression*.</span></span>

<span data-ttu-id="7a1c1-118">Il tipo di una variabile out tipizzata in modo implicito è il tipo del parametro corrispondente nella firma del metodo selezionato dalla risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-118">The type of an implicitly-typed out variable is the type of the corresponding parameter in the signature of the method selected by overload resolution.</span></span>

<span data-ttu-id="7a1c1-119">Viene aggiunto il nuovo nodo della sintassi `DeclarationExpressionSyntax` per rappresentare la dichiarazione in un argomento out var.</span><span class="sxs-lookup"><span data-stu-id="7a1c1-119">The new syntax node `DeclarationExpressionSyntax` is added to represent the declaration in an out var argument.</span></span>
