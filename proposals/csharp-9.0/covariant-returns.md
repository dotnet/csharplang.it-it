---
ms.openlocfilehash: 9cfc0758f16b2153d52faec1d19f0ecd817cde3b
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297476"
---
# <a name="covariant-return-types"></a>tipi restituiti covarianti

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [X] Specifica: non avviata

## <a name="summary"></a>Riepilogo
[summary]: #summary

Supportare _tipi restituiti covariante_. In particolare, consentire all'override di un metodo di restituire un tipo restituito più derivato rispetto al metodo sottoposto a override e in modo analogo per consentire l'override di una proprietà di sola lettura per restituire un tipo restituito più derivato. I chiamanti del metodo o della proprietà riceveranno in modo statico il tipo restituito più raffinato da una chiamata e le sostituzioni visualizzate in più tipi derivati sarebbero necessarie per fornire un tipo restituito almeno in base a quanto visualizzato nelle sostituzioni nei relativi tipi di base.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Si tratta di un modello comune nel codice in cui è necessario inventare nomi di metodo diversi per aggirare il vincolo di linguaggio che le sostituzioni devono restituire lo stesso tipo del metodo sottoposto a override.

Questa operazione è utile nel modello Factory. Ad esempio, nella base di codice Roslyn avremmo

``` cs
class Compilation ...
{
    virtual Compilation WithOptions(Options options)...
}
```

