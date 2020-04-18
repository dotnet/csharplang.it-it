---
ms.openlocfilehash: 57cdb682efd4cb169308e347d63e27c97b9f1b7a
ms.sourcegitcommit: 6901635c383801e4d177085587aaccadaa7b2f11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641976"
---
# <a name="pattern-matching-changes-for-c-90"></a>Modifiche di criteri di ricerca per c'è 9.0Pattern-matching changes for C's 9.0

Stiamo prendendo in considerazione una piccola manciata di miglioramenti alla corrispondenza dei modelli per la versione 9.0 di C, che presentano una sinergia naturale e funzionano bene per risolvere una serie di problemi di programmazione comuni:We are considering a small handful of enhancements to pattern-matching for C '9.0 that have natural synergy and work well to address a number of common programming problems:
- https://github.com/dotnet/csharplang/issues/2925Modelli di tipo
- https://github.com/dotnet/csharplang/issues/1350Modelli tra parentesi per applicare o enfatizzare la precedenza dei nuovi combinatori
- https://github.com/dotnet/csharplang/issues/1350Modelli congiuntivi `and` che richiedono entrambi i modelli diversi per corrispondere;
- https://github.com/dotnet/csharplang/issues/1350Modelli disgiuntivi `or` che richiedono due modelli diversi da abbinare;
- https://github.com/dotnet/csharplang/issues/1350Modelli `not` negati che richiedono un determinato modello *che non* corrispondono; E
- https://github.com/dotnet/csharplang/issues/812Modelli relazionali che richiedono che il valore di input sia minore, minore o uguale a e così via a una determinata costante.

## <a name="parenthesized-patterns"></a>Modelli tra parentesi

I motivi tra parentesi consentono al programmatore di inserire le parentesi intorno a qualsiasi modello.  Ciò non è così utile con i modelli esistenti in C . 8.0, tuttavia i nuovi combinatori di modelli introducono una precedenza che il programmatore potrebbe voler eseguire l'override.

```antlr
primary_pattern
    : parenthesized_pattern
    | // all of the existing forms
    ;
parenthesized_pattern
    : '(' pattern ')'
    ;
```

## <a name="type-patterns"></a>Modelli di tipo

Permettiamo un tipo come modello:

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

In questo modo l'espressione di *tipo* is esistente è *un'is-pattern-expression* in cui il pattern è un *pattern di tipo*, anche se non si modificherebbe l'albero della sintassi prodotto dal compilatore.

Un sottile problema di implementazione è che questa grammatica è ambigua.  Una stringa `a.b` come può essere analizzata come un nome completo (in un contesto di tipo) o un'espressione punteggiata (in un contesto di espressione).  Il compilatore è già in grado di trattare un nome completo `e is Color.Red`come un'espressione punteggiata per gestire un nome simile a .  L'analisi semantica del compilatore verrebbe ulteriormente estesa per essere in grado di associare un modello costante (sintattico) (ad esempio un'espressione punteggiata) come un tipo per considerarlo come un modello di tipo associato per supportare questo costrutto.

Dopo questa modifica, si sarebbe in grado di scrivere
```csharp
void M(object o1, object o2)
{
    var t = (o1, o2);
    if (t is (int, string)) {} // test if o1 is an int and o2 is a string
    switch (o1) {
        case int: break; // test if o1 is an int
        case System.String: break; // test if o1 is a string
    }
}
```

## <a name="relational-patterns"></a>Modelli relazionali

I modelli relazionali consentono al programmatore di esprimere che un valore di input deve soddisfare un vincolo relazionale rispetto a un valore costante:Relational patterns permit the programmer to express that an input value must satisfy a relational constraint when compared to a constant value:

``` C#
    public static LifeStage LifeStageAtAge(int age) => age switch
    {
        < 0 =>  LifeStage.Prenatal,
        < 2 =>  LifeStage.Infant,
        < 4 =>  LifeStage.Toddler,
        < 6 =>  LifeStage.EarlyChild,
        < 12 => LifeStage.MiddleChild,
        < 20 => LifeStage.Adolescent,
        < 40 => LifeStage.EarlyAdult,
        < 65 => LifeStage.MiddleAdult,
        _ =>    LifeStage.LateAdult,
    };
```

