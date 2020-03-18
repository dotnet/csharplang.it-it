---
ms.openlocfilehash: b0d0fa70d90f7493c6c23be576155a77cec36cf8
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485182"
---
# <a name="default-interface-methods"></a>metodi di interfaccia predefiniti

* [x] proposto
* [] Prototipo: [in corso](https://github.com/dotnet/roslyn/blob/master/docs/features/DefaultInterfaceImplementation.md)
* [] Implementazione: nessuna
* [] Specifica: in corso, di seguito

## <a name="summary"></a>Summary
[summary]: #summary

Aggiungere il supporto per i _metodi di estensione virtuale_ : metodi nelle interfacce con implementazioni concrete. Una classe o uno struct che implementa tale interfaccia è necessario disporre di una singola implementazione _più specifica_ per il metodo di interfaccia, implementata dalla classe o dallo struct, oppure ereditata dalle relative classi o interfacce di base. I metodi di estensione virtuali consentono a un autore di API di aggiungere metodi a un'interfaccia nelle versioni future senza compromettere la compatibilità di origine o binaria con le implementazioni esistenti di tale interfaccia.

Sono simili ai ["metodi predefiniti"](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)di Java.

(In base alla tecnica di implementazione probabile) questa funzionalità richiede il supporto corrispondente nell'interfaccia della riga di comando/CLR. I programmi che sfruttano questa funzionalità non possono essere eseguiti nelle versioni precedenti della piattaforma.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Le motivazioni principali per questa funzionalità sono

- I metodi di interfaccia predefiniti consentono a un autore di API di aggiungere metodi a un'interfaccia nelle versioni future senza compromettere la compatibilità di origine o binaria con le implementazioni esistenti di tale interfaccia.
- La funzionalità consente C# a di interagire con le API destinate ad [Android (Java)](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) e [iOS (SWIFT)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267), che supportano funzionalità simili.
- Come si scopre, l'aggiunta di implementazioni di interfaccia predefinite fornisce gli elementi della funzionalità del linguaggio "tratti" (<https://en.wikipedia.org/wiki/Trait_(computer_programming)>). I tratti si sono dimostrati una tecnica di programmazione avanzata (<http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf>).

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

La sintassi di un'interfaccia viene estesa per consentire

- dichiarazioni di membri che dichiarano costanti, operatori, costruttori statici e tipi annidati;
- *corpo* per un metodo o un indicizzatore, una proprietà o una funzione di accesso all'evento (ovvero, un'implementazione "predefinita");
- dichiarazioni di membri che dichiarano campi, metodi, proprietà, indicizzatori ed eventi statici;
- dichiarazioni di membri che usano la sintassi di implementazione esplicita dell'interfaccia; e
- Modificatori di accesso espliciti (l'accesso predefinito è `public`).

I membri con corpi consentono all'interfaccia di fornire un'implementazione "predefinita" per il metodo in classi e struct che non forniscono un'implementazione di override.

Le interfacce non possono contenere lo stato dell'istanza. Mentre i campi statici sono ora consentiti, i campi di istanza non sono consentiti nelle interfacce. Le proprietà automatiche dell'istanza non sono supportate nelle interfacce perché dichiarano in modo implicito un campo nascosto.

I metodi statici e privati consentono il refactoring e l'organizzazione del codice usati per implementare l'API pubblica dell'interfaccia.

Un override di metodo in un'interfaccia deve usare la sintassi di implementazione esplicita dell'interfaccia.

È un errore dichiarare un tipo di classe, un tipo di struct o un tipo enum nell'ambito di un parametro di tipo dichiarato con un *variance_annotation*.  Ad esempio, la dichiarazione di `C` seguente è un errore.

```csharp
interface IOuter<out T>
{
    class C { } // error: class declaration within the scope of variant type parameter 'T'
}
```

### <a name="concrete-methods-in-interfaces"></a>Metodi concreti nelle interfacce

La forma più semplice di questa funzionalità è la possibilità di dichiarare un *metodo concreto* in un'interfaccia, ovvero un metodo con un corpo.

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
```

Una classe che implementa questa interfaccia non deve implementare il metodo concreto.

```csharp
class C : IA { } // OK

IA i = new C();
i.M(); // prints "IA.M"
```

L'override finale per `IA.M` nella classe `C` è il metodo concreto `M` dichiarato in `IA`. Si noti che una classe non eredita i membri dalle relative interfacce. Questa funzionalità non è stata modificata:

```csharp
new C().M(); // error: class 'C' does not contain a member 'M'
```

All'interno di un membro di istanza di un'interfaccia, `this` dispone del tipo dell'interfaccia di inclusione.

### <a name="modifiers-in-interfaces"></a>Modificatori nelle interfacce

La sintassi di un'interfaccia è semplificata per consentire i modificatori sui relativi membri. Sono consentiti gli elementi seguenti: `private`, `protected`, `internal`, `public`, `virtual`, `abstract`, `sealed`, `static`, `extern`e `partial`.

> ***Todo***: controllare gli altri modificatori esistenti.

Un membro di interfaccia la cui dichiarazione include un corpo è un membro `virtual` a meno che non venga usato il modificatore `sealed` o `private`. Il modificatore `virtual` può essere usato in un membro di funzione che altrimenti verrebbe `virtual`in modo implicito. Analogamente, anche se `abstract` è l'impostazione predefinita sui membri di interfaccia senza corpi, il modificatore può essere specificato in modo esplicito. Un membro non virtuale può essere dichiarato con la parola chiave `sealed`.

Si tratta di un errore per un `private` o `sealed` membro di una funzione di un'interfaccia privo di corpo. Un membro `private` funzione non può avere il modificatore `sealed`.

I modificatori di accesso possono essere usati sui membri di interfaccia di tutti i tipi di membri consentiti. Il livello di accesso `public` è l'impostazione predefinita, ma può essere specificato in modo esplicito.

> ***Problema aperto:*** È necessario specificare il significato preciso dei modificatori di accesso, ad esempio `protected` e `internal`, e quali dichiarazioni eseguono e non eseguono l'override (in un'interfaccia derivata) o implementarle (in una classe che implementa l'interfaccia).

Le interfacce possono dichiarare membri `static`, inclusi tipi annidati, metodi, indicizzatori, proprietà, eventi e costruttori statici. Il livello di accesso predefinito per tutti i membri di interfaccia è `public`.

Le interfacce non possono dichiarare costruttori di istanza, distruttori o campi.

> ***Problema chiuso:*** Le dichiarazioni degli operatori devono essere consentite in un'interfaccia? Probabilmente non si tratta di operatori di conversione, ma di altri? ***Decisione***: gli operatori sono consentiti *eccetto* gli operatori di conversione, uguaglianza e disuguaglianza.

> ***Problema chiuso:*** Deve `new` essere consentito nelle dichiarazioni dei membri di interfaccia che nascondono i membri dalle interfacce di base? ***Decisione***: Sì.

> ***Problema chiuso:*** Attualmente non è consentito `partial` su un'interfaccia o sui relativi membri. Che richiederebbe una proposta separata. ***Decisione***: Sì. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>

### <a name="overrides-in-interfaces"></a>Sostituzioni nelle interfacce

Le dichiarazioni di override, ad esempio quelle che contengono il modificatore `override`, consentono al programmatore di fornire un'implementazione più specifica di un membro virtuale in un'interfaccia in cui il compilatore o il runtime non ne troverà uno. Consente inoltre di trasformare un membro astratto da un'interfaccia super in un membro predefinito in un'interfaccia derivata. Una dichiarazione di override è consentita per eseguire l'override *esplicito* di un particolare metodo di interfaccia di base qualificando la dichiarazione con il nome dell'interfaccia (in questo caso non è consentito alcun modificatore di accesso). Non sono consentite sostituzioni implicite.

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); } // explicitly named
}
interface IC : IA
{
    override void M() { WriteLine("IC.M"); } // implicitly named
}
```

Le dichiarazioni di override nelle interfacce non possono essere dichiarate `sealed`.

È possibile eseguire l'override in modo esplicito dei membri della funzione public `virtual` in un'interfaccia in un'interfaccia derivata (qualificando il nome nella dichiarazione di override con il tipo di interfaccia che ha originariamente dichiarato il metodo e omettendo un modificatore di accesso).

`virtual` membri di funzione in un'interfaccia possono essere sottoposti a override in modo esplicito (non implicito) nelle interfacce derivate e i membri che non sono `public` possono essere implementati solo in una classe o in uno struct in modo esplicito (non in modo implicito). In entrambi i casi, il membro sottoposto a override o implementato deve essere *accessibile* se ne viene eseguito l'override.

### <a name="reabstraction"></a>Riastrazione

Un metodo virtuale (concreto) dichiarato in un'interfaccia può essere sottoposto a override in modo da essere astratto in un'interfaccia derivata

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    abstract void IA.M();
}
class C : IB { } // error: class 'C' does not implement 'IA.M'.
```

Il modificatore `abstract` non è obbligatorio nella dichiarazione di `IB.M` (ovvero l'impostazione predefinita nelle interfacce), ma è consigliabile essere esplicita in una dichiarazione di override.

Questa operazione è utile nelle interfacce derivate in cui l'implementazione predefinita di un metodo non è appropriata ed è necessario fornire un'implementazione più appropriata mediante l'implementazione delle classi.

> ***Problema aperto:*** È necessario consentire la ritrazione?

### <a name="the-most-specific-override-rule"></a>Regola di sostituzione più specifica

È necessario che ogni interfaccia e classe disponga di un *override più specifico* per ogni membro virtuale tra le sostituzioni visualizzate nel tipo o nelle relative interfacce dirette e indirette. L'override *più specifico* è un override univoco più specifico di ogni altro override. Se non è presente alcun override, il membro stesso viene considerato l'override più specifico.

Una sostituzione `M1` viene considerata *più specifica* di un'altra `M2` se `M1` viene dichiarata sul tipo `T1`, `M2` viene dichiarata nel tipo `T2`e

1. `T1` contiene `T2` tra le interfacce dirette o indirette oppure
2. `T2` è un tipo di interfaccia, ma `T1` non è un tipo di interfaccia.

Ad esempio:

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    void IA.M() { WriteLine("IC.M"); }
}
interface ID : IB, IC { } // error: no most specific override for 'IA.M'
abstract class C : IB, IC { } // error: no most specific override for 'IA.M'
abstract class D : IA, IB, IC // ok
{
    public abstract void M();
}

```

