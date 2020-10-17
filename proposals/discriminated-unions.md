---
ms.openlocfilehash: c4168bb99a60688c65db0c146e20ee62c009e08e
ms.sourcegitcommit: f6a48200419ba9f03f5efc8500bfae610cc45abc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097677"
---

# <a name="discriminated-unions--enum-class"></a>Unioni discriminate/ `enum class`

`enum class`es sono un nuovo tipo di dichiarazione di tipo, talvolta definito unioni discriminate, in cui ogni istanza può essere elencata e ogni istanza è non sovrapposta.

Un `enum class` viene definito usando la sintassi seguente:

```antlr
enum_class
    : 'partial'? 'enum class' identifier type_parameter_list? type_parameter_constraints_clause* 
      '{' enum_class_body '}'
    ;

enum_class_body
    : enum_class_cases?
    | enum_class_cases ','
    ;

enum_class_cases
    : enum_class_case
    | enum_class_case ',' enum_class_cases
    ;

enum_class_case
    : enum_class
    | class_declaration
    | identifier type_parameter_list? '(' formal_parameter_list? ')'
    | identifier
    ;

```

Sintassi di esempio:

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius),
}
```

## <a name="semantics"></a>Semantica

Una `enum class` definizione definisce un tipo radice, ovvero una classe astratta con lo stesso nome della `enum class` dichiarazione, e un set di membri, ognuno dei quali ha un tipo che è un sottotipo del tipo radice. Se sono presenti più `partial enum class` definizioni, tutti i membri verranno considerati membri della definizione della classe Enum. A differenza di una definizione di classe astratta definita dall'utente, il `enum class` tipo radice è parziale per impostazione predefinita e viene definito in modo da disporre di un costruttore *privato* senza parametri predefinito.

Si noti che, poiché il tipo radice è definito come una classe astratta parziale, è possibile aggiungere anche definizioni parziali del *tipo radice* , in cui sono consentiti moduli di sintassi standard per un corpo della classe.
Tuttavia, nessun tipo può ereditare direttamente dal tipo radice in una dichiarazione, eccetto quelli specificati come `enum class` membri. Non è inoltre consentito alcun costruttore definito dall'utente per il tipo radice.

Sono disponibili quattro tipi di `enum class` dichiarazioni di membri:

* membri di classi semplici

* membri di classe complessi

* Membri di `enum class`

* membri del valore.

### <a name="simple-class-members"></a>Membri di classi semplici

Una semplice dichiarazione del membro di classe definisce una nuova classe "record" annidata (intenzionalmente lasciata non definita in questo documento) con lo stesso nome. La classe annidata eredita dal tipo radice.

Dato il codice di esempio precedente,

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius)
}
```

la `enum class` dichiarazione presenta una semantica equivalente alla seguente dichiarazione

```C#
 abstract partial class Shape
{
    public record Rectangle(float Width, float Length) : Shape;
    public record Circle(float Radius) : Shape;
}
```

### <a name="complex-class-members"></a>Membri di classe complessi

È anche possibile annidare un'intera dichiarazione di classe sotto una `enum class` dichiarazione. Verrà considerato come una classe annidata del tipo radice. La sintassi consente qualsiasi dichiarazione di classe, ma è necessaria affinché il membro della classe complessa erediti dalla dichiarazione contenitore diretta `enum class` . 

### <a name="enum-class-members"></a>Membri di `enum class`

`enum classes` possono essere annidati l'uno con l'altro, ad esempio

```C#
enum class Expr
{
    enum class Binary
    {
        Addition(Expr left, Expr right),
        Multiplication(Expr left, Expr right)
    }
}
```

Questo è quasi identico alla semantica di un livello di primo livello `enum class` , ad eccezione del fatto che la classe di enumerazione annidata definisce un tipo radice annidato e tutto il contenuto sotto la classe enum nidificata è un sottotipo del tipo radice annidato, anziché il tipo radice di primo livello.

```C#
abstract partial class Expr
{
    abstract partial class Binary : Expr
    {
        public record Addition(Expr left, Expr right) : Binary;
        public record Multiplication(Expr left, Expr right) : Binary;
    }
}
```

### <a name="value-members"></a>Membri valore

`enum classes` può contenere anche membri del valore. I membri del valore definiscono le proprietà statiche pubbliche solo Get sul tipo radice che restituiscono anche il tipo radice, ad esempio

```C#
enum class Color
{
    Red,
    Green
}
```

ha proprietà equivalenti a

```C#
partial abstract class Color
{
    public static Color Red => ...;
    public static Color Green => ...;
}
```

La semantica completa è considerata un dettaglio di implementazione, ma è garantita la restituzione di un'istanza univoca per ogni proprietà e la stessa istanza verrà restituita nelle chiamate ripetute.


### <a name="switch-expression-and-patterns"></a>Cambia espressione e modelli

Ci sono alcune modifiche proposte per la corrispondenza dei modelli e l'espressione switch da gestire `enum classes` . Le espressioni switch possono già corrispondere ai tipi tramite il modello di variabile, ma attualmente per i tipi di riferimento, nessun set di bracci di cambio nell'espressione switch viene considerato completo, tranne che per la corrispondenza con il tipo statico dell'argomento o un sottotipo.

Le espressioni switch verrebbero modificate in modo tale che, se il tipo di radice di un oggetto `enum class` è il tipo statico dell'argomento per l'espressione switch ed esiste un set di criteri che corrispondono a tutti i membri dell'enumerazione, l'opzione sarà considerata esaustiva.

Poiché i membri del valore non sono costanti e non definiscono nuovi tipi statici, attualmente non è possibile trovare una corrispondenza per il criterio. Per ottenere questo risultato, viene aggiunto un nuovo modello che usa la sintassi del criterio costante per consentire la corrispondenza con `enum class` i membri del valore. La corrispondenza viene definita in modo da avere esito positivo solo se l'argomento della corrispondenza del criterio e il valore restituito dal `enum class` membro del valore è uguale al riferimento, anche se l'implementazione non è necessaria per eseguire questo controllo.


## <a name="open-questions"></a>Domande aperte

- [] Quali sono le informazioni sull'algoritmo di tipo comune sui `enum class` membri? Questo codice è valido?
    * `var x = b ? new Shape.Rectangle(...) : new Shape.Circle(...)`

- [] L'aggiunta di un nuovo modello solo per i membri del valore sembra una mano intensa. Esiste una costruzione di versione più generale sensata?
    - [] Anche i membri del valore non eseguono il mapping a una costruzione della classe annidata parallela a causa di questo

- [] Il cambio rispetto a un argomento con un `enum class` tipo statico garantisce un tempo costante?

- [] Se non è possibile fare in modo che `enum class` non venga considerato completo nell'espressione switch, Prefisso `virtual` ?

- [] Quali modificatori devono essere consentiti `enum class` ?
