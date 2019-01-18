---
ms.openlocfilehash: c3b716e6eb331be2ee33fffeb42c1e2406cd3a5c
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229667"
---
# <a name="enums"></a>Enumerazioni

Un' ***tipo di enumerazione*** è un tipo valore distinto ([tipi di valore](types.md#value-types)) che dichiara un set di costanti denominate.

L'esempio

```csharp
enum Color
{
    Red,
    Green,
    Blue
}
```

dichiara un tipo di enumerazione denominato `Color` con i membri `Red`, `Green`, e `Blue`.

## <a name="enum-declarations"></a>Dichiarazioni di enumerazione

Una dichiarazione di enumerazione dichiara un nuovo tipo di enumerazione. Una dichiarazione enum inizia con la parola chiave `enum`e definisce il nome, accessibilità, tipo sottostante e i membri dell'enumerazione.

```antlr
enum_declaration
    : attributes? enum_modifier* 'enum' identifier enum_base? enum_body ';'?
    ;

enum_base
    : ':' integral_type
    ;

enum_body
    : '{' enum_member_declarations? '}'
    | '{' enum_member_declarations ',' '}'
    ;
```

Ogni tipo di enumerazione ha un tipo integrale corrispondente denominato il ***tipo sottostante*** del tipo di enumerazione. Questo tipo sottostante deve essere in grado di rappresentare tutti i valori dell'enumeratore definiti nell'enumerazione. Una dichiarazione di enumerazione possa dichiarare in modo esplicito un tipo sottostante `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` o `ulong`. Si noti che `char` non può essere utilizzato come un tipo sottostante. Una dichiarazione di enumerazione che non dichiara in modo esplicito un tipo sottostante è un tipo sottostante di `int`.

L'esempio

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

dichiara un'enumerazione con un tipo sottostante di `long`. Uno sviluppatore può scegliere di usare un tipo sottostante di `long`, come illustrato nell'esempio, per consentire l'uso di valori che sono nell'intervallo `long` ma non compresa nell'intervallo di `int`, o per mantenere questa opzione per il futuro.

## <a name="enum-modifiers"></a>Modificatori enum

Un' *enum_declaration* può includere una sequenza dei modificatori enum:

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

È un errore in fase di compilazione per lo stesso modificatore venga visualizzato più volte in una dichiarazione di enumerazione.