La regola di sostituzione più specifica garantisce che un conflitto, ad esempio un'ambiguità derivante dall'ereditarietà dei diamanti, venga risolto in modo esplicito dal programmatore nel momento in cui si verifica il conflitto.

Poiché è supportata l'esecuzione di override astratti espliciti nelle interfacce, è possibile eseguire questa operazione anche nelle classi

```csharp
abstract class E : IA, IB, IC // ok
{
    abstract void IA.M();
}
```

> ***Problema aperto***: è necessario supportare le sostituzioni esplicite dell'interfaccia esplicita nelle classi?

Inoltre, si tratta di un errore se in una dichiarazione di classe l'override più specifico di un metodo di interfaccia è un override astratto dichiarato in un'interfaccia. Si tratta di una regola esistente ridichiarata usando la nuova terminologia.

```csharp
interface IF
{
    void M();
}
abstract class F : IF { } // error: 'F' does not implement 'IF.M'
```

È possibile che una proprietà virtuale dichiarata in un'interfaccia disponga di un override più specifico per la relativa funzione di accesso `get` in un'interfaccia e un override più specifico per la relativa funzione di accesso `set` in un'interfaccia diversa. Questa operazione viene considerata una violazione della regola di *sostituzione più specifica* .

### <a name="static-and-private-methods"></a>Metodi `static` e `private`

