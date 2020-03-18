---
ms.openlocfilehash: 2070cf3b3269585055791adc3427cbd134df444d
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484657"
---
# <a name="pattern-based-fixed-statement"></a>Istruzione `fixed` basata su criteri

## <a name="summary"></a>Summary
[summary]: #summary

Introdurre un modello che consenta ai tipi di partecipare alle istruzioni `fixed`. 

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Il linguaggio fornisce un meccanismo per bloccare i dati gestiti e ottenere un puntatore nativo al buffer sottostante.

```csharp
fixed(byte* ptr = byteArray)
{
   // ptr is a native pointer to the first element of the array
   // byteArray is protected from being moved/collected by the GC for the duration of this block 
}

```

Il set di tipi che possono partecipare a `fixed` è hardcoded e limitato a matrici e `System.String`. Il hardcoded di tipi "speciali" non viene ridimensionato quando vengono introdotte nuove primitive, ad esempio `ImmutableArray<T>`, `Span<T>``Utf8String`. 

Inoltre, la soluzione corrente per `System.String` si basa su un'API piuttosto rigida. La forma dell'API implica che `System.String` è un oggetto contiguo che incorpora i dati codificati UTF16 a un offset fisso dall'intestazione dell'oggetto. Questo approccio è stato trovato problematico in diverse proposte che potrebbero richiedere modifiche al layout sottostante. Sarebbe opportuno poter passare a un elemento più flessibile che separa `System.String` oggetto dalla relativa rappresentazione interna allo scopo di interoperabilità non gestita. 

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

## <a name="pattern"></a>*Modello* ##
Una soluzione valida "Fixed" basata su modelli deve:
-   Fornire i riferimenti gestiti per aggiungere l'istanza e per inizializzare il puntatore (preferibilmente si tratta dello stesso riferimento)
-   Comunica in modo univoco il tipo dell'elemento non gestito (ad esempio "char" per "String")
-   Prescrivere il comportamento nel caso "Empty" quando non vi sono elementi a cui fare riferimento. 
-   Non deve effettuare il push degli autori di API nelle decisioni di progettazione che danneggiano l'uso del tipo al di fuori del `fixed`.

Ritengo che l'esempio precedente possa essere soddisfatto riconoscendo un membro con nome Ref returning speciale: `ref [readonly] T GetPinnableReference()`.

Per poter essere utilizzato dall'istruzione `fixed` è necessario che siano soddisfatte le condizioni seguenti:

1. Per un tipo è specificato un solo membro.
1. Restituisce per `ref` o `ref readonly`. (`readonly` è consentito, in modo che gli autori di tipi non modificabili/ReadOnly possano implementare il modello senza aggiungere un'API scrivibile che può essere usata in codice sicuro)
1. T è un tipo non gestito.
Poiché `T*` diventa il tipo di puntatore. La restrizione verrà espansa naturalmente se/quando la nozione di "non gestito" viene espansa)
1. Restituisce `nullptr` gestiti quando non sono presenti dati da aggiungere, probabilmente il modo più economico per comunicarne il vuoto.
Si noti che la stringa "" restituisce un riferimento a' \ 0' poiché le stringhe hanno terminazione null.

In alternativa, per la `#3` è possibile consentire il risultato in casi vuoti non definiti o specifici dell'implementazione. Questo, tuttavia, può rendere l'API più pericolosa e soggetta a abusi e oneri di compatibilità indesiderati. 

## <a name="translation"></a>*Traduzione* ##

```csharp
fixed(byte* ptr = thing)
{ 
    // <BODY>
}
```

diventa lo pseudocodice seguente (non tutti esprimibile in C#)

```csharp
byte* ptr;
// specially decorated "pinned" IL local slot, not visible to user code.
pinned ref byte _pinned;

try
{
    // NOTE: null check is omitted for value types 
    // NOTE: `thing` is evaluated only once (temporary is introduced if necessary) 
    if (thing != null)
    {
        // obtain and "pin" the reference
        _pinned = ref thing.GetPinnableReference();

        // unsafe cast in IL
        ptr = (byte*)_pinned;
    }
    else
    {
        ptr = default(byte*);
    }

    // <BODY> 
}
finally   // finally can be omitted when not observable
{
    // "unpin" the object
    _pinned = nullptr;
}
```

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

- GetPinnableReference è progettato per essere usato solo in `fixed`, ma nulla ne impedisce l'uso nel codice sicuro, quindi l'implementatore deve tenere presente questo aspetto.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Gli utenti possono introdurre GetPinnableReference o membri simili e usarli come
 
```csharp
fixed(byte* ptr = thing.GetPinnableReference())
{ 
    // <BODY>
}
```

Non esiste alcuna soluzione per `System.String` se si desidera una soluzione alternativa.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [] Comportamento nello stato "vuoto". - `nullptr` o `undefined`? 
- [] Se i metodi di estensione devono essere considerati? 
- [] Se viene rilevato un modello in `System.String`, in caso di superamento? 

## <a name="design-meetings"></a>Riunioni di progettazione

Nessuno ancora. 
