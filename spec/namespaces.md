---
ms.openlocfilehash: 33b3044c4c0a4095f4bf49e6cf23c721f538e911
ms.sourcegitcommit: 4e3f2e4ea5a50b186b08d1e93d3ffcdb3754596e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56411299"
---
# <a name="namespaces"></a>Spazi dei nomi

Organizzazione dei programmi C# usando spazi dei nomi. Gli spazi dei nomi vengono usati sia come un sistema di organizzazione "interno" per un programma che come un sistema di organizzazione "external", ovvero un modo per presentare gli elementi di programma che vengono esposti ad altri programmi.

Direttive using ([direttive Using](namespaces.md#using-directives)) sono fornite per facilitare l'uso degli spazi dei nomi.

## <a name="compilation-units"></a>Unità di compilazione

Oggetto *compilation_unit* definisce la struttura generale di un file di origine. Un'unità di compilazione è costituito da zero o più *using_directive*s seguito da zero o più *global_attributes* seguito da zero o più *namespace_member_declaration*s .

```antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? namespace_member_declaration*
    ;
```

Un programma C# è costituito da uno o più unità di compilazione, contenuti ognuno in un file di origine separato. Quando viene compilato un programma C#, tutte le unità di compilazione vengono elaborate insieme. Di conseguenza, unità di compilazione può dipendere tra loro, possibilmente in modo circolare.

Il *using_directive*s di un effetto di unità di compilazione le *global_attributes* e *namespace_member_declaration*s di tale unità di compilazione, ma non hanno alcun effetto altre unità di compilazione.

Il *global_attributes* ([attributi](attributes.md)) consentono la specifica di attributi per il modulo e assembly di destinazione di un'unità di compilazione. Moduli e assembly fungono da contenitori per i tipi. Un assembly può essere costituita da più moduli separati.

Il *namespace_member_declaration*s di ogni unità di compilazione di un programma contribuiscono ai membri di un unico spazio di dichiarazione chiamato spazio dei nomi globale. Ad esempio:

File `A.cs`:
```csharp
class A {}
```

File `B.cs`:
```csharp
class B {}
```

Le due unità di compilazione contribuiscono per il singolo spazio dei nomi globale, in questo caso dichiarare due classi con nomi completi `A` e `B`. Poiché le due unità di compilazione contribuiscono allo stesso spazio di dichiarazione, sarebbe stato un errore se ognuno contiene una dichiarazione di un membro con lo stesso nome.

## <a name="namespace-declarations"></a>Dichiarazioni dello spazio dei nomi

Oggetto *namespace_declaration* costituita dalla parola chiave `namespace`, seguito da uno spazio dei nomi e corpo, seguito facoltativamente da un punto e virgola.

```antlr
namespace_declaration
    : 'namespace' qualified_identifier namespace_body ';'?
    ;

qualified_identifier
    : identifier ('.' identifier)*
    ;

namespace_body
    : '{' extern_alias_directive* using_directive* namespace_member_declaration* '}'
    ;
```

Oggetto *namespace_declaration* può verificarsi una dichiarazione di livello superiore in un *compilation_unit* o come una dichiarazione di membro all'interno di altra *namespace_declaration*. Quando un *namespace_declaration* si verifica una dichiarazione di livello superiore in un *compilation_unit*, lo spazio dei nomi diventa un membro dello spazio dei nomi globali. Quando un *namespace_declaration* si verifica all'interno di altra *namespace_declaration*, lo spazio dei nomi interna diventa un membro dello spazio dei nomi esterno. In entrambi i casi, il nome di uno spazio dei nomi deve essere univoco all'interno dello spazio dei nomi che lo contiene.

Gli spazi dei nomi sono implicitamente `public` e la dichiarazione di uno spazio dei nomi non può includere modificatori di accesso.

All'interno di un *namespace_body*, l'opzione facoltativa *using_directive*s Importa i nomi di altri spazi dei nomi, tipi e membri, consentendo loro di fare riferimento direttamente anziché tramite i nomi completi. L'opzione facoltativa *namespace_member_declaration*s aggiungono membri allo spazio di dichiarazione dello spazio dei nomi. Si noti che tutti i *using_directive*s deve precedere le dichiarazioni di membri.

Il *qualified_identifier* di un *namespace_declaration* può essere un identificatore singolo o una sequenza di identificatori delimitati da "`.`" token. La seconda forma consente al programma per definire uno spazio dei nomi annidato senza annidamento lessicalmente diverse dichiarazioni dello spazio dei nomi. Ad esempio,

```csharp
namespace N1.N2
{
    class A {}

    class B {}
}
```
è semanticamente equivalente a
```csharp
namespace N1
{
    namespace N2
    {
        class A {}

        class B {}
    }
}
```

Gli spazi dei nomi sono aperti e due dichiarazioni dello spazio dei nomi con lo stesso nome completo contribuiscono allo stesso spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)). Nell'esempio
```csharp
namespace N1.N2
{
    class A {}
}

namespace N1.N2
{
    class B {}
}
```
le due dichiarazioni dello spazio dei nomi sopra contribuiscono allo stesso spazio di dichiarazione, in questo caso dichiarare due classi con nomi completi `N1.N2.A` e `N1.N2.B`. Poiché le due dichiarazioni contribuiscono allo stesso spazio di dichiarazione, sarebbe stato un errore se ogni contenuta una dichiarazione di un membro con lo stesso nome.

