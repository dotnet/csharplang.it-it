---
ms.openlocfilehash: 2e054c629f71ae37b112300905c3106f9ce977e8
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484664"
---
# <a name="auto-implemented-property-field-targeted-attributes"></a>Attributi di destinazione del campo delle proprietà implementate automaticamente

## <a name="summary"></a>Summary
[summary]: #summary

Questa funzionalità prevede di consentire agli sviluppatori di applicare gli attributi direttamente ai campi sottoposti a backup delle proprietà implementate automaticamente.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Attualmente non è possibile applicare attributi ai campi sottoposti a backup delle proprietà implementate automaticamente.  Nei casi in cui lo sviluppatore deve usare un attributo di targeting del campo, è forzato a dichiarare il campo manualmente e a usare la sintassi della proprietà più dettagliata.  Dato che C# ha sempre supportato gli attributi destinati ai campi nel campo di supporto generato per gli eventi, è opportuno estendere la stessa funzionalità alla proprietà Parent.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

In breve, le condizioni seguenti sarebbero valide C# e non generano un avviso:

```csharp
[Serializable]
public class Foo 
{
    [field: NonSerialized]
    public string MySecret { get; set; }
}
```

Ciò comporterebbe l'applicazione degli attributi destinati ai campi al campo sottostante generato dal compilatore:

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

Come indicato in precedenza, questo porta la parità con C# la sintassi dell'evento da 1,0, perché il codice seguente è già valido e si comporta come previsto:

```csharp
[Serializable]
public class Foo
{
    [field: NonSerialized]
    public event EventHandler MyEvent;
}
```

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

L'implementazione di questa modifica presenta due potenziali svantaggi:

1. Se si tenta di applicare un attributo al campo di una proprietà implementata automaticamente, viene generato un avviso del compilatore che gli attributi del blocco verranno ignorati.  Se il compilatore è stato modificato per supportare questi attributi, questi verranno applicati al campo sottostante in una ricompilazione successiva che potrebbe modificare il comportamento del programma in fase di esecuzione.
1. Il compilatore non convalida attualmente le destinazioni AttributeUsage degli attributi quando si tenta di applicarle al campo della proprietà implementata automaticamente.  Se il compilatore è stato modificato in modo da supportare gli attributi destinati ai campi e l'attributo in questione non può essere applicato a un campo, il compilatore genera un errore anziché un avviso, suddividendo la compilazione.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a>Riunioni di progettazione