I modificatori di una dichiarazione di enumerazione hanno lo stesso significato di quelli di una dichiarazione di classe ([classe modificatori](classes.md#class-modifiers)). Si noti tuttavia che il `abstract` e `sealed` modificatori non consentiti in una dichiarazione di enumerazione. Le enumerazioni non possono essere astratte e non consentono la derivazione.

## <a name="enum-members"></a>Membri enum

Il corpo di una dichiarazione di tipo enum definisce zero o più membri di enumerazione, che sono costanti denominate del tipo di enumerazione. Nessuna due membri di enumerazione possono avere lo stesso nome.

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

Ogni membro di enumerazione è associato un valore costante. Il tipo di questo valore è il tipo sottostante per l'enumerazione che lo contiene. Il valore costante per ogni membro di enumerazione deve essere compreso nell'intervallo del tipo sottostante per l'enumerazione. L'esempio

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

Genera un errore in fase di compilazione in quanto i valori delle costanti `-1`, `-2`, e `-3` non sono compresi nell'intervallo del tipo integrale sottostante `uint`.

Più membri di enumerazione possono condividere lo stesso valore associato. L'esempio

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

viene illustrata un'enumerazione nel quale due membri enum - `Blue` e `Max` : associato lo stesso valore.

Il valore associato di un membro di enumerazione è assegnato implicitamente o esplicitamente. Se la dichiarazione del membro enum ha un *constant_expression* inizializzatore, il valore dell'espressione costante, convertito in modo implicito al tipo sottostante dell'enumerazione, è il valore del membro enum associato. Se la dichiarazione del membro enum non include alcun inizializzatore, il valore associato viene impostato in modo implicito, come indicato di seguito:

*  Se il membro di enumerazione è il primo membro di enumerazione dichiarato nel tipo di enumerazione, il valore associato è zero.
*  In caso contrario, il valore del membro enum associato viene ottenuto incrementando il valore associato del membro enum testualmente precedente di uno. Questo aumento del valore deve essere compreso nell'intervallo di valori che può essere rappresentato dal tipo sottostante, in caso contrario, verrà generato un errore in fase di compilazione.

L'esempio

```csharp
using System;

enum Color
{
    Red,
    Green = 10,
    Blue
}

class Test
{
    static void Main() {
        Console.WriteLine(StringFromColor(Color.Red));
        Console.WriteLine(StringFromColor(Color.Green));
        Console.WriteLine(StringFromColor(Color.Blue));
    }

    static string StringFromColor(Color c) {
        switch (c) {
            case Color.Red: 
                return String.Format("Red = {0}", (int) c);

            case Color.Green:
                return String.Format("Green = {0}", (int) c);

            case Color.Blue:
                return String.Format("Blue = {0}", (int) c);

            default:
                return "Invalid color";
        }
    }
}
```

Stampa i nomi dei membri di enumerazione e i relativi valori associati. L'output è:

```
Red = 0
Green = 10
Blue = 11
```

per i motivi seguenti:

*  il membro di enumerazione `Red` viene automaticamente assegnato il valore zero (perché non include alcun inizializzatore ed è il primo membro di enumerazione).
*  il membro di enumerazione `Green` viene assegnato in modo esplicito il valore `10`;
*  e il membro di enumerazione `Blue` viene automaticamente assegnato il valore più grande rispetto al membro testualmente precedente.

Il valore associato di un membro enum non potrà, in modo diretto o indiretto, usare il valore di un proprio membro di enumerazione associata. Questa restrizione circolarità, inizializzatori di membro di enumerazione possono fare riferimento ad altri inizializzatori di membro enum, indipendentemente dalla loro posizione testuale. All'interno di un inizializzatore di membro di enumerazione, i valori degli altri membri di enumerazione vengono sempre trattati come se avessero il tipo del relativo tipo sottostante, in modo che i cast non sono necessari quando si fa riferimento ad altri membri di enumerazione.

L'esempio

```csharp
enum Circular
{
    A = B,
    B
}
```

Genera un errore in fase di compilazione in quanto le dichiarazioni delle `A` e `B` sono circolari. `A` dipende `B` in modo esplicito, e `B` dipende `A` in modo implicito.

Membri enum sono denominati e con ambiti in modo analogo ai campi all'interno delle classi. L'ambito di un membro di enumerazione è il corpo del tipo di enumerazione che li contiene. In tale ambito, i membri di enumerazione possono farvi riferimento.{0}{0}i il relativo nome semplice. Da tutto l'altro codice, il nome di un membro enum deve essere qualificato con il nome del relativo tipo di enumerazione. Membri enum sono privi di qualsiasi accessibilità dichiarata: un membro di enumerazione è accessibile se il tipo di enumerazione che lo contiene è accessibile.

## <a name="the-systemenum-type"></a>Il tipo System. Enum

Il tipo `System.Enum` è la classe base astratta di tutti i tipi di enumerazione (questo è diverso dal tipo sottostante del tipo di enumerazione e distinct) e i membri ereditati da `System.Enum` sono disponibili in qualsiasi tipo di enumerazione. Una conversione boxing ([conversioni Boxing](types.md#boxing-conversions)) esistente da qualsiasi tipo enum `System.Enum`e una conversione unboxing ([conversione Unboxing](types.md#unboxing-conversions)) esistente dal `System.Enum` a qualsiasi tipo di enumerazione.

Si noti che `System.Enum` non è a sua volta un' *enum_type*. Si tratta piuttosto di una *class_type* da cui tutti gli *enum_type*derivano. Il tipo `System.Enum` eredita dal tipo `System.ValueType` ([System. ValueType di tipo](types.md#the-systemvaluetype-type)), che, a sua volta eredita dal tipo `object`. In fase di esecuzione, un valore di tipo `System.Enum` può essere `null` o un riferimento a un valore boxed di qualsiasi tipo di enumerazione.

## <a name="enum-values-and-operations"></a>Operazioni e i valori di enumerazione

Ogni tipo di enumerazione definisce un tipo distinto. una conversione esplicita di enumerazione ([conversioni enumerazione esplicita](conversions.md#explicit-enumeration-conversions)) è necessaria per convertire tra un tipo enum e un tipo integrale o tra due tipi di enumerazione. Il set di valori che può assumere un tipo enum non è limitato dai relativi membri enum. In particolare, qualsiasi valore del tipo sottostante di un'enumerazione può essere convertito nel tipo di enumerazione e rappresenta un valore valido distinto di quel tipo di enumerazione.

Membri enum sono il tipo del rispettivo tipo enum contenitore (tranne che all'interno di altri inizializzatori del membro enum: vedere [membri Enum](enums.md#enum-members)). Il valore di un membro di enumerazione dichiarato nel tipo di enumerazione `E` con il valore associato `v` è `(E)v`.

Gli operatori seguenti possono essere utilizzati con valori dei tipi enum: `==`, `!=`, `<`, `>`, `<=`, `>=`  ([gli operatori di confronto di enumerazione](expressions.md#enumeration-comparison-operators)) , binario `+`  ([operatore di addizione](expressions.md#addition-operator)), binary `-`  ([operatore di sottrazione](expressions.md#subtraction-operator)), `^`, `&` , `|`  ([Operatori logici enumerazione](expressions.md#enumeration-logical-operators)), `~`  ([operatore di complemento bit per bit](expressions.md#bitwise-complement-operator)), `++` e`--`  ([Incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators) e [incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)).

Ogni tipo di enumerazione automaticamente deriva dalla classe `System.Enum` (che, a sua volta deriva da `System.ValueType` e `object`). Di conseguenza, metodi ereditati e le proprietà di questa classe sono utilizzabile con valori di un tipo enum.