I modelli relazionali `<` `<=`supportano gli operatori relazionali , , `>`e `>=` su tutti i tipi incorporati che supportano tali operatori relazionali binari con due operandi dello stesso tipo in un'espressione. In particolare, supportiamo tutti `sbyte`questi `byte` `short`modelli `ushort`relazionali per `char` `float`, `double` `decimal`, `nint`, `nuint`, `int` `uint` `long`, `ulong`, , , , , , , e .

```antlr
primary_pattern
    : relational_pattern
    ;
relational_pattern
    : '<' relational_expression
    | '<=' relational_expression
    | '>' relational_expression
    | '>=' relational_expression
    ;
```

L'espressione è necessaria per restituire un valore costante.  È un errore se tale `double.NaN` `float.NaN`valore costante è o .  Si tratta di un errore se l'espressione è una costante null.

Quando l'input è un tipo per il quale viene definito un operatore relazionale binario incorporato adatto che è applicabile con l'input come operando sinistro e la costante specificata come operando destro, la valutazione di tale operatore viene considerata come significato del modello relazionale.  In caso contrario, convertiamo l'input nel tipo dell'espressione usando una conversione unboxable o unboxing esplicita.  Si tratta di un errore in fase di compilazione se tale conversione non esiste.  Il modello è considerato non corrispondente se la conversione non riesce.  Se la conversione ha esito positivo, il risultato dell'operazione di corrispondenza dei modelli è il risultato della valutazione dell'espressione `e OP v` in cui `e` è l'input convertito, `OP` è l'operatore relazionale ed `v` è l'espressione costante.

## <a name="pattern-combinators"></a>Combinatori modello

