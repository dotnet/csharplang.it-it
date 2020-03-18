---
ms.openlocfilehash: 2e4a3a32def6900797c151264c984378b09b4988
ms.sourcegitcommit: 5983461e05be62f39c77383cb7857539518cb04f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "79485126"
---

# <a name="discriminated-unions--enum-class"></a>Unioni discriminate/`enum class`

`enum class`es sono un nuovo tipo di dichiarazione di tipo, talvolta definita unioni discriminate, in cui ogni istanza può essere elencata e ogni istanza è non sovrapposta.

Un `enum class` viene definito utilizzando la sintassi seguente:

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

## <a name="semantics"></a>Semantics

Una definizione `enum class` definisce un tipo radice, ovvero una classe astratta con lo stesso nome della dichiarazione di `enum class` e un set di membri, ognuno dei quali ha un tipo che è un sottotipo del tipo radice. Se sono presenti più definizioni di `partial enum class`, tutti i membri verranno considerati membri della definizione della classe Enum. A differenza di una definizione di classe astratta definita dall'utente, il tipo di radice `enum class` è parziale per impostazione predefinita e definito in modo da disporre di un costruttore *privato* senza parametri predefinito.

Si noti che, poiché il tipo radice è definito come una classe astratta parziale, è possibile aggiungere anche definizioni parziali del *tipo radice* , in cui sono consentiti moduli di sintassi standard per un corpo della classe.
Tuttavia, nessun tipo può ereditare direttamente dal tipo radice in una dichiarazione, eccetto quelli specificati come `enum class` membri. Non è inoltre consentito alcun costruttore definito dall'utente per il tipo radice.

Sono disponibili quattro tipi di dichiarazioni di membri `enum class`:

* Membri di classi semplici

* membri di classe complessi

* membri `enum class`

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

la dichiarazione di `enum class` presenta una semantica equivalente alla seguente dichiarazione

```C#
partial abstract class Shape
{
    public data class Rectangle(float Width, float Length) : Shape,
    public data class Circle(float Radius) : Shape
}
```

### <a name="complex-class-members"></a>Membri di classe complessi

È anche possibile annidare un'intera dichiarazione di classe al di sotto di una dichiarazione di `enum class`. Verrà considerato come una classe annidata del tipo radice. La sintassi consente qualsiasi dichiarazione di classe, ma è necessaria affinché il membro della classe complessa erediti dalla dichiarazione di `enum class` contenitore diretto. 

### <a name="enum-class-members"></a>membri `enum class`

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

Questo è quasi identico alla semantica di un `enum class`di primo livello, ad eccezione del fatto che la classe di enumerazione annidata definisce un tipo radice annidato e tutto il contenuto sotto la classe enum nidificata è un sottotipo del tipo radice annidato, anziché il tipo radice di primo livello.

```C#
partial abstract class Expr
{
    partial abstract class Binary : Expr
    {
        public data class Addition(Expr left, Expr right) : Binary,
        public data class Multiplication(Expr left, Expr right) : Binary
    }
}
```

### <a name="value-members"></a>Membri valore

`enum classes` possono inoltre contenere membri di valore. I membri del valore definiscono le proprietà statiche pubbliche solo Get sul tipo radice che restituiscono anche il tipo radice, ad esempio

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

Ci sono alcune modifiche proposte per la corrispondenza dei modelli e l'espressione switch per gestire `enum classes`. Le espressioni switch possono già corrispondere ai tipi tramite il modello di variabile, ma attualmente per i tipi di riferimento, nessun set di bracci di cambio nell'espressione switch viene considerato completo, tranne che per la corrispondenza con il tipo statico dell'argomento o un sottotipo.

Le espressioni switch verrebbero modificate in modo tale che, se il tipo radice di un `enum class` è il tipo statico dell'argomento all'espressione switch ed è presente un set di criteri che corrispondono a tutti i membri dell'enumerazione, l'opzione sarà considerata esaustiva.

Poiché i membri del valore non sono costanti e non definiscono nuovi tipi statici, attualmente non è possibile trovare una corrispondenza per il criterio. Per ottenere questo risultato, viene aggiunto un nuovo modello che usa la sintassi del criterio costante per consentire la corrispondenza con i membri del valore `enum class`. La corrispondenza viene definita in modo da avere esito positivo solo se l'argomento del criterio di ricerca e il valore restituito dal membro del valore `enum class` sono uguali al riferimento, anche se l'implementazione non è necessaria per eseguire questo controllo.


## <a name="open-questions"></a>Domande aperte

- [] Quali sono le informazioni sull'algoritmo di tipo comune sui membri `enum class`? Questo codice è valido?
    * `var x = b ? new Shape.Rectangle(...) : new Shape.Circle(...)`

- [] L'aggiunta di un nuovo modello solo per i membri del valore sembra una mano intensa. Esiste una costruzione di versione più generale sensata?
    - [] Anche i membri del valore non eseguono il mapping a una costruzione della classe annidata parallela a causa di questo

- [] Sta passando a un argomento con un tipo di `enum class` statico che garantisce un tempo costante?

- [] Dovrebbe essere possibile fare in modo che `enum class`es non venga considerato completo nell'espressione switch? Prefisso con `virtual`?

- [] Quali modificatori devono essere consentiti su `enum class`?