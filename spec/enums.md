---
ms.openlocfilehash: 3b142d7dbda8a94a4cf2c973a2e380065dcbf5ee
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703972"
---
# <a name="enums"></a>Enumerazioni

Un ***tipo enum*** è un tipo di valore Distinct ([tipi valore](types.md#value-types)) che dichiara un set di costanti denominate.

Esempio

```csharp
enum Color
{
    Red,
    Green,
    Blue
}
```

dichiara un tipo enum denominato `Color` con membri `Red`, `Green`e `Blue`.

## <a name="enum-declarations"></a>Dichiarazioni enum

Una dichiarazione enum dichiara un nuovo tipo enum. Una dichiarazione enum inizia con la parola chiave `enum`e definisce il nome, l'accessibilità, il tipo sottostante e i membri dell'enumerazione.

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

Ogni tipo di enumerazione ha un tipo integrale corrispondente denominato ***tipo sottostante*** del tipo enum. Questo tipo sottostante deve essere in grado di rappresentare tutti i valori di enumeratore definiti nell'enumerazione. Una dichiarazione enum può dichiarare in modo esplicito un tipo sottostante di `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` o `ulong`. Si noti che non è possibile usare `char` come tipo sottostante. Una dichiarazione enum che non dichiara in modo esplicito un tipo sottostante ha un tipo sottostante di `int`.

Esempio

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

dichiara un'enumerazione con un tipo sottostante di `long`. Uno sviluppatore può scegliere di utilizzare un tipo di `long`sottostante, come nell'esempio, per consentire l'utilizzo di valori compresi nell'intervallo di `long` ma non nell'intervallo di `int`oppure per mantenere questa opzione per il futuro.

## <a name="enum-modifiers"></a>Modificatori enum

Un *enum_declaration* può includere facoltativamente una sequenza di modificatori enum:

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione di enumerazione.

I modificatori di una dichiarazione di enumerazione hanno lo stesso significato di quelli di una dichiarazione di classe ([modificatori di classe](classes.md#class-modifiers)). Si noti, tuttavia, che i modificatori `abstract` e `sealed` non sono consentiti in una dichiarazione enum. Le enumerazioni non possono essere astratte e non consentire la derivazione.

## <a name="enum-members"></a>Membri enum

Il corpo di una dichiarazione di tipo enum definisce zero o più membri enum, ovvero le costanti denominate del tipo enum. Due membri enum non possono avere lo stesso nome.

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

A ogni membro enum è associato un valore costante. Il tipo di questo valore è il tipo sottostante per l'enumerazione che lo contiene. Il valore costante per ogni membro enum deve essere compreso nell'intervallo del tipo sottostante per l'enumerazione. Esempio

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

genera un errore in fase di compilazione perché i valori costanti `-1`, `-2`e `-3` non sono compresi nell'intervallo del tipo integrale sottostante `uint`.

Più membri enum possono condividere lo stesso valore associato. Esempio

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

Mostra un'enumerazione in cui due membri enum, `Blue` e `Max`, hanno lo stesso valore associato.

Il valore associato di un membro enum viene assegnato in modo implicito o esplicito. Se la dichiarazione del membro enum dispone di un inizializzatore di *constant_expression* , il valore di tale espressione costante, convertito in modo implicito nel tipo sottostante dell'enumerazione, è il valore associato del membro enum. Se la dichiarazione del membro enum non dispone di un inizializzatore, il relativo valore associato viene impostato in modo implicito, come indicato di seguito:

*  Se il membro enum è il primo membro enum dichiarato nel tipo enum, il valore associato è zero.
*  In caso contrario, il valore associato del membro enum viene ottenuto aumentando di uno il valore associato del membro enum testuale precedente. Questo valore maggiore deve essere compreso nell'intervallo di valori che possono essere rappresentati dal tipo sottostante; in caso contrario, si verifica un errore in fase di compilazione.

Esempio

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

stampa i nomi dei membri di enumerazione e i relativi valori associati. L'output è:

```console
Red = 0
Green = 10
Blue = 11
```

per i motivi seguenti:

*  al membro enum `Red` viene assegnato automaticamente il valore zero (poiché non contiene inizializzatore ed è il primo membro enum);
*  al membro enum `Green` viene assegnato in modo esplicito il valore `10`;
*  al membro enum `Blue` viene assegnato automaticamente il valore maggiore di quello del membro che lo precede in modo testuale.

Il valore associato di un membro enum non può, direttamente o indirettamente, utilizzare il valore del proprio membro enum associato. Oltre a questa restrizione della circolare, gli inizializzatori di membri di enumerazione possono fare riferimento ad altri inizializzatori di membri enum, indipendentemente dalla loro posizione testuale. All'interno di un inizializzatore di membro enum, i valori di altri membri enum vengono sempre considerati come aventi il tipo del relativo tipo sottostante, in modo che i cast non siano necessari quando si fa riferimento ad altri membri enum.

Esempio

```csharp
enum Circular
{
    A = B,
    B
}
```

genera un errore in fase di compilazione perché le dichiarazioni di `A` e `B` sono circolari. `A` dipende da `B` in modo esplicito e `B` dipende da `A` in modo implicito.

I membri enum sono denominati e hanno come ambito un modo esattamente analogo ai campi all'interno delle classi. L'ambito di un membro enum è il corpo del tipo enum che lo contiene. All'interno di tale ambito, è possibile fare riferimento ai membri enum con il nome semplice. Da tutto il codice, il nome di un membro enum deve essere qualificato con il nome del relativo tipo enum. I membri enum non hanno alcuna accessibilità dichiarata. un membro enum è accessibile se il tipo enum che lo contiene è accessibile.

## <a name="the-systemenum-type"></a>Tipo System. Enum

Il tipo `System.Enum` è la classe di base astratta di tutti i tipi enum (distinti e diversi dal tipo sottostante del tipo enum) e i membri ereditati da `System.Enum` sono disponibili in qualsiasi tipo enum. Una conversione boxing ([conversioni boxing](types.md#boxing-conversions)) esiste da qualsiasi tipo enum a `System.Enum`e una conversione unboxing (conversione[unboxing](types.md#unboxing-conversions)) esiste da `System.Enum` a qualsiasi tipo enum.

Si noti che `System.Enum` non è a sua volta una *enum_type*. Piuttosto, si tratta di un *class_type* da cui derivano tutti i *enum_type*. Il tipo `System.Enum` eredita dal tipo `System.ValueType` ([il tipo System. ValueType](types.md#the-systemvaluetype-type)), che a sua volta eredita dal tipo `object`. In fase di esecuzione, un valore di tipo `System.Enum` può essere `null` o un riferimento a un valore boxed di qualsiasi tipo enum.

## <a name="enum-values-and-operations"></a>Valori e operazioni di enumerazione

Ogni tipo enum definisce un tipo distinto; per la conversione tra un tipo enum e un tipo integrale o tra due tipi enum è necessaria una conversione esplicita dell'enumerazione ([conversioni esplicite dell'enumerazione](conversions.md#explicit-enumeration-conversions)). Il set di valori che un tipo enum può assumere non è limitato dai relativi membri enum. In particolare, è possibile eseguire il cast di qualsiasi valore del tipo sottostante di un'enumerazione al tipo enum ed è un valore valido distinto di quel tipo enum.

I membri enum hanno il tipo del tipo enum che lo contiene, tranne che all'interno di altri inizializzatori di membri enum: vedere [membri enum](enums.md#enum-members). Il valore di un membro enum dichiarato nel tipo enum `E` con valore associato `v` è `(E)v`.

Gli operatori seguenti possono essere utilizzati in valori di tipi enum: `==`, `!=`, `<`, `>`, `<=``>=` ([operatori di confronto di enumerazione](expressions.md#enumeration-comparison-operators)), `+`binario  (operatore di[addizione](expressions.md#addition-operator)), `-`binario  ([operatore di sottrazione](expressions.md#subtraction-operator)), `^`, `&`, `|` ([operatori logici di enumerazione](expressions.md#enumeration-logical-operators)), `~` (operatore di[complemento bit per bit](expressions.md#bitwise-complement-operator)), `++` e `--`[Operatori di incremento e decremento suffissi](expressions.md#postfix-increment-and-decrement-operators) e [operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)). 

Ogni tipo enum deriva automaticamente dalla classe `System.Enum` (che, a sua volta, deriva da `System.ValueType` e `object`). Pertanto, i metodi e le proprietà ereditati di questa classe possono essere utilizzati in valori di un tipo enum.