``` cs
class CSharpCompilation : Compilation
{
    override CSharpCompilation WithOptions(Options options)...
}
```

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Si tratta di una bozza proposta per i [tipi restituiti covarianti](https://github.com/dotnet/csharplang/issues/49) in C#.  Il nostro obiettivo è consentire l'override di un metodo per restituire un tipo restituito più derivato rispetto al metodo sottoposto a override e in modo analogo per consentire all'override di una proprietà di sola lettura di restituire un tipo restituito più derivato.  I chiamanti del metodo o della proprietà riceveranno in modo statico il tipo restituito più raffinato da una chiamata e le sostituzioni visualizzate in più tipi derivati sarebbero necessarie per fornire un tipo restituito almeno in base a quanto visualizzato nelle sostituzioni nei relativi tipi di base.

Si tratta di una prima bozza, quindi è stata necessariamente inventata da zero.  Molte delle idee introdotte sono provvisorie e possono essere modificate o eliminate nelle future revisioni.

--------------

### <a name="class-method-override"></a>Override del metodo della classe

[Vincolo esistente sui metodi di override della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#override-methods)

> - Il metodo di override e il metodo di base sottoposto a override hanno lo stesso tipo restituito.

viene modificato in

> - Il metodo di override deve avere un tipo restituito convertibile da un'identità o conversione di un riferimento implicito al tipo restituito del metodo di base sottoposto a override.

E i seguenti requisiti aggiuntivi vengono aggiunti all'elenco:

> - Il metodo di override deve avere un tipo restituito convertibile da un'identità o conversione di un riferimento implicito nel tipo restituito di ogni override del metodo di base sottoposto a override dichiarato in un tipo di base (diretto o indiretto) del metodo di override.
> - Il tipo restituito del metodo di override deve essere accessibile almeno quanto il metodo di override ([domini di accessibilità](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).

Questo vincolo consente a un metodo di override in una `private` classe di avere un `private` tipo restituito.  Tuttavia, richiede un `public` metodo di override in un `public` tipo per avere un `public` tipo restituito.

### <a name="class-property-and-indexer-override"></a>Override della proprietà della classe e dell'indicizzatore

[Vincolo esistente sulle proprietà di override della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#virtual-sealed-override-and-abstract-property-accessors)

> Una dichiarazione di proprietà che esegue l'override deve specificare esattamente gli stessi modificatori di accessibilità e il nome della proprietà ereditata e deve essere presente una conversione di identità ~~tra il tipo di override e la proprietà ereditata~~. Se la proprietà ereditata ha solo una singola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o di sola scrittura), la proprietà che esegue l'override deve includere solo tale funzione di accesso. Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è di lettura/scrittura), la proprietà che esegue l'override può includere una singola funzione di accesso o entrambe le funzioni di accesso.

viene modificato in

> Una dichiarazione di proprietà che esegue l'override deve specificare esattamente gli stessi modificatori di accessibilità e il nome della proprietà ereditata, ed è presente una conversione di identità **o (se la proprietà ereditata è di sola lettura) conversione di riferimento implicita dal tipo della proprietà che esegue l'override al tipo della proprietà ereditata**. Se la proprietà ereditata ha solo una singola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o di sola scrittura), la proprietà che esegue l'override deve includere solo tale funzione di accesso. Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è di lettura/scrittura), la proprietà che esegue l'override può includere una singola funzione di accesso o entrambe le funzioni di accesso. **Il tipo della proprietà che esegue l'override deve essere accessibile almeno quanto la proprietà di override ([domini di accessibilità](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).**

***Il resto della specifica bozza riportata di seguito propone un'ulteriore estensione per i ritorni covariante dei metodi di interfaccia da prendere in considerazione in un secondo momento.***

### <a name="interface-method-property-and-indexer-override"></a>Override del metodo di interfaccia, della proprietà e dell'indicizzatore

Aggiungendo ai tipi di membri consentiti in un'interfaccia con l'aggiunta della funzionalità DIM in C# 8,0, viene aggiunto ulteriormente il supporto per `override` i membri insieme a Returns covariante.  Seguono le regole dei `override` membri come specificato per le classi, con le differenze seguenti:

Il testo seguente nelle classi:

> Il metodo sottoposto a override da una dichiarazione di override è noto come ***metodo di base sottoposto***a override. Per un metodo di override `M` dichiarato in una classe `C` , il metodo di base sottoposto a override viene determinato esaminando ogni classe base di `C` , iniziando con la classe di base diretta di `C` e continuando con ogni classe di base diretta successiva, finché in un determinato tipo di classe di base viene individuato almeno un metodo accessibile con la stessa firma di `M` dopo la sostituzione di argomenti di tipo.

viene fornita la specifica corrispondente per le interfacce:

> Il metodo sottoposto a override da una dichiarazione di override è noto come ***metodo di base sottoposto***a override. Per un metodo di override `M` dichiarato in un'interfaccia `I` , il metodo di base sottoposto a override viene determinato esaminando ogni interfaccia di base diretta o indiretta di `I` , raccogliendo il set di interfacce che dichiarano un metodo accessibile con la stessa firma di `M` dopo la sostituzione degli argomenti di tipo.  Se questo set di interfacce dispone di un *tipo più derivato*, a cui è associata una conversione di identità o di riferimento implicita da ogni tipo in questo set e tale tipo contiene una dichiarazione di metodo univoca, questo è il *metodo di base sottoposto a override*.

In modo analogo, le proprietà e gli indicizzatori sono consentiti `override` nelle interfacce come specificato per le classi nelle *funzioni di accesso 15.7.6 Virtual, sealed, override e abstract*.

### <a name="name-lookup"></a>Ricerca nome

La ricerca del nome in presenza di `override` dichiarazioni di classe modifica attualmente il risultato della ricerca del nome imponendo i dettagli dei membri trovati dalla dichiarazione più derivata `override` nella gerarchia di classi a partire dal tipo di qualificatore dell'identificatore (o `this` quando non è presente alcun qualificatore).  Ad esempio, in *12.6.2.2 parametri corrispondenti* abbiamo

> Per i metodi virtuali e gli indicizzatori definiti nelle classi, l'elenco di parametri viene selezionato dalla prima dichiarazione o dall'override del membro della funzione trovato quando si inizia con il tipo statico del destinatario e si cerca nelle relative classi di base.

per aggiungere

> Per i metodi virtuali e gli indicizzatori definiti nelle interfacce, l'elenco di parametri viene selezionato dalla dichiarazione o dall'override del membro della funzione trovato nel tipo più derivato tra quei tipi che contengono la dichiarazione di override del membro di funzione.  Si tratta di un errore in fase di compilazione se non esiste alcun tipo univoco di questo tipo.

Per il tipo di risultato di un accesso a una proprietà o a un indicizzatore, il testo esistente

> - Se si identifica una proprietà di istanza, il risultato è un accesso alla proprietà con un'espressione di istanza associata di e e un tipo associato che è il tipo della proprietà. Se T è un tipo di classe, il tipo associato viene selezionato dalla prima dichiarazione o override della proprietà trovata quando si inizia con T e si esegue una ricerca nelle relative classi di base.

è aumentata con

> Se T è un tipo di interfaccia, il tipo associato viene selezionato dalla dichiarazione o dall'override della proprietà trovata nell'oggetto più derivato di T o nelle interfacce di base dirette o indirette.  Si tratta di un errore in fase di compilazione se non esiste alcun tipo univoco di questo tipo.

È necessario apportare una modifica simile nell' *accesso dell'indicizzatore 12.7.7.3*

Nelle *espressioni di chiamata 12.7.6* si aumenta il testo esistente

> - In caso contrario, il risultato è un valore, con un tipo associato del tipo restituito del metodo o del delegato. Se la chiamata è di un metodo di istanza e il ricevitore è di un tipo di classe T, il tipo associato viene selezionato dalla prima dichiarazione o dall'override del metodo trovato quando si inizia con T e si esegue la ricerca nelle relative classi di base.

con

> Se la chiamata è di un metodo di istanza e il ricevitore è di un tipo di interfaccia T, il tipo associato viene selezionato dalla dichiarazione o dall'override del metodo trovato nell'interfaccia più derivata tra T e le interfacce di base dirette e indirette.  Si tratta di un errore in fase di compilazione se non esiste alcun tipo univoco di questo tipo.

### <a name="implicit-interface-implementations"></a>Implementazioni di interfacce implicite

Questa sezione della specifica

> Ai fini del mapping dell'interfaccia, un membro di classe `A` corrisponde a un membro di interfaccia `B` quando:
> 
> - `A`e `B` sono metodi e gli elenchi nome, tipo e parametro formale di `A` e `B` sono identici.
> - `A`e `B` sono proprietà, il nome e il tipo di `A` e `B` sono identici e hanno `A` le stesse funzioni di accesso di `B` ( `A` possono avere funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia).
> - `A`e `B` sono eventi e il nome e il tipo di `A` e `B` sono identici.
> - `A`e `B` sono indicizzatori, gli elenchi di parametri di tipo e formale di `A` e `B` sono identici e `A` hanno le stesse funzioni di accesso di `B` ( `A` possono avere funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia).

viene modificato come segue:

> Ai fini del mapping dell'interfaccia, un membro di classe `A` corrisponde a un membro di interfaccia `B` quando:
> 
> - `A`e `B` sono metodi e gli elenchi di parametri di nome e formale di `A` e `B` sono identici e il tipo restituito di `A` è convertibile nel tipo restituito `B` tramite un'identità di conversione implicita dei riferimenti al tipo restituito di `B` .
> - `A`e `B` sono proprietà, il nome di `A` e `B` sono identici, `A` ha le stesse funzioni di accesso di `B` ( `A` è consentito disporre di funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia) e il tipo di `A` è convertibile nel tipo restituito `B` tramite una conversione di identità o, se `A` è una proprietà ReadOnly, una conversione di un riferimento implicito.
> - `A`e `B` sono eventi e il nome e il tipo di `A` e `B` sono identici.
> - `A`e `B` sono indicizzatori, gli elenchi di parametri formali di `A` e `B` sono identici, `A` hanno le stesse funzioni di accesso di `B` ( `A` possono avere funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia) e il tipo di `A` è convertibile nel tipo restituito `B` tramite una conversione di identità o, se `A` è un indicizzatore di sola lettura, una conversione implicita di riferimenti.

Si tratta tecnicamente di una modifica di rilievo, perché il programma seguente stampa "C1. M "oggi, ma stampa" C2 ". M "nella revisione proposta.

``` c#
using System;

interface I1 { object M(); }
class C1 : I1 { public object M() { return "C1.M"; } }
class C2 : C1, I1 { public new string M() { return "C2.M"; } }
class Program
{
    static void Main()
    {
        I1 i = new C2();
        Console.WriteLine(i.M());
    }
}
```

A causa di questa modifica sostanziale, si potrebbe considerare di non supportare tipi restituiti covarianti nelle implementazioni implicite.

### <a name="constraints-on-interface-implementation"></a>Vincoli sull'implementazione dell'interfaccia

**È necessaria una regola che un'implementazione esplicita dell'interfaccia debba dichiarare un tipo restituito non meno derivato rispetto al tipo restituito dichiarato in qualsiasi override nelle relative interfacce di base.**

### <a name="api-compatibility-implications"></a>Implicazioni per la compatibilità delle API

*TBD*

### <a name="open-issues"></a>Problemi non risolti

La specifica non indica in che modo il chiamante ottiene il tipo restituito più raffinato. Presumibilmente che verrebbe eseguita in modo analogo al modo in cui i chiamanti ottengono le specifiche dei parametri dell'override più derivato.

--------------

Se sono disponibili le interfacce seguenti:

```csharp
interface I1 { I1 M(); }
interface I2 { I2 M(); }
interface I3: I1, I2 { override I3 M(); }
```

Si noti che in `I3` i metodi `I1.M()` e `I2.M()` sono stati "Uniti".  Quando `I3` si implementa, è necessario implementarli insieme.

In genere, è necessaria un'implementazione esplicita per fare riferimento al metodo originale.  La domanda è, in una classe

```csharp
class C : I1, I2, I3
{
    C IN.M();
}
```

Che cosa significa?  Che cosa *dovrebbe essere* ?

Suggerisco di consentire l'implementazione `I1.M` `I2.M` di o (ma non entrambi) e considerarlo come un'implementazione di entrambi.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

- [] Ogni modifica della lingua deve corrispondere a se stessa.
- [] È necessario assicurarsi che le prestazioni siano ragionevoli, anche in caso di gerarchie di ereditarietà approfondita
- [] È necessario assicurarsi che gli artefatti della strategia di traduzione non influiscano sulla semantica del linguaggio, anche quando si utilizza IL nuovo IL da compilatori obsoleti.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

È possibile ridurre leggermente le regole del linguaggio per consentire, in origine,

```csharp
abstract class Cloneable
{
    public abstract Cloneable Clone();
}

class Digit : Cloneable
{
    public override Cloneable Clone()
    {
        return this.Clone();
    }

    public new Digit Clone() // Error: 'Digit' already defines a member called 'Clone' with the same parameter types
    {
        return this;
    }
}
```

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [] In che modo le API compilate per utilizzare questa funzionalità funzionano in versioni precedenti del linguaggio?

## <a name="design-meetings"></a>Riunioni di progettazione

- una discussione all'indirizzo <https://github.com/dotnet/roslyn/issues/357> .
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-01-08.md
- Discussione offline verso la decisione di supportare l'override dei metodi della classe solo in C# 9,0.

