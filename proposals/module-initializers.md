---
ms.openlocfilehash: e345bb5d9a4f998f80c13a255cec808bd2c9299f
ms.sourcegitcommit: aa2d828c824c5d81c5aa20ba4d3c6880633088ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85279069"
---
# <a name="module-initializers"></a>Inizializzatori di modulo

* [x] proposto
* [] Prototipo: [in corso](https://github.com/jnm2/roslyn/tree/module_initializer)
* [] Implementazione: [in corso](https://github.com/dotnet/roslyn/tree/features/module-initializers)
* [] Specifica: [non avviata]()

## <a name="summary"></a>Riepilogo
[summary]: #summary

Sebbene la piattaforma .NET disponga di una [funzionalità](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer) che supporta direttamente la scrittura del codice di inizializzazione per l'assembly (tecnicamente, il modulo), non viene esposta in C#.  Si tratta di uno scenario di nicchia piuttosto semplice, ma dopo l'esecuzione le soluzioni sembrano essere piuttosto dolorose.  Ci sono rapporti di [un numero di clienti](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (all'interno e all'esterno di Microsoft) che faticano a riscontrare il problema e non ci sono dubbi più casi non documentati.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

- Consentire alle librerie di eseguire l'inizializzazione unica, una volta caricata, con un sovraccarico minimo e senza che l'utente debba chiamare esplicitamente niente
- Un particolare punto problematico degli `static` approcci dei costruttori correnti è che il runtime deve eseguire verifiche aggiuntive sull'utilizzo di un tipo con un costruttore statico, per decidere se il costruttore statico deve essere eseguito o meno. Questo aggiunge un sovraccarico misurabile.
- Consentire ai generatori di origine di eseguire una logica di inizializzazione globale senza che l'utente debba chiamare esplicitamente un elemento

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Un metodo può essere designato come inizializzatore di modulo, decorato con un `[ModuleInitializer]` attributo.

```cs
using System;
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = false)]
    public sealed class ModuleInitializerAttribute : Attribute { }
}
```

L'attributo può essere utilizzato come segue:

```cs
using System.Runtime.CompilerServices;
class C
{
    [ModuleInitializer]
    internal static void M1()
    {
        // ...
    }
}
```

Alcuni requisiti vengono applicati al metodo di destinazione con questo attributo:
1. Il metodo deve essere `static` .
1. Il metodo deve essere senza parametri.
1. Il metodo deve restituire `void`.
1. Il metodo non deve essere generico o essere incluso in un tipo generico.
1. Il metodo deve essere accessibile dal modulo contenitore.
    - Ciò significa che l'accessibilità effettiva del metodo deve essere `internal` o `public` .
    - Questo significa anche che il metodo non può essere una funzione locale.
    
Quando uno o più metodi validi con questo attributo vengono trovati in una compilazione, il compilatore emetterà un inizializzatore di modulo che chiama ognuno dei metodi con attributi. Le chiamate verranno emesse in un ordine riservato, ma deterministico.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Perché *non* eseguire questa operazione?

- Forse gli strumenti di terze parti esistenti per gli inizializzatori di modulo "inserimento" sono sufficienti per gli utenti che hanno richiesto questa funzionalità.

## <a name="design-meetings"></a>Riunioni di progettazione

### <a name="april-8th-2020"></a>[8 aprile 2020](/meetings/2020/LDM-2020-04-08.md#module-initializers)
