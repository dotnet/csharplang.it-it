---
ms.openlocfilehash: 1c3d05674f8f7b69e70e0d9e06021537fc45f7ed
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229661"
---
# <a name="basic-concepts"></a>Concetti di base

## <a name="application-startup"></a>Avvio dell'applicazione

Un assembly con un ***punto di ingresso*** viene chiamato un ***applicazione***. Quando un'applicazione viene eseguita, una nuova ***dominio applicazione*** viene creato. Più istanze diverse di un'applicazione possono esistere nello stesso computer contemporaneamente e ognuno ha il proprio dominio applicazione.

Un dominio applicazione permette l'isolamento dell'applicazione che agisce come contenitore per lo stato dell'applicazione. Un dominio dell'applicazione funge da contenitore e limiti per i tipi definiti nell'applicazione e Usa le librerie di classi. I tipi caricati in un dominio dell'applicazione sono distinti dello stesso tipo caricato in un altro dominio applicazione e le istanze degli oggetti non sono condivisi tra domini delle applicazioni direttamente. Ad esempio, ogni dominio dell'applicazione ha una propria copia di variabili statiche per questi tipi e un costruttore statico per un tipo viene eseguito al massimo una volta per ogni dominio dell'applicazione. Le implementazioni sono gratuite fornire criteri specifici dell'implementazione o meccanismi per la creazione e distruzione dei domini applicazione.

***Avvio dell'applicazione*** si verifica quando l'ambiente di esecuzione chiama un metodo designato, che fa riferimento come punto di ingresso dell'applicazione. Questo metodo del punto di ingresso è sempre denominato `Main`e può avere uno dei seguenti firme:

```csharp
static void Main() {...}

static void Main(string[] args) {...}

static int Main() {...}

static int Main(string[] args) {...}
```

