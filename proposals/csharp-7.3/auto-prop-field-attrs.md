---
ms.openlocfilehash: 2e054c629f71ae37b112300905c3106f9ce977e8
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484664"
---
# <a name="auto-implemented-property-field-targeted-attributes"></a><span data-ttu-id="2aa13-101">Attributi di destinazione del campo delle proprietà implementate automaticamente</span><span class="sxs-lookup"><span data-stu-id="2aa13-101">Auto-Implemented Property Field-Targeted Attributes</span></span>

## <a name="summary"></a><span data-ttu-id="2aa13-102">Summary</span><span class="sxs-lookup"><span data-stu-id="2aa13-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="2aa13-103">Questa funzionalità prevede di consentire agli sviluppatori di applicare gli attributi direttamente ai campi sottoposti a backup delle proprietà implementate automaticamente.</span><span class="sxs-lookup"><span data-stu-id="2aa13-103">This feature intends to allow developers to apply attributes directly to the backing fields of auto-implemented properties.</span></span>

## <a name="motivation"></a><span data-ttu-id="2aa13-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="2aa13-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="2aa13-105">Attualmente non è possibile applicare attributi ai campi sottoposti a backup delle proprietà implementate automaticamente.</span><span class="sxs-lookup"><span data-stu-id="2aa13-105">Currently it is not possible to apply attributes to the backing fields of auto-implemented properties.</span></span>  <span data-ttu-id="2aa13-106">Nei casi in cui lo sviluppatore deve usare un attributo di targeting del campo, è forzato a dichiarare il campo manualmente e a usare la sintassi della proprietà più dettagliata.</span><span class="sxs-lookup"><span data-stu-id="2aa13-106">In those cases where the developer must use a field-targeting attribute they are forced to declare the field manually and use the more verbose property syntax.</span></span>  <span data-ttu-id="2aa13-107">Dato che C# ha sempre supportato gli attributi destinati ai campi nel campo di supporto generato per gli eventi, è opportuno estendere la stessa funzionalità alla proprietà Parent.</span><span class="sxs-lookup"><span data-stu-id="2aa13-107">Given that C# has always supported field-targeted attributes on the generated backing field for events it makes sense to extend the same functionality to their property kin.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="2aa13-108">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="2aa13-108">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="2aa13-109">In breve, le condizioni seguenti sarebbero valide C# e non generano un avviso:</span><span class="sxs-lookup"><span data-stu-id="2aa13-109">In short, the following would be legal C# and not produce a warning:</span></span>

```csharp
[Serializable]
public class Foo 
{
    [field: NonSerialized]
    public string MySecret { get; set; }
}
```

<span data-ttu-id="2aa13-110">Ciò comporterebbe l'applicazione degli attributi destinati ai campi al campo sottostante generato dal compilatore:</span><span class="sxs-lookup"><span data-stu-id="2aa13-110">This would result in the field-targeted attributes being applied to the compiler-generated backing field:</span></span>

```csharp
[Serializable]
public class Foo 
{
    [NonSerialized]
    private string _mySecretBackingField;
    
    public string MySecret
    {
        get { return _mySecretBackingField; }
        set { _mySecretBackingField = value; }
    }
}
```

<span data-ttu-id="2aa13-111">Come indicato in precedenza, questo porta la parità con C# la sintassi dell'evento da 1,0, perché il codice seguente è già valido e si comporta come previsto:</span><span class="sxs-lookup"><span data-stu-id="2aa13-111">As mentioned, this brings parity with event syntax from C# 1.0 as the following is already legal and behaves as expected:</span></span>

```csharp
[Serializable]
public class Foo
{
    [field: NonSerialized]
    public event EventHandler MyEvent;
}
```

## <a name="drawbacks"></a><span data-ttu-id="2aa13-112">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="2aa13-112">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="2aa13-113">L'implementazione di questa modifica presenta due potenziali svantaggi:</span><span class="sxs-lookup"><span data-stu-id="2aa13-113">There are two potential drawbacks to implementing this change:</span></span>

1. <span data-ttu-id="2aa13-114">Se si tenta di applicare un attributo al campo di una proprietà implementata automaticamente, viene generato un avviso del compilatore che gli attributi del blocco verranno ignorati.</span><span class="sxs-lookup"><span data-stu-id="2aa13-114">Attempting to apply an attribute to the field of an auto-implemented property produces a compiler warning that the attributes in that block will be ignored.</span></span>  <span data-ttu-id="2aa13-115">Se il compilatore è stato modificato per supportare questi attributi, questi verranno applicati al campo sottostante in una ricompilazione successiva che potrebbe modificare il comportamento del programma in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="2aa13-115">If the compiler were changed to support those attributes they would be applied to the backing field on a subsequent recompilation which could alter the behavior of the program at runtime.</span></span>
1. <span data-ttu-id="2aa13-116">Il compilatore non convalida attualmente le destinazioni AttributeUsage degli attributi quando si tenta di applicarle al campo della proprietà implementata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="2aa13-116">The compiler does not currently validate the AttributeUsage targets of the attributes when attempting to apply them to the field of the auto-implemented property.</span></span>  <span data-ttu-id="2aa13-117">Se il compilatore è stato modificato in modo da supportare gli attributi destinati ai campi e l'attributo in questione non può essere applicato a un campo, il compilatore genera un errore anziché un avviso, suddividendo la compilazione.</span><span class="sxs-lookup"><span data-stu-id="2aa13-117">If the compiler were changed to support field-targeted attributes and the attribute in question cannot be applied to a field the compiler would emit an error instead of a warning, breaking the build.</span></span>

## <a name="alternatives"></a><span data-ttu-id="2aa13-118">Alternativi</span><span class="sxs-lookup"><span data-stu-id="2aa13-118">Alternatives</span></span>
[alternatives]: #alternatives

## <a name="unresolved-questions"></a><span data-ttu-id="2aa13-119">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="2aa13-119">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a><span data-ttu-id="2aa13-120">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="2aa13-120">Design meetings</span></span>
