---
ms.openlocfilehash: 45ab9a04928916323f2d78d74bda8b691ac85858
ms.sourcegitcommit: 8df4f29c1c9b691b30a2672c440b82248a99c2cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609308"
---
# <a name="extension-getenumerator-support-for-foreach-loops"></a>`GetEnumerator`Supporto dell'estensione per i `foreach` cicli.

## <a name="summary"></a>Riepilogo
[summary]: #summary

Consentire ai cicli foreach di riconoscere un metodo di estensione GetEnumerator che altrimenti soddisfa il modello foreach ed eseguire il ciclo sull'espressione quando sarebbe altrimenti un errore.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

In questo modo, foreach inline verrà implementato come altre funzionalità in C#, tra cui la decostruzione asincrona e basata su modelli.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

La modifica delle specifiche è relativamente semplice. `The foreach statement`La sezione viene modificata in questo testo:

>L'elaborazione in fase di compilazione di un'istruzione foreach determina innanzitutto il tipo di ***raccolta***, il tipo di ***enumeratore*** e il ***tipo di elemento*** dell'espressione. Questa determinazione procede come segue:
>
>*  Se il tipo `X` di *espressione* è un tipo di matrice, esiste una conversione implicita del riferimento da `X` all' `IEnumerable` interfaccia (poiché `System.Array` implementa questa interfaccia). Il ***tipo di raccolta*** è l' `IEnumerable` interfaccia, il ***tipo di enumeratore*** è l' `IEnumerator` interfaccia e il tipo di ***elemento*** è il tipo di elemento del tipo di matrice `X` .
>*  Se il tipo `X` di *espressione* è, `dynamic` esiste una conversione implicita dall' *espressione* all' `IEnumerable` interfaccia ([conversioni dinamiche implicite](../../spec/conversions.md#implicit-dynamic-conversions)). Il ***tipo di raccolta*** è l' `IEnumerable` interfaccia e il ***tipo di enumeratore*** è l' `IEnumerator` interfaccia. Se l' `var` identificatore viene fornito come *local_variable_type* , il ***tipo di elemento*** è; `dynamic` in caso contrario, è `object` .
>*  In caso contrario, determinare se il tipo `X` dispone di un `GetEnumerator` metodo appropriato:
>    * Eseguire la ricerca di membri sul tipo `X` con identificatore `GetEnumerator` e nessun argomento di tipo. Se la ricerca dei membri non produce una corrispondenza o produce un'ambiguità o produce una corrispondenza che non è un gruppo di metodi, verificare la presenza di un'interfaccia enumerabile come descritto di seguito. Si consiglia di emettere un avviso se la ricerca di membri produce qualsiasi elemento tranne un gruppo di metodi o nessuna corrispondenza.
>    * Eseguire la risoluzione dell'overload usando il gruppo di metodi risultante e un elenco di argomenti vuoto. Se la risoluzione dell'overload non produce metodi applicabili, comporta un'ambiguità o produce un unico metodo migliore, ma tale metodo è statico o non pubblico, verificare la presenza di un'interfaccia enumerabile come descritto di seguito. Si consiglia di emettere un avviso se la risoluzione dell'overload produce qualsiasi elemento tranne un metodo di istanza pubblica non ambiguo o senza metodi applicabili.
>    * Se il tipo restituito `E` del `GetEnumerator` metodo non è una classe, uno struct o un tipo di interfaccia, viene generato un errore e non vengono eseguiti altri passaggi.
>    * La ricerca del membro viene eseguita `E` con l'identificatore `Current` e nessun argomento di tipo. Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione, ad eccezione di una proprietà di istanza pubblica che consente la lettura, viene generato un errore e non vengono eseguiti altri passaggi.
>    * La ricerca del membro viene eseguita `E` con l'identificatore `MoveNext` e nessun argomento di tipo. Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione a un gruppo di metodi, viene generato un errore e non vengono eseguiti altri passaggi.
>    * La risoluzione dell'overload viene eseguita sul gruppo di metodi con un elenco di argomenti vuoto. Se la risoluzione dell'overload non produce metodi applicabili, comporta un'ambiguità o produce un unico metodo migliore, ma tale metodo è statico o non pubblico oppure il tipo restituito non lo è `bool` , viene generato un errore e non vengono eseguiti altri passaggi.
>    * Il ***tipo di raccolta*** è `X` , il ***tipo di enumeratore*** è `E` e il tipo di ***elemento*** è il tipo della `Current` Proprietà.
>
>*  In caso contrario, verificare la presenza di un'interfaccia enumerabile:
>    * Se tra tutti i tipi `Ti` per i quali esiste una conversione implicita da `X` a `IEnumerable<Ti>` , esiste un tipo univoco `T` che `T` non è `dynamic` e per tutti gli altri `Ti` esiste una conversione implicita da `IEnumerable<T>` a `IEnumerable<Ti>` , quindi il tipo di ***raccolta*** è l'interfaccia `IEnumerable<T>` , il ***tipo di enumeratore*** è l'interfaccia `IEnumerator<T>` e il ***tipo di elemento*** è `T` .
>    * In caso contrario, se è presente più di un tipo di questo tipo `T` , viene generato un errore e non vengono eseguiti altri passaggi.
>    * In caso contrario, se esiste una conversione implicita da `X` all' `System.Collections.IEnumerable` interfaccia, il ***tipo di raccolta*** è questa interfaccia, il ***tipo di enumeratore*** è l'interfaccia `System.Collections.IEnumerator` e il ***tipo di elemento*** è `object` .
>*  In caso contrario, determinare se il tipo ' X ' ha un `GetEnumerator` metodo di estensione appropriato:
>    * Eseguire la ricerca del metodo di estensione sul tipo `X` con l'identificatore `GetEnumerator` . Se la ricerca dei membri non produce una corrispondenza o produce un'ambiguità o produce una corrispondenza che non è un gruppo di metodi, viene generato un errore e non vengono eseguiti altri passaggi. Si consiglia di generare un avviso se la ricerca dei membri produce qualsiasi elemento tranne un gruppo di metodi o nessuna corrispondenza.
>    * Eseguire la risoluzione dell'overload usando il gruppo di metodi risultante e un singolo argomento di tipo `X` . Se la risoluzione dell'overload non produce metodi applicabili, genera un'ambiguità o genera un solo metodo migliore, ma tale metodo non è accessibile, viene generato un errore senza ulteriori passaggi.
>        * Questa risoluzione consente il passaggio del primo argomento tramite Ref se `X` è un tipo struct e il tipo di riferimento è `in` .
>    * Se il tipo restituito `E` del `GetEnumerator` metodo non è una classe, uno struct o un tipo di interfaccia, viene generato un errore e non vengono eseguiti altri passaggi.
>    * La ricerca del membro viene eseguita `E` con l'identificatore `Current` e nessun argomento di tipo. Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione, ad eccezione di una proprietà di istanza pubblica che consente la lettura, viene generato un errore e non vengono eseguiti altri passaggi.
>    * La ricerca del membro viene eseguita `E` con l'identificatore `MoveNext` e nessun argomento di tipo. Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione a un gruppo di metodi, viene generato un errore e non vengono eseguiti altri passaggi.
>    * La risoluzione dell'overload viene eseguita sul gruppo di metodi con un elenco di argomenti vuoto. Se la risoluzione dell'overload non produce metodi applicabili, comporta un'ambiguità o produce un unico metodo migliore, ma tale metodo è statico o non pubblico oppure il tipo restituito non lo è `bool` , viene generato un errore e non vengono eseguiti altri passaggi.
>    * Il ***tipo di raccolta*** è `X` , il ***tipo di enumeratore*** è `E` e il tipo di ***elemento*** è il tipo della `Current` Proprietà.
>*  In caso contrario, viene generato un errore e non vengono eseguiti altri passaggi.

Per `await foreach` , le regole vengono modificate in modo analogo. L'unica modifica necessaria a tale specifica è la rimozione della `Extension methods do not contribute.` riga dalla descrizione, poiché il resto di tale specifica è basato sulle regole precedenti con nomi diversi sostituiti dai metodi di pattern.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Ogni modifica aggiunge ulteriore complessità al linguaggio e questo potenzialmente consente a elementi che non sono stati progettati per essere `foreach` ed `foreach` , ad esempio `Range` .

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Non eseguire alcuna operazione.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

Nessuno a questo punto.
