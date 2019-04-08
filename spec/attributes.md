---
ms.openlocfilehash: a8ad8a8b3eda1d00fa745bd92e4371eacc36b79f
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229612"
---
# <a name="attributes"></a>Attributi

Gran parte del linguaggio C# consente al programmatore di specificare informazioni dichiarative sulle entità definite nel programma. Ad esempio, specificare l'accessibilità di un metodo in una classe decorandolo con il *method_modifier*s `public`, `protected`, `internal`, e `private`.

C# consente ai programmatori di creare nuovi tipi di informazioni dichiarative, dette ***attributi***. I programmatori possono quindi allegare gli attributi a diverse entità di programma e recuperare informazioni sugli attributi in un ambiente di runtime. Ad esempio, un framework è possibile definire un `HelpAttribute` attributo che può essere inserito su determinati elementi di programma (ad esempio classi e metodi) per fornire un mapping tra gli elementi di programma e la relativa documentazione.

Gli attributi sono definiti tramite la dichiarazione di classi di attributi ([classi Attribute](attributes.md#attribute-classes)), che possono avere parametri posizionali e denominati ([posizionale e i parametri denominati](attributes.md#positional-and-named-parameters)). Gli attributi sono associati all'entità in un programma C# con specifiche per gli attributi ([specifica degli attributi](attributes.md#attribute-specification)) e possono essere recuperati in fase di esecuzione come istanze di attributi ([istanze di attributo](attributes.md#attribute-instances)).

## <a name="attribute-classes"></a>Classi di attributi

Una classe che deriva dalla classe astratta `System.Attribute`, direttamente o indirettamente, è un ***classe attribute***. La dichiarazione di un attributo definisce un nuovo tipo di ***attributo*** che può essere inserito in una dichiarazione. Per convenzione, le classi di attributi sono denominate con un suffisso di `Attribute`. Usi di un attributo possono includere oppure omettere questo suffisso.

### <a name="attribute-usage"></a>Utilizzo dell'attributo

L'attributo `AttributeUsage` ([attributo AttributeUsage The](attributes.md#the-attributeusage-attribute)) viene utilizzato per descrivere l'utilizzo di una classe attribute.

`AttributeUsage` ha un parametro posizionale ([posizionale e i parametri denominati](attributes.md#positional-and-named-parameters)) che consente di specificare i tipi di dichiarazioni in cui può essere utilizzata una classe attribute. L'esempio

```csharp
using System;

[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface)]
public class SimpleAttribute: Attribute 
{
    ...
}
```

definisce una classe attribute denominata `SimpleAttribute` che può essere inserito nel *class_declaration*s e *interface_declaration*solo. L'esempio

```csharp
[Simple] class Class1 {...}

[Simple] interface Interface1 {...}
```

vengono illustrati diversi utilizzi del `Simple` attributo. Anche se questo attributo viene definito con il nome `SimpleAttribute`, quando questo attributo viene utilizzato, il `Attribute` suffisso può essere omesso, causando il nome breve `Simple`. Di conseguenza, l'esempio precedente è semanticamente equivalente alla seguente:

```csharp
[SimpleAttribute] class Class1 {...}

[SimpleAttribute] interface Interface1 {...}
```

`AttributeUsage` ha un parametro denominato ([posizionale e i parametri denominati](attributes.md#positional-and-named-parameters)) denominato `AllowMultiple`, che indica se l'attributo può essere specificato più volte per una determinata entità. Se `AllowMultiple` per un attributo di classe è true e quindi tale classe di attributo è un ***classi di attributo multiuso***e può essere specificato più volte su un'entità. Se `AllowMultiple` per un attributo di classe è false o non viene specificato, quindi tale classe di attributo è un ***monouso classe attribute***e può essere specificato più di una volta in un'entità.

L'esempio

```csharp
using System;

[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class AuthorAttribute: Attribute
{
    private string name;

    public AuthorAttribute(string name) {
        this.name = name;
    }

    public string Name {
        get { return name; }
    }
}
```

definisce una classe di attributo multiuso denominata `AuthorAttribute`. L'esempio

```csharp
[Author("Brian Kernighan"), Author("Dennis Ritchie")] 
class Class1
{
    ...
}
```

viene illustrata una dichiarazione di classe con due utilizzi del `Author` attributo.

`AttributeUsage` include un altro parametro denominato `Inherited`, che indica se l'attributo, quando viene specificato in una classe base, inoltre viene ereditato dalle classi che derivano da tale classe. Se `Inherited` per un attributo di classe è true, l'attributo in questione viene ereditata. Se `Inherited` per un attributo di classe è false, l'attributo non è ereditato. Se non viene specificato, il valore predefinito è true.

Una classe attribute `X` non ha un `AttributeUsage` attributo collegato, come in

```csharp
using System;

class X: Attribute {...}
```

È equivalente alla seguente:

```csharp
using System;

[AttributeUsage(
    AttributeTargets.All,
    AllowMultiple = false,
    Inherited = true)
]
class X: Attribute {...}
```

### <a name="positional-and-named-parameters"></a>Parametri posizionali e denominati

Le classi di attributo possono avere ***parametri posizionali*** e ***parametri denominati***. Ogni costruttore di istanza pubblici per una classe attribute definisce una sequenza valida di parametri posizionali per classe di attributi. Ogni campo di lettura / scrittura pubblica non statico e la proprietà di una classe attribute definisce un parametro denominato per la classe di attributo.

L'esempio

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpAttribute: Attribute
{
    public HelpAttribute(string url) {        // Positional parameter
        ...
    }

    public string Topic {                     // Named parameter
        get {...}
        set {...}
    }

    public string Url {
        get {...}
    }
}
```

definisce una classe attribute denominata `HelpAttribute` che dispone di un parametro posizionale `url`e un parametro denominato, `Topic`. Anche se è non statico e pubblico, la proprietà `Url` non definisce un parametro denominato, perché non è in lettura / scrittura.

Questa classe di attributi può essere usata come indicato di seguito:

```csharp
[Help("http://www.mycompany.com/.../Class1.htm")]
class Class1
{
    ...
}

[Help("http://www.mycompany.com/.../Misc.htm", Topic = "Class2")]
class Class2
{
    ...
}
```

### <a name="attribute-parameter-types"></a>Tipi di parametro di attributo

I tipi di parametri posizionali e denominati per una classe di attributo sono limitati per il ***tipi di parametro di attributo***, che sono:

*  Uno dei tipi seguenti: `bool`, `byte`, `char`, `double`, `float`, `int`, `long`, `sbyte`, `short`, `string`, `uint`, `ulong`, `ushort`.
*  Tipo `object`.
*  Tipo `System.Type`.
*  Un tipo enum, purché con accessibilità pubblica e i tipi in cui è annidato (se presente) avere accessibilità pubblica ([specifica degli attributi](attributes.md#attribute-specification)).
*  Matrici unidimensionali dei tipi precedenti.
*  Un argomento del costruttore o un campo pubblico che non dispone di uno di questi tipi, non può essere utilizzato come un parametro posizionale o denominato in una specifica di attributo.

## <a name="attribute-specification"></a>Specifica di attributo

***Specifica degli attributi*** è l'applicazione di un attributo definito in precedenza a una dichiarazione. Un attributo è una parte di informazioni dichiarative aggiuntive che sono specificate per una dichiarazione. Attributi possono essere specificati in ambito globale (per specificare attributi per l'assembly o un modulo che lo contiene) e per *type_declaration*s ([dichiarazioni di tipo](namespaces.md#type-declarations)), *class_member_declaration* s ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)), *interface_member_declaration*s ([membri di interfaccia](interfaces.md#interface-members)), *struct_member _declaration*s ([i membri Struct](structs.md#struct-members)), *enum_member_declaration*s ([membri Enum](enums.md#enum-members)), *accessor_declarations*  ([Le funzioni di accesso](classes.md#accessors)), *event_accessor_declarations* ([di eventi campo](classes.md#field-like-events)), e *formal_parameter_list*s ([i parametri del metodo](classes.md#method-parameters)).

Gli attributi sono specificati in ***sezioni attributo***. Una sezione di attributo è costituito da una coppia di parentesi quadre che racchiudono un elenco delimitato da virgole di uno o più attributi. L'ordine in cui gli attributi sono specificati in tale elenco, e l'ordine in cui sezioni collegata alla stessa entità di programma sono disposti, non è significativo. Ad esempio, le specifiche per gli attributi `[A][B]`, `[B][A]`, `[A,B]`, e `[B,A]` sono equivalenti.

```antlr
global_attributes
    : global_attribute_section+
    ;

global_attribute_section
    : '[' global_attribute_target_specifier attribute_list ']'
    | '[' global_attribute_target_specifier attribute_list ',' ']'
    ;

global_attribute_target_specifier
    : global_attribute_target ':'
    ;

global_attribute_target
    : 'assembly'
    | 'module'
    ;

attributes
    : attribute_section+
    ;

attribute_section
    : '[' attribute_target_specifier? attribute_list ']'
    | '[' attribute_target_specifier? attribute_list ',' ']'
    ;

attribute_target_specifier
    : attribute_target ':'
    ;

attribute_target
    : 'field'
    | 'event'
    | 'method'
    | 'param'
    | 'property'
    | 'return'
    | 'type'
    ;

attribute_list
    : attribute (',' attribute)*
    ;

attribute
    : attribute_name attribute_arguments?
    ;

attribute_name
    : type_name
    ;

attribute_arguments
    : '(' positional_argument_list? ')'
    | '(' positional_argument_list ',' named_argument_list ')'
    | '(' named_argument_list ')'
    ;

positional_argument_list
    : positional_argument (',' positional_argument)*
    ;

positional_argument
    : attribute_argument_expression
    ;

named_argument_list
    : named_argument (','  named_argument)*
    ;

named_argument
    : identifier '=' attribute_argument_expression
    ;

attribute_argument_expression
    : expression
    ;
```

Un attributo è costituito da un *attribute_name* e un elenco facoltativo di argomenti posizionali e denominati. Gli argomenti posizionali, se presente, precedono gli argomenti denominati. Un argomento posizionale è costituito da un *attribute_argument_expression*; un argomento denominato è costituito da un nome, seguito da un segno di uguale, seguito da un *attribute_argument_expression*che, insieme , sono limitati dalle stesse regole di assegnazione semplice. L'ordine degli argomenti denominati non è significativo.

Il *attribute_name* identifica una classe attribute. Se la forma di *attribute_name* viene *type_name* quindi questo nome deve fare riferimento a una classe attribute. In caso contrario, si verifica un errore in fase di compilazione. L'esempio

```csharp
class Class1 {}

[Class1] class Class2 {}    // Error
```

comporta un errore in fase di compilazione perché tenta di usare `Class1` come attributo quando viene chiamato `Class1` non è una classe attribute.

Determinati contesti consentono la specifica di un attributo più di una destinazione. Un programma può specificare in modo esplicito la destinazione, includendo un' *attribute_target_specifier*. Quando viene inserito un attributo a livello globale, un *global_attribute_target_specifier* è obbligatorio. In tutti gli altri percorsi, viene applicata un'impostazione predefinita adeguata, ma un' *attribute_target_specifier* consente dunque confermare o sostituire l'impostazione predefinita in determinati casi ambigui (o a confermano semplicemente il valore predefinito in casi non ambiguo). In questo modo, in genere *attribute_target_specifier*s può essere omesso tranne a livello globale. I contesti potenzialmente ambigui vengano risolto come segue:

*  Un attributo specificato in ambito globale può essere applicato all'assembly di destinazione o il modulo di destinazione. Esiste alcun valore predefinito per questo contesto, pertanto, un' *attribute_target_specifier* è sempre obbligatorio in questo contesto. La presenza del `assembly` *attribute_target_specifier* indica che l'attributo si applica alla destinazione assembly; la presenza del `module` *attribute_target_specifier* indica che l'attributo si applica al modulo di destinazione.
*  Un attributo specificato in una dichiarazione di delegato è possibile applicare al delegato dichiarato o al relativo valore restituito. In assenza di un *attribute_target_specifier*, l'attributo viene applicato al delegato. La presenza del `type` *attribute_target_specifier* indica che l'attributo si applica al delegato; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.
*  Può applicare un attributo specificato in una dichiarazione di metodo al metodo da dichiarare o al relativo valore restituito. In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo. La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.
*  Un attributo specificato in una dichiarazione dell'operatore è possibile applicare l'operatore viene dichiarato o al relativo valore restituito. In assenza di un *attribute_target_specifier*, l'attributo viene applicato all'operatore. La presenza del `method` *attribute_target_specifier* indica che l'attributo viene applicato all'operatore; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.
*  Un attributo specificato in una dichiarazione di evento che omette queste funzioni possa applicare all'evento dichiarato, al campo associato (se l'evento non è astratta) o per il componente associato e rimuovere i metodi. In assenza di un *attribute_target_specifier*, l'attributo viene applicato all'evento. La presenza del `event` *attribute_target_specifier* indica che l'attributo viene applicato all'evento; la presenza del `field` *attribute_target_specifier* indica che l'attributo viene applicato al campo. la presenza di e il `method` *attribute_target_specifier* indica che l'attributo si applica ai metodi.
*  Un attributo specificato in una dichiarazione di funzione di accesso get per una dichiarazione di proprietà o l'indicizzatore è possibile applicare per il metodo associato o al relativo valore restituito. In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo. La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.
*  Un attributo specificato in una funzione di accesso set per una dichiarazione di proprietà o un indicizzatore può essere applicato al metodo associato o al solo parametro implicito. In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo. La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `param` *attribute_target_specifier* indica che l'attributo viene applicato al parametro. la presenza del `return` *attribute_target_specifier* indica che l'attributo si applica al valore restituito.
*  Un attributo specificato in una dichiarazione di funzione di accesso add o remove per una dichiarazione di evento può essere applicato al metodo associato o al relativo singolo parametro. In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo. La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `param` *attribute_target_specifier* indica che l'attributo viene applicato al parametro. la presenza del `return` *attribute_target_specifier* indica che l'attributo si applica al valore restituito.

In altri contesti, inclusione di un' *attribute_target_specifier* è consentito, ma non necessari. Ad esempio, una dichiarazione di classe può includere o omettere l'identificatore di `type`:

```csharp
[type: Author("Brian Kernighan")]
class Class1 {}

[Author("Dennis Ritchie")]
class Class2 {}
```

È possibile specificare un valore non valido *attribute_target_specifier*. Ad esempio, l'identificatore di `param` non può essere utilizzato in una dichiarazione di classe:

```csharp
[param: Author("Brian Kernighan")]        // Error
class Class1 {}
```

Per convenzione, le classi di attributi sono denominate con un suffisso di `Attribute`. Un' *attribute_name* del form *type_name* potrebbe includere oppure omettere questo suffisso. Se una classe attribute viene trovata con e senza il suffisso, è presente un'ambiguità e verrà generato un errore in fase di compilazione. Se il *attribute_name* sia stato digitato in modo che il più a destra *identificatore* è un identificatore verbatim ([identificatori](lexical-structure.md#identifiers)), quindi viene confrontato solo con un attributo senza un suffisso, consentendo in questo tipo un'ambiguità da risolvere. L'esempio

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class X: Attribute
{}

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Error: ambiguity
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Refers to X
class Class3 {}

[@XAttribute]           // Refers to XAttribute
class Class4 {}
```

Mostra due classi attribute denominate `X` e `XAttribute`. L'attributo `[X]` , è ambiguo poiché potrebbe fare riferimento a una delle due `X` o `XAttribute`. Utilizzando un identificatore verbatim consente all'obiettivo esatta di essere specificati in questi rari casi. L'attributo `[XAttribute]` non è ambiguo (anche se sarebbe se si è verificato una classe attribute denominata `XAttributeAttribute`!). Se la dichiarazione della classe `X` viene rimosso, quindi entrambi gli attributi fanno riferimento alla classe attribute denominata `XAttribute`, come indicato di seguito:

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Refers to XAttribute
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Error: no attribute named "X"
class Class3 {}
```

È un errore in fase di compilazione da usare più volte una classe di attributo monouso per la stessa entità. L'esempio

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpStringAttribute: Attribute
{
    string value;

    public HelpStringAttribute(string value) {
        this.value = value;
    }

    public string Value {
        get {...}
    }
}

[HelpString("Description of Class1")]
[HelpString("Another description of Class1")]
public class Class1 {}
```

comporta un errore in fase di compilazione perché tenta di usare `HelpString`, ovvero una classe di attributo monouso, più volte nella dichiarazione di `Class1`.

Un'espressione `E` è un *attribute_argument_expression* se vengono soddisfatte tutte le istruzioni seguenti:

*  Il tipo della `E` è un tipo di parametro di attributo ([tipi di parametro di attributo](attributes.md#attribute-parameter-types)).
*  In fase di compilazione, il valore di `E` può essere risolto in uno dei seguenti:
   * Un valore costante.
   * Oggetto `System.Type`.
   * Una matrice unidimensionale di *attribute_argument_expression*s.

Ad esempio:

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class TestAttribute: Attribute
{
    public int P1 {
        get {...}
        set {...}
    }

    public Type P2 {
        get {...}
        set {...}
    }

    public object P3 {
        get {...}
        set {...}
    }
}

[Test(P1 = 1234, P3 = new int[] {1, 3, 5}, P2 = typeof(float))]
class MyClass {}
```

Oggetto *typeof_expression* ([l'operatore typeof](expressions.md#the-typeof-operator)) utilizzata come un'espressione di argomento di attributo può fare riferimento a un tipo non generico, un tipo costruito chiuso o un tipo generico non associato, ma non a un tipo Open. Questo modo si garantisce che l'espressione possa essere risolto in fase di compilazione.

```csharp
class A: Attribute
{
    public A(Type t) {...}
}

class G<T>
{
    [A(typeof(T))] T t;                  // Error, open type in attribute
}

class X
{
    [A(typeof(List<int>))] int x;        // Ok, closed constructed type
    [A(typeof(List<>))] int y;           // Ok, unbound generic type
}
```

## <a name="attribute-instances"></a>Istanze di attributo

Un' ***istanza dell'attributo*** è un'istanza che rappresenta un attributo in fase di esecuzione. Un attributo viene definito con una classe attribute, gli argomenti posizionali e gli argomenti denominato. Un'istanza di attributo è un'istanza della classe di attributo che viene inizializzata con gli argomenti posizionali e denominati.

Il recupero di un'istanza dell'attributo comporta l'elaborazione in fase di compilazione sia in fase di esecuzione, come descritto nelle sezioni seguenti.

### <a name="compilation-of-an-attribute"></a>Compilazione di un attributo

La compilazione di un' *attributo* con una classe attribute `T`, *positional_argument_list* `P` e *named_argument_list* `N`, costituita dai passaggi seguenti:

*  Seguire i passaggi di elaborazione in fase di compilazione per la compilazione di un' *object_creation_expression* del form `new T(P)`. Questi passaggi, generano un errore in fase di compilazione o determinare un costruttore di istanze `C` su `T` che può essere richiamato in fase di esecuzione.
*  Se `C` non dispone di accessibilità pubblica, si verifica un errore in fase di compilazione.
*  Per ognuno *named_argument* `Arg` in `N`:
   * Let `Name` essere il *identificatore* del *named_argument* `Arg`.
   * `Name` è necessario identificare un campo pubblico in lettura / scrittura non statico o una proprietà sul `T`. Se `T` ha non tale campo o proprietà, quindi si verifica un errore in fase di compilazione.
*  Mantenere le informazioni seguenti per la creazione di istanze di runtime dell'attributo: la classe attribute `T`, il costruttore di istanze `C` sul `T`, il *positional_argument_list* `P` e il *named_argument_list* `N`.

### <a name="run-time-retrieval-of-an-attribute-instance"></a>Recupero in fase di esecuzione di un'istanza di attributo

Compilazione di un' *attributo* produce una classe attribute `T`, un costruttore di istanze `C` sul `T`, un *positional_argument_list* `P`e un *named_argument_list* `N`. Se viene fornito queste informazioni, un'istanza dell'attributo può essere recuperata in fase di esecuzione usando la procedura seguente:

*  Seguire i passaggi di elaborazione in fase di esecuzione per l'esecuzione di un' *object_creation_expression* del form `new T(P)`, usando il costruttore di istanza `C` determinata in fase di compilazione. Questi passaggi, generano un'eccezione o produrre un'istanza `O` di `T`.
*  Per ognuno *named_argument* `Arg` in `N`, nell'ordine:
   * Let `Name` essere il *identificatore* del *named_argument* `Arg`. Se `Name` non identifica un campo di lettura / scrittura pubblica non statico o una proprietà su `O`, viene generata un'eccezione.
   * Let `Value` essere il risultato della valutazione di *attribute_argument_expression* di `Arg`.
   * Se `Name` identifica un campo sul `O`, quindi impostare questo campo su `Value`.
   * In caso contrario, `Name` identifica una proprietà su `O`. Impostare questa proprietà su `Value`.
   * Il risultato viene `O`, un'istanza della classe attribute `T` che è stato inizializzato con il *positional_argument_list* `P` e la *named_argument_list* `N`.

## <a name="reserved-attributes"></a>Attributi riservati

Un numero ridotto di attributi influisce sulla lingua in qualche modo. Questi attributi includono:

*  `System.AttributeUsageAttribute` ([Attributo AttributeUsage the](attributes.md#the-attributeusage-attribute)), che viene utilizzato per descrivere i modi in cui è possibile usare una classe attribute.
*  `System.Diagnostics.ConditionalAttribute` ([Attributo Conditional](attributes.md#the-conditional-attribute)), che viene usato per definire metodi condizionali.
*  `System.ObsoleteAttribute` ([Attributo the Obsolete](attributes.md#the-obsolete-attribute)), che viene usato per contrassegnare un membro come obsoleto.
*  `System.Runtime.CompilerServices.CallerLineNumberAttribute`, `System.Runtime.CompilerServices.CallerFilePathAttribute` e `System.Runtime.CompilerServices.CallerMemberNameAttribute` ([attributi informativi sul chiamante](attributes.md#caller-info-attributes)), che sono usati per fornire informazioni sul contesto del chiama ai parametri facoltativi.

### <a name="the-attributeusage-attribute"></a>L'attributo AttributeUsage

L'attributo `AttributeUsage` viene usato per descrivere il modo in cui è possibile usare la classe attribute.

Una classe decorata con il `AttributeUsage` attributo deve derivare da `System.Attribute`, direttamente o indirettamente. In caso contrario, si verifica un errore in fase di compilazione.

```csharp
namespace System
{
    [AttributeUsage(AttributeTargets.Class)]
    public class AttributeUsageAttribute: Attribute
    {
        public AttributeUsageAttribute(AttributeTargets validOn) {...}
        public virtual bool AllowMultiple { get {...} set {...} }
        public virtual bool Inherited { get {...} set {...} }
        public virtual AttributeTargets ValidOn { get {...} }
    }

    public enum AttributeTargets
    {
        Assembly     = 0x0001,
        Module       = 0x0002,
        Class        = 0x0004,
        Struct       = 0x0008,
        Enum         = 0x0010,
        Constructor  = 0x0020,
        Method       = 0x0040,
        Property     = 0x0080,
        Field        = 0x0100,
        Event        = 0x0200,
        Interface    = 0x0400,
        Parameter    = 0x0800,
        Delegate     = 0x1000,
        ReturnValue  = 0x2000,

        All = Assembly | Module | Class | Struct | Enum | Constructor | 
            Method | Property | Field | Event | Interface | Parameter | 
            Delegate | ReturnValue
    }
}
```

### <a name="the-conditional-attribute"></a>L'attributo Conditional

L'attributo `Conditional` consente la definizione delle ***metodi condizionali*** e ***classi di attributi condizionali***.

```csharp
namespace System.Diagnostics
{
    [AttributeUsage(AttributeTargets.Method | AttributeTargets.Class, AllowMultiple = true)]
    public class ConditionalAttribute: Attribute
    {
        public ConditionalAttribute(string conditionString) {...}
        public string ConditionString { get {...} }
    }
}
```

#### <a name="conditional-methods"></a>Metodi condizionali

Un metodo decorata con il `Conditional` attributo è un metodo condizionale. Il `Conditional` attributo indica una condizione dal test di un simbolo di compilazione condizionale. Chiamate a un metodo condizionale sono inclusi o omessi a seconda del fatto che questo simbolo viene definito al momento della chiamata. Se il simbolo è definito, la chiamata viene inclusa; in caso contrario, viene omessa (inclusi evaluation del ricevitore e i parametri della chiamata).

Un metodo condizionale è soggetto alle restrizioni seguenti:

*  Il metodo condizionale deve essere un metodo in un *class_declaration* oppure *struct_declaration*. Si verifica un errore in fase di compilazione se il `Conditional` attributo è specificato in un metodo in una dichiarazione di interfaccia.
*  Il metodo condizionale deve avere un tipo restituito `void`.
*  Il metodo condizionale non deve essere contrassegnato con il `override` modificatore. Un metodo condizionale può essere contrassegnato con il `virtual` modificatore, tuttavia. Esegue l'override di questo metodo sono implicitamente condizionale e non deve essere contrassegnata in modo esplicito con una `Conditional` attributo.
*  Il metodo condizionale non deve essere un'implementazione di un metodo di interfaccia. In caso contrario, si verifica un errore in fase di compilazione.

Inoltre, in cui si verifica un errore in fase di compilazione se un metodo condizionale viene usato in un *delegate_creation_expression*. L'esempio

```csharp
#define DEBUG

using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void M() {
        Console.WriteLine("Executed Class1.M");
    }
}

class Class2
{
    public static void Test() {
        Class1.M();
    }
}
```

dichiara `Class1.M` come un metodo condizionale. `Class2`del `Test` chiama questo metodo. Dopo il simbolo di compilazione condizionale `DEBUG` è definito, se `Class2.Test` viene chiamato, verrà chiamato `M`. Se il simbolo `DEBUG` non fosse stato definito, quindi `Class2.Test` non chiamasse `Class1.M`.

È importante notare che l'inclusione o esclusione di una chiamata a un metodo condizionale è controllata dai simboli di compilazione condizionale al momento della chiamata. Nell'esempio

File `class1.cs`:

```csharp
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void F() {
        Console.WriteLine("Executed Class1.F");
    }
}
```

File `class2.cs`:

```csharp
#define DEBUG

class Class2
{
    public static void G() {
        Class1.F();                // F is called
    }
}
```

File `class3.cs`:

```csharp
#undef DEBUG

class Class3
{
    public static void H() {
        Class1.F();                // F is not called
    }
}
```

le classi `Class2` e `Class3` ognuno dei quali contiene chiamate al metodo condizionale `Class1.F`, ovvero condizionale basata o meno `DEBUG` è definito. Poiché questo simbolo è definito nel contesto di `Class2` ma non `Class3`, la chiamata a `F` nelle `Class2` è incluso, mentre la chiamata a `F` in `Class3` viene omesso.

L'uso di metodi condizionali in una catena di ereditarietà può generare confusione. Le chiamate effettuate a un metodo condizionale attraverso `base`, nel formato `base.M`, sono soggetti alle regole di chiamata di metodo condizionale normale. Nell'esempio

File `class1.cs`:

```csharp
using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public virtual void M() {
        Console.WriteLine("Class1.M executed");
    }
}
```

File `class2.cs`:

```csharp
using System;

class Class2: Class1
{
    public override void M() {
        Console.WriteLine("Class2.M executed");
        base.M();                        // base.M is not called!
    }
}
```

File `class3.cs`:

```csharp
#define DEBUG

using System;

class Class3
{
    public static void Test() {
        Class2 c = new Class2();
        c.M();                            // M is called
    }
}
```

`Class2` include una chiamata al `M` definite nella relativa classe base. Questa chiamata viene omesso in quanto il metodo di base è condizionale in base alla presenza del simbolo `DEBUG`, che non è definito. Di conseguenza, il metodo scrive nella console "`Class2.M executed`" solo. L'utilizzo di razionale *pp_declaration*s può evitare tali problemi.

#### <a name="conditional-attribute-classes"></a>Classi di attributo Conditional

Una classe attribute ([classi Attribute](attributes.md#attribute-classes)) decorato con uno o più `Conditional` attributi è un ***classe attribute condizionale***. Una classe attribute condizionale è pertanto associata con i simboli di compilazione condizionale dichiarati nel relativo `Conditional` attributi. In questo esempio:

```csharp
using System;
using System.Diagnostics;
[Conditional("ALPHA")]
[Conditional("BETA")]
public class TestAttribute : Attribute {}
```

dichiara `TestAttribute` come classe di attributi condizionali associata con i simboli le compilazioni condizionali `ALPHA` e `BETA`.

Specifiche dell'attributo ([specifica degli attributi](attributes.md#attribute-specification)) di un attributo conditional sono inclusi se uno o più dei relativi simboli di compilazione condizionale associato è stato definito, in caso contrario, l'attributo verrà omessa.

È importante notare che l'inclusione o esclusione di una specifica di attributo di una classe di attributi condizionali è controllata dai simboli al momento della specifica di compilazione condizionale. Nell'esempio

File `test.cs`:

```csharp
using System;
using System.Diagnostics;

[Conditional("DEBUG")]

public class TestAttribute : Attribute {}
```

File `class1.cs`:

```csharp
#define DEBUG

[Test]                // TestAttribute is specified

class Class1 {}
```

File `class2.cs`:

```csharp
#undef DEBUG

[Test]                 // TestAttribute is not specified

class Class2 {}
```

le classi `Class1` e `Class2` sono entrambe decorate con l'attributo `Test`, ovvero condizionale basata o meno `DEBUG` è definito. Poiché questo simbolo è definito nel contesto di `Class1` ma non `Class2`, la specifica del `Test` attributo `Class1` è incluso, durante la specifica del `Test` dell'attributo su `Class2` viene omesso.

### <a name="the-obsolete-attribute"></a>Attributo Obsolete

L'attributo `Obsolete` viene usato per contrassegnare tipi e membri dei tipi che non devono più essere utilizzati.

```csharp
namespace System
{
    [AttributeUsage(
        AttributeTargets.Class | 
        AttributeTargets.Struct |
        AttributeTargets.Enum | 
        AttributeTargets.Interface | 
        AttributeTargets.Delegate |
        AttributeTargets.Method | 
        AttributeTargets.Constructor |
        AttributeTargets.Property | 
        AttributeTargets.Field |
        AttributeTargets.Event,
        Inherited = false)
    ]
    public class ObsoleteAttribute: Attribute
    {
        public ObsoleteAttribute() {...}
        public ObsoleteAttribute(string message) {...}
        public ObsoleteAttribute(string message, bool error) {...}
        public string Message { get {...} }
        public bool IsError { get {...} }
    }
}
```

Se un programma Usa un tipo o membro che è dotato di `Obsolete` attributo, il compilatore genera un avviso o un errore. In particolare, il compilatore genera un avviso se viene fornito alcun parametro di errore o se il parametro di errore viene fornito e ha il valore `false`. Il compilatore genera un errore se il parametro di errore specificato e ha il valore `true`.

Nell'esempio

```csharp
[Obsolete("This class is obsolete; use class B instead")]
class A
{
    public void F() {}
}

class B
{
    public void F() {}
}

class Test
{
    static void Main() {
        A a = new A();         // Warning
        a.F();
    }
}
```

la classe `A` decorata con il `Obsolete` attributo. Ogni uso di `A` in `Main` genera un avviso indicante che il messaggio specificato, "questa classe è obsoleta; Utilizzare la classe B."

### <a name="caller-info-attributes"></a>Attributi informativi sul chiamante

Per scopi, ad esempio la registrazione e creazione di report, è talvolta utile per un membro di funzione ottenere determinate informazioni in fase di compilazione relativa al codice chiama. Di attributi informativi sul chiamante consentono di passare tali informazioni in modo trasparente.

Quando un parametro facoltativo è annotato con uno degli attributi di informazioni sul chiamante, omettendo l'argomento corrispondente in una chiamata non necessariamente causano il valore del parametro predefinito deve essere sostituito. In alternativa, se le informazioni specificate sul contesto del chiama sono disponibile, verranno passate tali informazioni come il valore dell'argomento.

Ad esempio:

```csharp
using System.Runtime.CompilerServices

...

public void Log(
    [CallerLineNumber] int line = -1,
    [CallerFilePath]   string path = null,
    [CallerMemberName] string name = null
)
{
    Console.WriteLine((line < 0) ? "No line" : "Line "+ line);
    Console.WriteLine((path == null) ? "No file path" : path);
    Console.WriteLine((name == null) ? "No member name" : name);
}
```

Una chiamata a `Log()` senza argomenti, stamperebbe il percorso di file e numero di riga della chiamata, oltre al nome del membro all'interno del quale si è verificata la chiamata.

Attributi informativi sul chiamante possono verificarsi in qualsiasi origine, i parametri facoltativi incluse nelle dichiarazioni di delegato. Tuttavia, di attributi informativi sul chiamante specifico presentano restrizioni sui tipi di parametri che possono attributo, in modo che sarà sempre presente una conversione implicita da un valore sostitutivo per il tipo di parametro.

È un errore per avere lo stessa attributi informativi sul chiamante in un parametro di definizione e implementazione della parte di una dichiarazione di metodo parziale. Vengono applicati solo attributi informativi sul chiamante nella parte definizione, mentre attributi informativi sul chiamante che si verificano solo in parte l'implementazione vengono ignorati.

Informazioni sul chiamante non interessa la risoluzione dell'overload. Come i parametri facoltativi con attributi ancora vengono omessi dal codice sorgente del chiamante, la risoluzione dell'overload ignora tali parametri nello stesso modo ignora altri omessi parametri facoltativi ([risoluzione dell'Overload](expressions.md#overload-resolution)).

Informazioni sul chiamante viene sostituiti solo quando una funzione viene richiamata in modo esplicito nel codice sorgente. Le chiamate implicite, ad esempio le chiamate di costruttore implicito padre non è un percorso di origine e non consente di sostituire le informazioni sul chiamante. Inoltre, le chiamate che sono associate in modo dinamico non sostituirà con informazioni sul chiamante. Quando un informazioni sul chiamante con attributi di parametro viene omesso in questi casi, viene invece utilizzato il valore predefinito specificato del parametro.

Un'eccezione riguarda le espressioni di query. Questi indirizzi sono considerati espansioni sintattiche e se le chiamate impegnate nell'espansione per omettere i parametri facoltativi con attributi informativi sul chiamante, informazioni sul chiamante verrà sostituiti. Il percorso utilizzato è il percorso della clausola che la chiamata è stata generata dalla query.

Se in un determinato parametro è specificato più di un attributo di informazioni sul chiamante, sono preferibili nell'ordine seguente: `CallerLineNumber`, `CallerFilePath`, `CallerMemberName`.

#### <a name="the-callerlinenumber-attribute"></a>L'attributo CallerLineNumber

Il `System.Runtime.CompilerServices.CallerLineNumberAttribute` JE povolen parametri facoltativi quando viene eseguita una conversione implicita standard ([conversioni implicite](conversions.md#standard-implicit-conversions)) dal valore di costante `int.MaxValue` per il tipo del parametro. Ciò garantisce che qualsiasi numero di riga non negativo fino a tale valore può essere passato senza errori.

Se una chiamata alla funzione da una posizione nel codice sorgente omette un parametro facoltativo e il `CallerLineNumberAttribute`, un valore letterale numerico che rappresenta il numero di riga dell'indirizzo che viene usato come argomento alla chiamata anziché il valore del parametro predefinito.

Se la chiamata si estende su più righe, la riga scelta è dipendente dall'implementazione.

Si noti che il numero di riga può essere influenzato `#line` direttive ([direttive di riga](lexical-structure.md#line-directives)).

#### <a name="the-callerfilepath-attribute"></a>L'attributo CallerFilePath

Il `System.Runtime.CompilerServices.CallerFilePathAttribute` JE povolen parametri facoltativi quando viene eseguita una conversione implicita standard ([conversioni implicite](conversions.md#standard-implicit-conversions)) da `string` per il tipo del parametro.

Se una chiamata alla funzione da una posizione nel codice sorgente omette un parametro facoltativo e il `CallerFilePathAttribute`, un valore letterale stringa che rappresenta tale percorso del file viene usato come argomento alla chiamata anziché il valore del parametro predefinito.

Il formato del percorso del file è dipendente dall'implementazione.

Si noti che il percorso del file potrebbe essere interessato dalle `#line` direttive ([direttive di riga](lexical-structure.md#line-directives)).

#### <a name="the-callermembername-attribute"></a>L'attributo CallerMemberName

Il `System.Runtime.CompilerServices.CallerMemberNameAttribute` JE povolen parametri facoltativi quando viene eseguita una conversione implicita standard ([conversioni implicite](conversions.md#standard-implicit-conversions)) da `string` per il tipo del parametro.

Se una chiamata alla funzione da una posizione all'interno del corpo di un membro di funzione o all'interno di un attributo applicato al membro di funzione stesso o il tipo restituito, i parametri o parametri di tipo nel codice sorgente omette un parametro facoltativo e il `CallerMemberNameAttribute`, un valore letterale stringa che rappresenta il nome di tale membro viene usato come argomento alla chiamata anziché il valore del parametro predefinito.

Per le chiamate che si verificano all'interno di metodi generici, viene usato solo il nome del metodo stesso, senza elenco parametri di tipo.

Per le chiamate che si verificano all'interno di implementazioni esplicite dell'interfaccia membro, viene usato solo il nome del metodo stesso, senza la qualificazione di interfaccia precedente.

Per le chiamate che si verificano all'interno di funzioni di accesso a proprietà o un evento, il nome del membro utilizzato è quello della proprietà o evento stesso.

Per le chiamate che si verificano all'interno di funzioni di accesso dell'indicizzatore, il nome del membro utilizzato è fornito da un `IndexerNameAttribute` ([attributo IndexerName](attributes.md#the-indexername-attribute)) il membro di un indicizzatore, se presente, o il nome predefinito `Item` in caso contrario.

Per le chiamate che si verificano all'interno delle dichiarazioni dei costruttori di istanze, i costruttori statici, distruttori e operatori membro nome usato è dipendente dall'implementazione.

## <a name="attributes-for-interoperation"></a>Attributi per l'interoperabilità

Nota: In questa sezione è applicabile solo per l'implementazione di Microsoft .NET di C#.

### <a name="interoperation-with-com-and-win32-components"></a>Interazione con componenti COM e Win32

Il runtime di .NET offre un numero elevato di attributi che consentono ai programmi C# di interoperare con componenti scritti con DLL Win32 e COM. Ad esempio, il `DllImport` attributo può essere utilizzato in un `static extern` metodo per indicare che l'implementazione del metodo deve essere trovato in una DLL Win32. Questi attributi si trovano nel `System.Runtime.InteropServices` dello spazio dei nomi e la documentazione dettagliata per questi attributi sono disponibili nella documentazione di runtime di .NET.

### <a name="interoperation-with-other-net-languages"></a>Interoperabilità con altri linguaggi .NET

#### <a name="the-indexername-attribute"></a>L'attributo IndexerName

Gli indicizzatori vengono implementati in .NET usando le proprietà indicizzate e hanno un nome nei metadati di .NET. Se nessun `IndexerName` attributo è presente per un indicizzatore e quindi dal nome `Item` viene usato per impostazione predefinita. Il `IndexerName` attributo consente agli sviluppatori di eseguire l'override di questa impostazione predefinita e specificare un nome diverso.

```csharp
namespace System.Runtime.CompilerServices.CSharp
{
    [AttributeUsage(AttributeTargets.Property)]
    public class IndexerNameAttribute: Attribute
    {
        public IndexerNameAttribute(string indexerName) {...}
        public string Value { get {...} } 
    }
}
```
