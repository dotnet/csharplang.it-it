---
ms.openlocfilehash: ff31585520c9090ad92893a930327112743c8e77
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704001"
---
# <a name="basic-concepts"></a>Concetti di base

## <a name="application-startup"></a>Avvio dell'applicazione

Un assembly con un ***punto di ingresso*** è denominato ***applicazione***. Quando viene eseguita un'applicazione, viene creato un nuovo ***dominio applicazione*** . Diverse creazioni di istanze di un'applicazione possono essere presenti nello stesso computer nello stesso momento, ognuna con un proprio dominio applicazione.

Un dominio applicazione consente l'isolamento delle applicazioni agendo come un contenitore per lo stato dell'applicazione. Un dominio applicazione funge da contenitore e limite per i tipi definiti nell'applicazione e le librerie di classi utilizzate. I tipi caricati in un dominio dell'applicazione sono diversi dallo stesso tipo caricato in un altro dominio applicazione e le istanze di oggetti non vengono condivise direttamente tra i domini applicazione. Ogni dominio applicazione, ad esempio, dispone di una propria copia di variabili statiche per questi tipi e un costruttore statico per un tipo viene eseguito al massimo una volta per ogni dominio dell'applicazione. Le implementazioni sono gratuite per fornire criteri specifici dell'implementazione o meccanismi per la creazione e l'eliminazione di domini applicazione.

L' ***avvio dell'applicazione*** si verifica quando l'ambiente di esecuzione chiama un metodo designato, noto come punto di ingresso dell'applicazione. Questo metodo del punto di ingresso è sempre denominato `Main` e può avere una delle seguenti firme:

```csharp
static void Main() {...}

static void Main(string[] args) {...}

static int Main() {...}

static int Main(string[] args) {...}
```