## <a name="extern-aliases"></a>Alias extern

Un' *extern_alias_directive* introduce un identificatore che funge da un alias per uno spazio dei nomi. La specifica dello spazio dei nomi con alias è esterna al codice sorgente del programma e si applica anche a spazi dei nomi annidati dello spazio dei nomi con alias.

```antlr
extern_alias_directive
    : 'extern' 'alias' identifier ';'
    ;
```

L'ambito di un' *extern_alias_directive* consente di estendere tramite il *using_directive*s, *global_attributes* e *namespace_member_declaration*s della relativa compilazione unità oppure lo spazio dei nomi nel corpo.

All'interno di un corpo di spazio dei nomi o unità di compilazione che contiene un' *extern_alias_directive*, l'identificatore introdotto dalle *extern_alias_directive* può essere utilizzato per lo spazio dei nomi con alias di riferimento. Tratta di un errore in fase di compilazione per il *identifier* è la parola `global`.

Un' *extern_alias_directive* rende disponibile un alias all'interno di un particolare compilazione unità o dello spazio dei nomi nel corpo, ma non aggiunge nuovi membri allo spazio di dichiarazione sottostante. In altre parole, un' *extern_alias_directive* non è transitiva, ma, invece, interessa solo la compilazione unità oppure lo spazio dei nomi del corpo in cui si verifica.

Il programma seguente viene dichiarata due alias extern, `X` e `Y`, ognuno dei quali rappresentano la radice di una gerarchia dello spazio dei nomi distinti:
```csharp
extern alias X;
extern alias Y;

class Test
{
    X::N.A a;
    X::N.B b1;
    Y::N.B b2;
    Y::N.C c;
}
```

Il programma dichiara l'esistenza di extern alias `X` e `Y`, ma le definizioni degli alias effettive sono esterne al programma. Con nome identico `N.B` classi sono ora possibile fare riferimento come `X.N.B` e `Y.N.B`, o, utilizzando il qualificatore di alias dello spazio dei nomi, `X::N.B` e `Y::N.B`. Si verifica un errore se un programma dichiara un alias extern per il quale viene specificata alcuna definizione esterna.

## <a name="using-directives"></a>direttive using

***Direttive using*** facilitare l'uso di spazi dei nomi e tipi definiti in altri spazi dei nomi. Tramite il processo di risoluzione dei nomi di impatto direttive *namespace_or_type_name*s ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) e *simple_name*s ([nomi semplici ](expressions.md#simple-names)), ma a differenza delle dichiarazioni, le direttive using non vengono conteggiati i nuovi membri a spazi di dichiarazione sottostante dell'unità di compilazione o gli spazi dei nomi in cui vengono usati.

```antlr
using_directive
    : using_alias_directive
    | using_namespace_directive
    | using_static_directive
    ;
```