Poiché le interfacce possono ora contenere codice eseguibile, è utile astrarre il codice comune nei metodi privati e statici. Queste sono ora consentite nelle interfacce.

> ***Problema chiuso***: è necessario supportare i metodi privati? Sono supportati i metodi statici? **Decisione: Sì**

> ***Problema aperto***: è necessario consentire ai metodi di interfaccia di essere `protected` o `internal` o altro accesso? In tal caso, qual è la semantica? Sono `virtual` per impostazione predefinita? In caso affermativo, è possibile renderli non virtuali?

> ***Problema di apertura***: se sono supportati i metodi statici, è necessario supportare gli operatori (statici)?

### <a name="base-interface-invocations"></a>Chiamate dell'interfaccia di base

Il codice in un tipo che deriva da un'interfaccia con un metodo predefinito può richiamare in modo esplicito l'implementazione "base" dell'interfaccia.

```csharp
interface I0
{
   void M() { Console.WriteLine("I0"); }
}
interface I1 : I0
{
   override void M() { Console.WriteLine("I1"); }
}
interface I2 : I0
{
   override void M() { Console.WriteLine("I2"); }
}
interface I3 : I1, I2
{
   // an explicit override that invoke's a base interface's default method
   void I0.M() { I2.base.M(); }
}

```

Un metodo di istanza (non statico) può richiamare l'implementazione di un metodo di istanza accessibile in un'interfaccia di base diretta in modo non virtuale assegnando loro un nome usando la sintassi `base(Type).M`. Questa operazione è utile quando viene risolta una sostituzione che è necessario fornire a causa dell'ereditarietà a rombo delegando a una particolare implementazione di base.

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    override void IA.M() { WriteLine("IC.M"); }
}

class D : IA, IB, IC
{
    void IA.M() { base(IB).M(); }
}
```

Quando si accede a un `virtual` o a un membro di `abstract` utilizzando la sintassi `base(Type).M`, è necessario che `Type` contenga un *override più specifico* univoco per `M`.

### <a name="binding-base-clauses"></a>Binding di clausole di base

Le interfacce ora contengono tipi.  Questi tipi possono essere utilizzati nella clausola di base come interfacce di base.  Quando si associa una clausola di base, potrebbe essere necessario individuare il set di interfacce di base per associare tali tipi (ad esempio, per eseguire la ricerca e per risolvere l'accesso protetto).  Il significato della clausola di base di un'interfaccia è quindi definito in modo circolare.  Per interrompere il ciclo, si aggiungono nuove regole della lingua corrispondenti a una regola simile già presente per le classi.

Quando si determina il significato del *interface_base* di un'interfaccia, le interfacce di base vengono temporaneamente considerate vuote. In modo intuitivo, questo garantisce che il significato di una clausola di base non possa dipendere in modo ricorsivo da se stesso. 

**Sono state usate le regole seguenti:**

"Quando una classe B deriva da una classe A, si tratta di un errore in fase di compilazione affinché un oggetto dipenda da B. Una classe **dipende direttamente** dalla relativa classe di base diretta, se presente, e **dipende direttamente** dalla ~~**classe**~~ in cui è immediatamente annidata (se presente). Data questa definizione, il set completo di ~~**classi**~~ da cui dipende una classe è la chiusura riflessiva e transitiva del **dipendente direttamente dalla** relazione ".

Si tratta di un errore in fase di compilazione per un'interfaccia che eredita direttamente o indirettamente da se stesso.
Le **interfacce** di base di un'interfaccia sono le interfacce di base esplicite e le relative interfacce di base. In altre parole, il set di interfacce di base è la chiusura transitiva completa delle interfacce di base esplicite, le interfacce di base esplicite e così via.

**Verranno regolate come indicato di seguito:**

Quando una classe B deriva da una classe A, si tratta di un errore in fase di compilazione affinché un oggetto dipenda da B. Una classe **dipende direttamente** dalla relativa classe di base diretta, se presente, e **dipende direttamente** dal _**tipo**_ in cui è immediatamente annidato (se presente).

Quando un'interfaccia IB estende un'interfaccia IA, si tratta di un errore in fase di compilazione perché IA dipenda da IB. Un'interfaccia **dipende direttamente** dalle interfacce di base dirette (se presenti) e **dipende direttamente** dal tipo in cui è immediatamente annidato (se presente).

Date queste definizioni, il set completo di **tipi** su cui dipende un tipo è la chiusura riflessiva e transitiva del **dipendente direttamente dalla** relazione.

### <a name="effect-on-existing-programs"></a>Effetti sui programmi esistenti

Le regole presentate in questo documento hanno lo scopo di non avere alcun effetto sul significato dei programmi esistenti.

Esempio 1:

```csharp
interface IA
{
    void M();
}
class C: IA // Error: IA.M has no concrete most specific override in C
{
    public static void M() { } // method unrelated to 'IA.M' because static
}
```

Esempio 2:

```csharp
interface IA
{
    void M();
}
class Base: IA
{
    void IA.M() { }
}
class Derived: Base, IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