Come illustrato, il punto di ingresso può, facoltativamente, restituire un valore `int`. Questo valore restituito viene usato nella terminazione dell'applicazione ([terminazione dell'applicazione](basic-concepts.md#application-termination)).

Il punto di ingresso può facoltativamente avere un parametro formale. Il parametro può avere qualsiasi nome, ma il tipo del parametro deve essere `string[]`. Se il parametro formale è presente, l'ambiente di esecuzione crea e passa un argomento `string[]` contenente gli argomenti della riga di comando specificati al momento dell'avvio dell'applicazione. L'argomento `string[]` non è mai null, ma può avere una lunghezza pari a zero se non è stato specificato alcun argomento della riga di comando.

Poiché C# supporta l'overload del metodo, una classe o uno struct può contenere più definizioni di un metodo, purché ognuna abbia una firma diversa. Tuttavia, all'interno di un singolo programma, nessuna classe o struct può contenere più di un metodo denominato `Main` la cui definizione lo qualifica come punto di ingresso dell'applicazione. Sono consentite altre versioni di overload di `Main`, purché dispongano di più di un parametro oppure il relativo solo parametro è diverso dal tipo `string[]`.

Un'applicazione può essere costituita da più classi o struct. È possibile che più di una di queste classi o struct contengano un metodo denominato `Main` la cui definizione lo qualifica per essere usato come punto di ingresso dell'applicazione. In questi casi, è necessario usare un meccanismo esterno, ad esempio un'opzione del compilatore da riga di comando, per selezionare uno di questi metodi `Main` come punto di ingresso.

In C#ogni metodo deve essere definito come membro di una classe o di uno struct. In genere, l'accessibilità dichiarata ([accessibilità](basic-concepts.md#declared-accessibility)dichiarata) di un metodo è determinata dai modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) specificati nella relativa dichiarazione e, analogamente, l'accessibilità dichiarata di un tipo è determinata dal parametro modificatori di accesso specificati nella relativa dichiarazione. Affinché un determinato metodo di un determinato tipo sia richiamabile, sia il tipo che il membro devono essere accessibili. Tuttavia, il punto di ingresso dell'applicazione è un caso speciale. In particolare, l'ambiente di esecuzione può accedere al punto di ingresso dell'applicazione indipendentemente dall'accessibilità dichiarata e indipendentemente dall'accessibilità dichiarata delle dichiarazioni di tipo di inclusione.

Il metodo del punto di ingresso dell'applicazione non può trovarsi in una dichiarazione di classe generica.

In tutti gli altri aspetti, i metodi del punto di ingresso si comportano come quelli che non sono punti di ingresso.

## <a name="application-termination"></a>Terminazione dell'applicazione

La ***terminazione dell'applicazione*** restituisce il controllo all'ambiente di esecuzione.

Se il tipo restituito del metodo del ***punto di ingresso*** dell'applicazione è `int`, il valore restituito funge da codice di ***stato di terminazione***dell'applicazione. Lo scopo di questo codice è consentire la comunicazione dell'esito positivo o negativo dell'ambiente di esecuzione.

Se il tipo restituito del metodo del punto di ingresso è `void`, raggiungendo la parentesi graffa destra (`}`) che termina il metodo oppure eseguendo un'istruzione `return` senza espressione, viene restituito un codice di stato di terminazione di `0`.

Prima della terminazione di un'applicazione, i distruttori per tutti gli oggetti che non sono ancora stati sottoposti a Garbage Collection vengono chiamati, a meno che tale pulizia non sia stata eliminata (tramite una chiamata al metodo della libreria `GC.SuppressFinalize`, ad esempio).

## <a name="declarations"></a>Dichiarazioni

Le dichiarazioni in un C# programma definiscono gli elementi costitutivi del programma. C#i programmi sono organizzati usando gli spazi dei nomi ([spazi dei](namespaces.md)nomi), che possono contenere dichiarazioni di tipo e dichiarazioni dello spazio dei nomi nidificate. Le dichiarazioni di tipo[(dichiarazioni di tipo](namespaces.md#type-declarations)) vengono usate per definire classi ([classi](classes.md)), struct ([struct](structs.md)), interfacce ([interfacce](interfaces.md)), enumerazioni ([enumerazioni](enums.md)) e delegati ([delegati](delegates.md)). I tipi di membri consentiti in una dichiarazione di tipo dipendono dal formato della dichiarazione del tipo. Ad esempio, le dichiarazioni di classe possono contenere dichiarazioni per costanti ([costanti](classes.md#constants)), campi ([campi](classes.md#fields)), metodi ([Metodi](classes.md#methods)), proprietà ([Proprietà](classes.md#properties)), eventi ([eventi](classes.md#events)), indicizzatori ([indicizzatori](classes.md#indexers)), operatori ([operatori](classes.md#operators)), costruttori di istanza ([costruttori di istanza](classes.md#instance-constructors)), costruttori statici ([costruttori statici](classes.md#static-constructors)), distruttori ([distruttori](classes.md#destructors)) e tipi annidati ([tipi annidati](classes.md#nested-types)).

Una dichiarazione definisce un nome nello ***spazio di dichiarazione*** a cui appartiene la dichiarazione. Ad eccezione dei membri sottoposto a overload ([firme e overload](basic-concepts.md#signatures-and-overloading)), si tratta di un errore in fase di compilazione con due o più dichiarazioni che introducono membri con lo stesso nome in uno spazio di dichiarazione. Non è mai possibile per uno spazio di dichiarazione contenere tipi diversi di membri con lo stesso nome. Uno spazio di dichiarazione, ad esempio, non può mai contenere un campo e un metodo con lo stesso nome.

Sono disponibili diversi tipi di spazi di dichiarazione, come descritto di seguito.

*  All'interno di tutti i file di origine di un programma, *namespace_member_declaration*s senza *namespace_declaration* di inclusione sono membri di un singolo spazio di dichiarazione combinato denominato ***spazio globale di dichiarazione***.
*  All'interno di tutti i file di origine di un programma, *namespace_member_declaration*s all'interno di *namespace_declaration*con lo stesso nome completo dello spazio dei nomi sono membri di un singolo spazio di dichiarazione combinato.
*  Ogni dichiarazione di classe, struct o interfaccia crea un nuovo spazio di dichiarazione. I nomi sono introdotti in questo spazio di dichiarazione tramite *class_member_declaration*s, *struct_member_declaration*s, *interface_member_declaration*s o *type_parameter*s. Ad eccezione delle dichiarazioni dei costruttori di istanza in overload e delle dichiarazioni di Costruttore statiche, una classe o uno struct non può contenere una dichiarazione di membro con lo stesso nome della classe o dello struct. Una classe, uno struct o un'interfaccia consente la dichiarazione di metodi e indicizzatori di overload. Una classe o uno struct consente inoltre la dichiarazione di costruttori di istanza e operatori di overload. Ad esempio, una classe, uno struct o un'interfaccia può contenere più dichiarazioni di metodo con lo stesso nome, purché queste dichiarazioni di metodo differiscano nella firma ([firme e overload](basic-concepts.md#signatures-and-overloading)). Si noti che le classi base non contribuiscono allo spazio di dichiarazione di una classe e le interfacce di base non contribuiscono allo spazio di dichiarazione di un'interfaccia. Pertanto, una classe o un'interfaccia derivata può dichiarare un membro con lo stesso nome di un membro ereditato. Tale membro viene definito ***Nascondi*** il membro ereditato.
*  Ogni dichiarazione di delegato crea un nuovo spazio di dichiarazione. I nomi vengono introdotti in questo spazio di dichiarazione tramite parametri formali (*fixed_parameter*s e *parameter_array*s) e *type_parameter*s.
*  Ogni dichiarazione di enumerazione crea un nuovo spazio di dichiarazione. I nomi vengono introdotti in questo spazio di dichiarazione tramite *enum_member_declarations*.
*  Ogni dichiarazione di metodo, dichiarazione di indicizzatore, dichiarazione di operatore, dichiarazione del costruttore di istanza e funzione anonima crea un nuovo spazio di dichiarazione denominato ***spazio di dichiarazione di variabile locale***. I nomi vengono introdotti in questo spazio di dichiarazione tramite parametri formali (*fixed_parameter*s e *parameter_array*s) e *type_parameter*s. Il corpo del membro della funzione o della funzione anonima, se presente, viene considerato annidato nello spazio di dichiarazione della variabile locale. È un errore per uno spazio di dichiarazione di variabile locale e uno spazio di dichiarazione di variabile locale annidato per contenere elementi con lo stesso nome. In uno spazio di dichiarazione annidato, quindi, non è possibile dichiarare una variabile o una costante locale con lo stesso nome di una variabile locale o di una costante in uno spazio di dichiarazione di inclusione. È possibile che due spazi di dichiarazione contengano elementi con lo stesso nome purché nessuno spazio di dichiarazione contenga l'altro.
*  Ogni *Block* o *switch_block* , nonché un'istruzione *for*, *foreach* e *using* , crea uno spazio di dichiarazione di variabile locale per le variabili locali e le costanti locali. I nomi sono introdotti in questo spazio di dichiarazione tramite *local_variable_declaration*s e *local_constant_declaration*s. Si noti che i blocchi che si verificano come o all'interno del corpo di un membro di funzione o di una funzione anonima sono annidati nello spazio di dichiarazione della variabile locale dichiarato da tali funzioni per i relativi parametri. Pertanto, è un errore avere ad esempio un metodo con una variabile locale e un parametro con lo stesso nome.
*  Ogni *Block* o *switch_block* crea uno spazio di dichiarazione separato per le etichette. I nomi vengono introdotti in questo spazio di dichiarazione tramite *labeled_statement*s e ai nomi viene fatto riferimento tramite *goto_statement*s. Lo ***spazio di dichiarazione dell'etichetta*** di un blocco include tutti i blocchi annidati. Quindi, all'interno di un blocco annidato non è possibile dichiarare un'etichetta con lo stesso nome di un'etichetta in un blocco di inclusione.

L'ordine testuale in cui vengono dichiarati i nomi non è in genere significativo. In particolare, l'ordine testuale non è significativo per la dichiarazione e l'uso di spazi dei nomi, costanti, metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori, costruttori statici e tipi. L'ordine delle dichiarazioni è significativo nei modi seguenti:

*  L'ordine di dichiarazione per le dichiarazioni di campo e le dichiarazioni di variabili locali determina l'ordine in cui vengono eseguiti gli inizializzatori (se presenti).
*  Le variabili locali devono essere definite prima di essere utilizzate ([ambiti](basic-concepts.md#scopes)).
*  L'ordine di dichiarazione per le dichiarazioni di membri enum ([membri enum](enums.md#enum-members)) è significativo quando i valori *constant_expression* vengono omessi.

Lo spazio della dichiarazione di uno spazio dei nomi è "Open ended" e due dichiarazioni dello spazio dei nomi con lo stesso nome completo contribuiscono allo stesso spazio di dichiarazione. Esempio:
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

Le due dichiarazioni dello spazio dei nomi precedenti contribuiscono allo stesso spazio di dichiarazione, in questo caso la dichiarazione di due classi con i nomi completi `Megacorp.Data.Customer` e `Megacorp.Data.Order`. Poiché le due dichiarazioni contribuiscono allo stesso spazio di dichiarazione, avrebbe causato un errore in fase di compilazione se ognuna conteneva una dichiarazione di una classe con lo stesso nome.

Come specificato in precedenza, lo spazio di dichiarazione di un blocco include tutti i blocchi annidati. Nell'esempio seguente, quindi, i metodi `F` e `G` generano un errore in fase di compilazione perché il nome `i` viene dichiarato nel blocco esterno e non può essere ridichiarato nel blocco interno. Tuttavia, i metodi `H` e `I` sono validi poiché i due `i` sono dichiarati in blocchi non annidati separati.

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

## <a name="members"></a>Members

Gli spazi dei nomi e ***i***tipi hanno membri. I membri di un'entità sono disponibili a livello generale tramite l'uso di un nome completo che inizia con un riferimento all'entità, seguito da un token "`.`", seguito dal nome del membro.

I membri di un tipo sono dichiarati nella dichiarazione di tipo o ***ereditati*** dalla classe base del tipo. Quando un tipo eredita da una classe base, tutti i membri della classe di base, ad eccezione dei costruttori di istanza, dei distruttori e dei costruttori statici, diventano membri del tipo derivato. L'accessibilità dichiarata di un membro della classe base non controlla se il membro è ereditato, ovvero l'ereditarietà si estende a qualsiasi membro che non sia un costruttore di istanza, un costruttore statico o un distruttore. Tuttavia, un membro ereditato potrebbe non essere accessibile in un tipo derivato, a causa dell'accessibilità dichiarata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)) o perché è nascosto da una dichiarazione nel tipo stesso ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)).

### <a name="namespace-members"></a>Membri dello spazio dei nomi

Gli spazi dei nomi e i tipi senza spazio dei nomi che lo contiene sono membri dello ***spazio dei nomi globale***. Corrisponde direttamente ai nomi dichiarati nello spazio di dichiarazione globale.

Gli spazi dei nomi e i tipi dichiarati in uno spazio dei nomi sono membri dello spazio dei nomi. Corrisponde direttamente ai nomi dichiarati nello spazio di dichiarazione dello spazio dei nomi.

Gli spazi dei nomi non hanno restrizioni di accesso. Non è possibile dichiarare spazi dei nomi privati, protetti o interni e i nomi degli spazi dei nomi sono sempre accessibili pubblicamente.

### <a name="struct-members"></a>Membri struct

I membri di uno struct sono i membri dichiarati nello struct e i membri ereditati dalla classe base diretta dello struct `System.ValueType` e dalla classe base indiretta `object`.

I membri di un tipo semplice corrispondono direttamente ai membri del tipo struct con alias del tipo semplice:

*  I membri di `sbyte` sono i membri dello struct `System.SByte`.
*  I membri di `byte` sono i membri dello struct `System.Byte`.
*  I membri di `short` sono i membri dello struct `System.Int16`.
*  I membri di `ushort` sono i membri dello struct `System.UInt16`.
*  I membri di `int` sono i membri dello struct `System.Int32`.
*  I membri di `uint` sono i membri dello struct `System.UInt32`.
*  I membri di `long` sono i membri dello struct `System.Int64`.
*  I membri di `ulong` sono i membri dello struct `System.UInt64`.
*  I membri di `char` sono i membri dello struct `System.Char`.
*  I membri di `float` sono i membri dello struct `System.Single`.
*  I membri di `double` sono i membri dello struct `System.Double`.
*  I membri di `decimal` sono i membri dello struct `System.Decimal`.
*  I membri di `bool` sono i membri dello struct `System.Boolean`.

### <a name="enumeration-members"></a>Membri dell'enumerazione

I membri di un'enumerazione sono le costanti dichiarate nell'enumerazione e i membri ereditati dalla classe di base diretta dell'enumerazione `System.Enum` e le classi base indirette `System.ValueType` e `object`.

### <a name="class-members"></a>Membri della classe

I membri di una classe sono i membri dichiarati nella classe e i membri ereditati dalla classe di base (ad eccezione della classe `object` che non ha una classe base). I membri ereditati dalla classe di base includono costanti, campi, metodi, proprietà, eventi, indicizzatori, operatori e tipi della classe di base, ma non i costruttori di istanza, i distruttori e i costruttori statici della classe di base. I membri della classe base vengono ereditati senza considerare l'accessibilità.

Una dichiarazione di classe può contenere dichiarazioni di costanti, campi, metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori, costruttori e tipi statici.

I membri di `object` e `string` corrispondono direttamente ai membri dei tipi di classe alias:

*  I membri di `object` sono i membri della classe `System.Object`.
*  I membri di `string` sono i membri della classe `System.String`.

### <a name="interface-members"></a>Membri di interfaccia

I membri di un'interfaccia sono i membri dichiarati nell'interfaccia e in tutte le interfacce di base dell'interfaccia. I membri della classe `object` non sono, in modo esplicito, membri di qualsiasi interfaccia ([membri di interfaccia](interfaces.md#interface-members)). Tuttavia, i membri della classe `object` sono disponibili tramite la ricerca di membri in qualsiasi tipo di interfaccia ([ricerca di membri](expressions.md#member-lookup)).

### <a name="array-members"></a>Membri della matrice

I membri di una matrice sono i membri ereditati dalla classe `System.Array`.

### <a name="delegate-members"></a>Membri delegati

I membri di un delegato sono i membri ereditati dalla classe `System.Delegate`.

## <a name="member-access"></a>Accesso ai membri

Le dichiarazioni di membri consentono di controllare l'accesso ai membri. L'accessibilità di un membro viene stabilita dall'accessibilità dichiarata ([accessibilità](basic-concepts.md#declared-accessibility)dichiarata) del membro combinato con l'accessibilità del tipo che lo contiene immediatamente, se presente.

Quando l'accesso a un determinato membro è consentito, il membro viene definito ***accessibile***. Viceversa, quando l'accesso a un particolare membro non è consentito, il membro viene definito ***inaccessibile***. L'accesso a un membro è consentito quando il percorso testuale in cui si verifica l'accesso è incluso nel dominio di accessibilità ([domini di accessibilità](basic-concepts.md#accessibility-domains)) del membro.

### <a name="declared-accessibility"></a>Accessibilità dichiarata

L' ***accessibilità dichiarata*** di un membro può essere una delle seguenti:

*  Public, che viene selezionato includendo un modificatore `public` nella dichiarazione del membro. Il significato intuitivo di `public` è "accesso non limitato".
*  Protected, che viene selezionato includendo un modificatore `protected` nella dichiarazione del membro. Il significato intuitivo di `protected` è "accesso limitato alla classe o ai tipi derivati dalla classe che lo contiene".
*  Internal, che viene selezionato includendo un modificatore `internal` nella dichiarazione del membro. Il significato intuitivo di `internal` è "accesso limitato a questo programma".
*  Interno protetto (ovvero protetto o interno), che viene selezionato includendo sia un modificatore `protected` che un modificatore `internal` nella dichiarazione del membro. Il significato intuitivo di `protected internal` è "accesso limitato al programma o ai tipi derivati dalla classe che lo contiene".
*  Privato, che viene selezionato includendo un modificatore `private` nella dichiarazione del membro. Il significato intuitivo di `private` è "accesso limitato al tipo che lo contiene".

A seconda del contesto in cui si verifica una dichiarazione di membro, sono consentiti solo determinati tipi di accessibilità dichiarata. Inoltre, quando una dichiarazione di membro non include modificatori di accesso, il contesto in cui viene eseguita la dichiarazione determina l'accessibilità dichiarata predefinita.

*  Gli spazi dei nomi hanno in modo implicito l'accessibilità dichiarata @no__t 0. Non sono consentiti modificatori di accesso nelle dichiarazioni dello spazio dei nomi.
*  I tipi dichiarati negli spazi dei nomi o nelle unità di compilazione possono avere `public` o `internal` dichiarati di accessibilità e l'impostazione predefinita è `internal`.
*  I membri della classe possono avere uno dei cinque tipi di accessibilità dichiarata e il valore predefinito per l'accessibilità dichiarata `private`. Si noti che un tipo dichiarato come membro di una classe può avere uno dei cinque tipi di accessibilità dichiarata, mentre un tipo dichiarato come membro di uno spazio dei nomi può avere solo `public` o `internal` ha dichiarato l'accessibilità.
*  I membri struct possono avere `public`, `internal` o `private` dichiarati di accessibilità e l'impostazione predefinita è `private` accessibilità dichiarata perché gli struct sono implicitamente sealed. I membri struct introdotti in uno struct, ovvero non ereditati da tale struct, non possono avere l'accessibilità dichiarata `protected` o `protected internal`. Si noti che un tipo dichiarato come membro di uno struct può avere `public`, `internal` o `private` dichiarati di accessibilità, mentre un tipo dichiarato come membro di uno spazio dei nomi può avere solo `public` o `internal` dichiarati di accessibilità.
*  I membri di interfaccia hanno in modo implicito l'accessibilità dichiarata @no__t 0. Non sono consentiti modificatori di accesso nelle dichiarazioni di membri di interfaccia.
*  I membri di enumerazione hanno in modo implicito l'accessibilità dichiarata @no__t 0. Non sono consentiti modificatori di accesso per le dichiarazioni di membri di enumerazione.

### <a name="accessibility-domains"></a>Domini di accessibilità

Il ***dominio di accessibilità*** di un membro è costituito dalle sezioni (probabilmente non contigue) del testo del programma in cui è consentito l'accesso al membro. Ai fini della definizione del dominio di accessibilità di un membro, un membro viene definito di ***primo livello*** se non è dichiarato all'interno di un tipo e un membro viene definito ***annidato*** se viene dichiarato all'interno di un altro tipo. Inoltre, il ***testo*** di programma di un programma viene definito come tutto il testo del programma contenuto in tutti i file di origine del programma e il testo del programma di un tipo viene definito come tutto il testo del programma contenuto in *type_declaration*s di quel tipo (incluso, probabilmente, tipi annidati all'interno del tipo).

Il dominio di accessibilità di un tipo predefinito, ad esempio `object`, `int` o `double`, è illimitato.

Il dominio di accessibilità di un tipo non associato di primo livello `T` ([tipi associati e non associati](types.md#bound-and-unbound-types)) dichiarati in un programma `P` viene definito come segue:

*  Se l'accessibilità dichiarata di `T` è `public`, il dominio di accessibilità di `T` corrisponde al testo del programma `P` e a qualsiasi programma che fa riferimento a `P`.
*  Se l'accessibilità dichiarata di `T` è `internal`, il dominio di accessibilità di `T` è il testo di programma di `P`.

Da queste definizioni segue che il dominio di accessibilità di un tipo non associato di primo livello è sempre almeno il testo del programma in cui è dichiarato il tipo.

Il dominio di accessibilità per un tipo costruito `T<A1, ..., An>` è l'intersezione del dominio di accessibilità del tipo generico non associato `T` e i domini di accessibilità degli argomenti tipo `A1, ..., An`.

Il dominio di accessibilità di un membro annidato `M` dichiarati in un tipo `T` all'interno di un programma `P` viene definito come segue (si noti che `M` potrebbe essere un tipo):

*  Se l'accessibilità dichiarata di `M` è `public`, il dominio di accessibilità di `M` è il dominio di accessibilità di `T`.
*  Se l'accessibilità dichiarata di `M` è `protected internal`, lasciare che `D` sia l'Unione del testo del programma `P` e il testo di programma di qualsiasi tipo derivato da `T`, dichiarato all'esterno di `P`. Il dominio di accessibilità di `M` è l'intersezione del dominio di accessibilità di `T` con `D`.
*  Se l'accessibilità dichiarata di `M` è `protected`, lasciare che `D` sia l'Unione del testo del programma `T` e il testo di programma di qualsiasi tipo derivato da `T`. Il dominio di accessibilità di `M` è l'intersezione del dominio di accessibilità di `T` con `D`.
*  Se l'accessibilità dichiarata di `M` è `internal`, il dominio di accessibilità di `M` è l'intersezione del dominio di accessibilità di `T` con il testo di programma di `P`.
*  Se l'accessibilità dichiarata di `M` è `private`, il dominio di accessibilità di `M` è il testo di programma di `T`.

Da queste definizioni segue che il dominio di accessibilità di un membro annidato è sempre almeno il testo del programma del tipo in cui il membro è dichiarato. Inoltre, segue che il dominio di accessibilità di un membro non è mai più inclusivo del dominio di accessibilità del tipo in cui il membro è dichiarato.

In termini intuitivi, quando si accede a un tipo o a un membro `M`, vengono valutati i passaggi seguenti per assicurarsi che l'accesso sia consentito:

*  Innanzitutto, se `M` è dichiarato all'interno di un tipo (in contrapposizione a un'unità di compilazione o a uno spazio dei nomi), si verifica un errore in fase di compilazione se tale tipo non è accessibile.
*  Quindi, se `M` è `public`, l'accesso è consentito.
*  In caso contrario, se `M` è `protected internal`, l'accesso è consentito se si verifica all'interno del programma in cui viene dichiarata la `M` o se si verifica all'interno di una classe derivata dalla classe in cui `M` viene dichiarata e viene eseguita tramite il tipo di classe derivata ([protected accesso per i membri dell'istanza](basic-concepts.md#protected-access-for-instance-members).
*  In caso contrario, se `M` è `protected`, l'accesso è consentito se si verifica all'interno della classe in cui viene dichiarata la `M` o se si verifica all'interno di una classe derivata dalla classe in cui `M` è dichiarata e viene eseguita tramite il tipo di classe derivata ([protected accesso per i membri dell'istanza](basic-concepts.md#protected-access-for-instance-members).
*  In caso contrario, se `M` è `internal`, l'accesso è consentito se si verifica all'interno del programma in cui viene dichiarata la `M`.
*  In caso contrario, se `M` è `private`, l'accesso è consentito se si verifica nel tipo in cui viene dichiarata la `M`.
*  In caso contrario, il tipo o il membro non è accessibile e si verifica un errore in fase di compilazione.

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
le classi e i membri dispongono dei seguenti domini di accessibilità:

*  Il dominio di accessibilità di `A` e `A.X` è illimitato.
*  Il dominio di accessibilità di `A.Y`, `B`, `B.X`, `B.Y`, `B.C`, `B.C.X` e `B.C.Y` è il testo del programma che lo contiene.
*  Il dominio di accessibilità di `A.Z` è il testo del programma `A`.
*  Il dominio di accessibilità di `B.Z` e `B.D` è il testo di programma di `B`, incluso il testo del programma `B.C` e `B.D`.
*  Il dominio di accessibilità di `B.C.Z` è il testo del programma `B.C`.
*  Il dominio di accessibilità di `B.D.X` e `B.D.Y` è il testo di programma di `B`, incluso il testo del programma `B.C` e `B.D`.
*  Il dominio di accessibilità di `B.D.Z` è il testo del programma `B.D`.

Come illustrato nell'esempio, il dominio di accessibilità di un membro non è mai maggiore di quello di un tipo contenitore. Ad esempio, anche se tutti i membri `X` hanno un'accessibilità dichiarata pubblica, tutti tranne `A.X` hanno domini di accessibilità vincolati da un tipo che lo contiene.

Come descritto in [membri](basic-concepts.md#members), tutti i membri di una classe di base, ad eccezione dei costruttori di istanza, dei distruttori e dei costruttori statici, vengono ereditati dai tipi derivati. Sono inclusi anche i membri privati di una classe base. Tuttavia, il dominio di accessibilità di un membro privato include solo il testo del programma del tipo in cui il membro è dichiarato. Nell'esempio
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
la classe `B` eredita il membro privato `x` dalla classe `A`. Poiché il membro è privato, è accessibile solo all'interno del *class_body* di `A`. Di conseguenza, l'accesso a `b.x` ha esito positivo nel metodo `A.F`, ma ha esito negativo nel metodo `B.F`.

### <a name="protected-access-for-instance-members"></a>Accesso protetto per i membri dell'istanza

Quando si accede a un membro di istanza `protected` all'esterno del testo di programma della classe in cui è dichiarato e quando si accede a un membro di istanza `protected internal` all'esterno del testo del programma in cui è dichiarato, l'accesso deve essere eseguito all'interno di una classe dichiarazione che deriva dalla classe in cui è dichiarata. Inoltre, è necessario che l'accesso avvenga tramite un'istanza del tipo di classe derivata o di un tipo di classe costruito. Questa restrizione impedisce a una classe derivata di accedere ai membri protetti di altre classi derivate, anche quando i membri vengono ereditati dalla stessa classe di base.

Lasciare che `B` sia una classe di base che dichiara un membro di istanza protetto `M` e lasciare che `D` sia una classe che deriva da `B`. All'interno del *class_body* di `D`, l'accesso a `M` può assumere uno dei seguenti formati:

*  *Type_name* o *primary_expression* non qualificato nel formato `M`.
*  Un *primary_expression* nel formato `E.M`, a condizione che il tipo di `E` sia `T` o una classe derivata da `T`, dove `T` è il tipo di classe `D` o un tipo di classe costruito da `D`
*  *Primary_expression* nel formato `base.M`.

Oltre a queste forme di accesso, una classe derivata può accedere a un costruttore di istanze protette di una classe di base in un *constructor_initializer* ([inizializzatori di costruttori](classes.md#constructor-initializers)).

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
all'interno `A` è possibile accedere `x` tramite istanze di `A` e `B`, poiché in entrambi i casi l'accesso viene eseguita tramite un'istanza di `A` o una classe derivata da `A`. Tuttavia, all'interno di `B`, non è possibile accedere `x` tramite un'istanza di `A`, poiché `A` non deriva da `B`.

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
le tre assegnazioni a `x` sono consentite perché tutte avvengono tramite istanze di tipi di classe costruite dal tipo generico.

### <a name="accessibility-constraints"></a>Vincoli di accessibilità

Per diversi costrutti nel C# linguaggio è necessario che un tipo sia ***accessibile almeno*** quanto un membro o un altro tipo. Un tipo `T` è detto almeno accessibile come membro o tipo `M` se il dominio di accessibilità di `T` è un superset del dominio di accessibilità di `M`. In altre parole, `T` è almeno accessibile come `M` se `T` è accessibile in tutti i contesti in cui `M` è accessibile.

Esistono i seguenti vincoli di accessibilità:

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
la classe `B` genera un errore in fase di compilazione perché `A` non è almeno accessibile come `B`.

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
il metodo `H` in `B` genera un errore in fase di compilazione perché il tipo restituito `A` non è almeno accessibile come il metodo.

## <a name="signatures-and-overloading"></a>Firme e overload

Metodi, costruttori di istanza, indicizzatori e operatori sono caratterizzati dalle rispettive ***firme***:

*  La firma di un metodo è costituita dal nome del metodo, dal numero di parametri di tipo e dal tipo e dal tipo (valore, riferimento o output) di ognuno dei parametri formali, considerati nell'ordine da sinistra a destra. Per questi scopi, qualsiasi parametro di tipo del metodo che si verifica nel tipo di un parametro formale viene identificato senza il relativo nome, ma in base alla posizione ordinale nell'elenco di argomenti di tipo del metodo. La firma di un metodo in particolare non include il tipo restituito, il modificatore `params` che può essere specificato per il parametro più a destra, né vincoli facoltativi per il parametro di tipo.
*  La firma di un costruttore di istanza è costituita dal tipo e dal tipo (valore, riferimento o output) di ognuno dei parametri formali, considerati nell'ordine da sinistra a destra. La firma di un costruttore di istanza in particolare non include il modificatore `params` che è possibile specificare per il parametro più a destra.
*  La firma di un indicizzatore è costituita dal tipo di ognuno dei parametri formali, considerati nell'ordine da sinistra a destra. La firma di un indicizzatore in particolare non include il tipo di elemento, né include il modificatore `params` che è possibile specificare per il parametro più a destra.
*  La firma di un operatore è costituita dal nome dell'operatore e dal tipo di ognuno dei parametri formali, considerati nell'ordine da sinistra a destra. La firma di un operatore non include specificamente il tipo di risultato.

Le firme sono il meccanismo di abilitazione per l' ***Overload*** di membri in classi, struct e interfacce:

*  L'overload dei metodi consente a una classe, uno struct o un'interfaccia di dichiarare più metodi con lo stesso nome, purché le relative firme siano univoche all'interno di tale classe, struct o interfaccia.
*  L'overload dei costruttori di istanza consente a una classe o struct di dichiarare più costruttori di istanza, purché le relative firme siano univoche all'interno di tale classe o struct.
*  L'overload degli indicizzatori consente a una classe, uno struct o un'interfaccia di dichiarare più indicizzatori, purché le relative firme siano univoche all'interno di tale classe, struct o interfaccia.
*  L'overload degli operatori consente a una classe o struct di dichiarare più operatori con lo stesso nome, purché le relative firme siano univoche all'interno di tale classe o struct.

Sebbene i modificatori di parametro `out` e `ref` siano considerati parte di una firma, i membri dichiarati in un unico tipo non possono differire dalla firma esclusivamente da `ref` e `out`. Si verifica un errore in fase di compilazione se due membri vengono dichiarati nello stesso tipo con firme identiche se tutti i parametri in entrambi i metodi con modificatori `out` sono stati modificati in modificatori `ref`. Per altri scopi di corrispondenza della firma (ad esempio, nascondere o eseguire l'override), `ref` e `out` sono considerati parte della firma e non corrispondono tra loro. Questa restrizione consente C# ai programmi di essere facilmente convertiti per l'esecuzione nel Common Language Infrastructure (CLI), che non consente di definire metodi che differiscono esclusivamente per `ref` e `out`.

Ai fini delle firme, i tipi `object` e `dynamic` sono considerati uguali. I membri dichiarati in un singolo tipo possono pertanto non differire dalla firma esclusivamente da `object` e `dynamic`.

Nell'esempio seguente viene illustrato un set di dichiarazioni di metodo di overload insieme alle relative firme.
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

Si noti che tutti i modificatori di parametro `ref` e `out` ([parametri del metodo](classes.md#method-parameters)) fanno parte di una firma. Pertanto, `F(int)` e `F(ref int)` sono firme univoche. Tuttavia, `F(ref int)` e `F(out int)` non possono essere dichiarate all'interno della stessa interfaccia perché le relative firme sono diverse solo da `ref` e `out`. Si noti anche che il tipo restituito e il modificatore `params` non fanno parte di una firma, pertanto non è possibile eseguire l'overload solo in base al tipo restituito o all'inclusione o esclusione del modificatore `params`. Di conseguenza, le dichiarazioni dei metodi `F(int)` e `F(params string[])` identificate sopra comportano un errore in fase di compilazione.

## <a name="scopes"></a>Ambiti

L' ***ambito*** di un nome è l'area del testo del programma in cui è possibile fare riferimento all'entità dichiarata dal nome senza qualificazione del nome. Gli ambiti possono essere ***annidati***e un ambito interno può ridichiarare il significato di un nome da un ambito esterno. questa operazione, tuttavia, non consente di rimuovere la restrizione imposta dalle [dichiarazioni](basic-concepts.md#declarations) che all'interno di un blocco annidato non è possibile dichiarare una variabile locale con il stesso nome di una variabile locale in un blocco di inclusione). Il nome dall'ambito esterno viene quindi definito ***nascosto*** nell'area del testo del programma coperto dall'ambito interno e l'accesso al nome esterno è possibile solo qualificando il nome.

*  L'ambito di un membro dello spazio dei nomi dichiarato da un *namespace_member_declaration* ([membri dello spazio dei nomi](namespaces.md#namespace-members)) senza *namespace_declaration* di inclusione è l'intero testo del programma.
*  L'ambito di un membro dello spazio dei nomi dichiarato da un *namespace_member_declaration* all'interno di un *namespace_declaration* il cui nome completo è `N` è *namespace_body* di ogni *namespace_declaration* la cui proprietà è completa il nome completo è `N` o inizia con `N`, seguito da un punto.
*  L'ambito del nome definito da un *extern_alias_directive* si estende su *using_directive*s, *global_attributes* e *namespace_member_declaration*s dell'unità di compilazione immediatamente contenitore o del corpo dello spazio dei nomi. Un *extern_alias_directive* non contribuisce a tutti i nuovi membri dello spazio di dichiarazione sottostante. In altre parole, un *extern_alias_directive* non è transitivo, ma influiscono solo sull'unità di compilazione o sul corpo dello spazio dei nomi in cui si verifica.
*  L'ambito di un nome definito o importato da un *using_directive* ([direttive using](namespaces.md#using-directives)) si estende su *namespace_member_declaration*di *compilation_unit* o *namespace_body* in cui *using_directive* si verifica. Un *using_directive* può creare zero o più nomi di spazio dei nomi, tipi o membri disponibili all'interno di un determinato *compilation_unit* o *namespace_body*, ma non contribuisce a tutti i nuovi membri dello spazio di dichiarazione sottostante. In altre parole, un *using_directive* non è transitivo, ma influiscono solo su *compilation_unit* o *namespace_body* in cui si verifica.
*  L'ambito di un parametro di tipo dichiarato da un *type_parameter_list* in un *class_declaration* ([dichiarazioni di classe](classes.md#class-declarations)) è *class_base*, *type_parameter_constraints_clause*s e *class_body* di tale  *class_declaration*.
*  L'ambito di un parametro di tipo dichiarato da un *type_parameter_list* in *un struct_declaration* ([dichiarazioni struct](structs.md#struct-declarations)) è *struct_interfaces*, *type_parameter_constraints_clause*s e *struct_body* di *struct_declaration*.
*  L'ambito di un parametro di tipo dichiarato da un *type_parameter_list* in un *interface_declaration* ([dichiarazioni di interfaccia](interfaces.md#interface-declarations)) è *interface_base*, *type_parameter_constraints_clause*s e *interface_body* del *interface_declaration*.
*  L'ambito di un parametro di tipo dichiarato da un *type_parameter_list* in un *delegate_declaration* ([dichiarazioni di Delegate](delegates.md#delegate-declarations)) è *return_type*, *formal_parameter_list*e *type_parameter_constraints_clause* s di tale *delegate_declaration*.
*  L'ambito di un membro dichiarato da un *class_member_declaration* ([corpo della classe](classes.md#class-body)) è il *class_body* in cui si verifica la dichiarazione. Inoltre, l'ambito di un membro della classe si estende a *class_body* di quelle classi derivate incluse nel dominio di accessibilità ([domini di accessibilità](basic-concepts.md#accessibility-domains)) del membro.
*  L'ambito di un membro dichiarato da un *struct_member_declaration* ([membri struct](structs.md#struct-members)) è il *struct_body* in cui si verifica la dichiarazione.
*  L'ambito di un membro dichiarato da un *enum_member_declaration* ([membri enum](enums.md#enum-members)) è il *enum_body* in cui si verifica la dichiarazione.
*  L'ambito di un parametro dichiarato in un *method_declaration* ([Methods](classes.md#methods)) è il *method_body* di tale *method_declaration*.
*  L'ambito di un parametro dichiarato in un *indexer_declaration* ([indicizzatori](classes.md#indexers)) è il *accessor_declarations* di tale *indexer_declaration*.
*  L'ambito di un parametro dichiarato in un *operator_declaration* ([Operators](classes.md#operators)) è il *blocco* di tale *operator_declaration*.
*  L'ambito di un parametro dichiarato in un *constructor_declaration* ([costruttori di istanza](classes.md#instance-constructors)) è il *constructor_initializer* e il *blocco* di tale *constructor_declaration*.
*  L'ambito di un parametro dichiarato in un *lambda_expression* ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) è il *anonymous_function_body* di tale *lambda_expression*
*  L'ambito di un parametro dichiarato in un *anonymous_method_expression* ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) è il *blocco* di tale *anonymous_method_expression*.
*  L'ambito di un'etichetta dichiarata in un *labeled_statement* ([istruzioni con etichetta](statements.md#labeled-statements)) è il *blocco* in cui si verifica la dichiarazione.
*  L'ambito di una variabile locale dichiarata in un *local_variable_declaration* ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) è il blocco in cui si verifica la dichiarazione.
*  L'ambito di una variabile locale dichiarata in un *switch_block* di un'istruzione `switch` ([l'istruzione switch](statements.md#the-switch-statement)) è *switch_block*.
*  L'ambito di una variabile locale dichiarata in un *for_initializer* di un'istruzione `for` ([l'istruzione for](statements.md#the-for-statement)) *è for_initializer*, *for_condition*, *for_iterator*e l' *istruzione* contained del istruzione `for`.
*  L'ambito di una costante locale dichiarata in un *local_constant_declaration* ([dichiarazioni di costanti locali](statements.md#local-constant-declarations)) è il blocco in cui si verifica la dichiarazione. Si tratta di un errore in fase di compilazione per fare riferimento a una costante locale in una posizione testuale che precede il relativo *constant_declarator*.
*  L'ambito di una variabile dichiarata come parte di *foreach_statement*, *using_statement*, *lock_statement* o *query_expression* è determinato dall'espansione del costrutto specificato.

Nell'ambito di uno spazio dei nomi, di una classe, di una struttura o di un membro di enumerazione è possibile fare riferimento al membro in una posizione testuale che precede la dichiarazione del membro. Esempio:
```csharp
class A
{
    void F() {
        i = 1;
    }

    int i = 0;
}
```
In questo caso, è valido per `F` per fare riferimento a `i` prima che venga dichiarata.

Nell'ambito di una variabile locale, si tratta di un errore in fase di compilazione per fare riferimento alla variabile locale in una posizione testuale che precede *local_variable_declarator* della variabile locale. Esempio:
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

Nel metodo `F` precedente, la prima assegnazione a `i` in particolare non fa riferimento al campo dichiarato nell'ambito esterno. Si riferisce invece alla variabile locale e genera un errore in fase di compilazione perché precede in modo testuale la dichiarazione della variabile. Nel metodo `G`, l'uso di `j` nell'inizializzatore per la dichiarazione di `j` è valido perché l'uso non precede *local_variable_declarator*. Nel metodo `H`, un *local_variable_declarator* successivo si riferisce correttamente a una variabile locale dichiarata in un *local_variable_declarator* precedente nello stesso *local_variable_declaration*.

Le regole di ambito per le variabili locali sono progettate per garantire che il significato di un nome usato in un contesto di espressione sia sempre lo stesso all'interno di un blocco. Se l'ambito di una variabile locale venisse esteso solo dalla relativa dichiarazione alla fine del blocco, nell'esempio precedente, la prima assegnazione verrebbe assegnata alla variabile di istanza e la seconda assegnazione verrebbe assegnata alla variabile locale, probabilmente portando errori in fase di compilazione se le istruzioni del blocco sono state riorganizzate in un secondo momento.

Il significato di un nome all'interno di un blocco può variare a seconda del contesto in cui viene usato il nome. Nell'esempio
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
il nome `A` viene usato in un contesto di espressione per fare riferimento alla variabile locale `A` e in un contesto di tipo per fare riferimento alla classe `A`.

### <a name="name-hiding"></a>Nascondere il nome

L'ambito di un'entità include in genere più testo del programma rispetto allo spazio di dichiarazione dell'entità. In particolare, l'ambito di un'entità può includere dichiarazioni che introducono nuovi spazi di dichiarazione contenenti entità con lo stesso nome. Tali dichiarazioni fanno sì che l'entità originale diventi ***nascosta***. Viceversa, un'entità è detta ***visibile*** quando non è nascosta.

Il nascondimento dei nomi si verifica quando gli ambiti si sovrappongono attraverso l'annidamento e quando gli ambiti si sovrappongono tramite ereditarietà. Le caratteristiche dei due tipi di Nascondi sono descritte nelle sezioni seguenti.

#### <a name="hiding-through-nesting"></a>Occultamento tramite annidamento

Il nome nascosto tramite annidamento può verificarsi in seguito all'annidamento di spazi dei nomi o tipi all'interno di spazi dei nomi, in seguito all'annidamento di tipi all'interno di classi o struct e come risultato di dichiarazioni di variabili locali e parametri.

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
all'interno del metodo `F`, la variabile di istanza `i` è nascosta dalla variabile locale `i`, ma all'interno del metodo `G`, `i` fa ancora riferimento alla variabile di istanza.

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
la chiamata `F(1)` richiama la `F` dichiarata in `Inner` perché tutte le occorrenze esterne di `F` sono nascoste dalla dichiarazione interna. Per lo stesso motivo, la chiamata `F("Hello")` genera un errore in fase di compilazione.

#### <a name="hiding-through-inheritance"></a>Occultamento tramite ereditarietà

Il nome nascosto tramite ereditarietà si verifica quando le classi o gli struct ridichiarano i nomi ereditati dalle classi di base. Questo tipo di nascosto nome accetta uno dei formati seguenti:

*  Una costante, un campo, una proprietà, un evento o un tipo introdotto in una classe o uno struct nasconde tutti i membri della classe base con lo stesso nome.
*  Un metodo introdotto in una classe o uno struct nasconde tutti i membri della classe base non del metodo con lo stesso nome e tutti i metodi della classe base con la stessa firma (nome del metodo e conteggio dei parametri, modificatori e tipi).
*  Un indicizzatore introdotto in una classe o uno struct nasconde tutti gli indicizzatori della classe base con la stessa firma (numero di parametri e tipi).

Le regole che governano le dichiarazioni di operatore ([operatori](classes.md#operators)) rendono impossibile per una classe derivata dichiarare un operatore con la stessa firma di un operatore in una classe di base. Quindi, gli operatori non si nascondono mai l'uno all'altro.

Contrariamente al nascondere un nome da un ambito esterno, nascondendo un nome accessibile da un ambito ereditato, viene segnalato un avviso. Nell'esempio
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
la dichiarazione di `F` in `Derived` comporta la segnalazione di un avviso. Nascondere un nome ereditato non è un errore, in quanto ciò impedisce l'evoluzione separata delle classi di base. Ad esempio, la situazione precedente potrebbe essere dovuta al fatto che una versione successiva di `Base` ha introdotto un metodo `F` che non era presente in una versione precedente della classe. Con la situazione precedente si è verificato un errore, qualsiasi modifica apportata a una classe di base in una libreria di classi con versioni separate potrebbe potenzialmente causare l'invalidità delle classi derivate.

L'avviso causato da un nome ereditato può essere eliminato mediante l'uso del modificatore `new`:
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

Il modificatore `new` indica che il `F` in `Derived` è "New" e che è effettivamente progettato per nascondere il membro ereditato.

Una dichiarazione di un nuovo membro nasconde un membro ereditato solo nell'ambito del nuovo membro.

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

Nell'esempio precedente, la dichiarazione di `F` in `Derived` nasconde il `F` ereditato da `Base`, ma poiché il nuovo `F` in `Derived` ha accesso privato, l'ambito non si estende a `MoreDerived`. La chiamata `F()` in `MoreDerived.G` è quindi valida e richiama `Base.F`.

## <a name="namespace-and-type-names"></a>Nomi di spazio dei nomi e tipi

Per diversi contesti di C# un programma è necessario specificare *namespace_name* o *type_name* .

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

Un *namespace_name* è un *namespace_or_type_name* che fa riferimento a uno spazio dei nomi. Dopo la risoluzione descritta di seguito, il *namespace_or_type_name* di un *namespace_name* deve fare riferimento a uno spazio dei nomi oppure si verifica un errore in fase di compilazione. Nessun argomento di tipo ([argomenti di tipo](types.md#type-arguments)) può essere presente in un *namespace_name* (solo i tipi possono avere argomenti di tipo).

Un *type_name* è un *namespace_or_type_name* che fa riferimento a un tipo. Dopo la risoluzione, come descritto di seguito, il *namespace_or_type_name* di un *type_name* deve fare riferimento a un tipo o in caso contrario si verifica un errore in fase di compilazione.

Se il *namespace_or_type_name* è un membro di alias qualificato, il suo significato è come descritto in [qualificatori di alias degli spazi dei nomi](namespaces.md#namespace-alias-qualifiers). In caso contrario, un *namespace_or_type_name* ha uno dei quattro formati seguenti:

*  `I`
*  `I<A1, ..., Ak>`
*  `N.I`
*  `N.I<A1, ..., Ak>`

dove `I` è un identificatore singolo, `N` è *namespace_or_type_name* e `<A1, ..., Ak>` è un *type_argument_list*facoltativo. Se non viene specificato alcun *type_argument_list* , prendere in considerazione `k` come zero.

Il significato di un *namespace_or_type_name* viene determinato nel modo seguente:

*   Se il formato del *namespace_or_type_name* è `I` o nel formato `I<A1, ..., Ak>`:
    * Se `K` è zero e *namespace_or_type_name* viene visualizzato all'interno di una dichiarazione di metodo generico ([Metodi](classes.md#methods)) e se tale dichiarazione include un parametro di tipo ([parametri di tipo](classes.md#type-parameters)) denominato @ no__t-4, *namespace_or_type_ il nome* fa riferimento a tale parametro di tipo.
    * In caso contrario, se *namespace_or_type_name* viene visualizzato all'interno di una dichiarazione di tipo, per ogni istanza digitare @ no__t-1 ([il tipo di istanza](classes.md#the-instance-type)), iniziando con il tipo di istanza di tale dichiarazione di tipo e continuando con il tipo di istanza di ogni Dichiarazione della classe o dello struct contenitore (se presente):
        * Se `K` è zero e la dichiarazione di `T` include un parametro di tipo denominato @ no__t-2, il *namespace_or_type_name* fa riferimento a tale parametro di tipo.
        * In caso contrario, se il *namespace_or_type_name* viene visualizzato all'interno del corpo della dichiarazione di tipo e `T` o uno qualsiasi dei relativi tipi di base contiene un tipo accessibile annidato con nome @ no__t-2 e `K` @ no__t-4type parametri, *namespace_or_type _name* fa riferimento a quel tipo costruito con gli argomenti di tipo specificati. Se è presente più di un tipo di questo tipo, viene selezionato il tipo dichiarato all'interno del tipo più derivato. Si noti che i membri non di tipo (costanti, campi, metodi, proprietà, indicizzatori, operatori, costruttori di istanze, distruttori e costruttori statici) e i membri di tipo con un numero diverso di parametri di tipo vengono ignorati quando si determina il significato di *namespace_or_type_name*.
    * Se i passaggi precedenti non hanno avuto esito positivo, per ogni spazio dei nomi @ no__t-0, a partire dallo spazio dei nomi in cui si verifica il *namespace_or_type_name* , continuando con ogni spazio dei nomi che lo contiene (se presente) e terminando con lo spazio dei nomi globale, il codice seguente i passaggi vengono valutati fino a quando non si individua un'entità:
        * Se `K` è zero e `I` è il nome di uno spazio dei nomi in @ no__t-2, quindi:
            * Se il percorso in cui si verifica *namespace_or_type_name* è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* o *using_alias_directive* che associa il nome @ no __t-4 con uno spazio dei nomi o un tipo, il *namespace_or_type_name* è ambiguo e si verifica un errore in fase di compilazione.
            * In caso contrario, *namespace_or_type_name* fa riferimento allo spazio dei nomi denominato `I` in `N`.
        * In caso contrario, se `N` contiene un tipo accessibile con nome @ no__t-1 e `K` @ no__t-3type parametri, quindi:
            * Se `K` è zero e la posizione in cui si verifica *namespace_or_type_name* è racchiusa tra una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* o *using_alias_directive* che associa il nome @ no__t-5 con uno spazio dei nomi o un tipo, quindi il *namespace_or_type_name* è ambiguo e si verifica un errore in fase di compilazione.
            * In caso contrario, *namespace_or_type_name* fa riferimento al tipo costruito con gli argomenti di tipo specificati.
        * In caso contrario, se il percorso in cui si verifica *namespace_or_type_name* è racchiuso da una dichiarazione dello spazio dei nomi per `N`:
            * Se `K` è zero e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* o *using_alias_directive* che associa il nome @ no__t-3 con uno spazio dei nomi o un tipo importato, *namespace_or_type_name* fa riferimento a tale spazio dei nomi o tipo.
            * In caso contrario, se gli spazi dei nomi e le dichiarazioni di tipo importati da *using_namespace_directive*s e *using_alias_directive*della dichiarazione dello spazio dei nomi contengono esattamente un tipo accessibile con nome @ no__t-2 e `K` @ no__t-4type , quindi *namespace_or_type_name* fa riferimento a quel tipo costruito con gli argomenti di tipo specificati.
            * In caso contrario, se gli spazi dei nomi e le dichiarazioni di tipo importati dalle *using_namespace_directive*s e *using_alias_directive*della dichiarazione dello spazio dei nomi contengono più di un tipo accessibile con nome @ no__t-2 e `K` @ no__t-4type parametri, *namespace_or_type_name* è ambiguo e si verifica un errore.
    * In caso contrario, *namespace_or_type_name* non è definito e si verifica un errore in fase di compilazione.
*  In caso contrario, il formato di *namespace_or_type_name* è `N.I` o nel formato `N.I<A1, ..., Ak>`. `N` viene innanzitutto risolto come *namespace_or_type_name*. Se la risoluzione di `N` ha esito negativo, si verifica un errore in fase di compilazione. In caso contrario, `N.I` o `N.I<A1, ..., Ak>` viene risolto come segue:
    * Se `K` è zero e `N` fa riferimento a uno spazio dei nomi e `N` contiene uno spazio dei nomi annidato denominato `I`, *namespace_or_type_name* fa riferimento a tale spazio dei nomi annidato.
    * In caso contrario, se `N` fa riferimento a uno spazio dei nomi e `N` contiene un tipo accessibile con i parametri Name @ no__t-2 e `K` @ no__t-4type, *namespace_or_type_name* fa riferimento a tale tipo costruito con gli argomenti di tipo specificati.
    * In caso contrario, se `N` fa riferimento a una classe o a un tipo struct (possibilmente costruito) e `N` o una delle relative classi base contiene un tipo accessibile annidato con i parametri Name @ no__t-2 e `K` @ no__t-4type, *namespace_or_type_name* fa riferimento a tipo costruito con gli argomenti di tipo specificati. Se è presente più di un tipo di questo tipo, viene selezionato il tipo dichiarato all'interno del tipo più derivato. Si noti che se il significato di `N.I` viene determinato come parte della risoluzione della specifica della classe base di `N`, la classe di base diretta di `N` viene considerata come oggetto ([classi base](classes.md#base-classes)).
    * In caso contrario, `N.I` è un *namespace_or_type_name*non valido e si verifica un errore in fase di compilazione.

A un *namespace_or_type_name* è consentito fare riferimento a una classe statica ([classi statiche](classes.md#static-classes)) solo se

*  *Namespace_or_type_name* è il `T` in un *namespace_or_type_name* nel formato `T.I`, oppure
*  *Namespace_or_type_name* è il `T` in un *typeof_expression* ([elenco di argomenti](expressions.md#argument-lists)1) nel formato `typeof(T)`.

### <a name="fully-qualified-names"></a>Nomi completi

Ogni spazio dei nomi e tipo ha un ***nome***completo che identifica in modo univoco lo spazio dei nomi o il tipo tra tutti gli altri. Il nome completo di uno spazio dei nomi o di un tipo `N` è determinato nel modo seguente:

*  Se `N` è un membro dello spazio dei nomi globale, il nome completo sarà `N`.
*  In caso contrario, il nome completo è `S.N`, dove `S` è il nome completo dello spazio dei nomi o del tipo in cui viene dichiarato `N`.

In altre parole, il nome completo di `N` è il percorso gerarchico completo degli identificatori che portano a `N`, a partire dallo spazio dei nomi globale. Poiché ogni membro di uno spazio dei nomi o di un tipo deve avere un nome univoco, il nome completo di uno spazio dei nomi o di un tipo è sempre univoco.

Nell'esempio seguente vengono illustrate diverse dichiarazioni di tipo e spazio dei nomi insieme ai nomi completi associati.
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

C#Usa la gestione automatica della memoria, che consente agli sviluppatori di allocare e liberare manualmente la memoria occupata dagli oggetti. I criteri di gestione della memoria automatici sono implementati da un ***Garbage Collector***. Il ciclo di vita di gestione della memoria di un oggetto è il seguente:

1. Quando viene creato l'oggetto, viene allocata la memoria, viene eseguito il costruttore e l'oggetto viene considerato attivo.
2. Se non è possibile accedere all'oggetto, o a una parte di esso, da qualsiasi possibile continuazione di esecuzione, ad eccezione dell'esecuzione dei distruttori, l'oggetto viene considerato non più in uso e diventa idoneo per la distruzione. Il C# compilatore e il Garbage Collector possono scegliere di analizzare il codice per determinare quali riferimenti a un oggetto possono essere utilizzati in futuro. Se, ad esempio, una variabile locale nell'ambito è l'unico riferimento esistente a un oggetto, ma tale variabile locale non viene mai indicata in alcuna continuazione di esecuzione dal punto di esecuzione corrente nella procedura, è possibile che la Garbage Collector (ma non obbligatorio per) considerare l'oggetto come non più utilizzato.
3. Quando l'oggetto è idoneo per la distruzione, in un secondo momento non specificato, il distruttore ([distruttori](classes.md#destructors)) (se presente) per l'oggetto viene eseguito. In circostanze normali, il distruttore per l'oggetto viene eseguito una sola volta, anche se le API specifiche dell'implementazione possono consentire l'override di questo comportamento.
4. Quando viene eseguito il distruttore per un oggetto, se tale oggetto o una parte di esso non è accessibile da qualsiasi possibile continuazione di esecuzione, inclusa l'esecuzione di distruttori, l'oggetto viene considerato inaccessibile e l'oggetto diventa idoneo per la raccolta.
5. Infine, in un determinato momento dopo che l'oggetto diventa idoneo per la raccolta, il Garbage Collector libera la memoria associata a tale oggetto.

Il Garbage Collector gestisce le informazioni sull'utilizzo degli oggetti e utilizza tali informazioni per prendere decisioni relative alla gestione della memoria, ad esempio la posizione in memoria per individuare un oggetto appena creato, quando rilocare un oggetto e quando un oggetto non è più in uso o inaccessibile.

Analogamente ad altri linguaggi che presuppongono l'esistenza C# di un Garbage Collector, è progettato in modo che il Garbage Collector possa implementare un'ampia gamma di criteri di gestione della memoria. Non richiede, C# ad esempio, che i distruttori vengano eseguiti o che gli oggetti vengano raccolti non appena sono idonei oppure che i distruttori vengano eseguiti in un ordine particolare o in un thread particolare.

Il comportamento del Garbage Collector può essere controllato, a un certo livello, tramite metodi statici sulla classe `System.GC`. Questa classe può essere utilizzata per richiedere l'esecuzione di una raccolta, i distruttori da eseguire (o non eseguire) e così via.

Poiché la Garbage Collector è consentita la latitudine ampia per decidere quando raccogliere oggetti ed eseguire distruttori, un'implementazione conforme può produrre output che differisce da quello mostrato dal codice seguente. Programma
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
Crea un'istanza della classe `A` e un'istanza della classe `B`. Questi oggetti diventano idonei per Garbage Collection quando alla variabile `b` viene assegnato il valore `null`, dal momento che dopo questo periodo non è possibile accedere a qualsiasi codice scritto dall'utente. L'output può essere

```console
Destruct instance of A
Destruct instance of B
```
oppure
```console
Destruct instance of B
Destruct instance of A
```
Poiché il linguaggio non impone vincoli sull'ordine in cui gli oggetti vengono sottoposti a Garbage Collection.

In casi sottili, la distinzione tra "idoneo per la distruzione" e "idoneo per la raccolta" può essere importante. Ad esempio,
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

Nel programma precedente, se il Garbage Collector sceglie di eseguire il distruttore di `A` prima del distruttore di `B`, l'output del programma potrebbe essere:
```console
Destruct instance of A
Destruct instance of B
A.F
RefA is not null
```

Si noti che anche se l'istanza di `A` non è in uso e il distruttore di `A` è stato eseguito, è comunque possibile che i metodi di `A` (in questo caso, `F`) vengano chiamati da un altro distruttore. Si noti inoltre che l'esecuzione di un distruttore può comportare l'utilizzo di un oggetto dal programma principale. In questo caso, l'esecuzione del distruttore `B` ha causato un'istanza di `A` che in precedenza non era in uso per essere accessibile dal riferimento attivo `Test.RefA`. Dopo la chiamata a `WaitForPendingFinalizers`, l'istanza di `B` è idonea per la raccolta, ma l'istanza di `A` non è, a causa del riferimento `Test.RefA`.

Per evitare confusione e comportamenti imprevisti, è in genere consigliabile che i distruttori eseguano solo la pulizia dei dati archiviati nei propri campi dell'oggetto e non eseguano azioni su oggetti o campi statici a cui si fa riferimento.

Un'alternativa all'utilizzo dei distruttori consiste nel consentire a una classe di implementare l'interfaccia `System.IDisposable`. Ciò consente al client dell'oggetto di determinare quando rilasciare le risorse dell'oggetto, in genere accedendo all'oggetto come risorsa in un'istruzione `using` ([istruzione using](statements.md#the-using-statement)).

## <a name="execution-order"></a>Ordine di esecuzione

L'esecuzione di C# un programma procede in modo tale che gli effetti collaterali di ogni thread in esecuzione vengano conservati nei punti di esecuzione critici. Un ***effetto collaterale*** è definito come lettura o scrittura di un campo volatile, una scrittura in una variabile non volatile, una scrittura in una risorsa esterna e la generazione di un'eccezione. I punti di esecuzione critici in cui deve essere mantenuto l'ordine di questi effetti collaterali sono i riferimenti a campi volatili ([campi volatili](classes.md#volatile-fields)), le istruzioni `lock` ([istruzione lock](statements.md#the-lock-statement)) e la creazione e la terminazione del thread. L'ambiente di esecuzione è libero di modificare l'ordine di esecuzione di C# un programma, in base ai vincoli seguenti:

*  La dipendenza dei dati viene mantenuta all'interno di un thread di esecuzione. Ovvero, il valore di ogni variabile viene calcolato come se tutte le istruzioni nel thread venissero eseguite nell'ordine del programma originale.
*  Le regole di ordinamento dell'inizializzazione vengono mantenute (inizializzazione del[campo](classes.md#field-initialization) e [inizializzatori di variabili](classes.md#variable-initializers)).
*  L'ordinamento degli effetti collaterali viene mantenuto per quanto riguarda le letture e le scritture volatili ([campi volatili](classes.md#volatile-fields)). Inoltre, l'ambiente di esecuzione non deve valutare parte di un'espressione se può dedurre che il valore di tale espressione non viene utilizzato e che non sono stati generati effetti collaterali necessari, inclusi quelli causati dalla chiamata a un metodo o dall'accesso a un campo volatile. Quando l'esecuzione del programma viene interrotta da un evento asincrono, ad esempio un'eccezione generata da un altro thread, non è garantito che gli effetti collaterali osservabili siano visibili nell'ordine del programma originale.
