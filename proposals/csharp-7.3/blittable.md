---
ms.openlocfilehash: 11e9d21bda9e69be692c5c5f5aee80c2da1894ab
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484622"
---
# <a name="unmanaged-type-constraint"></a>Vincolo di tipo non gestito

## <a name="summary"></a>Summary
[summary]: #summary

La funzionalità di vincolo non gestito consente di applicare il linguaggio alla classe di tipi noti come "tipi non gestiti" nella specifica del C# linguaggio.  Questa operazione è definita nella sezione 18,2 come tipo che non è un tipo riferimento e non contiene campi di tipo riferimento a qualsiasi livello di annidamento.  

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Il motivo principale è quello di semplificare la creazione di codice di interoperabilità C#di basso livello in. I tipi non gestiti sono uno dei principali blocchi predefiniti per il codice di interoperabilità, ma la mancanza di supporto nei generics rende impossibile creare routine riutilizzabili in tutti i tipi non gestiti. Al contrario, gli sviluppatori devono creare lo stesso codice della piastra calda per ogni tipo non gestito nella libreria:

```csharp
int Hash(Point point) { ... } 
int Hash(TimeSpan timeSpan) { ... } 
```

Per abilitare questo tipo di scenario, il linguaggio introdurrà un nuovo vincolo: non gestito:

```csharp
void Hash<T>(T value) where T : unmanaged
{
    ...
}
```

Questo vincolo può essere soddisfatto solo da tipi che rientrano nella definizione di tipo non gestito nella C# specifica del linguaggio. Un altro modo per esaminarlo è che un tipo soddisfa il vincolo non gestito IFF, che può essere usato anche come puntatore. 

```csharp
Hash(new Point()); // Okay 
Hash(42); // Okay
Hash("hello") // Error: Type string does not satisfy the unmanaged constraint
```

I parametri di tipo con il vincolo non gestito possono usare tutte le funzionalità disponibili per i tipi non gestiti: puntatori, fissi e così via... 

```csharp
void Hash<T>(T value) where T : unmanaged
{
    // Okay
    fixed (T* p = &value) 
    { 
        ...
    }
}
```

Questo vincolo consente inoltre di ottenere conversioni efficienti tra dati strutturati e flussi di byte. Si tratta di un'operazione comune negli stack di rete e nei livelli di serializzazione:

```csharp
Span<byte> Convert<T>(ref T value) where T : unmanaged 
{
    ...
}
```

Tali routine sono vantaggiose perché sono dimostrate sicure in fase di compilazione e di allocazione gratuita.  Attualmente gli autori di interoperabilità non possono eseguire questa operazione (anche se si trova a un livello in cui Perf è fondamentale).  È invece necessario affidarsi alle routine di allocazione con controlli di runtime costosi per verificare che i valori siano correttamente non gestiti.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Il linguaggio introdurrà un nuovo vincolo denominato `unmanaged`. Per soddisfare questo vincolo, un tipo deve essere uno struct e tutti i campi del tipo devono rientrare in una delle categorie seguenti:

- Hanno il tipo `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `IntPtr` o `UIntPtr`.
- Qualsiasi tipo di `enum`.
- È un tipo di puntatore.
- Essere uno struct definito dall'utente che satsifies il vincolo `unmanaged`.

I campi di istanza generati dal compilatore, ad esempio le proprietà implementate automaticamente, devono soddisfare anche tali vincoli. 

Ad esempio:

```csharp
// Unmanaged type
struct Point 
{ 
    int X;
    int Y {get; set;}
}

// Not an unmanaged type
struct Student 
{ 
    string FirstName;
    string LastName;
}
``` 

Impossibile combinare il vincolo `unmanaged` con `struct`, `class` o `new()`. Questa restrizione deriva dal fatto che `unmanaged` implica `struct` quindi gli altri vincoli non hanno senso.

Il vincolo `unmanaged` non viene applicato da CLR, solo dal linguaggio. Per impedire l'uso scorretto da altri linguaggi, i metodi che presentano questo vincolo saranno protetti da un mod-req. Questo impedirà ad altri linguaggi di usare argomenti di tipo che non sono tipi non gestiti.

Il token `unmanaged` nel vincolo non è una parola chiave, né una parola chiave contestuale. È invece come `var` in quanto viene valutato in tale posizione e sarà:

- Eseguire l'associazione a un tipo definito dall'utente o a cui si fa riferimento denominato `unmanaged`: questa operazione verrà trattata come qualsiasi altro vincolo di tipo denominato gestito. 
- Associa a nessun tipo: verrà interpretato come vincolo `unmanaged`.

Se è presente un tipo denominato `unmanaged` ed è disponibile senza qualifica nel contesto corrente, non sarà possibile usare il vincolo `unmanaged`. Questa operazione è parallela alle regole che racchiudono la funzionalità `var` e i tipi definiti dall'utente con lo stesso nome. 

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Lo svantaggio principale di questa funzionalità è che offre un numero ridotto di sviluppatori: in genere autori o Framework di librerie di basso livello.  Quindi, il tempo dedicato al linguaggio è molto prezioso per un numero ridotto di sviluppatori. 

Tuttavia, questi Framework sono spesso la base per la maggior parte delle applicazioni .NET.  Di conseguenza, le prestazioni e la correttezza di questo livello possono avere un effetto Ripple sull'ecosistema .NET.  In questo modo è opportuno considerare la funzionalità anche con i destinatari limitati.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Ci sono due alternative da considerare:

- Status quo: la funzionalità non è giustificata per i propri meriti e gli sviluppatori continuano a usare il comportamento implicito di consenso esplicito.

## <a name="questions"></a>Domande
[quesions]: #questions

### <a name="metadata-representation"></a>Rappresentazione dei metadati

Il F# linguaggio codifica il vincolo nel file di firma, il che significa C# che non è possibile riutilizzare la relativa rappresentazione. Per questo vincolo sarà necessario scegliere un nuovo attributo. Un metodo con questo vincolo, inoltre, deve essere protetto da un mod-req.

### <a name="blittable-vs-unmanaged"></a>Copiabile rispetto a non gestita
Il F# linguaggio ha una [funzionalità molto simile](https://docs.microsoft.com/dotnet/articles/fsharp/language-reference/generics/constraints) che usa la parola chiave unmanaged. Il nome copiabile deriva dall'utilizzo di Midori.  È possibile che si desideri predisporre la precedenza e utilizzare invece non gestito. 

**Risoluzione** dei problemi Il linguaggio decide di usare non gestito 

### <a name="verifier"></a>Verifier

Il Verifier/runtime deve essere aggiornato per comprendere l'utilizzo di puntatori a parametri di tipo generico?  Oppure può funzionare semplicemente senza modifiche?

**Risoluzione** dei problemi Non sono necessarie modifiche. Tutti i tipi di puntatore sono semplicemente non verificabili. 

## <a name="design-meetings"></a>Riunioni di progettazione

n/d