Oggetto *using_alias_directive* ([usando direttive alias](namespaces.md#using-alias-directives)) introduce un alias per un tipo o dello spazio dei nomi.

Oggetto *using_namespace_directive* ([usando le direttive dello spazio dei nomi](namespaces.md#using-namespace-directives)) Importa i membri di tipo di uno spazio dei nomi.

Oggetto *using_static_directive* ([direttive Using static](namespaces.md#using-static-directives)) Importa i tipi annidati e membri statici di un tipo.

L'ambito di una *using_directive* consente di estendere tramite il *namespace_member_declaration*s della relativa compilazione unità oppure lo spazio dei nomi nel corpo. L'ambito di una *using_directive* in modo specifico, non include il relativo peer *using_directive*s. Di conseguenza, il peering *using_directive*s non interferiscano tra loro e l'ordine in cui vengono scritti non è significativo.

### <a name="using-alias-directives"></a>Usando direttive alias

Oggetto *using_alias_directive* introduce un identificatore che funge da un alias per un tipo all'interno del corpo di spazio dei nomi o unità di compilazione che la contiene o dello spazio dei nomi.

```antlr
using_alias_directive
    : 'using' identifier '=' namespace_or_type_name ';'
    ;
```

All'interno delle dichiarazioni di membro in un corpo di spazio dei nomi o unità di compilazione che contiene un *using_alias_directive*, l'identificatore introdotto dalle *using_alias_directive* può essere usato per fare riferimento al determinato spazio dei nomi o tipo. Ad esempio:
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;

    class B: A {}
}
```

Precedente, all'interno delle dichiarazioni di membro nel `N3` spazio dei nomi `A` è un alias per `N1.N2.A`e la classe `N3.B` deriva dalla classe `N1.N2.A`. Lo stesso effetto può essere ottenuto tramite la creazione di un alias `R` per `N1.N2` e fare riferimento a `R.A`:
```csharp
namespace N3
{
    using R = N1.N2;

    class B: R.A {}
}
```

Il *identifier* di un *using_alias_directive* devono essere univoci nello spazio di dichiarazione di unità di compilazione o dello spazio dei nomi che contiene direttamente il *using_alias_directive* . Ad esempio:
```csharp
namespace N3
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;        // Error, A already exists
}
```

Precedente `N3` contiene già un membro `A`, in modo che tratta di un errore in fase di compilazione per una *using_alias_directive* utilizzare tale identificatore. Analogamente, è un errore in fase di compilazione per due o più *using_alias_directive*s presenti nella stessa corpo spazio dei nomi o unità di compilazione di dichiarare gli alias con lo stesso nome.

Oggetto *using_alias_directive* rende disponibile un alias all'interno di un particolare compilazione unità o dello spazio dei nomi nel corpo, ma non aggiunge nuovi membri allo spazio di dichiarazione sottostante. In altre parole, una *using_alias_directive* non è transitiva, ma solo la compilazione unità oppure lo spazio dei nomi al corpo in cui si verifica. Nell'esempio
```csharp
namespace N3
{
    using R = N1.N2;
}

namespace N3
{
    class B: R.A {}            // Error, R unknown
}
```
l'ambito del *using_alias_directive* che introduce `R` estende solo alle dichiarazioni dei membri all'interno dello spazio dei nomi in cui è contenuto, in modo `R` sconosciuto nella seconda dichiarazione dello spazio dei nomi. Tuttavia, posizionare il *using_alias_directive* nella compilazione contenente unit fa sì che l'alias da diventano disponibili in entrambe le dichiarazioni dello spazio dei nomi:
```csharp
using R = N1.N2;

namespace N3
{
    class B: R.A {}
}

namespace N3
{
    class C: R.A {}
}
```

Proprio come i membri normali, i nomi introdotti dalla *using_alias_directive*s sono nascosti da altri membri allo stesso modo nome negli ambiti annidati. Nell'esempio
```csharp
using R = N1.N2;

namespace N3
{
    class R {}

    class B: R.A {}        // Error, R has no member A
}
```
il riferimento al `R.A` nella dichiarazione di `B` provoca un errore in fase di compilazione perché `R` si intende `N3.R`, non `N1.N2`.

L'ordine in cui *using_alias_directive*sono scritte non ha alcun significato e la risoluzione delle *namespace_or_type_name* fa un *using_alias_directive*non è influenzato il *using_alias_directive* stesso o da altri *using_directive*s nel corpo di spazio dei nomi o unità di compilazione che lo contiene. In altre parole, il *namespace_or_type_name* di un *using_alias_directive* viene risolta come se la compilazione unità oppure lo spazio dei nomi nel corpo non fosse no *using_directive*s. Oggetto *using_alias_directive* può tuttavia essere influenzato *extern_alias_directive*s nel corpo di spazio dei nomi o unità di compilazione che lo contiene. Nell'esempio
```csharp
namespace N1.N2 {}

namespace N3
{
    extern alias E;

    using R1 = E.N;        // OK

    using R2 = N1;         // OK

    using R3 = N1.N2;      // OK

    using R4 = R2.N2;      // Error, R2 unknown
}
```
l'ultima *using_alias_directive* genera un errore in fase di compilazione in quanto non dipende dal primo *using_alias_directive*. Il primo *using_alias_directive* non produce un errore perché l'ambito dell'alias extern `E` include il *using_alias_directive*.

Oggetto *using_alias_directive* può creare un alias per qualsiasi tipo, incluso lo spazio dei nomi all'interno del quale viene visualizzato o dello spazio dei nomi e qualsiasi tipo o dello spazio dei nomi annidato all'interno di tale spazio dei nomi.

L'accesso a un tipo o dello spazio dei nomi tramite un alias restituisce esattamente lo stesso risultato come l'accesso a tale spazio dei nomi o tipo tramite il relativo nome dichiarato. Si consideri, ad esempio,
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using R1 = N1;
    using R2 = N1.N2;

    class B
    {
        N1.N2.A a;            // refers to N1.N2.A
        R1.N2.A b;            // refers to N1.N2.A
        R2.A c;               // refers to N1.N2.A
    }
}
```
i nomi `N1.N2.A`, `R1.N2.A`, e `R2.A` sono equivalenti e tutte fanno riferimento alla classe cui nome completo è `N1.N2.A`.

Utilizzo degli alias possibile denominare un tipo costruito chiuso, ma non una dichiarazione di tipo generico non associate nome senza fornire argomenti di tipo. Ad esempio:
```csharp
namespace N1
{
    class A<T>
    {
        class B {}
    }
}

namespace N2
{
    using W = N1.A;          // Error, cannot name unbound generic type

    using X = N1.A.B;        // Error, cannot name unbound generic type

    using Y = N1.A<int>;     // Ok, can name closed constructed type

    using Z<T> = N1.A<T>;    // Error, using alias cannot have type parameters
}
```

### <a name="using-namespace-directives"></a>Le direttive dello spazio dei nomi using

Oggetto *using_namespace_directive* Importa i tipi contenuti in uno spazio dei nomi nel contenitore immediatamente la compilazione unità oppure lo spazio dei nomi corpo, consentendo l'identificatore di ogni tipo di essere utilizzati senza qualifica.

```antlr
using_namespace_directive
    : 'using' namespace_name ';'
    ;
```

All'interno delle dichiarazioni di membro in un corpo di spazio dei nomi o unità di compilazione che contiene un *using_namespace_directive*, possibile fare direttamente riferimento ai tipi contenuti nello spazio dei nomi specificato. Ad esempio:
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1.N2;

    class B: A {}
}
```

Precedente, all'interno delle dichiarazioni di membro nel `N3` dello spazio dei nomi, i membri del tipo `N1.N2` sono direttamente disponibili e la classe `N3.B` deriva dalla classe `N1.N2.A`.

Oggetto *using_namespace_directive* Importa i tipi contenuti nello spazio dei nomi specificato, ma non spazi dei nomi annidati. Nell'esempio
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1;

    class B: N2.A {}        // Error, N2 unknown
}
```
il *using_namespace_directive* Importa i tipi contenuti nel `N1`, ma non gli spazi dei nomi annidati `N1`. Di conseguenza, il riferimento al `N2.A` nella dichiarazione di `B` comporta un errore in fase di compilazione perché nessun membro denominato `N2` rientrano nell'ambito.

A differenza di una *using_alias_directive*, un *using_namespace_directive* può importare i tipi con gli identificatori sono già definiti all'interno del corpo di spazio dei nomi o unità di compilazione che lo contiene. In effetti, i nomi importato da un *using_namespace_directive* nascosti dai membri denominati in modo analogo nel corpo di spazio dei nomi o unità di compilazione che lo contiene. Ad esempio:
```csharp
namespace N1.N2
{
    class A {}

    class B {}
}

namespace N3
{
    using N1.N2;

    class A {}
}
```

A questo punto, all'interno delle dichiarazioni di membro nel `N3` spazio dei nomi `A` fa riferimento a `N3.A` anziché `N1.N2.A`.

Quando più di uno spazio dei nomi o il tipo importato dal *using_namespace_directive*s oppure *using_static_directive*s nel corpo di spazio dei nomi o unità di compilazione stessa contengono i tipi con lo stesso nome, i riferimenti a tale nome come una *type_name* sono considerati ambigui. Nell'esempio
```csharp
namespace N1
{
    class A {}
}

namespace N2
{
    class A {}
}

namespace N3
{
    using N1;

    using N2;

    class B: A {}                // Error, A is ambiguous
}
```
entrambe `N1` e `N2` può contenere un membro `A`e poiché `N3` Importa entrambe, che fa riferimento `A` in `N3` è un errore in fase di compilazione. In questo caso, il conflitto può essere risolto sia attraverso qualifica dei riferimenti a `A`, o con l'introduzione di un *using_alias_directive* che seleziona un determinato `A`. Ad esempio:
```csharp
namespace N3
{
    using N1;

    using N2;

    using A = N1.A;

    class B: A {}                // A means N1.A
}
```

Inoltre, quando più di uno spazio dei nomi o tipo importati dal *using_namespace_directive*s o *using_static_directive*s nel corpo di spazio dei nomi o unità di compilazione stessa contengono i tipi o membri per il stesso nome, i riferimenti a tale nome come una *simple_name* sono considerati ambigui. Nell'esempio
```csharp
namespace N1
{
    class A {}
}

class C
{
    public static int A;
}

namespace N2
{
    using N1;
    using static C;

    class B
    {
        void M() 
        { 
            A a = new A();   // Ok, A is unambiguous as a type-name
            A.Equals(2);     // Error, A is ambiguous as a simple-name
        }
    }
}
```
`N1` contiene un membro di tipo `A`, e `C` contiene un campo statico `A`e poiché `N2` Importa entrambe, che fa riferimento `A` come un *simple_name* è ambiguo e una fase di compilazione errore. 

Ad esempio un *using_alias_directive*, un *using_namespace_directive* non aggiunge nuovi membri allo spazio di dichiarazione sottostante dell'unità di compilazione o dello spazio dei nomi, ma solo il compilazione unità o dello spazio dei nomi nel corpo in cui è presente.

Il *namespace_name* fa riferimento un *using_namespace_directive* viene risolto nello stesso modo il *namespace_or_type_name* fa un  *using_alias_directive*. Pertanto *using_namespace_directive*s nel corpo di spazio dei nomi o unità di compilazione stesso non interferiscano tra loro e possono essere scritti in qualsiasi ordine.

### <a name="using-static-directives"></a>Direttive using static

Oggetto *using_static_directive* Importa i tipi annidati e membri statici contenuti direttamente in una dichiarazione di tipo nel contenitore immediatamente la compilazione unità oppure lo spazio dei nomi corpo, consentendo l'identificatore di ogni membro e il tipo sia utilizzati senza qualifica.

```antlr
using_static_directive
    : 'using' 'static' type_name ';'
    ;
```

All'interno delle dichiarazioni di membro in un corpo di spazio dei nomi o unità di compilazione che contiene un *using_static_directive*, accessibile l'annidati di tipi e membri statici (tranne i metodi di estensione) contenuti direttamente nella dichiarazione del tipo specificato può essere fatto riferimento direttamente. Ad esempio:

```csharp
namespace N1
{
    class A 
    {
        public class B{}
        public static B M(){ return new B(); }
    }
}

namespace N2
{
    using static N1.A;
    class C
    {
        void N() { B b = M(); }
    }
}
```

Precedente, all'interno delle dichiarazioni di membro nel `N2` dello spazio dei nomi, i membri statici e i tipi annidati di `N1.A` sono direttamente disponibili e pertanto il metodo `N` è in grado di fare riferimento a entrambi il `B` e `M` i membri del `N1.A`.

Oggetto *using_static_directive* in modo specifico, non importa i metodi di estensione direttamente come metodi statici, ma li rende disponibili per la chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)). Nell'esempio

```csharp
namespace N1 
{
    static class A 
    {
        public static void M(this string s){}
    }
}

namespace N2
{
    using static N1.A;

    class B
    {
        void N() 
        {
            M("A");      // Error, M unknown
            "B".M();     // Ok, M known as extension method
            N1.A.M("C"); // Ok, fully qualified
        }
    }
}
```
il *using_static_directive* Importa il metodo di estensione `M` contenuti in `N1.A`, ma solo come un metodo di estensione. Di conseguenza, il primo riferimento alla `M` all'interno delle `B.N` comporta un errore in fase di compilazione perché nessun membro denominato `M` rientrano nell'ambito.

Oggetto *using_static_directive* Importa solo i membri e i tipi dichiarati direttamente nel tipo specificato, non i tipi e membri dichiarati nelle classi di base.

TODO: Esempio

Ambiguità tra più *using_namespace_directives* e *using_static_directives* vengono discussi in [usando le direttive dello spazio dei nomi](namespaces.md#using-namespace-directives).

## <a name="namespace-members"></a>Membri di Namespace

Oggetto *namespace_member_declaration* può essere una *namespace_declaration* ([Namespace dichiarazioni](namespaces.md#namespace-declarations)) o un *type_declaration* ( [Dichiarazioni di tipo](namespaces.md#type-declarations)).

```antlr
namespace_member_declaration
    : namespace_declaration
    | type_declaration
    ;
```

Un'unità di compilazione o un corpo dello spazio dei nomi può contenere *namespace_member_declaration*s e tali dichiarazioni aggiungono nuovi membri allo spazio di dichiarazione sottostante del corpo di spazio dei nomi o unità di compilazione che lo contiene.

## <a name="type-declarations"></a>Dichiarazioni di tipo

Oggetto *type_declaration* è un *class_declaration* ([le dichiarazioni di classe](classes.md#class-declarations)), un *struct_declaration* ([Struct le dichiarazioni](structs.md#struct-declarations)), un' *interface_declaration* ([dichiarazioni di interfaccia](interfaces.md#interface-declarations)), un *enum_declaration* ([Enum le dichiarazioni](enums.md#enum-declarations)), o un *delegate_declaration* ([dichiarazioni Delegate](delegates.md#delegate-declarations)).

```antlr
type_declaration
    : class_declaration
    | struct_declaration
    | interface_declaration
    | enum_declaration
    | delegate_declaration
    ;
```

Oggetto *type_declaration* può verificarsi come una dichiarazione di primo livello in un'unità di compilazione o come una dichiarazione di membro all'interno dello spazio dei nomi, classe o struct.

Quando una dichiarazione del tipo per un tipo `T` viene visualizzata come una dichiarazione di primo livello in un'unità di compilazione, il nome completo del tipo appena dichiarato è semplicemente `T`. Quando una dichiarazione del tipo per un tipo `T` si verifica all'interno di uno spazio dei nomi, classe o struct, specificare il nome completo del tipo dichiarato appena `N.T`, dove `N` è il nome completo dello spazio dei nomi, della classe o struct che contiene.

Un tipo dichiarato all'interno di una classe o struct viene chiamato un tipo annidato ([i tipi annidati](classes.md#nested-types)).

I modificatori di accesso autorizzato e l'accesso predefinito per una dichiarazione del tipo dipende dal contesto in cui viene eseguita la dichiarazione ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)):

*  I tipi dichiarati in unità di compilazione o gli spazi dei nomi possono avere `public` o `internal` accesso. Il valore predefinito è `internal` accesso.
*  Tipi dichiarati nelle classi possono avere `public`, `protected internal`, `protected`, `internal`, o `private` accesso. Il valore predefinito è `private` accesso.
*  I tipi dichiarati negli struct possono avere `public`, `internal`, o `private` accesso. Il valore predefinito è `private` accesso.

## <a name="namespace-alias-qualifiers"></a>Qualificatori di alias Namespace

Il ***qualificatore di alias dello spazio dei nomi*** `::` rende possibile garantire che le ricerche di nomi di tipo non sono interessate dall'introduzione di nuovi tipi e membri. Il qualificatore di alias dello spazio dei nomi viene sempre visualizzata tra due identificatori detti gli identificatori sinistro e destro. A differenza dei normali `.` qualificatore, l'identificatore a sinistra della `::` qualificatore viene eseguita una ricerca solo come extern o tramite l'alias.

Oggetto *qualified_alias_member* viene definito come segue:

```antlr
qualified_alias_member
    : identifier '::' identifier type_argument_list?
    ;
```

Oggetto *qualified_alias_member* può essere usato come una *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) o come operando di sinistra in un *member_access* ([L'accesso ai membri](expressions.md#member-access)).

Oggetto *qualified_alias_member* presenta uno dei due formati:

*  `N::I<A1, ..., Ak>`, dove `N` e `I` rappresentano gli identificatori, e `<A1, ..., Ak>` è un elenco di argomenti. (`K` è sempre almeno uno.)
*  `N::I`, dove `N` e `I` rappresentano gli identificatori. (In questo caso, `K` viene considerato uguale a zero.)

Mediante questa notazione, il significato di un *qualified_alias_member* viene determinata come segue:

*  Se `N` è l'identificatore `global`, quindi lo spazio dei nomi globale viene cercato `I`:
   * Se lo spazio dei nomi globale contiene uno spazio dei nomi denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento allo spazio dei nomi.
   * In caso contrario, se lo spazio dei nomi globale contiene un tipo non generico denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento a quel tipo.
   * In caso contrario, se lo spazio dei nomi globale contiene un tipo denominato `I` dotato `K`  parametri di tipo, il *qualified_alias_member* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato.
   * In caso contrario, il *qualified_alias_member* è non definito e si verifica un errore in fase di compilazione.

*  In caso contrario, inizia con la dichiarazione dello spazio dei nomi ([Namespace dichiarazioni](namespaces.md#namespace-declarations)) che contiene il *qualified_alias_member* (se presente), proseguendo con ogni dichiarazione dello spazio dei nomi contenitore (se presente) e che termina con l'unità di compilazione contenente le *qualified_alias_member*, la procedura seguente viene valutata fino a quando non viene individuata un'entità:

   * Se l'unità di compilazione o di dichiarazione dello spazio dei nomi contiene un *using_alias_directive* che associa `N` con un tipo, il *qualified_alias_member* è definito e una fase di compilazione si verifica l'errore.
   * In caso contrario, se l'unità di compilazione o di dichiarazione dello spazio dei nomi contiene un' *extern_alias_directive* oppure *using_alias_directive* che associa `N` con uno spazio dei nomi, quindi:
     * Se lo spazio dei nomi associati `N` contiene uno spazio dei nomi denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento allo spazio dei nomi.
     * In caso contrario, se lo spazio dei nomi associati `N` contiene un tipo non generico denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento a quel tipo.
     * In caso contrario, se lo spazio dei nomi associati `N` contiene un tipo denominato `I` dotato `K`  parametri di tipo, il *qualified_alias_member* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato.
     * In caso contrario, il *qualified_alias_member* è non definito e si verifica un errore in fase di compilazione.
*  In caso contrario, il *qualified_alias_member* è non definito e si verifica un errore in fase di compilazione.

Si noti che tramite il qualificatore di alias dello spazio dei nomi con un alias che fa riferimento a un tipo causa un errore in fase di compilazione. Si noti anche che se l'identificatore `N` viene `global`, quindi la ricerca viene eseguita nello spazio dei nomi globale, anche se è presente un'associazione di alias usando `global` con un tipo o dello spazio dei nomi.

### <a name="uniqueness-of-aliases"></a>Univocità degli alias

Ogni unità di compilazione o corpo dello spazio dei nomi ha uno spazio di dichiarazione separata per gli alias extern e utilizzo degli alias. Di conseguenza, anche se il nome di alias extern o tramite l'alias deve essere univoco all'interno del set di alias extern e utilizzo degli alias dichiarate nel corpo di spazio dei nomi o unità di compilazione che li contiene, un alias può avere lo stesso nome di un tipo o spazio dei nomi, purché si t viene usato solo con la `::` qualificatore.

Nell'esempio
```csharp
namespace N
{
    public class A {}

    public class B {}
}

namespace N
{
    using A = System.IO;

    class X
    {
        A.Stream s1;            // Error, A is ambiguous

        A::Stream s2;           // Ok
    }
}
```
il nome `A` ha due significati possibili all'interno dello spazio dei nomi secondo perché la classe `A` e l'utilizzo alias `A` rientrano nell'ambito. Per questo motivo, l'uso di `A` nel nome qualificato `A.Stream` è ambiguo e genera un errore in fase di compilazione si verifichi. Tuttavia, usare `A` con il `::` qualificatore non è un errore perché `A` viene cercato solo come un alias dello spazio dei nomi.