I *combinatori* di modelli consentono `and` di abbinare entrambi i diversi modelli utilizzando `and`(questo può essere `or` esteso a qualsiasi numero di modelli mediante l'uso ripetuto di ), uno dei due modelli diversi utilizzando (ditto) o la *negazione* di un modello utilizzando `not`.

Un uso comune di un combinatore sarà l'idioma

``` c#
if (e is not null) ...
```

Più leggibile dell'idioma `e is object`corrente, questo modello esprime chiaramente che si sta verificando un valore non null.

I `and` `or` combinatori e saranno utili per testare intervalli di valori

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

In questo esempio `and` viene illustrato che avrà una priorità di analisi più `or`alta (ad esempio verrà associata più strettamente) rispetto a .  Il programmatore può utilizzare il *modello tra parentesi* per rendere esplicita la precedenza:

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

Come tutti i modelli, questi combinatori possono essere utilizzati in qualsiasi contesto in cui è previsto un modello, inclusi i modelli annidati, *l'espressione is-pattern*, l'espressione *switch*e il modello dell'etichetta case di un'istruzione switch.

```antlr
pattern
    : disjunctive_pattern
    ;
disjunctive_pattern
    : disjunctive_pattern 'or' conjunctive_pattern
    | conjunctive_pattern
    ;
conjunctive_pattern
    : conjunctive_pattern 'and' negated_pattern
    | negated_pattern
    ;
negated_pattern
    : 'not' negated_pattern
    | primary_pattern
    ;
primary_pattern
    : // all of the patterns forms previously defined
    ;
```

## <a name="open-issues-with-proposed-changes"></a>Problemi aperti con le modifiche proposte

### <a name="syntax-for-relational-operators"></a>Sintassi per gli operatori relazionali

Sono `and` `or`, `not` e qualche tipo di parola chiave contestuale?  In caso affermativo, vi è una modifica sostanziale (ad esempio rispetto al loro utilizzo come designatore in un *modello di dichiarazione*).

### <a name="semantics-eg-type-for-relational-operators"></a>Semantica (ad esempio tipo) per gli operatori relazionali

Prevediamo di supportare tutti i tipi primitivi che possono essere confrontati in un'espressione utilizzando un operatore relazionale.  Il significato in casi semplici è chiaro

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

Ma quando l'input non è un tipo così primitivo, che tipo cerchiamo di convertirlo?

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

Abbiamo proposto che quando il tipo di input è già una primitiva comparabile, questo è il tipo di confronto. Tuttavia, quando l'input non è una primitiva comparabile, trattiamo l'relazionale come incluso un test di tipo implicito per il tipo della costante sul lato destro dell'relazionale.  Se il programmatore intende supportare più di un tipo di input, è necessario eseguire questa operazione in modo esplicito:If the programmer intends to support more than one input type, that must be done explicitly:

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a>Informazioni sul tipo scorrevoli da sinistra a destra di`and`

È stato suggerito che quando `and` si scrive un combinatore, le informazioni sul tipo apprese a sinistra sul tipo di primo livello potrebbero fluire verso destra.  Ad esempio:

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

In questo caso, il tipo di *input* al secondo modello viene `and`ristretto dai requisiti di *restringimento* del tipo di sinistra dell'oggetto .  È possibile definire la semantica di restringimento dei tipi per tutti i modelli come indicato di seguito.  Il *tipo ristretto* `P` di un modello è definito come segue:
1. Se `P` è un modello di tipo, il *tipo ristretto* è il tipo del tipo del modello di tipo.
2. Se `P` è un modello di dichiarazione, il *tipo ristretto* è il tipo del tipo del modello di dichiarazione.
3. Se `P` è un modello ricorsivo che fornisce un tipo esplicito, il *tipo ristretto* è quel tipo.
4. Se `P` è un modello costante in cui la costante non è la costante null e in cui l'espressione non dispone di alcuna *conversione* di espressione costante nel *tipo di input*, il tipo *ristretto* è il tipo della costante.
5. Se `P` è un modello relazionale in cui l'espressione costante non dispone di *alcuna conversione dell'espressione costante* nel tipo di *input,* il *tipo ristretto* è il tipo della costante.
6. Se `P` è `or` un modello, il *tipo ristretto* è il tipo comune del *tipo ristretto* dei sottomodelli se esiste un tipo comune di questo tipo. A tale scopo, l'algoritmo di tipo comune considera solo le conversioni di identità, `or` boxing e riferimenti implicite e considera tutti i sottomodelli di una sequenza di modelli (ignorando i modelli tra parentesi).
7. Se `P` è `and` un modello, il *tipo ristretto* è il *tipo ristretto* del modello destro. Inoltre, il *tipo ristretto* del pattern sinistro è il tipo di *input* del modello destro.
8. In caso contrario, `P`il *tipo ristretto* di è 's tipo di `P` input.

### <a name="variable-definitions-and-definite-assignment"></a>Definizioni delle variabili e assegnazione definita

L'aggiunta `or` `not` di e modelli crea alcuni interessanti nuovi problemi relativi alle variabili di modello e all'assegnazione definita.  Poiché le variabili possono in genere essere dichiarate al massimo `or` una volta, sembrerebbe che qualsiasi variabile di modello dichiarata su un lato di un modello non venga assegnata in modo definito quando il modello corrisponde.  Analogamente, una variabile `not` dichiarata all'interno di un modello non dovrebbe essere assegnata in modo definito quando il modello corrisponde.  Il modo più semplice per risolvere questo problema consiste nel proibire la dichiarazione di variabili di pattern in questi contesti.  Tuttavia, questo può essere troppo restrittivo.  Ci sono altri approcci da considerare.

Uno scenario che vale la pena considerare è questo

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

Questo non funziona oggi perché, per *un'espressione è-pattern*, le variabili di modello vengono considerate *assegnate in modo definito* solo dove l'is-pattern-expression è true ("definitely assigned when true"). *is-pattern-expression*

A supporto di questo sarebbe più semplice (dal punto di vista `if` del programmatore) che aggiungere anche il supporto per un'istruzione di condizione negata.  Anche se aggiungiamo tale supporto, i programmatori si chiederebbero perché lo snippet di cui sopra non funziona.  D'altra parte, lo stesso `switch` scenario in un ha meno senso, in quanto non esiste un punto corrispondente nel programma in cui *assegnata in modo definito quando false* sarebbe significativo.  Lo consentiremmo in *un'espressione is-pattern,* ma non in altri contesti in cui i modelli sono consentiti?  Sembra irregolare.

Correlato a questo è il problema dell'assegnazione definita in un *modello disgiuntivo*.

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

Ci si `i` aspetterebbe di essere assegnato in modo definito solo quando l'ingresso non è zero.  Ma dal momento che non sappiamo se l'ingresso `i` è zero o meno all'interno del blocco, non è assegnato in modo definito.  Tuttavia, cosa `i` succede se permettiamo di essere dichiarati in diversi modelli che si escludono a vicenda?

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

In questo `i` caso, la variabile viene assegnata in modo definito all'interno del blocco e accetta il valore dall'altro elemento della tupla quando viene trovato un elemento zero.

È stato anche suggerito di consentire alle variabili di essere definite (moltiplicazione) in ogni caso di un blocco di maiuscole e minuscole:

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

Per fare qualsiasi di questo lavoro, dovremmo definire attentamente dove tali definizioni multiple sono consentite e a quali condizioni tale variabile è considerata assegnata in modo definito.

Dovremmo scegliere di rinviare tale lavoro fino a dopo (che vi consiglio), potremmo dire in C
- sotto `not` a `or`o , le variabili di pattern non possono essere dichiarate.

Poi, avremmo il tempo di sviluppare un'esperienza che fornisca informazioni sul possibile valore di rilassarsi in seguito.

### <a name="diagnostics-subsumption-and-exhaustiveness"></a>Diagnostica, suntitudine ed esaustività

Queste nuove forme di modello introducono molte nuove opportunità per l'errore del programmatore diagnosable.  Dovremo decidere quali tipi di errori diagnosticare e come farlo.  Di seguito sono riportati alcuni esempi:

``` csharp
case >= 0 and <= 100D:
```

Questo caso non può mai corrispondere (perché l'input non può essere sia un e `int` un `double`).  Abbiamo già un errore quando rileviamo un caso che non può mai corrispondere, ma la sua formulazione ("Il caso switch è già stato gestito da un caso precedente" e "Il modello è già stato gestito da un braccio precedente dell'espressione switch") può essere fuorviante in nuovi scenari.  Potremmo dover modificare la formulazione per dire solo che il modello non corrisponderà mai all'input.

``` csharp
case 1 and 2:
```

Analogamente, si tratterebbe di un `1` errore `2`perché un valore non può essere entrambi e .

``` csharp
case 1 or 2 or 3 or 1:
```

Questo caso è possibile trovare `or 1` una corrispondenza, ma alla fine non aggiunge alcun significato al modello.  Suggerisco di mirare a produrre un errore ogni volta che qualche congiunzione o disgiunzione di un modello composto non definisce una variabile di modello né influisce sul set di valori corrispondenti.

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

In `0 or 1 or` questo caso, non aggiunge nulla al secondo caso, in quanto tali valori sarebbero stati gestiti dal primo caso.  Anche questo merita un errore.

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

Un'espressione switch come questa deve essere considerata *esaustiva* (gestisce tutti i valori di input possibili).

Nella versione 8.0 di C, un'espressione switch con un input di tipo `byte` viene considerata completa solo se contiene un braccio finale il cui modello corrisponde a tutto (un *discard-pattern* o *var-pattern*).  Anche un'espressione switch che dispone `byte` di un braccio per ogni valore distinto non è considerata esaustiva in C .  Al fine di gestire correttamente l'esaurimento dei modelli relazionali, dovremo gestire anche questo caso.  Tecnicamente si tratterà di un cambiamento di rilievo, ma nessun utente è probabile che se ne accorga.
