---
ms.openlocfilehash: d162d4b6a489032dcdfca9094a39d88fd03d4013
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704090"
---
# <a name="delegates"></a>Delegati

I delegati consentono scenari in cui altri C++linguaggi, ad esempio, Pascal e modula, sono stati risolti con i puntatori a funzione. A differenza C++ dei puntatori a funzione, tuttavia, i delegati sono completamente orientati agli oggetti e, a differenza C++ dei puntatori alle funzioni membro, i delegati incapsulano sia un'istanza dell'oggetto che un metodo.

Una dichiarazione di delegato definisce una classe derivata dalla classe `System.Delegate`. Un'istanza del delegato incapsula un elenco chiamate, ovvero un elenco di uno o più metodi, ognuno dei quali viene definito entità richiamabile. Per i metodi di istanza, un'entità chiamabile è costituita da un'istanza e da un metodo su tale istanza. Per i metodi statici, un'entità chiamabile è costituita da un solo metodo. La chiamata di un'istanza del delegato con un set di argomenti appropriato comporta la chiamata di ogni entità chiamabile del delegato con il set di argomenti specificato.

Una proprietà interessante e utile di un'istanza del delegato è che non è in grado di conoscere o preoccuparsi delle classi dei metodi incapsulati. ciò che conta è che questi metodi siano compatibili ([dichiarazioni delegate](delegates.md#delegate-declarations)) con il tipo del delegato. Questa operazione rende i delegati perfettamente adatti alla chiamata "anonima".

## <a name="delegate-declarations"></a>Dichiarazioni di delegati

Un *delegate_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo tipo delegato.

```antlr
delegate_declaration
    : attributes? delegate_modifier* 'delegate' return_type
      identifier variant_type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;

delegate_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | delegate_modifier_unsafe
    ;
```

Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione di delegato.

Il modificatore `new` è consentito solo su delegati dichiarati all'interno di un altro tipo, nel qual caso specifica che tale delegato nasconde un membro ereditato con lo stesso nome, come descritto nel [nuovo modificatore](classes.md#the-new-modifier).

I modificatori `public`, `protected`, `internal` e `private` controllano l'accessibilità del tipo delegato. A seconda del contesto in cui si verifica la dichiarazione del delegato, alcuni di questi modificatori potrebbero non essere consentiti ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).

Il nome del tipo del delegato è *Identifier*.

Il *formal_parameter_list* facoltativo specifica i parametri del delegato e *return_type* indica il tipo restituito del delegato.