Le stesse regole forniscono risultati simili alla situazione analoga che riguarda i metodi di interfaccia predefiniti:

```csharp
interface IA
{
    void M() { }
}
class Derived: IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

> ***Problema chiuso***: confermare che si tratta di una conseguenza prevista della specifica. **Decisione: Sì**

### <a name="runtime-method-resolution"></a>Risoluzione del metodo di runtime

> ***Problema chiuso:*** La specifica deve descrivere l'algoritmo di risoluzione del metodo di runtime nella faccia dei metodi predefiniti dell'interfaccia. È necessario assicurarsi che la semantica sia coerente con la semantica del linguaggio, ad esempio i metodi dichiarati e non eseguono l'override o implementano un metodo `internal`.

### <a name="clr-support-api"></a>API di supporto CLR

Per consentire ai compilatori di rilevare quando vengono compilati per un runtime che supporta questa funzionalità, le librerie per tali Runtime vengono modificate per annunciare tale fatto tramite l'API descritta in <https://github.com/dotnet/corefx/issues/17116>. Aggiungiamo

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeFeature
    {
        // Presence of the field indicates runtime support
        public const string DefaultInterfaceImplementation = nameof(DefaultInterfaceImplementation);
    }
}
```

> ***Problema aperto***: è il nome migliore per la funzionalità *CLR* ? La funzionalità CLR funziona molto più di quanto non solo (ad esempio, rilassa i vincoli di protezione, supporta le sostituzioni nelle interfacce e così via). Forse dovrebbe essere definito come "metodi concreti nelle interfacce" o "tratti"?

### <a name="further-areas-to-be-specified"></a>Altre aree da specificare

- [] Sarebbe utile catalogare i tipi di effetti di compatibilità binari e di origine causati dall'aggiunta di metodi di interfaccia predefiniti e delle sostituzioni alle interfacce esistenti.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Questa proposta richiede un aggiornamento coordinato alla specifica CLR (per supportare metodi concreti nelle interfacce e nella risoluzione del metodo). È quindi piuttosto "costoso" e può essere utile in combinazione con altre funzionalità che ci si aspettano di dover apportare modifiche a CLR.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

No.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- Le domande aperte sono denominate in tutto il progetto, sopra.
- Per un elenco di domande aperte, vedere anche <https://github.com/dotnet/csharplang/issues/406>.
- La specifica dettagliata deve descrivere il meccanismo di risoluzione utilizzato in fase di esecuzione per selezionare il metodo preciso da richiamare.
- L'interazione dei metadati prodotti da nuovi compilatori e utilizzata dai compilatori meno recenti deve essere elaborata in dettaglio. Ad esempio, è necessario assicurarsi che la rappresentazione dei metadati utilizzata non provochi l'aggiunta di un'implementazione predefinita in un'interfaccia per interrompere una classe esistente che implementa tale interfaccia quando viene compilata da un compilatore precedente. Questo può influire sulla rappresentazione dei metadati che è possibile usare.
- La progettazione deve considerare l'interoperabilità con altri linguaggi e compilatori esistenti per altri linguaggi.

## <a name="resolved-questions"></a>Domande risolte

### <a name="abstract-override"></a>Override astratto

