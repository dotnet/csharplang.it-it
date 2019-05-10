---
ms.openlocfilehash: 08c14d9ef2afe30580f456995066c141653ede92
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488977"
---
# <a name="delegates"></a>Delegati

I delegati consentono scenari che gli altri linguaggi, ad esempio C++, la convenzione Pascal e Modula - siano risolti con i puntatori a funzione. A differenza dei puntatori a funzione di C++, tuttavia, i delegati sono completamente orientata agli oggetti e a differenza dei puntatori di C++ per le funzioni membro, i delegati incapsulare un'istanza dell'oggetto e un metodo.

Una dichiarazione di delegato definisce una classe che deriva dalla classe `System.Delegate`. Un'istanza del delegato incapsula un elenco di chiamate, ovvero un elenco di uno o più metodi, ognuno dei quali è detta entità richiamabile. Ad esempio metodi, entità richiamabile costituito da un'istanza e un metodo su quell'istanza. Per i metodi statici, entità richiamabile è costituito semplicemente da un metodo. Richiamo di un'istanza del delegato con un set appropriato di argomenti, ognuna delle entità del delegato da richiamare con il set specificato di argomenti.

Una proprietà interessante e utile di un'istanza del delegato è che non conoscono o interessano le classi dei metodi che incapsula; Ciò che conta è che questi metodi devono essere compatibili ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con il tipo del delegato. In questo modo i delegati ideali per la chiamata "anonymous".

## <a name="delegate-declarations"></a>Dichiarazioni delegate

Oggetto *delegate_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo tipo di delegato.

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

È un errore in fase di compilazione per lo stesso modificatore venga visualizzato più volte in una dichiarazione di delegato.

Il `new` modificatore è consentito solo su delegati dichiarati all'interno di un altro tipo, nel qual caso specifica che un delegato nasconde un membro ereditato con lo stesso nome, come descritto in [il nuovo modificatore](classes.md#the-new-modifier).

Il `public`, `protected`, `internal`, e `private` modificatori determinano l'accessibilità del tipo delegato. A seconda del contesto in cui si verifica nella dichiarazione del delegato, alcuni di questi modificatori potrebbe non essere consentito ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).

Nome del tipo del delegato è *identificatore*.

L'opzione facoltativa *formal_parameter_list* specifica i parametri del delegato, e *return_type* indica il tipo restituito del delegato.

L'opzione facoltativa *variant_type_parameter_list* ([elenchi di parametri di tipo variante](interfaces.md#variant-type-parameter-lists)) specifica i parametri di tipo per il delegato.