Il *variant_type_parameter_list* facoltativo ([elenchi di parametri di tipo Variant](interfaces.md#variant-type-parameter-lists)) specifica i parametri di tipo per il delegato stesso.

Il tipo restituito di un tipo di delegato deve essere `void` o indipendente dall'output ([sicurezza dalla varianza](interfaces.md#variance-safety)).

Tutti i tipi di parametro formali di un tipo delegato devono essere indipendenti dall'input. Inoltre, tutti i tipi di parametro `out` o `ref` devono essere anche indipendenti dall'output. Si noti che è necessario che anche i parametri `out` siano indipendenti dall'input, a causa di una limitazione della piattaforma di esecuzione sottostante.

I tipi di C# delegati in sono equivalenti al nome e non sono strutturalmente equivalenti. In particolare, due tipi delegati diversi che hanno gli stessi elenchi di parametri e il tipo restituito sono considerati tipi delegati diversi. Tuttavia, le istanze di due tipi delegati distinti ma strutturalmente equivalenti possono essere confrontate come uguali ([operatori di uguaglianza dei delegati](expressions.md#delegate-equality-operators)).

Esempio:

```csharp
delegate int D1(int i, double d);

class A
{
    public static int M1(int a, double b) {...}
}

class B
{
    delegate int D2(int c, double d);
    public static int M1(int f, double g) {...}
    public static void M2(int k, double l) {...}
    public static int M3(int g) {...}
    public static void M4(int g) {...}
}
```

I metodi `A.M1` e `B.M1` sono compatibili con i tipi delegati `D1` e `D2`, perché hanno lo stesso tipo restituito e l'elenco di parametri. Tuttavia, questi tipi delegati sono due tipi diversi, quindi non sono intercambiabili. I metodi `B.M2`, `B.M3` e `B.M4` sono incompatibili con i tipi delegati `D1` e `D2`, poiché presentano tipi restituiti o elenchi di parametri diversi.

Analogamente ad altre dichiarazioni di tipo generico, è necessario assegnare argomenti di tipo per creare un tipo delegato costruito. I tipi di parametro e il tipo restituito di un tipo delegato costruito vengono creati sostituendo, per ogni parametro di tipo nella dichiarazione del delegato, l'argomento di tipo corrispondente del tipo delegato costruito. Il tipo restituito e i tipi di parametro risultanti vengono utilizzati per determinare quali metodi sono compatibili con un tipo delegato costruito. Esempio:

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

Il metodo `X.F` è compatibile con il tipo delegato `Predicate<int>` e il metodo `X.G` è compatibile con il tipo di delegato `Predicate<string>`.

L'unico modo per dichiarare un tipo delegato è tramite un *delegate_declaration*. Un tipo delegato è un tipo di classe derivato da `System.Delegate`. I tipi delegati sono implicitamente `sealed`, pertanto non è consentito derivare alcun tipo da un tipo delegato. Non è inoltre consentito derivare un tipo di classe non delegata da `System.Delegate`. Si noti che `System.Delegate` non è un tipo delegato. si tratta di un tipo di classe da cui vengono derivati tutti i tipi delegati.

C#fornisce una sintassi speciale per la creazione e la creazione di istanze di delegati. Fatta eccezione per la creazione di istanze, qualsiasi operazione che può essere applicata a una classe o a un'istanza di classe può essere applicata rispettivamente a una classe o a un'istanza di delegato. In particolare, è possibile accedere ai membri del tipo `System.Delegate` tramite la consueta sintassi di accesso ai membri.

Il set di metodi incapsulati da un'istanza di delegato è denominato elenco chiamate. Quando viene creata un'istanza del delegato (la compatibilità con i[delegati](delegates.md#delegate-compatibility)) da un singolo metodo, il metodo viene incapsulato e il relativo elenco chiamate contiene una sola voce. Tuttavia, quando vengono combinate due istanze delegate non null, i rispettivi elenchi chiamate vengono concatenati nell'operando sinistro dell'ordine, quindi nell'operando destro, per formare un nuovo elenco chiamate, che contiene due o più voci.

I delegati vengono combinati usando l'operatore binario `+` ([operatore di addizione](expressions.md#addition-operator)) e gli operatori `+=` ([assegnazione composta](expressions.md#compound-assignment)). Un delegato può essere rimosso da una combinazione di delegati, usando il binario `-` ([operatore di sottrazione](expressions.md#subtraction-operator)) e gli operatori `-=` ([assegnazione composta](expressions.md#compound-assignment)). I delegati possono essere confrontati per verificarne[l'uguaglianza.](expressions.md#delegate-equality-operators)

Nell'esempio seguente viene illustrata la creazione di un'istanza di un numero di delegati e gli elenchi chiamate corrispondenti:

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public static void M2(int i) {...}

}

class Test
{
    static void Main() {
        D cd1 = new D(C.M1);      // M1
        D cd2 = new D(C.M2);      // M2
        D cd3 = cd1 + cd2;        // M1 + M2
        D cd4 = cd3 + cd1;        // M1 + M2 + M1
        D cd5 = cd4 + cd3;        // M1 + M2 + M1 + M1 + M2
    }

}
```

Quando vengono create istanze di `cd1` e `cd2`, ognuna incapsula un metodo. Quando viene creata un'istanza di `cd3`, dispone di un elenco chiamate di due metodi, `M1` e `M2`, in questo ordine. l'elenco chiamate di `cd4` contiene `M1`, `M2` e `M1`, in questo ordine. Infine, l'elenco chiamate di `cd5` contiene `M1`, `M2`, `M1`, `M1` e `M2`, in questo ordine. Per altri esempi di combinazione e rimozione di delegati, vedere [chiamata al delegato](delegates.md#delegate-invocation).

## <a name="delegate-compatibility"></a>Compatibilità con i delegati

Un metodo o un delegato `M` è ***compatibile*** con un tipo delegato `D` Se si verificano tutte le condizioni seguenti:

*  `D` e `M` hanno lo stesso numero di parametri e ogni parametro in `D` ha gli stessi modificatori `ref` o `out` come parametro corrispondente in `M`.
*  Per ogni parametro di valore (un parametro senza modificatore `ref` o `out`), esiste una conversione di identità ([conversione di identità](conversions.md#identity-conversion)) o conversione di un riferimento implicito ([conversioni di riferimenti impliciti](conversions.md#implicit-reference-conversions)) dal tipo di parametro in `D` al tipo di parametro corrispondente in `M`.
*  Per ogni parametro `ref` o `out`, il tipo di parametro in `D` corrisponde al tipo di parametro in `M`.
*  Una conversione di identità o di riferimento implicita esiste dal tipo restituito di `M` al tipo restituito di `D`.

## <a name="delegate-instantiation"></a>Creazione di istanze di delegati

Un'istanza di un delegato viene creata da un *delegate_creation_expression* ([espressioni di creazione di delegati](expressions.md#delegate-creation-expressions)) o una conversione in un tipo delegato. L'istanza del delegato appena creata fa quindi riferimento a uno dei seguenti valori:

*  Metodo statico a cui si fa riferimento in *delegate_creation_expression*o
*  L'oggetto di destinazione (che non può essere `null`) e il metodo di istanza a cui si fa riferimento in *delegate_creation_expression*o
*  Un altro delegato.

Esempio:

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public void M2(int i) {...}
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);        // static method
        C t = new C();
        D cd2 = new D(t.M2);        // instance method
        D cd3 = new D(cd2);        // another delegate
    }
}
```

Una volta creata un'istanza, le istanze delegate fanno sempre riferimento allo stesso oggetto e metodo di destinazione. Tenere presente che quando due delegati vengono combinati o uno viene rimosso da un altro, un nuovo delegato restituisce il proprio elenco chiamate; gli elenchi chiamate dei delegati combinati o rimossi restano invariati.

## <a name="delegate-invocation"></a>Chiamata al delegato

C#fornisce una sintassi speciale per richiamare un delegato. Quando un'istanza di delegato non null il cui elenco chiamate contiene una voce viene richiamata, richiama il metodo con gli stessi argomenti che è stato fornito e restituisce lo stesso valore del metodo a cui si fa riferimento. Per informazioni dettagliate sulla chiamata al delegato, vedere [chiamate di delegati](expressions.md#delegate-invocations) . Se si verifica un'eccezione durante la chiamata di un delegato di questo tipo e tale eccezione non viene rilevata all'interno del metodo richiamato, la ricerca di una clausola catch di eccezione continua nel metodo che ha chiamato il delegato, come se il metodo avesse chiamato direttamente il metodo Metodo a cui fa riferimento il delegato.

La chiamata di un'istanza del delegato il cui elenco chiamate contiene più voci continua richiamando ognuno dei metodi nell'elenco chiamate, in modo sincrono, nell'ordine. A ogni metodo chiamato viene passato lo stesso set di argomenti fornito all'istanza del delegato. Se tale chiamata al delegato include parametri di riferimento ([parametri di riferimento](classes.md#reference-parameters)), ogni chiamata al metodo viene eseguita con un riferimento alla stessa variabile. le modifiche apportate a tale variabile da un metodo nell'elenco chiamate saranno visibili ai metodi successivi nell'elenco chiamate. Se la chiamata al delegato include parametri di output o un valore restituito, il relativo valore finale proverrà dalla chiamata dell'ultimo delegato nell'elenco.

Se si verifica un'eccezione durante l'elaborazione della chiamata di un delegato di questo tipo e tale eccezione non viene rilevata all'interno del metodo richiamato, la ricerca di una clausola catch di eccezione continua nel metodo che ha chiamato il delegato ed eventuali metodi successivi l'elenco chiamate non viene richiamato.

Se si tenta di richiamare un'istanza del delegato il cui valore è null, viene generata un'eccezione di tipo `System.NullReferenceException`.

Nell'esempio seguente viene illustrato come creare un'istanza, combinare, rimuovere e richiamare delegati:

```csharp
using System;

delegate void D(int x);

class C
{
    public static void M1(int i) {
        Console.WriteLine("C.M1: " + i);
    }

    public static void M2(int i) {
        Console.WriteLine("C.M2: " + i);
    }

    public void M3(int i) {
        Console.WriteLine("C.M3: " + i);
    }
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);
        cd1(-1);                // call M1

        D cd2 = new D(C.M2);
        cd2(-2);                // call M2

        D cd3 = cd1 + cd2;
        cd3(10);                // call M1 then M2

        cd3 += cd1;
        cd3(20);                // call M1, M2, then M1

        C c = new C();
        D cd4 = new D(c.M3);
        cd3 += cd4;
        cd3(30);                // call M1, M2, M1, then M3

        cd3 -= cd1;             // remove last M1
        cd3(40);                // call M1, M2, then M3

        cd3 -= cd4;
        cd3(50);                // call M1 then M2

        cd3 -= cd2;
        cd3(60);                // call M1

        cd3 -= cd2;             // impossible removal is benign
        cd3(60);                // call M1

        cd3 -= cd1;             // invocation list is empty so cd3 is null

        cd3(70);                // System.NullReferenceException thrown

        cd3 -= cd1;             // impossible removal is benign
    }
}
```

Come illustrato nell'istruzione `cd3 += cd1;`, un delegato può essere presente più volte in un elenco chiamate. In questo caso, viene semplicemente richiamato una volta per ogni occorrenza. In un elenco chiamate come questo, quando il delegato viene rimosso, l'ultima occorrenza nell'elenco chiamate è quella effettivamente rimossa.

Immediatamente prima dell'esecuzione dell'istruzione finale, `cd3 -= cd1;`, il delegato `cd3` fa riferimento a un elenco chiamate vuoto. Il tentativo di rimuovere un delegato da un elenco vuoto (o di rimuovere un delegato non esistente da un elenco non vuoto) non è un errore.

L'output prodotto è:

```console
C.M1: -1
C.M2: -2
C.M1: 10
C.M2: 10
C.M1: 20
C.M2: 20
C.M1: 20
C.M1: 30
C.M2: 30
C.M1: 30
C.M3: 30
C.M1: 40
C.M2: 40
C.M3: 40
C.M1: 50
C.M2: 50
C.M1: 60
C.M1: 60
```