La specifica bozza precedente conteneva la possibilità di "astrarre" un metodo ereditato:

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { }
}
interface IC : IB
{
    override void M(); // make it abstract again
}
```

Le mie note per 2017-03-20 hanno mostrato che abbiamo deciso di non consentire questo problema. Tuttavia, esistono almeno due casi d'uso:

1. Le API Java, con cui alcuni utenti di questa funzionalità hanno la speranza di interoperare, dipendono da questa funzionalità.
2. La programmazione con *tratti* trae vantaggio da questa. La ritrazione è uno degli elementi della funzionalità del linguaggio "tratti" (https://en.wikipedia.org/wiki/Trait_(computer_programming)). Con le classi è consentito quanto segue:

```csharp
public abstract class Base
{
    public abstract void M();
}
public abstract class A : Base
{
    public override void M() { }
}
public abstract class B : A
{
    public override abstract void M(); // reabstract Base.M
}
```

Sfortunatamente, questo codice non può essere sottoposto a refactoring come un set di interfacce (tratti), a meno che non sia consentito. Il *principio di Jared di greed*dovrebbe essere consentito.

> ***Problema chiuso:*** È necessario consentire la ritrazione? Sì Le mie note erano errate. Le [Note LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md) dicono che la ritrazione è consentita in un'interfaccia. Non in una classe.

### <a name="virtual-modifier-vs-sealed-modifier"></a>Modificatore virtuale rispetto al modificatore sealed

Da [Aleksey Tsingauz](https://github.com/AlekseyTs):

> Si è deciso di consentire ai modificatori indicati in modo esplicito sui membri dell'interfaccia, a meno che non esista un motivo per impedirne alcuni. Si tratta di una domanda interessante intorno al modificatore virtuale. Deve essere richiesto per i membri con implementazione predefinita?
>
> Possiamo dire:
>
> - Se non è presente alcuna implementazione e non è stato specificato alcun oggetto virtuale, né sealed, si presuppone che il membro sia astratto.
> - Se è presente un'implementazione, né abstract, né sealed, si presuppone che il membro sia virtuale.
> - il modificatore sealed è necessario per rendere un metodo non virtuale, né abstract.
>
> In alternativa, si potrebbe dire che il modificatore virtual è obbligatorio per un membro virtuale. Se, ad esempio, un membro con implementazione non è contrassegnato in modo esplicito con il modificatore virtual, non è né Virtual né abstract. Questo approccio potrebbe offrire un'esperienza migliore quando un metodo viene spostato da una classe a un'interfaccia:
>
> - un metodo astratto rimane astratto.
> - un metodo virtuale rimane virtuale.
> - un metodo senza modificatore non resta né Virtual né abstract.
> - non è possibile applicare il modificatore sealed a un metodo che non è un override.
>
> Che ne pensi?

> ***Problema chiuso:*** Un metodo concreto (con implementazione) deve essere `virtual`in modo implicito? Sì

***Decisioni:*** Realizzato in LDM 2017-04-05:

1. non virtuale deve essere espressa in modo esplicito tramite `sealed` o `private`.
2. `sealed` è la parola chiave per rendere i membri dell'istanza di interfaccia con corpi non virtuali
3. Si desidera consentire tutti i modificatori nelle interfacce  
4. L'accessibilità predefinita per i membri di interfaccia è Public, compresi i tipi annidati
5. i membri della funzione privata nelle interfacce sono implicitamente sealed e `sealed` non sono consentiti su di essi.
6. Le classi private (nelle interfacce) sono consentite e possono essere sealed e questo significa sealed nel senso della classe sealed.
7. In assenza di una proposta corretta, il parziale non è ancora consentito sulle interfacce o sui relativi membri.

### <a name="binary-compatibility-1"></a>Compatibilità binaria 1

Quando una libreria fornisce un'implementazione predefinita

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
}
class C : I2
{
}
```

Si è consapevoli che l'implementazione di `I1.M` in `C` è `I1.M`. Cosa accade se l'assembly contenente `I2` viene modificato come segue e ricompilato

```csharp
interface I2 : I1
{
    override void M() { Impl2 }
}
```

ma `C` non viene ricompilato. Cosa accade quando il programma viene eseguito? Chiamata di `(C as I1).M()`

1. Esegue `I1.M`
2. Esegue `I2.M`
3. Genera un tipo di errore di runtime

***Decisione:*** Prodotto 2017-04-11: esegue `I2.M`, che rappresenta l'override più specifico in modo non ambiguo in fase di esecuzione.

### <a name="event-accessors-closed"></a>Funzioni di accesso agli eventi (closed)

> ***Problema chiuso:*** È possibile eseguire l'override di un evento "a tratti"?

Considerare questo caso:

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        // error: "remove" accessor missing
    }
}
```

Questa implementazione "parziale" dell'evento non è consentita perché, come in una classe, la sintassi per una dichiarazione di evento non consente una sola funzione di accesso. è necessario specificare sia (che nessuno). È possibile eseguire la stessa operazione consentendo di astrarre in modo implicito la funzione di accesso abstract Remove nella sintassi per l'assenza di un corpo:

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        remove; // implicitly abstract
    }
}
```

Si noti che si *tratta di una nuova sintassi (proposta)* . Nella grammatica corrente, le funzioni di accesso agli eventi hanno un corpo obbligatorio.

> ***Problema chiuso:*** È possibile che una funzione di accesso agli eventi sia (in modo implicito) astratta dall'omissione di un corpo, in modo analogo al modo in cui i metodi nelle interfacce e nelle funzioni di accesso alle proprietà sono implicitamente astratti dall'omissione di un corpo?

***Decisione:*** (2017-04-18) No, le dichiarazioni di evento richiedono entrambe le funzioni di accesso concrete o nessuna delle due.

### <a name="reabstraction-in-a-class-closed"></a>Riastrazione in una classe (chiusa)

***Problema chiuso:*** È necessario confermare che questa operazione è consentita. in caso contrario, l'aggiunta di un'implementazione predefinita potrebbe essere una modifica di rilievo:

```csharp
interface I1
{
    void M() { }
}
abstract class C : I1
{
    public abstract void M(); // implement I1.M with an abstract method in C
}
```

***Decisione:*** (2017-04-18) Sì, l'aggiunta di un corpo a una dichiarazione di membro di interfaccia non deve interrompere C.

### <a name="sealed-override-closed"></a>Sostituzione sealed (chiusa)

Nella domanda precedente si presuppone implicitamente che il modificatore di `sealed` possa essere applicato a una `override` in un'interfaccia. In questo senso è contraddetta la specifica bozza. Si desidera consentire la chiusura di una sostituzione? Devono essere considerati gli effetti di compatibilità binari e di origine del sealing.

> ***Problema chiuso:*** È necessario consentire la chiusura di una sostituzione?

***Decisione:*** (2017-04-18) non è consentito `sealed` sulle sostituzioni nelle interfacce. L'unico utilizzo di `sealed` sui membri di interfaccia consiste nel renderli non virtuali nella dichiarazione iniziale.

### <a name="diamond-inheritance-and-classes-closed"></a>Ereditarietà e classi di diamanti (chiusa)

Il draft della proposta predilige le sostituzioni delle classi per le sostituzioni di interfaccia negli scenari di ereditarietà dei diamanti:

> È necessario che ogni interfaccia e classe dispongano di un *override più specifico* per ogni metodo di interfaccia tra le sostituzioni visualizzate nel tipo o nelle relative interfacce dirette e indirette. L'override *più specifico* è un override univoco più specifico di ogni altro override. Se non è presente alcun override, il metodo stesso viene considerato l'override più specifico.
>
> Una sostituzione `M1` viene considerata *più specifica* di un'altra `M2` se `M1` viene dichiarata sul tipo `T1`, `M2` viene dichiarata nel tipo `T2`e
>
> 1. `T1` contiene `T2` tra le interfacce dirette o indirette oppure
> 2. `T2` è un tipo di interfaccia, ma `T1` non è un tipo di interfaccia.

Lo scenario è il seguente

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { WriteLine("IB"); }
}
class Base : IA
{
    void IA.M() { WriteLine("Base"); }
}
class Derived : Base, IB // allowed?
{
    static void Main()
    {
        Ia a = new Derived();
        a.M();           // what does it do?
    }
}
```

È necessario confermare questo comportamento (o decidere diversamente)

> ***Problema chiuso:*** Confermare la specifica bozza, sopra, per *la sostituzione più specifica* , in quanto si applica a classi e interfacce miste (una classe ha la priorità su un'interfaccia). Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#diamonds-with-classes>.

### <a name="interface-methods-vs-structs-closed"></a>Metodi di interfaccia vs struct (closed)

Esistono alcune spiacevoli interazioni tra i metodi di interfaccia predefiniti e gli struct.

```csharp
interface IA
{
    public void M() { }
}
struct S : IA
{
}
```

Si noti che i membri di interfaccia non vengono ereditati:

```csharp
var s = default(S);
s.M(); // error: 'S' does not contain a member 'M'
```

Di conseguenza, il client deve eseguire il box dello struct per richiamare i metodi di interfaccia

```csharp
IA s = default(S); // an S, boxed
s.M(); // ok
```

La conversione boxing in questo modo vanifica i vantaggi principali di un tipo di `struct`. Inoltre, tutti i metodi di mutazione non avranno alcun effetto evidente, perché funzionano su una *copia boxed* dello struct:

```csharp
interface IB
{
    public void Increment() { P += 1; }
    public int P { get; set; }
}
struct T : IB
{
    public int P { get; set; } // auto-property
}