Deve essere il tipo restituito di un tipo delegato `void`, o output-safe ([safety varianza](interfaces.md#variance-safety)).

Tutti i tipi di parametri formali di un tipo delegato devono essere di input-safe. Inoltre, qualsiasi `out` o `ref` i tipi di parametro devono essere anche output-safe. Si noti che anche `out` parametri devono essere di input-safe, a causa di una limitazione della piattaforma sottostante di esecuzione.

I tipi di delegati in c# sono il nome equivalenti, non è strutturalmente equivalente. In particolare, due tipi di delegati diversi che hanno lo stesso parametro vengono elencate e restituire tipo sono considerati tipi delegati diversi. Tuttavia, le istanze di due tipi di delegati strutturalmente equivalenti, ma distinti possono risultare uguali ([delegare gli operatori di uguaglianza](expressions.md#delegate-equality-operators)).

Ad esempio:

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

I metodi `A.M1` e `B.M1 `compatibili con entrambi i tipi di delegati `D1` e `D2` , dal momento che hanno lo stesso restituisce il tipo e l'elenco parametri, tuttavia, questi tipi di delegati sono due tipi diversi, in modo che non siano intercambiabili. I metodi `B.M2`, `B.M3`, e `B.M4` non sono compatibili con i tipi delegato `D1` e `D2`, dal momento che presentano tipi restituiti diversi o elenchi di parametri.

Come altre dichiarazioni di tipo generico, gli argomenti di tipo devono essere concessa a creare un tipo delegato costruito. I tipi di parametro e tipo restituito di un tipo costruito delegato vengono creati tramite la sostituzione, per ogni parametro di tipo nella dichiarazione del delegato, l'argomento di tipo corrispondente del tipo delegato costruito. Il tipo di risultato restituito e i tipi di parametro vengono usati per determinare quali metodi sono compatibili con un tipo delegate costruiti. Ad esempio:

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

Il metodo `X.F` è compatibile con il tipo di delegato `Predicate<int>` e il metodo `X.G` compatibile con il tipo di delegato `Predicate<string>` .

L'unico modo per dichiarare un tipo delegato è tramite un *delegate_declaration*. Un tipo delegato è un tipo di classe che deriva da `System.Delegate`. I tipi delegati sono implicitamente `sealed`, pertanto non è possibile derivare un tipo da un tipo delegato. Non è inoltre possibile derivare un tipo di classe non delegato da `System.Delegate`. Si noti che `System.Delegate` è non stesso tipo di un delegato, ovvero un tipo di classe da cui derivano tutti i tipi di delegato.

C# offre una sintassi speciale per delegato la creazione di istanze e la chiamata. Ad eccezione di creazione dell'istanza, qualsiasi operazione che può essere applicato a una classe o istanza della classe anche è applicabile a una classe delegata o un'istanza, rispettivamente. In particolare, è possibile accedere ai membri del `System.Delegate` tipo tramite la sintassi di accesso membro normale.

Il set di metodi incapsulate da un'istanza del delegato viene chiamato un elenco di chiamate. Quando viene creata un'istanza di delegato ([compatibilità dei delegati](delegates.md#delegate-compatibility)) da un singolo metodo, incapsula tale metodo e il relativo elenco chiamate contiene solo una voce. Tuttavia, quando vengono combinate due istanze dei delegati non null, nei relativi elenchi chiamate vengono concatenati, nell'ordine operando di sinistra quello destro, in modo da formare un nuovo elenco di chiamate, che contiene due o più voci.

Si combinano delegati usando il file binario `+` ([operatore di addizione](expressions.md#addition-operator)) e `+=` operatori ([assegnazione composta](expressions.md#compound-assignment)). Un delegato può essere rimosso da una combinazione di delegati, utilizzando il file binario `-` ([operatore di sottrazione](expressions.md#subtraction-operator)) e `-=` operatori ([assegnazione composta](expressions.md#compound-assignment)). I delegati possono essere confrontati per verificarne l'uguaglianza ([delegare gli operatori di uguaglianza](expressions.md#delegate-equality-operators)).

L'esempio seguente illustra la creazione di istanze di un numero di delegati e relativo elenco chiamate:

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

Quando `cd1` e `cd2` vengono create istanze, ognuna di esse incapsula un metodo. Quando `cd3` viene creata un'istanza, presenta un elenco di chiamate dei due metodi `M1` e `M2`, in questo ordine. `cd4`dell'elenco chiamate contiene `M1`, `M2`, e `M1`, in questo ordine. Infine `cd5`dell'elenco chiamate contiene `M1`, `M2`, `M1`, `M1`, e `M2`, in questo ordine. Per altri esempi di combinazione di delegati (e di rimozione), vedere [chiamata a un delegato](delegates.md#delegate-invocation).

## <a name="delegate-compatibility"></a>Compatibilità dei delegati

Un metodo o delegato `M` viene ***compatibili*** con un tipo delegato `D` se tutte le operazioni seguenti sono vere:

*  `D` e `M` hanno lo stesso numero di parametri e ogni parametro nel `D` ha lo stesso `ref` oppure `out` modificatori del parametro corrispondente nel `M`.
*  Per ogni parametro di valore (un parametro senza alcun `ref` oppure `out` modificatore), una conversione di identità ([conversione di identità](conversions.md#identity-conversion)) o la conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) dal tipo di parametro in cui è presente `D` al tipo di parametro corrispondente nel `M`.
*  Per ognuno `ref` oppure `out` parametro, digitare il parametro `D` corrisponde al tipo di parametro in `M`.
*  Esiste un identità o una conversione implicita del riferimento dal tipo restituito di `M` per il tipo restituito di `D`.

## <a name="delegate-instantiation"></a>Istanze di delegati

Viene creata un'istanza di un delegato da un *delegate_creation_expression* ([delegare le espressioni di creazione](expressions.md#delegate-creation-expressions)) o una conversione in un tipo delegato. L'istanza del delegato appena creato fa quindi riferimento a:

*  Il metodo statico a cui fa riferimento il *delegate_creation_expression*, o
*  L'oggetto di destinazione (non può essere presente `null`) e l'istanza di metodo a cui fa riferimento il *delegate_creation_expression*, o
*  Un altro delegato.

Ad esempio:

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

Una volta creata un'istanza, le istanze dei delegati sempre fare riferimento al stesso oggetto di destinazione e metodo. Tenere presente che, quando si combinano due delegati o uno viene rimosso da un altro, un nuovo delegato ha come risultato con il proprio elenco di chiamate. gli elenchi chiamate dei delegati combinati o rimosso rimangono invariati.

## <a name="delegate-invocation"></a>Chiamata del delegato

C# offre una sintassi speciale per richiamare un delegato. Quando viene richiamata un'istanza del delegato non null il cui elenco chiamate contiene una voce, richiama il metodo con gli stessi argomenti è stato specificato e restituisce lo stesso valore di cui si fa riferimento al metodo. (Vedere [delegare le chiamate](expressions.md#delegate-invocations) per informazioni dettagliate sulla chiamata del delegato.) Se si verifica un'eccezione durante la chiamata di tale delegato e tale eccezione non rilevata all'interno del metodo richiamato, la ricerca di una clausola catch delle eccezioni continua nel metodo che ha chiamato il delegato, come se tale metodo avesse chiamato direttamente il metodo a cui il delegato è noto.

Chiamata di un'istanza del delegato il cui elenco chiamate contiene più voci procede da richiamare in modo sincrono, ciascuno dei metodi nell'elenco chiamate, in ordine. Ogni metodo chiamato in tal modo viene passato lo stesso set di argomenti è stato assegnato all'istanza del delegato. Se tale chiamata di delegato include i parametri per riferimento ([facciano riferimento ai parametri](classes.md#reference-parameters)), si verificherà ogni chiamata al metodo con un riferimento alla stessa variabile; le modifiche apportate alla variabile da un metodo nell'elenco chiamate verranno visibile nei metodi successivi verso il basso l'elenco chiamate. Se la chiamata di delegato include parametri di output o valore restituito, il valore finale dipenderà dalla chiamata del delegato ultimo nell'elenco.

Se si verifica un'eccezione durante l'elaborazione della chiamata di tale delegato e tale eccezione non rilevata all'interno del metodo che è stato richiamato, continua la ricerca di una clausola catch delle eccezioni nel metodo che ha chiamato il delegato e i metodi più avanti elenco di chiamate non vengono richiamati.

Tentativo di richiamare un'istanza di delegato il cui valore è null genera un'eccezione di tipo `System.NullReferenceException`.

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

Come illustrato nell'istruzione `cd3 += cd1;`, un delegato può essere presente in un elenco di chiamate più volte. In questo caso, verrà richiamato solo una volta per ogni occorrenza. In un elenco di chiamate come questo, quando viene rimosso il delegato, l'ultima occorrenza nell'elenco chiamate è quello effettivamente rimosso.

Immediatamente prima dell'esecuzione dell'istruzione finale `cd3 -= cd1;`, il delegato `cd3` fa riferimento a un elenco di chiamate vuote. Il tentativo di rimuovere un delegato da un elenco vuoto (o per rimuovere un delegato non esistente da un elenco non vuoto) non è un errore.

L'output generato è:

```
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
