---
ms.openlocfilehash: 52b43abd2d8fb56088a68c7169289a63c43ce96f
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297477"
---
# <a name="suppress-emitting-of-localsinit-flag"></a>Non consente l'emissione del `localsinit` flag.

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Riepilogo
[summary]: #summary

Consente l'eliminazione dell'emissione del `localsinit` flag tramite l' `SkipLocalsInitAttribute` attributo. 

## <a name="motivation"></a>Motivazione
[motivation]: #motivation


### <a name="background"></a>Background
Le variabili locali per specifiche CLR che non contengono riferimenti non vengono inizializzate su un valore specifico dalla macchina virtuale/JIT. La lettura da tali variabili senza inizializzazione è indipendente dai tipi, ma in caso contrario il comportamento non è definito e specifico dell'implementazione. In genere, le variabili locali non inizializzate contengono tutti i valori rimasti nella memoria che ora è occupata dal stack frame. Questo potrebbe portare a un comportamento non deterministico e a riprodurre bug. 

Esistono due modi per "assegnare" una variabile locale: 
- archiviando un valore o 
- specificando `localsinit` il flag che impone che tutti gli elementi allocati formano il pool di memoria locale come nota inizializzata su zero: sono inclusi sia le variabili locali che `stackalloc` i dati.    

L'utilizzo di dati non inizializzati è sconsigliato e non è consentito nel codice verificabile. Sebbene sia possibile dimostrare che, per mezzo dell'analisi dei flussi, è consentito che l'algoritmo di verifica sia conservativo e che sia semplicemente necessario che `localsinit` sia impostato.

In passato il compilatore C# emette `localsinit` il flag su tutti i metodi che dichiarano variabili locali.

Mentre C# usa un'analisi di assegnazione definitiva più restrittiva rispetto a quella richiesta da CLR (C# deve anche considerare l'ambito delle variabili locali), non è assolutamente garantito che il codice risultante sia formalmente verificabile:
- Le regole di CLR e C# potrebbero non essere concordate se il passaggio di un argomento As locale `out` è un oggetto `use` .
- Le regole di CLR e C# potrebbero non concordare sul trattamento dei rami condizionali quando le condizioni sono note (propagazione costante).
- CLR potrebbe essere semplicemente necessario `localinits` , perché è consentito.  

### <a name="problem"></a>Problema
Nell'applicazione a prestazioni elevate il costo dell'inizializzazione zero forzata potrebbe essere evidente. È particolarmente evidente quando `stackalloc` si usa.

In alcuni casi JIT può Elide l'inizializzazione zero iniziale delle singole variabili locali quando tale inizializzazione viene "terminata" da assegnazioni successive. Non tutti i JIT eseguono questa operazione e tale ottimizzazione ha limiti. Non è utile per `stackalloc` .

Per illustrare che il problema è reale, esiste un bug noto in cui un metodo che non contiene `IL` variabili locali non ha `localsinit` flag. Il bug è già stato sfruttato dagli utenti inserendo `stackalloc` questi metodi, intenzionalmente per evitare i costi di inizializzazione. Ciò è dovuto al fatto che l'assenza di `IL` variabili locali è una metrica instabile e può variare a seconda delle modifiche apportate alla strategia codegen. Il bug dovrebbe essere risolto e gli utenti dovrebbero ottenere un metodo più documentato e affidabile per l'eliminazione del flag. 

## <a name="detailed-design"></a>Progettazione dettagliata

Consente `System.Runtime.CompilerServices.SkipLocalsInitAttribute` di specificare come metodo per indicare al compilatore di non emettere `localsinit` flag.
 
Il risultato finale sarà che le variabili locali non possono essere inizializzate a zero da JIT, che nella maggior parte dei casi non è osservabile in C#.  
Oltre a questi `stackalloc` dati non verranno inizializzati a zero. Questo è decisamente osservabile, ma è anche lo scenario più motivante.

Le destinazioni degli attributi consentite e riconosciute sono: `Method` ,, `Property` `Module` , `Class` , `Struct` , `Interface` , `Constructor` . Tuttavia, il compilatore non richiede che l'attributo venga definito con le destinazioni elencate, né l'assembly in cui è definito l'attributo. 

Quando si specifica attribute in un contenitore ( `class` , `module` , che contiene il metodo per un metodo annidato,...), il flag influiscono su tutti i metodi contenuti nel contenitore.

I metodi sintetizzati "ereditano" il flag dal contenitore/proprietario logico. 

Il flag ha effetto solo sulla strategia codegen per i corpi dei metodi effettivi. I.E. il flag non ha alcun effetto sui metodi astratti e non viene propagato ai metodi di override e di implementazione.

Si tratta in modo esplicito di una **_funzionalità del compilatore_** e **_non di una funzionalità del linguaggio_**.  
In modo analogo alla riga di comando del compilatore, la funzionalità controlla i dettagli di implementazione di una strategia di codegen specifica e non deve essere necessaria per la specifica C#.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

- I compilatori precedenti/altri potrebbero non rispettare l'attributo.
L'attributo è un comportamento compatibile. Solo può comportare una lieve hit delle prestazioni.

- Il codice senza `localinits` flag può attivare errori di verifica.
Gli utenti che richiedono questa funzionalità non sono in genere interessati alla verificabilità. 
 
- L'applicazione dell'attributo a livelli superiori rispetto a un singolo metodo ha un effetto non locale, che è osservabile quando `stackalloc` si usa. Tuttavia, questo è lo scenario più richiesto.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

- omettere il `localinits` flag quando il metodo viene dichiarato nel `unsafe` contesto. Questo potrebbe causare una modifica del comportamento invisibile all'utente e pericolosa da deterministica a non deterministica nel caso di `stackalloc` .

- omettere `localinits` sempre il flag.
Peggio ancora.

- omettere `localinits` il flag a meno che non `stackalloc` venga usato nel corpo del metodo.
Non risolve lo scenario più richiesto e può disabilitare il codice non verificabile senza che sia possibile ripristinarlo.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- L'attributo deve essere effettivamente emesso ai metadati? 

## <a name="design-meetings"></a>Riunioni di progettazione

Nessuno ancora. 