T t = default(T);
Console.WriteLine(t.P); // prints 0
(t as IB).Increment();
Console.WriteLine(t.P); // prints 0
```

> ***Problema chiuso:*** Cosa è possibile fare:
>
> 1. Impedire a un `struct` di ereditare un'implementazione predefinita. Tutti i metodi di interfaccia verrebbero considerati come astratti in una `struct`. In seguito, potrebbe essere necessario un po' di tempo per decidere come migliorarne il funzionamento.
> 2. Si tratta di un tipo di strategia di generazione del codice che evita la conversione boxing. All'interno di un metodo come `IB.Increment`, il tipo di `this` sarebbe probabilmente analogo a un parametro di tipo vincolato al `IB`. In combinazione con tale, per evitare la conversione boxing nel chiamante, i metodi non astratti verrebbero ereditati dalle interfacce. Questo può migliorare notevolmente il lavoro di implementazione del compilatore e del CLR.
> 3. Senza preoccuparsi di questo aspetto e lasciarlo semplicemente come una verruca.
> 4. Altre idee?

***Decisione:*** Senza preoccuparsi di questo aspetto e lasciarlo semplicemente come una verruca. Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#structs-and-default-implementations>.

### <a name="base-interface-invocations-closed"></a>Chiamate dell'interfaccia di base (chiuse)

La specifica bozza suggerisce una sintassi per le chiamate dell'interfaccia di base ispirate da Java: `Interface.base.M()`. È necessario selezionare una sintassi, almeno per il prototipo iniziale. Il mio preferito è `base<Interface>.M()`.

> ***Problema chiuso:*** Qual è la sintassi per la chiamata di un membro di base?

***Decisione:*** La sintassi è `base(Interface).M()`. Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>. L'interfaccia denominata deve essere un'interfaccia di base, ma non deve essere un'interfaccia di base diretta.

> ***Problema aperto:*** È necessario consentire le chiamate dell'interfaccia di base nei membri della classe?

***Decisione***: Sì. <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>

### <a name="overriding-non-public-interface-members-closed"></a>Override dei membri di interfaccia non pubblici (closed)

In un'interfaccia i membri non pubblici delle interfacce di base vengono sottoposti a override usando il modificatore `override`. Se si tratta di un override "esplicito" che assegna un nome all'interfaccia contenente il membro, il modificatore di accesso viene omesso.

> ***Problema chiuso:*** Se si tratta di un override "implicito" che non assegna un nome all'interfaccia, il modificatore di accesso deve corrispondere?

***Decisione:*** Solo i membri pubblici possono essere sottoposti a override in modo implicito e l'accesso deve corrispondere. Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.

> ***Problema aperto:*** Il modificatore di accesso è obbligatorio, facoltativo o omesso in un override esplicito, ad esempio `override void IB.M() {}`?

> ***Problema aperto:*** È `override` obbligatorio, facoltativo o omesso in un override esplicito, ad esempio `void IB.M() {}`?

In che modo un membro di interfaccia non pubblico viene implementato in una classe? Forse è necessario eseguire questa operazione in modo esplicito?

```csharp
interface IA
{
    internal void MI();
    protected void MP();
}
class C : IA
{
    // are these implementations?
    internal void MI() {}
    protected void MP() {}
}
```

> ***Problema chiuso:*** In che modo un membro di interfaccia non pubblico viene implementato in una classe?

***Decisione:*** È possibile implementare i membri di interfaccia non pubblici in modo esplicito. Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.

***Decisione***: nessuna parola chiave `override` consentita nei membri di interfaccia. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>

### <a name="binary-compatibility-2-closed"></a>Compatibilità binaria 2 (chiusa)

Si consideri il codice seguente in cui ogni tipo si trova in un assembly separato

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
    override void M() { Impl2 }
}
interface I3 : I1
{
}
class C : I2, I3
{
}
```

Si è consapevoli che l'implementazione di `I1.M` in `C` è `I2.M`. Cosa accade se l'assembly contenente `I3` viene modificato come segue e ricompilato

```csharp
interface I3 : I1
{
    override void M() { Impl3 }
}
```

ma `C` non viene ricompilato. Cosa accade quando il programma viene eseguito? Chiamata di `(C as I1).M()`

1. Esegue `I1.M`
2. Esegue `I2.M`
3. Esegue `I3.M`
4. 2 o 3, in modo deterministico
5. Genera un tipo di eccezione di runtime

***Decisione***: generare un'eccezione (5). Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#issues-in-default-interface-methods>.

### <a name="permit-partial-in-interface-closed"></a>Consentire `partial` nell'interfaccia? chiuso

Dato che le interfacce possono essere utilizzate in modo analogo al modo in cui vengono utilizzate le classi astratte, può essere utile dichiararle `partial`. Questa operazione è particolarmente utile in caso di generatori.

> ***Proposta:*** Rimuovere la restrizione della lingua che le interfacce e i membri delle interfacce non possono essere dichiarati `partial`.

***Decisione***: Sì. Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>.

### <a name="main-in-an-interface-closed"></a>`Main` in un'interfaccia? chiuso

> ***Problema aperto:*** Un metodo di `static Main` in un'interfaccia è candidato a essere il punto di ingresso del programma?

***Decisione***: Sì. Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#main-in-an-interface>.

### <a name="confirm-intent-to-support-public-non-virtual-methods-closed"></a>Conferma finalità per supportare metodi non virtuali pubblici (closed)

È possibile confermare o annullare la decisione di consentire metodi pubblici non virtuali in un'interfaccia?

```csharp
interface IA
{
    public sealed void M() { }
}
```

> ***Problema semi-chiuso:*** (2017-04-18) pensiamo che sia utile, ma che tornerà. Si tratta di un blocco di attivazione del modello mentale.

***Decisione***: Sì. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#confirm-that-we-support-public-non-virtual-methods>.

### <a name="does-an-override-in-an-interface-introduce-a-new-member-closed"></a>Un `override` in un'interfaccia introduce un nuovo membro? chiuso

Esistono diversi modi per osservare se una dichiarazione di override introduce o meno un nuovo membro.

```csharp
interface IA
{
    void M(int x) { }
}
interface IB : IA
{
    override void M(int y) { }
}
interface IC : IB
{
    static void M2()
    {
        M(y: 3); // permitted?
    }
    override void IB.M(int z) { } // permitted? What does it override?
}
```

> ***Problema aperto:*** Una dichiarazione di override in un'interfaccia introduce un nuovo membro? chiuso

In una classe un metodo di override è "visibile" in alcuni sensi. I nomi dei parametri, ad esempio, hanno la precedenza sui nomi dei parametri nel metodo sottoposto a override. Potrebbe essere possibile duplicare questo comportamento nelle interfacce, in quanto esiste sempre un override più specifico. Si vuole però duplicare questo comportamento?

