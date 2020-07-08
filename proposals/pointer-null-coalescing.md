---
ms.openlocfilehash: 5584f6a67ab21ed92d0b8c98dd0bd18ca7a6bc3d
ms.sourcegitcommit: 1aaa2ce9416639485fd0ccbbd32f68e53be6d3ea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86074297"
---
# <a name="extend-null-coalescing--and-null-coalescing-assignment--operators-to-pointers"></a>Estendere l'assegnazione di valori null (??) e l'assegnazione di Unione null (?? =) per gli operatori di puntatori

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: in corso

## <a name="summary"></a>Summary
[summary]: #summary

Questa proposta estende il supporto di una funzionalità di uso comune in C# (?? e? =) per i tipi di puntatore e codice unsafe specifcally. 

## <a name="motivation"></a>Motivazione
[motivation]: #motivation


Non esiste alcun motivo per escludere i tipi di puntatore da questa funzionalità perché si adattano semanticamente al caso d'uso della funzionalità. Il supporto di questo consente di estendere l'ambito della funzionalità a quello che si prevede di supportare logicamente. Questo concetto è già supportato nelle espressioni ternarie, ad esempio:

 `T *foo = bar != null ? bar : baz;`

Pertanto, le stesse opzioni sintattiche offerte ai tipi non puntatore devono essere estese ai tipi di puntatore.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Per questa aggiunta al linguaggio, non sono necessarie modifiche grammaticali. I tipi supportati sono semplicemente aggiunti a un operatore esistente. Le regole di conversione correnti determineranno quindi il tipo risultante di? expression.
Le altre regole rimarranno invariate, ad eccezione delle regole di tipo rilassato che dovranno essere modificate.

La specifica di C# dovrà essere aggiornata per riflettere questa aggiunta con la modifica della riga.
> Espressione di Unione null del form `a` ? `b`deve `a` essere di un tipo nullable o un tipo di riferimento.

to

> Espressione di Unione null del form `a` ? `b`deve `a` essere di un tipo nullable, un tipo di riferimento o un tipo di puntatore.

e 
> Se `A` Exists e non è un tipo nullable o un tipo riferimento, si verifica un errore in fase di compilazione.

to

> Se `A` esiste e non è un tipo nullable, un tipo di riferimento o un tipo di puntatore, si verifica un errore in fase di compilazione.

Attualmente le regole per non `??=` proibiscono i tipi di puntatore. Tuttavia, l'operatore non è stato implementato in questo modo.


## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Il codice unsafe può essere considerato confuso e un vettore per i bug che non si verificheranno diversamente. L'aumento delle opzioni sintattiche disponibili per i tipi di puntatore potrebbe causare bug in cui lo sviluppatore non è a conoscenza del significato semantico di ciò che è stato scritto. 

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Non sono state considerate altre progettazioni perché questa non è una nuova funzionalità, ma è piuttosto un'estensione di una funzionalità già implementata.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

Una possibile domanda è se è necessario estendere il supporto alla dereferenziazione implicita nello stesso modo in cui sono supportate le conversioni implicite per `Nullable<T>` .

ES

    int* foo = null;
    int bar = foo ?? 3;

## <a name="design-meetings"></a>Riunioni di progettazione

https://github.com/dotnet/csharplang/issues/418



