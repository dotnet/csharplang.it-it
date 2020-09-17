---
ms.openlocfilehash: e3027c3fe9b05d3e5379535c010e148e4fbbc761
ms.sourcegitcommit: 2661a4b3950d2ec5c557b025af13e52ba200fd05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90687175"
---
# <a name="pattern-matching-changes-for-c-90"></a>Modifiche ai criteri di ricerca per C# 9,0

Si sta considerando un piccolo numero di miglioramenti apportati alla corrispondenza dei modelli per C# 9,0 che hanno una sinergia naturale e funzionano bene per risolvere una serie di problemi comuni di programmazione:
- https://github.com/dotnet/csharplang/issues/2925 Modelli di tipo
- https://github.com/dotnet/csharplang/issues/1350 Modelli tra parentesi per applicare o evidenziare la precedenza dei nuovi combinatori
- https://github.com/dotnet/csharplang/issues/1350`and`Modelli congiuntiva che richiedono la corrispondenza di due modelli diversi;
- https://github.com/dotnet/csharplang/issues/1350`or`Modelli disgiuntiva che richiedono uno dei due modelli diversi per la corrispondenza.
- https://github.com/dotnet/csharplang/issues/1350 Modelli negati `not` che richiedono un modello specificato di *non* corrispondenza; e
- https://github.com/dotnet/csharplang/issues/812 Modelli relazionali che richiedono che il valore di input sia minore di, minore o uguale a e così via, una costante specificata.

## <a name="parenthesized-patterns"></a>Modelli tra parentesi

I modelli tra parentesi consentono al programmatore di inserire parentesi intorno a qualsiasi modello.  Questa operazione non è così utile con i modelli esistenti in C# 8,0, tuttavia i nuovi combinatori di modelli introducono la precedenza che il programmatore potrebbe voler sottoporre a override.

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

Si consente un tipo come modello:

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

Questo retcons l'oggetto *is-Type-Expression* esistente come oggetto *is-pattern-Expression* , in cui il modello è un *modello di tipo*, anche se non si modifica l'albero della sintassi prodotto dal compilatore.

Un problema di implementazione sottile è che questa grammatica è ambigua.  Una stringa come `a.b` può essere analizzata come nome completo (in un contesto di tipo) o come espressione tratteggiata (in un contesto di espressione).  Il compilatore è già in grado di trattare un nome qualificato allo stesso modo di un'espressione punteggiata per gestire qualcosa di simile a `e is Color.Red` .  L'analisi semantica del compilatore viene estesa ulteriormente per essere in grado di associare un criterio costante (sintattico), ad esempio un'espressione tratteggiata, come un tipo, in modo da considerarlo come modello di tipo associato per supportare questo costrutto.

Dopo questa modifica, sarà possibile scrivere
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

I modelli relazionali consentono al programmatore di esprimere che un valore di input deve soddisfare un vincolo relazionale rispetto a un valore costante:

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

I modelli relazionali supportano gli operatori relazionali `<` ,, `<=` `>` e `>=` su tutti i tipi incorporati che supportano tali operatori relazionali binari con due operandi dello stesso tipo in un'espressione. In particolare, sono supportati tutti questi modelli relazionali per `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` , `nint` e `nuint` .

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

L'espressione è obbligatoria per restituire un valore costante.  Si tratta di un errore se il valore costante è `double.NaN` o `float.NaN` .  Si tratta di un errore se l'espressione è una costante null.

Quando l'input è un tipo per cui viene definito un operatore relazionale binario appropriato, applicabile all'input come operando sinistro e la costante specificata come operando destro, la valutazione di tale operatore viene considerata come il significato del modello relazionale.  In caso contrario, l'input viene convertito nel tipo dell'espressione utilizzando una conversione esplicita che ammette i valori null o unboxing.  Si tratta di un errore in fase di compilazione se non esiste alcuna conversione di questo tipo.  Il modello viene considerato non corrispondente se la conversione non riesce.  Se la conversione ha esito positivo, il risultato dell'operazione di corrispondenza dei criteri è il risultato della valutazione dell'espressione in `e OP v` cui `e` è l'input convertito, `OP` è l'operatore relazionale e `v` è l'espressione costante.

## <a name="pattern-combinators"></a>Combinatori di modelli