Inoltre, è possibile eseguire l'override di un metodo di override? Discutibile

***Decisione***: nessuna parola chiave `override` consentita nei membri di interfaccia. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>.

### <a name="properties-with-a-private-accessor-closed"></a>Proprietà con una funzione di accesso privata (closed)

Si dice che i membri privati non sono virtuali e la combinazione di virtuali e privati non è consentita. Ma cosa accade per una proprietà con una funzione di accesso privata?

```csharp
interface IA
{
    public virtual int P
    {
        get => 3;
        private set => { }
    }
}
```

Questa operazione è consentita? La funzione di accesso `set` è `virtual` o meno? È possibile eseguirne l'override laddove è accessibile? Il codice seguente implementa in modo implicito solo la funzione di accesso `get`?

```csharp
class C : IA
{
    public int P
    {
        get => 4;
        set { }
    }
}
```

Si tratta probabilmente di un errore in quanto IA. P. set non è virtuale e anche perché non è accessibile?

```csharp
class C : IA
{
    int IA.P
    {
        get => 4;
        set { }
    }
}
```

***Decisione***: il primo esempio è valido, mentre l'ultima non lo è. Questo problema viene risolto in modo analogo a come funziona C#già in. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#properties-with-a-private-accessor>

### <a name="base-interface-invocations-round-2-closed"></a>Chiamate dell'interfaccia di base, Round 2 (closed)

La "Risoluzione" precedente per gestire le chiamate di base non fornisce effettivamente un'espressività sufficiente. Si scopre che in C# e CLR, a differenza di Java, è necessario specificare sia l'interfaccia che contiene la dichiarazione del metodo che la posizione dell'implementazione da richiamare.

Si propone la sintassi seguente per le chiamate di base nelle interfacce. Non mi piace, ma viene illustrato il modo in cui qualsiasi sintassi deve essere in grado di esprimere:

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I4 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>(I1).M(); // calls I3's implementation of I1.M
        base<I4>(I1).M(); // calls I4's implementation of I1.M
    }
    void I2.M()
    {
        base<I3>(I2).M(); // calls I3's implementation of I2.M
        base<I4>(I2).M(); // calls I4's implementation of I2.M
    }
}
```

In assenza di ambiguità, è possibile scriverla più semplicemente

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I4 : I1 { void I1.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>.M(); // calls I3's implementation of I1.M
        base<I4>.M(); // calls I4's implementation of I1.M
    }
}
```

Oppure

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base(I1).M(); // calls I3's implementation of I1.M
    }
    void I2.M()
    {
        base(I2).M(); // calls I3's implementation of I2.M
    }
}
```

Oppure

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base.M(); // calls I3's implementation of I1.M
    }
}
```

***Decisione***: si è deciso di `base(N.I1<T>).M(s)`, che se è presente un'associazione di chiamata, è possibile che si verifichi un problema in un secondo momento. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md#default-interface-implementations>

### <a name="warning-for-struct-not-implementing-default-method-closed"></a>Avviso per struct che non implementa il metodo predefinito? chiuso

@vancem dichiara che è consigliabile generare un avviso se una dichiarazione del tipo di valore non è in grado di eseguire l'override di un metodo di interfaccia, anche se eredita un'implementazione di tale metodo da un'interfaccia. Poiché causa la conversione boxing e le chiamate vincolate.

***Decisione***: questo aspetto è più adatto per un analizzatore. Sembra anche che questo avviso potrebbe essere fastidioso, perché verrebbe attivato anche se il metodo di interfaccia predefinito non viene mai chiamato e non verrà mai eseguita alcuna conversione boxing. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#warning-for-struct-not-implementing-default-method>

### <a name="interface-static-constructors-closed"></a>Costruttori statici di interfaccia (closed)

Quando vengono eseguiti i costruttori statici di interfaccia?  La bozza corrente dell'interfaccia della riga di comando si propone di verificarsi quando si accede al primo metodo o campo statico. Se non sono presenti, è possibile che non venga mai eseguita?

[2018-10-09 il team CLR propone "rispecchiare le operazioni da eseguire per i ValueType (controllo cctor per l'accesso a ogni metodo di istanza)"]

***Decisione***: i costruttori statici vengono eseguiti anche in presenza di metodi di istanza, se il costruttore statico non è `beforefieldinit`, nel qual caso i costruttori statici vengono eseguiti prima dell'accesso al primo campo statico. <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#when-are-interface-static-constructors-run>

## <a name="design-meetings"></a>Riunioni di progettazione

[2017-03-08 note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-08.md) sulla riunione LDM
[2017-03-21 LDM note sulla riunione](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md)
[2017-03-23 riunione "comportamento CLR per i metodi di interfaccia predefiniti"](https://github.com/dotnet/csharplang/blob/master/meetings/2017/CLR-2017-03-23.md)
[2017-04-05 LDM riunioni note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md)
[2017-04-11 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-11.md)
[2017-04-18 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md)
[2017-04-19 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md)
[2017-05-17 LDM riunione](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-17.md) note
[2017-05-31 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md) [
2017-06-14 LDM Note sulla riunione](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-14.md)
2018-10-17 note sulla riunione [LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
[2018-11-14 LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md)
