---
ms.openlocfilehash: b51f27b2f58fd19851c80beb9cedcbd32b80b165
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484587"
---
# <a name="fixed-sized-buffers"></a>Buffer a dimensione fissa

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Summary
[summary]: #summary

Fornire un meccanismo generico e sicuro per la C# dichiarazione di buffer a dimensione fissa al linguaggio.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Attualmente, gli utenti hanno la possibilità di creare buffer a dimensione fissa in un contesto non sicuro. Tuttavia, è necessario che l'utente gestisca i puntatori, esegua manualmente i controlli dei limiti e supporti solo un set limitato di tipi (`bool`, `byte`, `char`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`, `float`e `double`).

Il problema più comune è che i buffer a dimensione fissa non possono essere indicizzati in codice sicuro. L'impossibilità di usare più tipi è il secondo.

Con alcune modifiche minime, è possibile fornire buffer a dimensione fissa per utilizzo generico che supportano qualsiasi tipo, può essere usato in un contesto sicuro e avere eseguito il controllo dei limiti automatici.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Si dichiarerebbe un buffer a dimensione fissa sicuro tramite il codice seguente:

```csharp
public fixed DXGI_RGB GammaCurve[1025];
```

La dichiarazione verrebbe convertita in una rappresentazione interna da parte del compilatore, simile alla seguente:

```csharp
[FixedBuffer(typeof(DXGI_RGB), 1024)]
public ConsoleApp1.<Buffer>e__FixedBuffer_1024<DXGI_RGB> GammaCurve;

// Pack = 0 is the default packing and should result in indexable layout.
[CompilerGenerated, UnsafeValueType, StructLayout(LayoutKind.Sequential, Pack = 0)]
struct <Buffer>e__FixedBuffer_1024<T>
{
    private T _e0;
    private T _e1;
    // _e2 ... _e1023
    private T _e1024;

    public ref T this[int index] => ref (uint)index <= 1024u ?
                                         ref RefAdd<T>(ref _e0, index):
                                         throw new IndexOutOfRange();
}
```

Poiché tali buffer a dimensione fissa non richiedono più l'uso di `fixed`, è opportuno consentire qualsiasi tipo di elemento.  

> Nota: `fixed` sarà ancora supportato, ma solo se il tipo di elemento è `blittable`

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

* È possibile che si verifichino problemi con la compatibilità con le versioni precedenti, ma dato che i buffer a dimensione fissa esistenti funzionano solo con una selezione di tipi primitivi, è possibile che il compilatore continui a funzionare solo se l'utente considera il buffer fisso come puntatore.
* Costrutti incompatibili potrebbero dover usare una codifica `v2` leggermente diversa per nascondere i campi del compilatore precedente.
* La compressione non è ben definita nella specifica IL per i tipi generici. Mentre l'approccio dovrebbe funzionare, verrà eseguito il bordering in caso di comportamento non documentato. Dobbiamo renderlo documentato e verificare che altri JIT come mono hanno lo stesso comportamento.
* Se si specifica un tipo separato per ogni lunghezza (un possibile altro per i campi `readonly`, se supportato), i metadati avranno un effetto sui metadati. Verrà associato dal numero di matrici di dimensioni diverse nell'app specificata.
* `ref` Math non è formalmente verificabile (poiché non è sicura). Sarà necessario trovare un modo per aggiornare le regole di verifica per sapere che l'uso è accettabile.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Dichiarare manualmente le strutture e usare codice unsafe per costruire gli indicizzatori.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- è necessario consentire la `readonly`?  (con indicizzatore ReadOnly)
- è necessario consentire gli inizializzatori di matrice?
- è necessario `fixed` parola chiave?
- `foreach`?
- solo i campi di istanza negli struct?

## <a name="design-meetings"></a>Riunioni di progettazione

Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.