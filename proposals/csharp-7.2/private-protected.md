---
ms.openlocfilehash: 6cf489595654236c18edee94c0af380e605c9571
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485175"
---
# <a name="private-protected"></a>protetto privato

* [x] proposto
* [x] prototipo: [completato](https://github.com/dotnet/roslyn/blob/master/docs/features/private-protected.md)
* Implementazione di [x]: [completa](https://github.com/dotnet/roslyn/blob/master/docs/features/private-protected.md)
* [x] Specifica: [completa](#detailed-design)

## <a name="summary"></a>Summary
[summary]: #summary

Esporre il livello di accessibilità `protectedAndInternal` C# CLR in come `private protected`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

In molti casi un'API contiene membri che devono essere implementati e usati solo dalle sottoclassi contenute nell'assembly che fornisce il tipo. Mentre CLR fornisce un livello di accessibilità a tale scopo, non è disponibile in C#. Di conseguenza, i proprietari delle API sono costretti a usare la protezione `internal` e la disciplina autonoma o un analizzatore personalizzato oppure di usare `protected` con documentazione aggiuntiva che spieghi che, mentre il membro viene visualizzato nella documentazione pubblica per il tipo, non è destinato a essere parte dell'API pubblica.  Per esempi di questi ultimi, vedere membri del `CSharpCompilationOptions` di Roslyn i cui nomi iniziano con `Common`.

Fornire direttamente il supporto per questo livello di C# accesso in consente di esprimere le circostanze in modo naturale nel linguaggio.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

### <a name="private-protected-access-modifier"></a>modificatore di accesso `private protected`

Si propone di aggiungere una nuova combinazione di modificatori di accesso `private protected`, che può essere presente in qualsiasi ordine tra i modificatori. Viene eseguito il mapping alla nozione CLR di protectedAndInternal e viene presa in prestito la stessa sintassi attualmente utilizzata in [ C++/CLI](https://docs.microsoft.com/cpp/dotnet/how-to-define-and-consume-classes-and-structs-cpp-cli#BKMK_Member_visibility).

È possibile accedere a un membro dichiarato `private protected` all'interno di una sottoclasse del relativo contenitore se tale sottoclasse si trova nello stesso assembly del membro.

La specifica del linguaggio viene modificata come segue (aggiunte in grassetto). I numeri di sezione non vengono mostrati di seguito poiché possono variare a seconda della versione della specifica in cui è integrata.

-----

> L'accessibilità dichiarata di un membro può essere una delle seguenti:
- Public, che viene selezionato includendo un modificatore public nella dichiarazione del membro. Il significato intuitivo del pubblico è "accesso non limitato".
- Protected, che viene selezionato includendo un modificatore protected nella dichiarazione del membro. Il significato intuitivo di Protected è "accesso limitato alla classe o ai tipi derivati dalla classe che lo contiene".
- Internal, che viene selezionato includendo un modificatore interno nella dichiarazione del membro. Il significato intuitivo di Internal è "accesso limitato a questo assembly".
- Interno protetto, che viene selezionato includendo sia un modificatore protetto che un modificatore interno nella dichiarazione del membro. Il significato intuitivo di internal protected è "accessibile all'interno di questo assembly e dei tipi derivati dalla classe che lo contiene".
- **Privato protetto, che viene selezionato includendo sia un modificatore privato che un modificatore protetto nella dichiarazione del membro. Il significato intuitivo di private protected è "accessibile all'interno di questo assembly dai tipi derivati dalla classe che lo contiene".**

-----

> A seconda del contesto in cui si verifica una dichiarazione di membro, sono consentiti solo determinati tipi di accessibilità dichiarata. Inoltre, quando una dichiarazione di membro non include modificatori di accesso, il contesto in cui viene eseguita la dichiarazione determina l'accessibilità dichiarata predefinita. 
- Gli spazi dei nomi dispongono implicitamente di accessibilità dichiarata pubblica. Non sono consentiti modificatori di accesso nelle dichiarazioni dello spazio dei nomi.
- I tipi dichiarati direttamente in unità di compilazione o spazi dei nomi (anziché all'interno di altri tipi) possono avere accessibilità dichiarata pubblica o interna e per impostazione predefinita l'accessibilità dichiarata interna.
- I membri della classe possono avere uno dei cinque tipi di accessibilità dichiarata e l'accessibilità privata dichiarata come predefinita. [Nota: un tipo dichiarato come membro di una classe può avere uno dei cinque tipi di accessibilità dichiarata, mentre un tipo dichiarato come membro di uno spazio dei nomi può avere solo l'accessibilità dichiarata pubblica o interna. Nota finale]
- I membri struct possono avere accessibilità dichiarata pubblica, interna o privata e il valore predefinito per l'accessibilità privata dichiarata perché gli struct sono implicitamente sealed. I membri struct introdotti in uno struct (ovvero non ereditato da tale struct) non possono avere l'accessibilità*protetta o* ~~protetta~~ privata o protetta**privata** . [Nota: un tipo dichiarato come membro di uno struct può avere un'accessibilità dichiarata pubblica, interna o privata, mentre un tipo dichiarato come membro di uno spazio dei nomi può avere solo l'accessibilità dichiarata pubblica o interna. Nota finale]
- I membri di interfaccia hanno implicitamente accessibilità dichiarata pubblica. Non sono consentiti modificatori di accesso nelle dichiarazioni di membri di interfaccia.
- I membri di enumerazione hanno implicitamente accessibilità dichiarata pubblica. Non sono consentiti modificatori di accesso per le dichiarazioni di membri di enumerazione.

-----

> Il dominio di accessibilità di un membro annidato M dichiarato in un tipo T all'interno di un programma P, viene definito come segue (notando che M potrebbe essere un tipo):
- Se l'accessibilità dichiarata di M è pubblica, il dominio di accessibilità di M è il dominio di accessibilità di T.
- Se l'accessibilità dichiarata di M è protetta interna, consentire a D essere l'Unione del testo del programma P e il testo di programma di qualsiasi tipo derivato da T, che viene dichiarato all'esterno di P. Il dominio di accessibilità di M è l'intersezione del dominio di accessibilità di T con D.
- **Se l'accessibilità dichiarata di M è privatamente protetta, lasciare che l'intersezione del testo di programma di P e il testo di programma di qualsiasi tipo derivato da T. Il dominio di accessibilità di M è l'intersezione del dominio di accessibilità di T con D.**
- Se l'accessibilità dichiarata di M è protetta, consentire a D essere l'Unione del testo di programma di T e il testo di programma di qualsiasi tipo derivato da T. Il dominio di accessibilità di M è l'intersezione del dominio di accessibilità di T con D.
- Se l'accessibilità dichiarata di M è interna, il dominio di accessibilità di M è l'intersezione del dominio di accessibilità di T con il testo di programma P.
- Se l'accessibilità dichiarata di M è privata, il dominio di accessibilità di M è il testo di programma di T.

-----

> Quando si accede a un membro di istanza protetta **o privata** protetta all'esterno del testo di programma della classe in cui viene dichiarata e quando si accede a un membro di istanza interno protetto all'esterno del testo del programma in cui viene dichiarato, l'accesso deve essere eseguito all'interno di una dichiarazione di classe che deriva dalla classe in cui è dichiarata. Inoltre, è necessario che l'accesso avvenga tramite un'istanza del tipo di classe derivata o di un tipo di classe costruito. Questa restrizione impedisce a una classe derivata di accedere ai membri protetti di altre classi derivate, anche quando i membri vengono ereditati dalla stessa classe di base.

-----

> I modificatori di accesso consentiti e l'accesso predefinito per una dichiarazione di tipo dipendono dal contesto in cui si verifica la dichiarazione (§ 9.5.2):
- I tipi dichiarati in unità di compilazione o spazi dei nomi possono avere accesso pubblico o interno. Il valore predefinito è accesso interno.
- I tipi dichiarati nelle classi possono avere accesso pubblico, protetto interno, **privato protetto**, protetto, interno o privato. Il valore predefinito è accesso privato.
- I tipi dichiarati in struct possono avere accesso pubblico, interno o privato. Il valore predefinito è accesso privato.

-----

> Una dichiarazione di classe statica è soggetta alle restrizioni seguenti:
- Una classe statica non deve includere un modificatore sealed o abstract. Tuttavia, poiché non è possibile creare un'istanza o derivare da una classe statica, si comporta come se fosse sealed e abstract.
- Una classe statica non deve includere una specifica di base della classe (§ 16.2.5) e non può specificare in modo esplicito una classe base o un elenco di interfacce implementate. Una classe statica eredita in modo implicito dall'oggetto Type.
- Una classe statica deve contenere solo membri statici (§ 16.4.8). [Nota: tutte le costanti e i tipi annidati sono classificati come membri statici. Nota finale]
- Una classe statica non deve avere membri con accessibilità dichiarata internamente protetta **,** protetta o protetta.

> Si tratta di un errore in fase di compilazione per violare una qualsiasi di queste restrizioni. 

-----

> Una dichiarazione di membro di classe può avere uno dei ~~cinque~~**sei** tipi possibili di accessibilità dichiarata (§ 9.5.2): Public, **private protected**, protected internal, protected, Internal o private. Ad eccezione**delle combinazioni protette**interne **e private protette** , si tratta di un errore in fase di compilazione per specificare più di un modificatore di accesso. Quando una dichiarazione di membro di classe non include alcun modificatore di accesso, si presuppone che sia privato.

-----

> I tipi non annidati possono avere accessibilità dichiarata pubblica o interna e hanno un'accessibilità dichiarata interna per impostazione predefinita. I tipi annidati possono avere anche queste forme di accessibilità dichiarata, oltre a una o più forme aggiuntive di accessibilità dichiarate, a seconda che il tipo che lo contiene sia una classe o uno struct:
- Un tipo annidato dichiarato in una classe può avere ~~cinque~~**sei** forme di accessibilità dichiarata (Public, **private protected**, protected internal, protected, Internal o private) e, come altri membri della classe, per impostazione predefinita l'accessibilità dichiarata privata.
- Un tipo annidato dichiarato in uno struct può avere tre forme di accessibilità dichiarata (pubblica, interna o privata) e, come altri membri struct, per impostazione predefinita l'accessibilità dichiarata privata.

-----

> Il metodo sottoposto a override da una dichiarazione di override è noto come metodo di base sottoposto a override per un metodo di override M dichiarato in una classe C, il metodo di base sottoposto a override viene determinato esaminando ogni tipo di classe di base di C, a partire dal tipo di classe di base diretta di C e Continuando con ogni tipo di classe base diretta successiva, finché in un determinato tipo di classe base viene individuato almeno un metodo accessibile con la stessa firma di M dopo la sostituzione degli argomenti di tipo. Per individuare il metodo di base sottoposto a override, un metodo viene considerato accessibile se è pubblico, se è protetto, se è interno protetto o **se è protetto internamente** **o privato** e dichiarato nello stesso programma di C.

-----

> L'uso di modificatori di funzione di accesso è regolato dalle restrizioni seguenti:
- Un modificatore di funzione di accesso non deve essere usato in un'interfaccia o in un'implementazione esplicita di un membro di interfaccia.
- Per una proprietà o un indicizzatore senza modificatori di override, un modificatore di funzione di accesso è consentito solo se la proprietà o l'indicizzatore ha entrambe le funzioni di accesso get e set, quindi è consentito solo in una di queste funzioni di accesso.
- Per una proprietà o un indicizzatore che include un modificatore di override, una funzione di accesso deve corrispondere al modificatore della funzione di accesso, se presente, della funzione di accesso sottoposta a override.
- Il modificatore della funzione di accesso deve dichiarare un'accessibilità strettamente più restrittiva dell'accessibilità dichiarata della proprietà o dell'indicizzatore stesso. Per essere precisi:
  - Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di Public, il modificatore della funzione di accesso può essere **protetto da privato**, protetto interno, interno, protetto o privato.
  - Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di protected internal, il modificatore della funzione di accesso può essere **privato protetto**, interno, protetto o privato.
  - Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di Internal o protected, il modificatore della funzione di accesso deve essere privato **o protetto** .
  - **Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di private protected, il modificatore della funzione di accesso deve essere privato.**
  - Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di private, non è possibile usare alcun modificatore di funzione di accesso.

-----

> Poiché l'ereditarietà non è supportata per gli struct, l'accessibilità dichiarata di un membro struct non può essere protetta, **privata protetta**o protetta interna.

-----

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Come per qualsiasi funzionalità del linguaggio, è necessario stabilire se la complessità aggiuntiva al linguaggio venga ripagata in base a una maggiore chiarezza offerta C# al corpo dei programmi che trarrebbero vantaggio dalla funzionalità.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Un'alternativa è il provisioning di un'API che combina un attributo e un analizzatore. L'attributo viene inserito dal programmatore su un membro `internal` per indicare che il membro deve essere usato solo nelle sottoclassi e l'analizzatore verifica che tali restrizioni siano rispettate. 

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

L'implementazione è in gran parte completata. L'unico elemento di lavoro aperto sta redigendo una specifica corrispondente per VB.

## <a name="design-meetings"></a>Riunioni di progettazione

TBD