I *combinatori* di pattern consentono di abbinare due modelli diversi usando `and` (questo può essere esteso a un numero qualsiasi di modelli mediante l'uso ripetuto di `and` ), uno dei due modelli diversi usando `or` (Ditto) o la *negazione* di un modello usando `not` .

Un uso comune di un combinatore sarà l'idioma

``` c#
if (e is not null) ...
```

Più leggibile dell'idioma corrente `e is object` , questo modello esprime chiaramente che è in corso la verifica di un valore non null.

I `and` `or` combinatori e saranno utili per il test degli intervalli di valori

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

Questo esempio illustra che `and` avrà una priorità di analisi più elevata (ad esempio, verrà associata più a vicino) rispetto a `or` .  Il programmatore può usare il *modello racchiuso tra parentesi* per rendere esplicita la precedenza:

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

Come tutti i modelli, questi combinatori possono essere usati in qualsiasi contesto in cui è previsto un modello, inclusi i modelli annidati, *is-pattern-Expression*, *Switch-Expression*e il modello dell'etichetta case di un'istruzione switch.

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

## <a name="change-to-7542-grammar-ambiguities"></a>Modificare le ambiguità della grammatica 7.5.4.2

A causa dell'introduzione del *modello di tipo*, è possibile che un tipo generico venga visualizzato prima del token `=>` .  Si aggiunge quindi `=>` al set di token elencati nelle *ambiguità della grammatica 7.5.4.2* per consentire la risoluzione dell'ambiguità dell'oggetto `<` che inizia l'elenco di argomenti di tipo.  Vedere anche https://github.com/dotnet/roslyn/issues/47614.

## <a name="open-issues-with-proposed-changes"></a>Problemi aperti con le modifiche proposte

### <a name="syntax-for-relational-operators"></a>Sintassi per gli operatori relazionali

Sono `and` , `or` e `not` un tipo di parola chiave contestuale?  In tal caso, è presente una modifica di rilievo, ad esempio rispetto al loro utilizzo come indicatore in un *modello di dichiarazione*.

### <a name="semantics-eg-type-for-relational-operators"></a>Semantica (ad esempio, tipo) per gli operatori relazionali

Si prevede di supportare tutti i tipi primitivi che possono essere confrontati in un'espressione utilizzando un operatore relazionale.  Il significato nei casi semplici è chiaro

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

Tuttavia, quando l'input non è un tipo primitivo, a quale tipo si tenta di convertirlo?

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

Si è proposto che quando il tipo di input è già una primitiva paragonabile, ovvero il tipo di confronto. Tuttavia, quando l'input non è una primitiva paragonabile, consideriamo il tipo relazionale come incluso un test di tipo implicito al tipo della costante sul lato destro della relazione.  Se il programmatore intende supportare più di un tipo di input, è necessario eseguire questa operazione in modo esplicito:

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a>Flusso delle informazioni sul tipo da sinistra a destra di `and`

È stato suggerito che, quando si scrive un `and` combinatore, le informazioni sul tipo acquisite a sinistra sul tipo di primo livello possono propagarsi a destra.  Ad esempio

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

In questo caso, il *tipo di input* per il secondo modello viene ridotto in base ai requisiti di *riduzione del tipo* a sinistra di `and` .  Si definisce la semantica di riduzione dei tipi per tutti i modelli come indicato di seguito.  Il *tipo limitato* di un modello `P` viene definito nel modo seguente:
1. Se `P` è un modello di tipo, il tipo *limitato* è il tipo del tipo del modello di tipo.
2. Se `P` è un modello di dichiarazione, il tipo *limitato* è il tipo del tipo del modello di dichiarazione.
3. Se `P` è un modello ricorsivo che fornisce un tipo esplicito, il tipo *limitato* è quel tipo.
4. Se `P` per viene eseguita la [corrispondenza con le regole per](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-8.0/patterns.md#positional-pattern) `ITuple` , il tipo *limitato* è il tipo `System.Runtime.CompilerServices.ITuple` .
5. Se `P` è un criterio costante in cui la costante non è la costante null e in cui l'espressione non dispone di una *conversione di espressione costante* nel *tipo di input*, il *tipo limitato* è il tipo della costante.
6. Se `P` è un modello relazionale in cui l'espressione costante non ha una *conversione dell'espressione costante* nel *tipo di input*, il *tipo limitato* è il tipo della costante.
7. Se `P` è un `or` modello, il tipo *limitato* è il tipo comune del *tipo ristretto* dei criteri di ricerca se tale tipo comune esiste. A questo scopo, l'algoritmo di tipo comune considera solo le conversioni di identità, Boxing e riferimento implicito e considera tutti i sottomodelli di una sequenza di `or` modelli (ignorando i modelli racchiusi tra parentesi).
8. Se `P` è un `and` modello, il *tipo ristretto* è il *tipo limitato* del criterio destro. Inoltre, il *tipo limitato* del criterio di sinistra è il *tipo di input* del criterio destro.
9. In caso *narrowed type* contrario, il tipo di `P` input di è limitato `P` .

### <a name="variable-definitions-and-definite-assignment"></a>Definizioni di variabili e assegnazione definitiva

L'aggiunta di `or` `not` modelli e consente di creare alcuni nuovi problemi interessanti sulle variabili del modello e sull'assegnazione definita.  Poiché le variabili possono in genere essere dichiarate al massimo una volta, sembrerebbe che qualsiasi variabile di pattern dichiarata su un lato di un `or` modello non venisse assegnata definitivamente quando il modello corrisponde.  Analogamente, una variabile dichiarata all'interno di un `not` modello non dovrebbe essere assegnata definitivamente quando il modello corrisponde a.  Il modo più semplice per risolvere questo problema è impedire la dichiarazione di variabili di modello in questi contesti.  Tuttavia, questa operazione potrebbe essere troppo restrittiva.  Ci sono altri approcci da considerare.

Uno scenario che vale la pena considerare è il seguente

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

Questa operazione non funziona oggi perché, per un *is-pattern-Expression*, le variabili del modello vengono considerate assegnate in modo *sicuro* solo quando l'oggetto *is-pattern-Expression* è true ("assegnato definitivamente se true").

Il supporto di questo metodo sarebbe più semplice (dal punto di vista del programmatore) rispetto all'aggiunta del supporto per un'istruzione di condizione negata `if` .  Anche se si aggiunge tale supporto, i programmatori chiederanno perché il frammento di codice precedente non funziona.  D'altra parte, lo stesso scenario in a `switch` è meno appropriato, in quanto non esiste un punto corrispondente nel programma in cui l' *assegnazione è assegnata quando false* sarebbe significativa.  Questa operazione è consentita in un *modello is-pattern-Expression,* ma non in altri contesti in cui sono consentiti i modelli?  Sembra irregolare.

Correlato a questo è il problema dell'assegnazione definita in un *modello disgiuntiva*.

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

Si prevede che `i` venga assegnato solo se l'input è diverso da zero.  Tuttavia, poiché non è noto se l'input è zero o non è presente nel blocco, `i` non viene assegnato definitivamente.  Tuttavia, cosa accade se si concede la `i` dichiarazione in modelli diversi che si escludono a vicenda?

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

In questo caso la variabile `i` viene assegnata in modo sicuro all'interno del blocco e accetta il valore dall'altro elemento della tupla quando viene trovato un elemento zero.

È stato inoltre suggerito di consentire le variabili da (moltiplicare) definite in ogni caso di un blocco case:

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

Per eseguire una di queste operazioni, è necessario definire con attenzione il punto in cui sono consentite tali definizioni e in quali condizioni tale variabile viene considerata definitivamente assegnata.

Se decidiamo di rinviare tale lavoro fino a un secondo momento (che Consiglio), potremmo dire in C# 9
- sotto un oggetto `not` o `or` , le variabili del modello non possono essere dichiarate.

Quindi, avremmo tempo per sviluppare una certa esperienza che forniva informazioni sul possibile valore di relax che in un secondo momento.

### <a name="diagnostics-subsumption-and-exhaustiveness"></a>Diagnostica, sussunzione e esaustività

Questi nuovi moduli presentano molte nuove opportunità per l'errore del programmatore diagnosticabile.  È necessario decidere quali tipi di errori si eseguiranno per la diagnosi e come farlo.  Ecco alcuni esempi:

``` csharp
case >= 0 and <= 100D:
```

Questo caso non può mai corrispondere (perché l'input non può essere sia un `int` che un `double` ).  Si è già verificato un errore quando viene rilevato un caso che non può mai corrispondere, ma la relativa formulazione ("il caso di cambio è già stato gestito da un case precedente" e "il modello è già stato gestito da un ARM precedente dell'espressione switch") può essere fuorviante nei nuovi scenari.  Potrebbe essere necessario modificare la formulazione per indicare semplicemente che il modello non corrisponderà mai all'input.

``` csharp
case 1 and 2:
```

Analogamente, si tratta di un errore perché un valore non può essere sia `1` che `2` .

``` csharp
case 1 or 2 or 3 or 1:
```

Questo caso è possibile per trovare una corrispondenza, ma la alla `or 1` fine non aggiunge alcun significato al modello.  Si consiglia di generare un errore ogni volta che alcuni congiunzione delle o disgiunta di uno schema composto non definiscono una variabile di pattern o influiscono sul set di valori corrispondenti.

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

Qui, `0 or 1 or` non aggiunge nulla al secondo caso, perché tali valori sarebbero stati gestiti dal primo caso.  Questa operazione merita anche un errore.

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

Un'espressione switch come questa deve essere considerata *esaustiva* (gestisce tutti i possibili valori di input).

In C# 8,0, un'espressione switch con un input di tipo `byte` è considerata esaustiva solo se contiene un ARM finale il cui modello corrisponde a tutti gli elementi (uno *scarto* o un *modello var*).  Anche un'espressione switch con ARM per ogni valore distinto `byte` non è considerata esaustiva in C# 8.  Per gestire correttamente la completezza dei modelli relazionali, sarà necessario gestire anche questo caso.  Tecnicamente si tratta di una modifica sostanziale, ma non è probabile che nessun utente si accorga.
