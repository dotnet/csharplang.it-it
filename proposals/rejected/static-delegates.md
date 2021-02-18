---
ms.openlocfilehash: ff1e135451b1c911c36022f6e55fc4bc1bfe4557
ms.sourcegitcommit: 1f5b1dc19d21038b59bfce169fd49e121a5a1f4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101846"
---
# <a name="static-delegates-superseded-by-csharp-90function-pointersmd"></a>Delegati statici (sostituiti da [.. /CSharp-9.0/function-pointers.MD](../csharp-9.0/function-pointers.md))

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Riepilogo
[summary]: #summary

Fornire una funzionalità di callback leggera e per utilizzo generico al linguaggio C#.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Attualmente, gli utenti hanno la possibilità di creare callback usando il `System.Delegate` tipo. Tuttavia, questi sono piuttosto pesanti, ad esempio richiedere un'allocazione dell'heap e gestire sempre il concatenamento dei callback.

Inoltre, non `System.Delegate` fornisce la migliore interoperabilità con i puntatori a funzione non gestita, ovvero perché non è copiabile e richiede il marshalling ogni volta che supera il limite gestito/non gestito.

Con alcune modifiche minime, è possibile fornire un nuovo tipo di delegato che è semplice, generico e interoperabilità con il codice nativo.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Uno dichiara un delegato statico tramite il codice seguente:

```C#
static delegate int Func()
```

È anche possibile attribuire la dichiarazione con un valore simile a `System.Runtime.InteropServices.UnmanagedFunctionPointer` , in modo che sia possibile controllare la convenzione di chiamata, il marshalling della stringa e impostare il comportamento dell'ultimo errore. Nota: l'uso di `System.Runtime.InteropServices.UnmanagedFunctionPointer` se stesso non funzionerà perché è utilizzabile solo nei delegati.

La dichiarazione verrebbe convertita in una rappresentazione interna da parte del compilatore, simile alla seguente:

```C#
struct <Func>e__StaticDelegate
{
    IntPtr pFunction;

    static int WellKnownCompilerName();
}
```

Ciò significa che è rappresentato internamente da uno struct che ha un singolo membro di tipo `IntPtr` (tale struct è copiabile e non comporta allocazioni heap):
* Il membro contiene l'indirizzo della funzione che deve essere il callback.
* Il tipo dichiara un metodo che corrisponde alla firma del metodo del callback.
* Il nome dello struct non deve essere user-costruibile (come per le altre strutture di supporto generate internamente).
 * Ad esempio: i buffer a dimensione fissa generano uno struct con un nome nel formato `<name>e__FixedBuffer` ( `<` e fanno `>` parte dell'identificatore e rendono l'identificatore non costruibile in C#, ma ancora utilizzabile in il).
* Il nome della dichiarazione del metodo deve essere un nome noto usato in tutti i tipi delegati statici (in questo modo il compilatore può conoscere il nome da cercare quando si determina la firma).

Il valore del delegato statico può essere associato solo a un metodo statico che corrisponde alla firma del callback.

Il concatenamento di callback non è supportato.

La chiamata del callback verrebbe implementata dall' `calli` istruzione.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

I delegati statici non funzionano con le API esistenti che usano i delegati regolari (è necessario eseguire il wrapping del delegato statico definito in un delegato normale della stessa firma).
* Dato che `System.Delegate` viene rappresentato internamente come un set `object` di `IntPtr` campi e ( http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs) , è possibile consentire la conversione implicita di un delegato statico in un `System.Delegate` che dispone di una firma del metodo corrispondente. È anche possibile fornire una conversione esplicita nella direzione opposta, purché sia `System.Delegate` conforme a tutti i requisiti di un delegato statico.

È necessario lavoro aggiuntivo per rendere il delegato statico prontamente utilizzabile nel Framework di base.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

TBD

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

Quali parti della progettazione sono ancora da definire?

## <a name="design-meetings"></a>Riunioni di progettazione

Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.