Come indicato, il punto di ingresso può facoltativamente restituire un `int` valore. Questo valore restituito viene utilizzato nella chiusura dell'applicazione ([chiusura dell'applicazione](basic-concepts.md#application-termination)).

Il punto di ingresso possa facoltativamente avere un parametro formale. Il parametro può avere qualsiasi nome, ma il tipo del parametro deve essere `string[]`. Se il parametro formale è presente, l'ambiente di esecuzione viene creato e passa un `string[]` argomento che contiene gli argomenti della riga di comando che sono stati specificati quando è stata avviata l'applicazione. Il `string[]` argomento non è mai null, ma può avere una lunghezza pari a zero se nessun argomento della riga di comando specificato.

Poiché c# supporta l'overload, una classe o struct può contenere più definizioni di un metodo, purché ognuna ha una firma diversa. Tuttavia, all'interno di un unico programma, nessuna classe o struttura può contenere più di un metodo chiamato `Main` la cui definizione ne può essere usato come punto di ingresso dell'applicazione. Altre versioni di overload `Main` sono consentite, tuttavia, purché questi hanno più di un parametro, o un unico parametro è diverso dal tipo `string[]`.

Un'applicazione può essere composto da più classi o struct. È possibile che più di una di queste classi o struct possono contenere un metodo denominato `Main` la cui definizione ne può essere usato come punto di ingresso dell'applicazione. In questi casi, un meccanismo esterno (ad esempio, un'opzione della riga di comando del compilatore) deve essere utilizzato per selezionare uno di questi `Main` metodi come punto di ingresso.

In c#, ogni metodo deve essere definito come membro di una classe o struct. In genere, l'accessibilità dichiarata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)) di un metodo è determinato dai modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) specificato nella relativa dichiarazione e allo stesso modo dichiarato accessibilità di un tipo è determinato dai modificatori di accesso specificati nella relativa dichiarazione. Affinché un determinato metodo di un determinato tipo possa essere chiamata, il tipo e il membro deve essere accessibile. Tuttavia, il punto di ingresso dell'applicazione è un caso speciale. In particolare, l'ambiente di esecuzione può accedere a punto di ingresso dell'applicazione indipendentemente dall'accessibilità dichiarata e indipendentemente dall'accessibilità dichiarata del relativo contenitore dichiarazioni di tipo.

Il metodo di punto di ingresso dell'applicazione potrebbe non essere in una dichiarazione di classe generica.

Tutti gli altri aspetti, i metodi del punto di ingresso si comportano come gli utenti che non punti di ingresso.

## <a name="application-termination"></a>Chiusura dell'applicazione

***Chiusura dell'applicazione*** restituisce il controllo all'ambiente di esecuzione.

Se il tipo restituito dell'applicazione ***punto di ingresso*** consiste `int`, il valore restituito viene utilizzato come l'applicazione ***codice di stato di chiusura***. Lo scopo di questo codice è per consentire la comunicazione di esito positivo o negativo per l'ambiente di esecuzione.

Se il tipo restituito del metodo del punto di ingresso `void`, raggiunge la parentesi graffa destra (`}`) che termina che metodo o l'esecuzione di un `return` istruzione che è presente alcuna espressione, come risultato un codice di stato di chiusura `0`.

Prima della chiusura di un'applicazione, vengono chiamati i distruttori di tutti i relativi oggetti che non sono ancora stato sottoposto a garbage collection, a meno che non è stato soppresso questo tipo di pulizia (da una chiamata al metodo libreria `GC.SuppressFinalize`, ad esempio).

## <a name="declarations"></a>Dichiarazioni

Le dichiarazioni in un programma c# definiscono gli elementi costitutivi del programma. Organizzazione dei programmi c# usando gli spazi dei nomi ([spazi dei nomi](namespaces.md)), che può contenere tipo dichiarazioni e le dichiarazioni dello spazio dei nomi annidati. Dichiarazioni di tipo ([dichiarazioni di tipo](namespaces.md#type-declarations)) vengono usate per definire le classi ([classi](classes.md)), struct ([struct](structs.md)), le interfacce ([interfacce](interfaces.md) ), le enumerazioni ([enumerazioni](enums.md)), i delegati e ([delegati](delegates.md)). I tipi di membri consentiti in una dichiarazione del tipo variano a seconda forma di dichiarazione del tipo. Ad esempio, le dichiarazioni di classe possono contenere dichiarazioni per le costanti ([costanti](classes.md#constants)), i campi ([campi](classes.md#fields)), i metodi ([metodi](classes.md#methods)), le proprietà ([ Le proprietà](classes.md#properties)), gli eventi ([eventi](classes.md#events)), gli indicizzatori ([indicizzatori](classes.md#indexers)), gli operatori ([operatori](classes.md#operators)), costruttori di istanze ([ Costruttori di istanze](classes.md#instance-constructors)), i costruttori statici ([costruttori statici](classes.md#static-constructors)), distruttori ([distruttori](classes.md#destructors)) e i tipi annidati ([tipiannidati](classes.md#nested-types)).

Una dichiarazione definisce un nome nel ***spazio di dichiarazione*** a cui appartiene la dichiarazione. Ad eccezione dei membri di overload ([firme e overload](basic-concepts.md#signatures-and-overloading)), è un errore in fase di compilazione hanno due o più dichiarazioni che introducono i membri con lo stesso nome in uno spazio di dichiarazione. Non è mai possibile che uno spazio di dichiarazione per contenere diversi tipi di membri con lo stesso nome. Ad esempio, uno spazio di dichiarazione non può mai contenere un campo e un metodo con lo stesso nome.

Esistono diversi tipi di spazi di dichiarazione, come descritto di seguito.

*  All'interno di tutti i file di origine di un programma *namespace_member_declaration*s con alcun tipo di inclusione *namespace_declaration* sono membri di un singola dichiarazione combinato spazio il ***globale spazio di dichiarazione***.
*  All'interno di tutti i file di origine di un programma *namespace_member_declaration*entro *namespace_declaration*che hanno lo stesso nome completo dello spazio dei nomi sono membri di una singola dichiarazione combinato spazio.
*  Ogni classe, struct o dichiarazione di interfaccia crea un nuovo spazio di dichiarazione. I nomi vengono introdotti in questo spazio di dichiarazione attraverso *class_member_declaration*s *struct_member_declaration*s *interface_member_declaration*s, o *type_parameter*s. Ad eccezione di costruttore di overload istanza dichiarazioni e un costruttore statico dichiarazioni, una classe o struct non può contenere una dichiarazione di membro con lo stesso nome della classe o struct. Una classe, struct o interfaccia consente la dichiarazione di metodi di overload e indicizzatori. Inoltre, una classe o struct consente la dichiarazione di costruttori di istanze in overload e operatori. Ad esempio, una classe, struct o interfaccia contenga più dichiarazioni di metodo con lo stesso nome, purché queste dichiarazioni differente abbiano una firma ([firme e overload](basic-concepts.md#signatures-and-overloading)). Si noti che le classi di base non vengono conteggiati per lo spazio di dichiarazione di una classe e le interfacce di base non vengono conteggiati per lo spazio di dichiarazione di un'interfaccia. Di conseguenza, una classe derivata o un'interfaccia è consentito dichiarare un membro con lo stesso nome di un membro ereditato. Tale membro viene detto ***nascondere*** il membro ereditato.
*  Ogni dichiarazione di delegato crea un nuovo spazio di dichiarazione. I nomi vengono introdotti in questo spazio di dichiarazione tramite i parametri formali (*fixed_parameter*s e *parameter_array*s) e *type_parameter*s.
*  Ogni dichiarazione di enumerazione crea un nuovo spazio di dichiarazione. I nomi vengono introdotti in questo spazio di dichiarazione attraverso *enum_member_declarations*.
*  Ogni dichiarazione di metodo, la dichiarazione di indicizzatore, dichiarazioni di operatore, dichiarazione di costruttore di istanza e funzione anonima consente di creare un nuovo spazio di dichiarazione chiamato un' ***dello spazio di dichiarazione di variabile locale***. I nomi vengono introdotti in questo spazio di dichiarazione tramite i parametri formali (*fixed_parameter*s e *parameter_array*s) e *type_parameter*s. Il corpo della funzione membro o della funzione anonima, se presente, viene considerato l'annidamento nello spazio di dichiarazione di variabile locale. È un errore per uno spazio di dichiarazione di variabile locale e uno spazio di dichiarazione di variabile locale annidati per contenere gli elementi con lo stesso nome. Di conseguenza, all'interno di uno spazio di dichiarazione annidati non è possibile dichiarare una variabile locale costante o variabile o costante con lo stesso nome di una variabile locale in uno spazio di dichiarazione che lo contiene. È possibile che due spazi di dichiarazione contenere gli elementi con lo stesso nome, purché nessuno spazio di dichiarazione contiene l'altro.
*  Ogni *blocco* oppure *switch_block* , nonché una *per*, *foreach* e *usando* istruzione, crea un spazio di dichiarazione di variabile locale per le variabili locali e le costanti locali. I nomi vengono introdotti in questo spazio di dichiarazione attraverso *local_variable_declaration*s e *local_constant_declaration*s. Si noti che i blocchi che si verificano come o all'interno del corpo di una funzione anonima o un membro di funzione sono annidati nello spazio di dichiarazione di variabile locale dichiarato da tali funzioni per i relativi parametri. In questo modo è errato, ad esempio un metodo con un parametro lo stesso nome e una variabile locale.
*  Ciascuna *block* oppure *switch_block* crea uno spazio di dichiarazione separata per le etichette. I nomi vengono introdotti in questo spazio di dichiarazione attraverso *labeled_statement*s e i nomi vengono fatto riferimento tramite *goto_statement*s. Il ***etichettare spazio di dichiarazione*** di un blocco include tutti i blocchi annidati. Di conseguenza, all'interno di un blocco annidato non è possibile dichiarare un'etichetta con lo stesso nome di un'etichetta in un blocco di inclusione.

L'ordine testuale in cui vengono dichiarati i nomi è in genere, non è importante. Ordine testuale in particolare, non è significativo per la dichiarazione e utilizzo di spazi dei nomi, costanti, metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori, i costruttori statici e tipi. Ordine di dichiarazione è significativo nei modi seguenti:

*  Ordine di dichiarazione per le dichiarazioni di campo e dichiarazioni di variabile locale determina l'ordine in cui vengono eseguiti i relativi inizializzatori (se presente).
*  Le variabili locali devono essere definite prima che vengano utilizzate ([ambiti](basic-concepts.md#scopes)).
*  Ordine di dichiarazione per le dichiarazioni di membro di enumerazione ([membri Enum](enums.md#enum-members)) è significativo quando *constant_expression* i valori vengono omessi.

Lo spazio di dichiarazione di uno spazio dei nomi è "aperta" e lo spazio dei nomi due dichiarazioni con lo stesso nome completo contribuiscono allo stesso spazio di dichiarazione. Esempio:
```csharp
namespace Megacorp.Data
{
    class Customer
    {
        ...
    }
}

namespace Megacorp.Data
{
    class Order
    {
        ...
    }
}
```

le due dichiarazioni dello spazio dei nomi sopra contribuiscono allo stesso spazio di dichiarazione, in questo caso dichiarare due classi con nomi completi `Megacorp.Data.Customer` e `Megacorp.Data.Order`. Poiché le due dichiarazioni contribuiscono allo stesso spazio di dichiarazione, si verificherebbe un errore in fase di compilazione se ognuno contiene una dichiarazione di una classe con lo stesso nome.

Come specificato in precedenza, lo spazio di dichiarazione di un blocco include tutti i blocchi annidati. Pertanto, nell'esempio seguente, il `F` e `G` metodi generato un errore in fase di compilazione perché il nome `i` viene dichiarato in un blocco esterno e non può essere ridichiarato nel blocco interno. Tuttavia, il `H` e `I` metodi sono validi perché i due `i`del vengono dichiarati in blocchi separati non annidata.

```csharp
class A
{
    void F() {
        int i = 0;
        if (true) {
            int i = 1;            
        }
    }

    void G() {
        if (true) {
            int i = 0;
        }
        int i = 1;                
    }

    void H() {
        if (true) {
            int i = 0;
        }
        if (true) {
            int i = 1;
        }
    }

    void I() {
        for (int i = 0; i < 10; i++)
            H();
        for (int i = 0; i < 10; i++)
            H();
    }
}
```

## <a name="members"></a>Membri

Gli spazi dei nomi e tipi hanno ***membri***. I membri di un'entità sono in genere disponibili tramite l'uso di un nome completo che inizia con un riferimento all'entità, seguito da un "`.`" token, seguito dal nome del membro.

I membri di un tipo sono dichiarati nella dichiarazione del tipo oppure ***ereditata*** dalla classe di base del tipo. Quando un tipo eredita da una classe di base, tutti i membri della classe di base, ad eccezione di costruttori di istanze, distruttori e i costruttori statici, diventano membri del tipo derivato. L'accessibilità dichiarata di un membro della classe base non controlla se il membro viene ereditato, ereditarietà si estende a tutti i membri che non sono un costruttore di istanza, costruttore statico o distruttore. Tuttavia, un membro ereditato non sia accessibile in un tipo derivato, a causa l'accessibilità dichiarata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)) o perché è nascosta da una dichiarazione del tipo stesso ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)).

### <a name="namespace-members"></a>Membri di Namespace

Spazi dei nomi e tipi che non dispongono di alcun spazio dei nomi contenitore sono membri del ***spazio dei nomi globale***. Corrisponde direttamente ai nomi dichiarati nello spazio di dichiarazione globale.

Gli spazi dei nomi e i tipi dichiarati all'interno di uno spazio dei nomi sono membri di tale spazio dei nomi. Corrisponde direttamente ai nomi dichiarati nello spazio di dichiarazione dello spazio dei nomi.

Gli spazi dei nomi non hanno restrizioni di accesso. Non è possibile dichiarare gli spazi dei nomi privato, protetto o interno e spazi dei nomi sono sempre accessibili pubblicamente.

### <a name="struct-members"></a>Membri struct

I membri di uno struct sono i membri dichiarati nella struttura e i membri ereditati dalla classe base diretta dello struct `System.ValueType` e la classe base indiretta `object`.

I membri di un tipo semplice corrispondono direttamente ai membri del tipo di struct con alias dal tipo semplice:

*  I membri del `sbyte` sono i membri del `System.SByte` struct.
*  I membri del `byte` sono i membri del `System.Byte` struct.
*  I membri del `short` sono i membri del `System.Int16` struct.
*  I membri del `ushort` sono i membri del `System.UInt16` struct.
*  I membri del `int` sono i membri del `System.Int32` struct.
*  I membri del `uint` sono i membri del `System.UInt32` struct.
*  I membri del `long` sono i membri del `System.Int64` struct.
*  I membri del `ulong` sono i membri del `System.UInt64` struct.
*  I membri del `char` sono i membri del `System.Char` struct.
*  I membri del `float` sono i membri del `System.Single` struct.
*  I membri del `double` sono i membri del `System.Double` struct.
*  I membri del `decimal` sono i membri del `System.Decimal` struct.
*  I membri del `bool` sono i membri del `System.Boolean` struct.

### <a name="enumeration-members"></a>Membri dell'enumerazione

I membri di un'enumerazione sono costanti dichiarate nell'enumerazione e i membri ereditati dalla classe base diretta dell'enumerazione `System.Enum` e le classi di base indirette `System.ValueType` e `object`.

### <a name="class-members"></a>Membri della classe

I membri di una classe sono i membri dichiarati nella classe e i membri ereditati dalla classe di base (ad eccezione di classe `object` che non dispone di alcuna classe di base). I membri ereditati dalla classe di base includono le costanti, campi, metodi, proprietà, eventi, indicizzatori, operatori e tipi di classe di base, ma non i costruttori di istanze, distruttori e i costruttori statici della classe di base. I membri di classe di base vengono ereditati senza considerare l'accessibilità.

Una dichiarazione di classe può contenere le dichiarazioni delle costanti, campi, metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori, i costruttori statici e i tipi.

I membri del `object` e `string` corrispondono direttamente ai membri dei tipi di classe costituiscono gli alias:

*  I membri del `object` sono i membri del `System.Object` classe.
*  I membri del `string` sono i membri del `System.String` classe.

### <a name="interface-members"></a>Membri di interfaccia

I membri di un'interfaccia sono i membri dichiarati nell'interfaccia e in tutte le interfacce di base dell'interfaccia. I membri nella classe `object` non lo sono, in senso stretto, membri di un'interfaccia ([membri di interfaccia](interfaces.md#interface-members)). Tuttavia, i membri nella classe `object` sono disponibili tramite ricerca di membri in qualsiasi tipo di interfaccia ([ricerca di membri](expressions.md#member-lookup)).

### <a name="array-members"></a>Membri di matrici

I membri di una matrice sono i membri ereditati dalla classe `System.Array`.

### <a name="delegate-members"></a>Membri di delegato

I membri di un delegato sono i membri ereditati dalla classe `System.Delegate`.

## <a name="member-access"></a>Accesso ai membri

Le dichiarazioni dei membri consentono di controllare l'accesso al membro. L'accessibilità di un membro viene stabilito dall'accessibilità dichiarata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)) del membro combinati con l'accessibilità del tipo contenitore, se presente.

Quando è consentito l'accesso a un determinato membro, il membro viene detto ***accessibile***. Per contro, non è consentito l'accesso a un determinato membro, il membro è detta ***inaccessibile***. Accesso a un membro è consentito quando la posizione di testo in cui si desidera accedere è incluso nel dominio di accessibilità ([domini accessibilità](basic-concepts.md#accessibility-domains)) del membro.

### <a name="declared-accessibility"></a>Accessibilità dichiarata

Il ***accessibilità dichiarata*** di un membro può essere uno dei seguenti:

*  Pubblico, che viene selezionato, includendo un `public` modificatore nella dichiarazione del membro. Intuibile `public` ad "non accesso limitato".
*  Protetto, che è selezionata, includendo un `protected` modificatore nella dichiarazione del membro. Intuibile `protected` è "accesso limitato alla classe o i tipi derivato dalla classe di appartenenza".
*  Interno, che viene selezionato, includendo un `internal` modificatore nella dichiarazione del membro. Intuibile `internal` ad "accesso limitato a questo programma".
*  Protected internal (ovvero protetto o interno), che è selezionata, includendo entrambi una `protected` e un `internal` modificatore nella dichiarazione del membro. Intuibile `protected internal` è "accesso limitato a questo programma o ai tipi derivati dalla classe di appartenenza".
*  Private, che viene selezionata, includendo un `private` modificatore nella dichiarazione del membro. Intuibile `private` ad "accesso limitato al tipo contenitore".

A seconda del contesto in cui è stata eseguita una dichiarazione di membro posizionate, sono consentiti solo determinati tipi di accessibilità dichiarata. Inoltre, quando una dichiarazione di membro non include tutti i modificatori di accesso, il contesto in cui viene eseguita la dichiarazione determina l'impostazione predefinita l'accessibilità dichiarata.

*  Gli spazi dei nomi dispongono implicitamente `public` l'accessibilità dichiarata. Nessun modificatore di accesso sono consentito su dichiarazioni dello spazio dei nomi.
*  I tipi dichiarati in unità di compilazione o gli spazi dei nomi possono avere `public` oppure `internal` l'accessibilità dichiarata per impostazione predefinita a `internal` l'accessibilità dichiarata.
*  I membri di classe possono avere uno dei cinque tipi di accessibilità dichiarata e per impostazione predefinita `private` l'accessibilità dichiarata. (Si noti che il tipo dichiarato come un membro di una classe può avere uno dei cinque tipi di accessibilità dichiarata, mentre il tipo dichiarato come un membro di uno spazio dei nomi può avere solo `public` o `internal` l'accessibilità dichiarata.)
*  I membri struct possono avere `public`, `internal`, o `private` l'accessibilità dichiarata per impostazione predefinita a `private` l'accessibilità dichiarata poiché gli struct sono sealed in modo implicito. I membri struct introdotti in una struttura (ovvero, non è ereditata da tale struct) non possono avere `protected` o `protected internal` l'accessibilità dichiarata. (Si noti che il tipo dichiarato come un membro di uno struct può avere `public`, `internal`, o `private` dichiarato accessibilità, mentre il tipo dichiarato come un membro di uno spazio dei nomi può avere solo `public` o `internal` l'accessibilità dichiarata.)
*  I membri di interfaccia dispongono implicitamente `public` l'accessibilità dichiarata. Nessun modificatore di accesso sono consentito su dichiarazioni di membro di interfaccia.
*  I membri dell'enumerazione dispongono implicitamente `public` l'accessibilità dichiarata. Nessun modificatore di accesso sono consentito su dichiarazioni di membro di enumerazione.

### <a name="accessibility-domains"></a>Domini di accessibilità

Il ***dominio di accessibilità*** di un membro include le sezioni di testo di programma in cui è consentito l'accesso al membro (probabilmente non contigui). Ai fini della definizione del dominio di accessibilità di un membro, viene definito un membro ***principale*** se non è dichiarato all'interno di un tipo e un membro viene detto ***nidificata*** se è dichiarata all'interno di un altro tipo. Inoltre, il ***testo di programma*** di un programma viene definito come tutti i programmi contenuto in tutti i file di origine del programma di testo e il testo di un tipo di programma viene definito come tutti i programmi contenuto nel testo di *type_declaration*s di quel tipo (inclusi gli eventuali tipi annidati all'interno del tipo).

Il dominio di accessibilità di un tipo predefinito (ad esempio `object`, `int`, o `double`) è illimitato.

Tipo non associato di dominio di accessibilità di un livello superiore `T` ([associate e non associato tipi](types.md#bound-and-unbound-types)) che viene dichiarato in un programma `P` viene definito come segue:

*  Se l'accessibilità dichiarata di `T` viene `public`, il dominio di accessibilità `T` è il testo del programma `P` e qualsiasi programma che fa riferimento a `P`.
*  Se l'accessibilità dichiarata di `T` è `internal`, il dominio di accessibilità di `T` è il testo di programma di `P`.

Da queste definizioni si deduce che il dominio di accessibilità di un tipo non associato di primo livello sia sempre almeno il testo di programma del programma in cui tale tipo viene dichiarato.

Il dominio di accessibilità per un tipo costruito `T<A1, ..., An>` rappresenta l'intersezione del dominio di accessibilità del tipo generico non associato `T` e i domini di accessibilità di argomenti di tipo `A1, ..., An`.

Il dominio di accessibilità di un membro annidato `M` dichiarati nel tipo `T` all'interno di un programma `P` viene definito come segue (notare che `M` potrebbe eventualmente essere un tipo):

*  Se l'accessibilità dichiarata di `M` è `public`, il dominio di accessibilità di `M` è il dominio di accessibilità di `T`.
*  Se l'accessibilità dichiarata di `M` viene `protected internal`, consentono `D` l'unione del testo del programma `P` e il testo di programma di qualsiasi tipo derivato da `T`, che viene dichiarato all'esterno `P`. Il dominio di accessibilità `M` rappresenta l'intersezione tra il dominio di accessibilità `T` con `D`.
*  Se l'accessibilità dichiarata di `M` viene `protected`, consentono `D` essere l'unione tra il testo del programma `T` e il testo di programma di qualsiasi tipo derivato da `T`. Il dominio di accessibilità `M` rappresenta l'intersezione tra il dominio di accessibilità `T` con `D`.
*  Se l'accessibilità dichiarata di `M` è `internal`, il dominio di accessibilità di `M` è l'intersezione del dominio di accessibilità di `T` con il testo di programma di `P`.
*  Se l'accessibilità dichiarata di `M` è `private`, il dominio di accessibilità di `M` è il testo di programma di `T`.

Da queste definizioni si deduce che il dominio di accessibilità di un membro annidato è sempre almeno il testo di programma del tipo in cui viene dichiarato il membro. Inoltre, ne consegue che il dominio di accessibilità di un membro non è mai più inclusivo rispetto al dominio di accessibilità del tipo in cui viene dichiarato il membro.

In pratica, quando un tipo o membro `M` è accessibile, la procedura seguente viene valutata per assicurarsi che l'accesso è consentito:

*  Innanzitutto, se `M` dichiarata all'interno di un tipo (come anziché un'unità di compilazione o uno spazio dei nomi), si verifica un errore in fase di compilazione se tale tipo non è accessibile.
*  Quindi, se `M` è `public`, l'accesso è consentito.
*  In caso contrario, se `M` viene `protected internal`, l'accesso è consentito se si verifica all'interno del programma in cui `M` viene dichiarata, o se si verifica all'interno di una classe derivata dalla classe in cui `M` viene dichiarata e viene eseguita tramite la derivata tipo di classe ([protetto, ad esempio accedere a membri](basic-concepts.md#protected-access-for-instance-members)).
*  In caso contrario, se `M` viene `protected`, l'accesso è consentito se si verifica all'interno della classe in cui `M` viene dichiarata, o se si verifica all'interno di una classe derivata dalla classe in cui `M` viene dichiarata e viene eseguita tramite la derivata tipo di classe ([protetto, ad esempio accedere a membri](basic-concepts.md#protected-access-for-instance-members)).
*  In caso contrario, se `M` viene `internal`, l'accesso è consentito se si verifica all'interno del programma in cui `M` viene dichiarato.
*  In caso contrario, se `M` viene `private`, l'accesso è consentito se si verifica all'interno del tipo in cui `M` viene dichiarato.
*  In caso contrario, il tipo o membro è accessibile e si verifica un errore in fase di compilazione.

Nell'esempio
```csharp
public class A
{
    public static int X;
    internal static int Y;
    private static int Z;
}

internal class B
{
    public static int X;
    internal static int Y;
    private static int Z;

    public class C
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }

    private class D
    {
        public static int X;
        internal static int Y;
        private static int Z;
    }
}
```
le classi e membri sono i domini di accessibilità seguenti:

*  Il dominio di accessibilità `A` e `A.X` è illimitato.
*  Il dominio di accessibilità `A.Y`, `B`, `B.X`, `B.Y`, `B.C`, `B.C.X`, e `B.C.Y` è il testo di programma del programma che lo contiene.
*  Il dominio di accessibilità `A.Z` è il testo del programma `A`.
*  Il dominio di accessibilità `B.Z` e `B.D` è il testo del programma `B`, incluso il testo del programma `B.C` e `B.D`.
*  Il dominio di accessibilità `B.C.Z` è il testo del programma `B.C`.
*  Il dominio di accessibilità `B.D.X` e `B.D.Y` è il testo del programma `B`, incluso il testo del programma `B.C` e `B.D`.
*  Il dominio di accessibilità `B.D.Z` è il testo del programma `B.D`.

Come illustrato nell'esempio, il dominio di accessibilità di un membro non è mai maggiore rispetto a quello di un tipo contenitore. Ad esempio, anche se tutti i `X` i membri hanno accessibilità dichiarata pubblica, tutto tranne `A.X` hanno accessibilità domini che sono vincolati da un tipo che lo contiene.

Come descritto in [membri](basic-concepts.md#members), tutti i membri di una classe di base, tranne ad esempio costruttori, distruttori e i costruttori statici, vengono ereditati dai tipi derivati. Ciò include anche ai membri privati di una classe base. Tuttavia, il dominio di accessibilità di un membro privato include solo il testo di programma del tipo in cui viene dichiarato il membro. Nell'esempio
```csharp
class A
{
    int x;

    static void F(B b) {
        b.x = 1;        // Ok
    }
}

class B: A
{
    static void F(B b) {
        b.x = 1;        // Error, x not accessible
    }
}
```
il `B` classe eredita il membro privato `x` dal `A` classe. Poiché il membro è privato, sarà accessibile solo entro le *class_body* di `A`. Di conseguenza, l'accesso al `b.x` ha esito positivo nel `A.F` metodo, ma non riesce nel `B.F` (metodo).

### <a name="protected-access-for-instance-members"></a>Accesso protetto per i membri di istanza

Quando un `protected` membro di istanza viene eseguito all'esterno del testo di programma della classe in cui è dichiarata, e quando un `protected internal` membro di istanza viene eseguito all'esterno del testo di programma del programma in cui è dichiarata, l'accesso deve avvenire all'interno di un dichiarazione di classe che deriva dalla classe in cui è dichiarata. Inoltre, l'accesso è necessaria per sfruttare tramite un'istanza di quel tipo di classe derivata o di un tipo di classe costruita da quest'ultimo. Questa restrizione impedisce l'accesso ai membri protetti delle altre classi derivate, anche quando i membri sono ereditati dalla stessa classe di base di una classe derivata.

Let `B` da una classe di base che dichiara un membro di istanza protetta `M`e consentire `D` essere una classe che deriva da `B`. All'interno di *class_body* dei `D`, l'accesso a `M` può assumere uno dei formati seguenti:

*  Un non qualificato *type_name* oppure *primary_expression* nel formato `M`.
*  Oggetto *primary_expression* del form `E.M`, purché il tipo di `E` viene `T` o una classe derivata da `T`, dove `T` è il tipo di classe `D`, o un tipo di classe creato da `D`
*  Oggetto *primary_expression* del form `base.M`.

Oltre a questi tipi di accesso, una classe derivata può accedere a un costruttore di istanza protetta di una classe base in un *constructor_initializer* ([inizializzatori del costruttore](classes.md#constructor-initializers)).

Nell'esempio
```csharp
public class A
{
    protected int x;

    static void F(A a, B b) {
        a.x = 1;        // Ok
        b.x = 1;        // Ok
    }
}

public class B: A
{
    static void F(A a, B b) {
        a.x = 1;        // Error, must access through instance of B
        b.x = 1;        // Ok
    }
}
```
all'interno `A`, è possibile accedere `x` tramite le istanze di entrambe `A` e `B`, dal momento che in entrambi i casi l'accesso avviene tramite un'istanza di `A` o una classe derivata da `A`. Tuttavia, all'interno `B`, non è possibile accedere ai `x` tramite un'istanza del `A`, poiché `A` non deriva da `B`.

Nell'esempio
```csharp
class C<T>
{
    protected T x;
}

class D<T>: C<T>
{
    static void F() {
        D<T> dt = new D<T>();
        D<int> di = new D<int>();
        D<string> ds = new D<string>();
        dt.x = default(T);
        di.x = 123;
        ds.x = "test";
    }
}
```
le assegnazioni a tre `x` sono consentite perché vengono eseguite tramite le istanze di tipi di classe costruiti dal tipo generico.

### <a name="accessibility-constraints"></a>Vincoli di accessibilità

Alcuni costrutti del linguaggio C# richiedono un tipo può essere ***accessibile almeno quanto come*** un membro o un altro tipo. Un tipo `T` viene definito per essere accessibili almeno quanto un tipo o membro `M` se il dominio di accessibilità `T` è un superset di dominio di accessibilità di `M`. In altre parole, `T` è accessibile almeno quanto come `M` se `T` accessibile in tutti i contesti in cui `M` è accessibile.

Sono disponibili i vincoli di accessibilità seguenti:

*  La classe di base diretta di un tipo di classe deve essere accessibile almeno quanto il tipo di classe.
*  Le interfacce di base esplicite di un tipo di interfaccia devono essere accessibili almeno quanto il tipo di interfaccia.
*  Il tipo restituito e i tipi di parametro di un tipo delegato devono essere accessibili almeno quanto il tipo delegato.
*  Il tipo di una costante deve essere accessibile almeno quanto la costante.
*  Il tipo di un campo deve essere accessibile almeno quanto il campo.
*  Il tipo restituito e i tipi di parametro di un metodo devono essere accessibili almeno quanto il metodo.
*  Il tipo di una proprietà deve essere accessibile almeno quanto la proprietà.
*  Il tipo di un evento deve essere accessibile almeno quanto l'evento.
*  Il tipo e i tipi di parametro di un indicizzatore devono essere accessibili almeno quanto l'indicizzatore.
*  Il tipo restituito e i tipi di parametro di un operatore devono essere accessibili almeno quanto l'operatore.
*  I tipi di parametro di un costruttore di istanza devono essere accessibili almeno quanto il costruttore di istanza.

Nell'esempio
```csharp
class A {...}

public class B: A {...}
```
il `B` classe genera un errore in fase di compilazione in quanto `A` non è accessibile almeno quanto come `B`.

Analogamente, nell'esempio
```csharp
class A {...}

public class B
{
    A F() {...}

    internal A G() {...}

    public A H() {...}
}
```
il `H` nel metodo `B` genera un errore in fase di compilazione perché il tipo restituito `A` non è accessibile almeno quanto il metodo.

## <a name="signatures-and-overloading"></a>Firme e overload

I metodi, costruttori di istanza, indicizzatori e gli operatori sono caratterizzati da loro ***firme***:

*  La firma di un metodo è costituito il nome del metodo, il numero di parametri di tipo e dal tipo (valore, riferimento o output) della ognuno dei parametri formali, considerati in ordine da sinistra a destra. A tale scopo, viene identificato qualsiasi parametro di tipo del metodo che si verifica nel tipo di un parametro formale non in base al nome, ma in base alla posizione ordinale nell'elenco di argomenti tipo del metodo. La firma di un metodo, in particolare, non include il tipo restituito, il `params` modificatore che può essere specificato per il parametro all'estrema destra, né i vincoli del parametro di tipo facoltativi.
*  La firma di un costruttore di istanza è costituita dal tipo (valore, riferimento o output) della ognuno dei parametri formali, considerati in ordine da sinistra a destra. La firma di un costruttore di istanza, in particolare, non include il `params` modificatore che può essere specificato per il parametro più a destra.
*  La firma di un indicizzatore è costituita dal tipo della ognuno dei parametri formali, considerati in ordine da sinistra a destra. La firma di un indicizzatore, in particolare, non include il tipo di elemento, né include la `params` modificatore che può essere specificato per il parametro più a destra.
*  La firma di un operatore è costituito il nome dell'operatore e il tipo della ognuno dei parametri formali, considerati in ordine da sinistra a destra. In particolare, la firma di un operatore non include il tipo di risultato.

Le firme sono il meccanismo di attivazione per ***overload*** dei membri di classi, struct e interfacce:

*  L'overload dei metodi consente a una classe, struct o interfaccia per dichiarare più metodi con lo stesso nome, purché le relative firme siano univoche all'interno di tale classe, struct o interfaccia.
*  L'overload dei costruttori di istanza consente a una classe o struct per dichiarare più costruttori di istanza, purché le relative firme siano univoche all'interno di tale classe o struct.
*  L'overload di indicizzatori consente a una classe, struct o interfaccia per dichiarare più indicizzatori purché le relative firme siano univoche all'interno di tale classe, struct o interfaccia.
*  Overload degli operatori consente a una classe o struct per dichiarare più operatori con lo stesso nome, purché le relative firme siano univoche all'interno di tale classe o struct.

Sebbene `out` e `ref` modificatori di parametro sono considerati parte di una firma, i membri dichiarati in un solo tipo non può essere diversa nella firma esclusivamente dalla `ref` e `out`. Si verifica un errore in fase di compilazione se due membri vengono dichiarati nello stesso tipo con firme che sarebbe la stessa se tutti i parametri in entrambi i metodi con `out` modificatori sono stati modificati per `ref` modificatori. Per altri scopi di firma corrispondente (ad esempio, nascondere o si esegue l'override), `ref` e `out` sono considerate parte della firma e non corrispondono tra loro. (Questa limitazione consiste nel consentire C# ai programmi di essere tradotta con facilità per l'esecuzione su di Common Language Infrastructure (CLI), che non fornisce un modo per definire metodi che differiscono unicamente nella `ref` e `out`.)

Ai fini di firme, i tipi `object` e `dynamic` sono considerati uguali. I membri dichiarati in un unico tipo può essere pertanto non diverso nella firma esclusivamente dalla `object` e `dynamic`.

Nell'esempio seguente mostra un set di dichiarazioni di metodo di overload e le relative firme.
```csharp
interface ITest
{
    void F();                        // F()

    void F(int x);                   // F(int)

    void F(ref int x);               // F(ref int)

    void F(out int x);               // F(out int)      error

    void F(int x, int y);            // F(int, int)

    int F(string s);                 // F(string)

    int F(int x);                    // F(int)          error

    void F(string[] a);              // F(string[])

    void F(params string[] a);       // F(string[])     error
}
```

Si noti che eventuali `ref` e `out` modificatori di parametro ([i parametri del metodo](classes.md#method-parameters)) fanno parte di una firma. Pertanto `F(int)` e `F(ref int)` sono firme univoche. Tuttavia `F(ref int)` e `F(out int)` non possono essere dichiarati all'interno dell'interfaccia stessa, dal momento che differiscono dal `ref` e `out`. Inoltre, tenere presente che il tipo restituito e il `params` modificatore non fanno parte di una firma, pertanto non è possibile eseguire l'overload esclusivamente in base al tipo restituito o di inclusione o esclusione del `params` modificatore. Pertanto, le dichiarazioni dei metodi `F(int)` e `F(params string[])` identificato in precedenza generano un errore in fase di compilazione.

## <a name="scopes"></a>Ambiti

Il ***ambito*** è l'area di testo di programma all'interno del quale è possibile fare riferimento a entità dichiarata tramite il nome senza qualifica del nome di un nome. Gli ambiti possono essere ***nidificata***, e un ambito interno possa dichiarare di nuovo il significato di un nome da un ambito esterno (questa operazione non, tuttavia, rimuove la restrizione imposta dal [dichiarazioni](basic-concepts.md#declarations) che all'interno di un blocco annidato non è è possibile eseguire per dichiarare una variabile locale con lo stesso nome come una variabile locale in un blocco di inclusione). Il nome utilizzato nell'ambito esterno viene quindi definito ***nascosto*** nell'area del programma di testo coperto da ambito interno e l'accesso per il nome esterno è solo possibile qualificando il nome.

*  L'ambito di un membro dello spazio dei nomi dichiarato tramite un *namespace_member_declaration* ([membri Namespace](namespaces.md#namespace-members)) con alcun tipo di inclusione *namespace_declaration* è l'intero programma testo.
*  L'ambito di un membro dello spazio dei nomi dichiarato tramite un *namespace_member_declaration* all'interno di un *namespace_declaration* il cui nome completo è `N` è il *namespace_body*  di ogni *namespace_declaration* il cui nome completo è `N` o inizia con `N`, seguito da un punto.
*  L'ambito del nome definito da un *extern_alias_directive* consente di estendere tramite il *using_directive*s, *global_attributes* e *namespace_member_ dichiarazione*s della relativa compilazione unità oppure lo spazio dei nomi nel corpo. Un' *extern_alias_directive* non include alcun nuovo membro allo spazio di dichiarazione sottostante. In altre parole, un' *extern_alias_directive* non è transitiva, ma, invece, interessa solo la compilazione unità oppure lo spazio dei nomi del corpo in cui si verifica.
*  L'ambito di un nome definito né importato da un *using_directive* ([direttive Using](namespaces.md#using-directives)) si estende il *namespace_member_declaration*s del  *compilation_unit* oppure *namespace_body* in cui il *using_directive* si verifica. Oggetto *using_directive* può rendere disponibili all'interno di un particolare oggetto zero o più nomi di spazio dei nomi, tipo o membro *compilation_unit* oppure *namespace_body*, ma non contribuire con nuovi membri allo spazio di dichiarazione sottostante. In altre parole, una *using_directive* non è transitiva, ma piuttosto interessa solo le *compilation_unit* oppure *namespace_body* in cui si trova.
*  L'ambito di un parametro di tipo dichiarato tramite un *type_parameter_list* in un *class_declaration* ([le dichiarazioni di classe](classes.md#class-declarations)) è il *class_base*, *type_parameter_constraints_clause*s, e *class_body* di tale *class_declaration*.
*  L'ambito di un parametro di tipo dichiarato tramite un *type_parameter_list* in un *struct_declaration* ([dichiarazioni Struct](structs.md#struct-declarations)) è il *struct_interfaces* , *type_parameter_constraints_clause*s, e *struct_body* di tale *struct_declaration*.
*  L'ambito di un parametro di tipo dichiarato tramite un *type_parameter_list* in un *interface_declaration* ([dichiarazioni di interfaccia](interfaces.md#interface-declarations)) è il *interface_base* , *type_parameter_constraints_clause*s, e *interface_body* di tale *interface_declaration*.
*  L'ambito di un parametro di tipo dichiarato tramite un *type_parameter_list* in un *delegate_declaration* ([dichiarazioni Delegate](delegates.md#delegate-declarations)) è il *return_type*, *formal_parameter_list*, e *type_parameter_constraints_clause*s di tale *delegate_declaration*.
*  L'ambito di un membro dichiarato tramite un *class_member_declaration* ([classe corpo](classes.md#class-body)) è la *class_body* in cui si trova la dichiarazione. Inoltre, l'ambito di un membro di classe si estende per il *class_body* di tali classi derivate che sono inclusi nel dominio di accessibilità ([domini accessibilità](basic-concepts.md#accessibility-domains)) del membro.
*  L'ambito di un membro dichiarato tramite un *struct_member_declaration* ([i membri Struct](structs.md#struct-members)) è la *struct_body* in cui si trova la dichiarazione.
*  L'ambito di un membro dichiarato tramite un *enum_member_declaration* ([membri Enum](enums.md#enum-members)) è la *enum_body* in cui si trova la dichiarazione.
*  L'ambito di un parametro dichiarato in un *method_declaration* ([metodi](classes.md#methods)) è la *method_body* ciò *method_declaration*.
*  L'ambito di un parametro dichiarato in un' *indexer_declaration* ([indicizzatori](classes.md#indexers)) è la *accessor_declarations* ciò *indexer_declaration*.
*  L'ambito di un parametro dichiarato in un' *operator_declaration* ([operatori](classes.md#operators)) è la *blocco* di tale *operator_declaration*.
*  L'ambito di un parametro dichiarato in un *constructor_declaration* ([costruttori di istanze](classes.md#instance-constructors)) è la *constructor_initializer* e *blocco* ciò *constructor_declaration*.
*  L'ambito di un parametro dichiarato in un *lambda_expression* ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)) è la *anonymous_function_body* ciò *lambda_ espressione*
*  L'ambito di un parametro dichiarato in un' *anonymous_method_expression* ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)) è la *blocco* ciò *anonymous_method _expression*.
*  L'ambito di un'etichetta dichiarati in un *labeled_statement* ([con etichetta istruzioni](statements.md#labeled-statements)) è la *blocco* in cui si trova la dichiarazione.
*  L'ambito di una variabile locale dichiarata un *local_variable_declaration* ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) è il blocco in cui si trova la dichiarazione.
*  L'ambito di una variabile locale dichiarata un *switch_block* di un `switch` istruzione ([istruzione switch](statements.md#the-switch-statement)) è il *switch_block*.
*  L'ambito di una variabile locale dichiarata un *for_initializer* di un `for` istruzione ([i per l'istruzione](statements.md#the-for-statement)) è il *for_initializer*, la  *for_condition*, il *for_iterator*e i contenuti *istruzione* del `for` istruzione.
*  L'ambito di una costante locale dichiarato in un *local_constant_declaration* ([dichiarazioni locali costante](statements.md#local-constant-declarations)) è il blocco in cui si trova la dichiarazione. Tratta di un errore in fase di compilazione per fare riferimento a una costante locale in una posizione di testo che precede relativi *constant_declarator*.
*  L'ambito di una variabile dichiarata come parte di un *foreach_statement*, *using_statement*, *lock_statement* oppure *query_expression* è determinato dall'espansione del costrutto specifico.

All'interno dell'ambito di un membro dello spazio dei nomi, classe, struct o enumerazione è possibile fare riferimento al membro in una posizione di testo che precede la dichiarazione del membro. Esempio:
```csharp
class A
{
    void F() {
        i = 1;
    }

    int i = 0;
}
```
In questo caso, è valido per `F` per fare riferimento a `i` prima che sia dichiarata.

Nell'ambito di una variabile locale, è un errore in fase di compilazione per fare riferimento alla variabile locale in una posizione di testo che precede il *local_variable_declarator* della variabile locale. Esempio:
```csharp
class A
{
    int i = 0;

    void F() {
        i = 1;                  // Error, use precedes declaration
        int i;
        i = 2;
    }

    void G() {
        int j = (j = 1);        // Valid
    }

    void H() {
        int a = 1, b = ++a;    // Valid
    }
}
```

Nel `F` metodo precedente, la prima assegnazione a `i` non fa riferimento al campo dichiarato nell'ambito esterno. Invece, fa riferimento alla variabile locale e generato un errore in fase di compilazione poiché testualmente precede la dichiarazione della variabile. Nel `G` metodo, l'uso del `j` nell'inizializzatore per la dichiarazione di `j` è valido perché l'uso non precede la *local_variable_declarator*. Nel `H` metodo, una successiva *local_variable_declarator* correttamente fa riferimento a una variabile locale dichiarata in una sessione *local_variable_declarator* all'interno della stessa  *local_variable_declaration*.

Le regole di ambito per le variabili locali sono progettate per garantire che il significato di un nome usato nel contesto di un'espressione è sempre lo stesso all'interno di un blocco. Se l'ambito di una variabile locale per estendere solo dalla relativa dichiarazione alla fine del blocco, quindi nell'esempio precedente, è necessario assegnare la prima assegnazione alla variabile di istanza e la seconda assegnazione è necessario assegnare alla variabile locale, causando eventualmente errori di compilazione se fosse successive riordinare le istruzioni del blocco.

Il significato di un nome all'interno di un blocco può variare in base al contesto in cui viene utilizzato il nome. Nell'esempio
```csharp
using System;

class A {}

class Test
{
    static void Main() {
        string A = "hello, world";
        string s = A;                            // expression context

        Type t = typeof(A);                      // type context

        Console.WriteLine(s);                    // writes "hello, world"
        Console.WriteLine(t);                    // writes "A"
    }
}
```
il nome `A` viene usato nel contesto di un'espressione per fare riferimento alla variabile locale `A` e in un contesto di tipo per fare riferimento alla classe `A`.

### <a name="name-hiding"></a>Nascondere il nome

L'ambito di un'entità include in genere più testo di programma rispetto allo spazio di dichiarazione dell'entità. In particolare, l'ambito di un'entità può includere dichiarazioni che introducono nuovi spazi di dichiarazione che contiene le entità con lo stesso nome. Tali dichiarazioni rendono l'entità originale diventare ***nascosto***. Per contro, un'entità è detta ***visibile*** quando non è nascosto.

Nascondere il nome si verifica quando si sovrappongono ambiti mediante l'annidamento e quando gli ambiti si sovrappongono attraverso l'ereditarietà. Nelle sezioni seguenti vengono descritte le caratteristiche dei due tipi di mascheramento.

#### <a name="hiding-through-nesting"></a>Disattivazione della visualizzazione tramite l'annidamento

Un nome nascosto tramite annidamento può verificarsi a causa di annidamento degli spazi dei nomi o tipi all'interno di spazi dei nomi, come risultato di annidamento dei tipi all'interno di classi o struct e in seguito a parametro e dichiarazioni di variabile locale.

Nell'esempio
```csharp
class A
{
    int i = 0;

    void F() {
        int i = 1;
    }

    void G() {
        i = 1;
    }
}
```
all'interno di `F` metodo, la variabile di istanza `i` è nascosto per la variabile locale `i`, ma entro la `G` metodo, `i` fa ancora riferimento alla variabile di istanza.

Quando un nome in un ambito interno nasconde un nome in un ambito esterno, nasconde tutte le occorrenze di overload di tale nome. Nell'esempio
```csharp
class Outer
{
    static void F(int i) {}

    static void F(string s) {}

    class Inner
    {
        void G() {
            F(1);              // Invokes Outer.Inner.F
            F("Hello");        // Error
        }

        static void F(long l) {}
    }
}
```
la chiamata `F(1)` richiama il `F` dichiarato in `Inner` perché tutte le occorrenze esterne di `F` sono nascosti dalla dichiarazione interna. Per lo stesso motivo, la chiamata `F("Hello")` genera un errore in fase di compilazione.

#### <a name="hiding-through-inheritance"></a>Nascosto tramite ereditarietà

Un nome nascosto tramite ereditarietà si verifica quando le classi o struct dichiarare nuovamente i nomi di quelli ereditati dalle classi di base. Questo tipo di nascondere il nome accetta uno dei formati seguenti:

*  Una costante, campo, proprietà, eventi o tipo introdotto in una classe o uno struct nasconde tutti i membri di classe di base con lo stesso nome.
*  Un metodo inserito in una classe o uno struct nasconde tutti i membri di classe di base non metodo con lo stesso nome e tutti i metodi della classe base con la stessa firma (nome del metodo e numero di parametri, i modificatori e tipi).
*  Un indicizzatore inserito in una classe o uno struct nasconde tutti gli indicizzatori di classe di base con la stessa firma (numero di parametri e tipi).

Le regole che controllano le dichiarazioni di operatore ([operatori](classes.md#operators)) rende impossibile per una classe derivata dichiarare un operatore con la stessa firma di un operatore in una classe di base. Di conseguenza, gli operatori non è mai nascondono tra loro.

Contrariamente a nascondere un nome da un ambito esterno, se si nasconde un nome accessibile da un ambito ereditato genera un avviso da segnalare. Nell'esempio
```csharp
class Base
{
    public void F() {}
}

class Derived: Base
{
    public void F() {}        // Warning, hiding an inherited name
}
```
la dichiarazione di `F` in `Derived` genera un avviso da segnalare. Nascondere un nome ereditato in particolare non è un errore, in quanto verrebbe preclusa evoluzione separata di classi di base. Ad esempio, la situazione precedente potrebbe essersi verificata perché una versione successiva di `Base` introdotta un' `F` metodo che non era presente in una versione precedente della classe. La situazione precedente fosse stata un errore, quindi eventuali modifiche apportate a una classe base in una libreria di classi con controllo delle versioni separatamente potrebbe potenzialmente causare alle classi derivate di diventano non validi.

Avviso restituito da nascondere un nome ereditato può essere eliminata tramite l'uso del `new` modificatore:
```csharp
class Base
{
    public void F() {}
}

class Derived: Base
{
    new public void F() {}
}
```

Il `new` modificatore indica che il `F` in `Derived` è "new" e che in effetti consente di nascondere il membro ereditato.

Una dichiarazione di un nuovo membro nasconde un membro ereditato solo all'interno dell'ambito del nuovo membro.

```csharp
class Base
{
    public static void F() {}
}

class Derived: Base
{
    new private static void F() {}    // Hides Base.F in Derived only
}

class MoreDerived: Derived
{
    static void G() { F(); }          // Invokes Base.F
}
```

Nell'esempio precedente, la dichiarazione di `F` in `Derived` nasconde il `F` che è stata ereditata da `Base`, ma, poiché il nuovo `F` in `Derived` con accesso privato, l'ambito non si estende a `MoreDerived` . Di conseguenza, la chiamata `F()` nelle `MoreDerived.G` valido e richiamerà `Base.F`.

## <a name="namespace-and-type-names"></a>Namespace e dei tipi

Contesti diversi in un C# programma richiede una *namespace_name* o un *type_name* specificare.

```antlr
namespace_name
    : namespace_or_type_name
    ;

type_name
    : namespace_or_type_name
    ;

namespace_or_type_name
    : identifier type_argument_list?
    | namespace_or_type_name '.' identifier type_argument_list?
    | qualified_alias_member
    ;
```

Oggetto *namespace_name* è un *namespace_or_type_name* che fa riferimento a uno spazio dei nomi. Come descritto di seguito, il *namespace_or_type_name* di un *namespace_name* deve fare riferimento a uno spazio dei nomi, o in caso contrario, verrà generato un errore in fase di compilazione. Nessun argomento di tipo ([gli argomenti di tipo](types.md#type-arguments)) possono essere presenti in un *namespace_name* (solo i tipi possono avere argomenti tipo).

Oggetto *type_name* è un *namespace_or_type_name* che fa riferimento a un tipo. Come descritto di seguito, il *namespace_or_type_name* di un *type_name* deve fare riferimento a un tipo, o in caso contrario, verrà generato un errore in fase di compilazione.

Se il *namespace_or_type_name* è qualificato-alias-membro relativo significato è, come descritto [qualificatori di alias Namespace](namespaces.md#namespace-alias-qualifiers). In caso contrario, un *namespace_or_type_name* dispone di una delle quattro forme:

*  `I`
*  `I<A1, ..., Ak>`
*  `N.I`
*  `N.I<A1, ..., Ak>`

in cui `I` è un identificatore singolo `N` è un *namespace_or_type_name* e `<A1, ..., Ak>` è facoltativo *type_argument_list*. Se non si specifica *type_argument_list* è specificato, prendere in considerazione `k` sia pari a zero.

Il significato di un *namespace_or_type_name* viene determinata come segue:

*   Se il *namespace_or_type_name* è nel formato `I` o del form `I<A1, ..., Ak>`:
    * Se `K` è uguale a zero e il *namespace_or_type_name* viene visualizzato all'interno di una dichiarazione di metodo generico ([metodi](classes.md#methods)) e se tale dichiarazione include un parametro di tipo ([tipo i parametri](classes.md#type-parameters)) con nome `I`, il *namespace_or_type_name* fa riferimento a tale parametro di tipo.
    * In caso contrario, se il *namespace_or_type_name* viene visualizzato all'interno di una dichiarazione del tipo, quindi per ogni tipo di istanza `T` ([il tipo di istanza](classes.md#the-instance-type)), che inizia con il tipo di istanza di tale tipo dichiarazione e continuare con il tipo di istanza di ogni dichiarazione di classe o struct che lo contiene (se presente):
        * Se `K` è zero e la dichiarazione di `T` include un parametro di tipo con nome `I`, quindi il *namespace_or_type_name* fa riferimento a tale parametro di tipo.
        * In caso contrario, se il *namespace_or_type_name* viene visualizzato all'interno del corpo della dichiarazione del tipo, e `T` o uno qualsiasi dei tipi di base contiene un tipo annidato accessibile con nome `I` e `K`  parametri di tipo, il *namespace_or_type_name* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato. Se è presente più di un tale tipo, viene selezionato il tipo dichiarato all'interno del tipo più derivato. Si noti che i membri non di tipo (le costanti, campi, metodi, proprietà, indicizzatori, operatori, costruttori di istanze, distruttori e i costruttori statici) e i membri dei tipi con un numero diverso di parametri di tipo vengono ignorati quando si determina il significato del *namespace_or_type_name*.
    * Se i passaggi precedenti hanno avuto esito positivo, per ogni spazio dei nomi `N`, che inizia con lo spazio dei nomi in cui la *namespace_or_type_name* si verifica, procedere con ogni spazio dei nomi contenitore (se presente) e terminando con il spazio dei nomi globale, la procedura seguente viene valutata fino a quando non viene individuata un'entità:
        * Se `K` è uguale a zero e `I` è il nome di uno spazio dei nomi in `N`, quindi:
            * Se la posizione in cui il *namespace_or_type_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un' *extern_alias_directive* o*using_alias_directive* che associa il nome `I` con uno spazio dei nomi o un tipo, il *namespace_or_type_name* è ambiguo e si verifica un errore in fase di compilazione.
            * In caso contrario, il *namespace_or_type_name* fa riferimento allo spazio dei nomi denominato `I` in `N`.
        * In caso contrario, se `N` contiene un tipo accessibile con nome `I` e `K`  parametri di tipo, quindi:
            * Se `K` è zero e il percorso in cui la *namespace_or_type_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive*  oppure *using_alias_directive* che associa il nome `I` con uno spazio dei nomi o un tipo, il *namespace_or_type_name* è ambiguo e una fase di compilazione si verifica l'errore.
            * In caso contrario, il *namespace_or_type_name* fa riferimento al tipo costruito con gli argomenti di tipo specificato.
        * In caso contrario, se la posizione in cui il *namespace_or_type_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N`:
            * Se `K` è uguale a zero e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* oppure *using_alias_directive* che associa il nome `I` con uno spazio dei nomi importato o tipo, il *namespace_or_type_name* fa riferimento a tale spazio dei nomi o tipo.
            * In caso contrario, se le dichiarazioni degli spazi dei nomi e il tipo importato per la *using_namespace_directive*s e *using_alias_directive*s della dichiarazione dello spazio dei nomi contiene esattamente un tipo accessibile con nome `I` e `K`  parametri di tipo, il *namespace_or_type_name* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato.
            * In caso contrario, se le dichiarazioni degli spazi dei nomi e il tipo importato per la *using_namespace_directive*s e *using_alias_directive*s della dichiarazione dello spazio dei nomi contiene più di un tipo accessibile con nome `I` e `K`  parametri di tipo, il *namespace_or_type_name* è ambiguo e si verifica un errore.
    * In caso contrario, il *namespace_or_type_name* è non definito e si verifica un errore in fase di compilazione.
*  In caso contrario, il *namespace_or_type_name* è nel formato `N.I` o del form `N.I<A1, ..., Ak>`. `N` viene prima risolto come un *namespace_or_type_name*. Se la risoluzione di `N` non ha esito positivo, si verifica un errore in fase di compilazione. In caso contrario, `N.I` o `N.I<A1, ..., Ak>` viene risolto come segue:
    * Se `K` è uguale a zero e `N` fa riferimento a uno spazio dei nomi e `N` contiene uno spazio dei nomi annidato con nome `I`, il *namespace_or_type_name* fa riferimento allo spazio dei nomi annidato.
    * In caso contrario, se `N` fa riferimento a uno spazio dei nomi e `N` contiene un tipo accessibile con nome `I` e `K`  parametri di tipo, il *namespace_or_type_name* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato.
    * In caso contrario, se `N` fa riferimento a un tipo di classe o struct (possibilmente costruito) e `N` o una delle relative classi di base contiene un tipo annidato accessibile con nome `I` e `K`  digitare i parametri, quindi il *namespace_or_type_name* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato. Se è presente più di un tale tipo, viene selezionato il tipo dichiarato all'interno del tipo più derivato. Si noti che se il significato dei `N.I` determinare come parte di risolvere la specifica della classe base `N` quindi la classe base diretta `N` viene considerato come un oggetto ([classi di Base](classes.md#base-classes)).
    * In caso contrario, `N.I` è un valore non valido *namespace_or_type_name*, e si verifica un errore in fase di compilazione.

Oggetto *namespace_or_type_name* è la possibilità di fare riferimento a una classe statica ([classi statiche](classes.md#static-classes)) solo se

*  Il *namespace_or_type_name* è la `T` in un *namespace_or_type_name* del form `T.I`, o
*  Il *namespace_or_type_name* è la `T` in un *typeof_expression* ([elenchi di argomenti](expressions.md#argument-lists)1) nel formato `typeof(T)`.

### <a name="fully-qualified-names"></a>Nomi completi

Ogni spazio dei nomi e il tipo ha un ***nome completo***, che identifica in modo univoco il tipo tra tutti gli altri utenti o dello spazio dei nomi. Il nome completo di un tipo o dello spazio dei nomi `N` viene determinata come segue:

*  Se `N` è un membro dello spazio dei nomi globali, il nome completo è `N`.
*  In caso contrario, il nome completo è `S.N`, dove `S` è il nome completo dello spazio dei nomi o tipo in cui `N` viene dichiarato.

In altre parole, il nome completo del `N` è il percorso gerarchico completo degli identificatori che portano a `N`, a partire dallo spazio dei nomi globale. Poiché ogni membro di un tipo o dello spazio dei nomi deve avere un nome univoco, ne consegue che il nome completo di un tipo o dello spazio dei nomi sia sempre univoco.

L'esempio seguente illustra diverse dichiarazioni dello spazio dei nomi e tipo di insieme ai rispettivi nomi associate nome completi.
```csharp
class A {}                // A

namespace X               // X
{
    class B               // X.B
    {
        class C {}        // X.B.C
    }

    namespace Y           // X.Y
    {
        class D {}        // X.Y.D
    }
}

namespace X.Y             // X.Y
{
    class E {}            // X.Y.E
}
```

## <a name="automatic-memory-management"></a>Gestione automatica della memoria

C# usa la gestione automatica della memoria, che libera gli sviluppatori da allocare e liberare la memoria occupata dagli oggetti manualmente. I criteri di gestione automatica della memoria vengono implementati da una ***garbage collector***. Il ciclo di vita di gestione della memoria di un oggetto è come segue:

1. Quando viene creato l'oggetto, di memoria allocata per il costruttore viene eseguito e l'oggetto viene considerato in tempo reale.
2. Se l'oggetto, o parte di esso, non è accessibile da qualsiasi possibile continuazione dell'esecuzione, sia l'esecuzione dei distruttori, l'oggetto viene considerato non più in uso e diventa idoneo per l'eliminazione. Il compilatore c# e il garbage collector potrebbe scegliere di analizzare il codice per determinare che fa riferimento a un oggetto può essere usato in futuro. Ad esempio, se una variabile locale che si trova nell'ambito è l'unico riferimento a un oggetto, ma tale variabile locale non viene mai al punto della procedura qualsiasi possibile continuazione dell'esecuzione dell'esecuzione corrente, il garbage collector può (ma non è obbligatorio per) trattare l'oggetto come non più in uso.
3. Una volta che l'oggetto è idoneo per l'eliminazione, alcune non viene specificato in un secondo momento di tempo il distruttore ([distruttori](classes.md#destructors)) (se presente) per l'oggetto è in esecuzione. In circostanze normali il distruttore per l'oggetto viene eseguito una sola volta, anche se API specifiche dell'implementazione possono consentire di eseguire l'override di questo comportamento.
4. Una volta che viene eseguito il distruttore per un oggetto, se tale oggetto, o parte di esso, non è accessibile da qualsiasi possibile continuazione dell'esecuzione, compresa l'esecuzione dei distruttori, dell'oggetto diventa inaccessibile e l'oggetto diventa idoneo per la raccolta.
5. Infine, in un momento dopo l'oggetto diventa idoneo per la raccolta, il garbage collector libera la memoria associata a quell'oggetto.

Il garbage collector mantiene le informazioni sull'uso degli oggetti e utilizza queste informazioni per prendere decisioni di gestione, di memoria, ad esempio la posizione in memoria per individuare un oggetto appena creato, quando per spostare un oggetto e, quando un oggetto non è più in uso o non accessibile.

Come altri linguaggi che presuppongono l'esistenza di un garbage collector, c# è progettato in modo che il garbage collector può implementare un'ampia gamma di criteri di gestione della memoria. Ad esempio, c# non richiede che i distruttori eseguire o che gli oggetti raccolti, non appena sono idonei, o eseguire i distruttori in un ordine specifico, o in qualsiasi thread specifico.

Controllare il comportamento del garbage collector può essere in certa misura, tramite i metodi statici sulla classe `System.GC`. Questa classe può essere usata per richiedere una raccolta, occorre che i distruttori per essere eseguire (o meno) e così via.

Poiché il garbage collector viene lasciato ampia scelta per decidere quando raccogliere oggetti ed eseguire i distruttori, un'implementazione conforme può produrre l'output è diverso da quello indicato dal codice seguente. Il programma
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }
}

class B
{
    object Ref;

    public B(object o) {
        Ref = o;
    }

    ~B() {
        Console.WriteLine("Destruct instance of B");
    }
}

class Test
{
    static void Main() {
        B b = new B(new A());
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
    }
}
```
Crea un'istanza della classe `A` e un'istanza della classe `B`. Questi oggetti diventano idonei per garbage collection quando la variabile `b` viene assegnato il valore `null`, poiché dopo questo periodo di tempo non è possibile per il codice scritto dall'utente per accedere a essi. L'output potrebbe essere
```
Destruct instance of A
Destruct instance of B
```
oppure
```
Destruct instance of B
Destruct instance of A
```
Poiché il linguaggio non impone alcun vincolo nell'ordine in cui gli oggetti vengono sottoposti a garbage collection.

In alcuni casi, la distinzione tra "idonei per l'eliminazione definitiva" e "idoneo per la raccolta" può essere importante. Ad esempio,
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("Destruct instance of A");
    }

    public void F() {
        Console.WriteLine("A.F");
        Test.RefA = this;
    }
}

class B
{
    public A Ref;

    ~B() {
        Console.WriteLine("Destruct instance of B");
        Ref.F();
    }
}

class Test
{
    public static A RefA;
    public static B RefB;

    static void Main() {
        RefB = new B();
        RefA = new A();
        RefB.Ref = RefA;
        RefB = null;
        RefA = null;

        // A and B now eligible for destruction
        GC.Collect();
        GC.WaitForPendingFinalizers();

        // B now eligible for collection, but A is not
        if (RefA != null)
            Console.WriteLine("RefA is not null");
    }
}
```

Nel programma precedente, se il garbage collector sceglie di eseguire il distruttore di `A` prima del distruttore di `B`, potrebbe essere l'output di questo programma:
```
Destruct instance of A
Destruct instance of B
A.F
RefA is not null
```

Si noti che anche se l'istanza di `A` non è stato utilizzato e `A`del distruttore è stato eseguito, è comunque possibile chiamare i metodi di `A` (in questo caso, `F`) essere chiamato dal distruttore un'altra. Si noti inoltre che l'esecuzione di un distruttore potrebbe essere un oggetto diventi utilizzabile nel programma principale. In questo caso, l'esecuzione di `B`del distruttore ha causato un'istanza di `A` che è stato in precedenza non è in uso per diventano accessibili dal riferimento in tempo reale `Test.RefA`. Dopo la chiamata a `WaitForPendingFinalizers`, l'istanza di `B` idoneo per la raccolta, ma l'istanza di `A` non lo è, a causa del riferimento `Test.RefA`.

Per evitare confusione e un comportamento imprevisto, è in genere una buona idea per i distruttori per eseguire solo la pulizia dei dati memorizzati nei rispettivi campi dell'oggetto e non per eseguire azioni su oggetti di riferimento o campi statici.

È un'alternativa all'uso di distruttori per consentire una classe implementa il `System.IDisposable` interfaccia. In questo modo il client dell'oggetto definire quando rilasciare le risorse dell'oggetto, in genere eseguendo l'accesso all'oggetto come una risorsa in un `using` istruzione ([l'istruzione using](statements.md#the-using-statement)).

## <a name="execution-order"></a>Ordine di esecuzione

Esecuzione di un programma c# consente di passare in modo che gli effetti collaterali di ogni thread in esecuzione vengono mantenuti in corrispondenza di punti critici di esecuzione. Oggetto ***effetto collaterale*** è definito come di lettura o scrittura di un campo volatile, la scrittura in una variabile non-volatile, la scrittura in una risorsa esterna e la generazione di un'eccezione. I punti critici di esecuzione in corrispondenza del quale deve essere mantenuto l'ordine di questi effetti collaterali sono riferimenti a campi volatili ([campi volatili](classes.md#volatile-fields)), `lock` istruzioni ([istruzione lock](statements.md#the-lock-statement)), e la creazione del thread e la chiusura. L'ambiente di esecuzione è gratuito modificare l'ordine di esecuzione di un programma c#, soggetto ai vincoli seguenti:

*  Dipendenza dei dati viene mantenuta all'interno di un thread di esecuzione. Vale a dire, il valore di ogni variabile viene calcolato come se tutte le istruzioni nel thread sono state eseguite nell'ordine del programma originale.
*  Inizializzazione di ordinamento vengono mantenute le regole di ([inizializzazione del campo](classes.md#field-initialization) e [inizializzatori](classes.md#variable-initializers)).
*  L'ordinamento degli effetti collaterali viene mantenuto per letture e scritture ([campi volatili](classes.md#volatile-fields)). Inoltre, l'ambiente di esecuzione non è necessario valutare parte di un'espressione se è possibile dedurre che il valore dell'espressione non viene utilizzato e che nessun effetto collaterale necessario vengono prodotte (incluse quelle dovuta a chiamate a un metodo o l'accesso a un campo volatile). Quando l'esecuzione del programma viene interrotta da un evento asincrono (ad esempio, un'eccezione generata da un altro thread), non è garantito che gli effetti collaterali osservabili siano visibili nell'ordine programma originale.
