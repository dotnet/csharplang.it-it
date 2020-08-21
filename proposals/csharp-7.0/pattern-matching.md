---
ms.openlocfilehash: a252f62178cc527c091b35780d750d1e61528c9f
ms.sourcegitcommit: 06ee75e6e3f1e0d9b4859ffed66024364d3d8f26
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720464"
---
# <a name="pattern-matching-for-c-7"></a>Criteri di ricerca per C# 7

Le estensioni di criteri di ricerca per C# consentono molti dei vantaggi dei tipi di dati algebrici e dei criteri di ricerca dai linguaggi funzionali, ma in modo da integrarsi perfettamente con l'aspetto del linguaggio sottostante. Le funzionalità di base sono: i [tipi di record](../csharp-9.0/records.md), ovvero i tipi il cui significato semantico è descritto dalla forma dei dati; e criteri di ricerca, ovvero un nuovo form di espressione che consente la scomposizione multilivello estremamente concisa di questi tipi di dati. Gli elementi di questo approccio sono ispirati dalle funzionalità correlate nei linguaggi di programmazione [F #](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/p29-syme.pdf "Criteri di ricerca estendibili tramite un linguaggio leggero") e [scala](https://infoscience.epfl.ch/record/98468/files/MatchingObjectsWithPatterns-TR.pdf "Corrispondenza di oggetti con modelli").

## <a name="is-expression"></a>Espressione is

L' `is` operatore viene esteso per testare un'espressione rispetto a un *criterio*.

```antlr
relational_expression
    : relational_expression 'is' pattern
    ;
```

Questa forma di *relational_expression* è aggiunta ai moduli esistenti nella specifica C#. Si tratta di un errore in fase di compilazione se il *relational_expression* a sinistra del `is` token non designa un valore o non ha un tipo.

Ogni *identificatore* del modello introduce una nuova variabile locale *assegnata definitivamente* dopo l' `is` operatore `true` (ovvero *assegnata definitivamente quando è true*).

> Nota: esiste tecnicamente un'ambiguità tra il *tipo* in `is-expression` e *constant_pattern*, una delle quali potrebbe essere un'analisi valida di un identificatore qualificato. Si tenta di associarlo come tipo per la compatibilità con le versioni precedenti del linguaggio; solo se l'operazione ha esito negativo, viene risolta come in altri contesti, fino alla prima cosa trovata (che deve essere una costante o un tipo). Questa ambiguità è presente solo sul lato destro di un' `is` espressione.

## <a name="patterns"></a>Modelli

Gli schemi vengono usati nell' `is` operatore e in una *switch_Statement* per esprimere la forma dei dati in base ai quali confrontare i dati in ingresso. I modelli possono essere ricorsivi in modo che sia possibile trovare una corrispondenza tra parti dei dati e modelli secondari.

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    ;

declaration_pattern
    : type simple_designation
    ;

constant_pattern
    : shift_expression
    ;

var_pattern
    : 'var' simple_designation
    ;
```

> Nota: esiste tecnicamente un'ambiguità tra il *tipo* in `is-expression` e *constant_pattern*, una delle quali potrebbe essere un'analisi valida di un identificatore qualificato. Si tenta di associarlo come tipo per la compatibilità con le versioni precedenti del linguaggio; solo se l'operazione ha esito negativo, viene risolta come in altri contesti, fino alla prima cosa trovata (che deve essere una costante o un tipo). Questa ambiguità è presente solo sul lato destro di un' `is` espressione.

### <a name="declaration-pattern"></a>Modello di dichiarazione

Il *declaration_pattern* verifica che un'espressione sia di un tipo specificato e ne esegue il cast a tale tipo se il test ha esito positivo. Se il *simple_designation* è un identificatore, introduce una variabile locale del tipo specificato denominato dall'identificatore specificato. La variabile locale viene *assegnata definitivamente* quando il risultato dell'operazione di corrispondenza dei modelli è true.

```antlr
declaration_pattern
    : type simple_designation
    ;
```

La semantica di runtime di questa espressione è che testa il tipo di runtime dell'operando di sinistra *relational_expression* sul *tipo* nel modello. Se è del tipo di runtime (o di un sottotipo), il risultato di `is operator` è `true` . Dichiara una nuova variabile locale denominata dall' *identificatore* a cui viene assegnato il valore dell'operando sinistro quando il risultato è `true` .

Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione. Un valore di tipo statico `E` viene definito *modello compatibile* con il tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione esplicita di un riferimento o una conversione unboxing da `E` a `T` . Si tratta di un errore in fase di compilazione se un'espressione di tipo `E` non è compatibile con il tipo in un modello di tipo con cui corrisponde.

> Nota: [in C# 7,1 questa](../csharp-7.1/generics-pattern-match.md) operazione viene estesa per consentire un'operazione di corrispondenza dei modelli se il tipo di input o il tipo `T` è un tipo aperto. Questo paragrafo viene sostituito da quanto segue:
> 
> Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione. Un valore di tipo statico `E` viene definito *modello compatibile* con il tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione di riferimento esplicita o una conversione unboxing `E` da `T` a **oppure se `E` o `T` è un tipo aperto**. Si tratta di un errore in fase di compilazione se un'espressione di tipo `E` non è compatibile con il tipo in un modello di tipo con cui corrisponde.

Il modello di dichiarazione è utile per l'esecuzione di test dei tipi di riferimento in fase di esecuzione e sostituisce l'idioma

```csharp
var v = expr as Type;
if (v != null) { // code using v }
```

Con l'aspetto leggermente più conciso

```csharp
if (expr is Type v) { // code using v }
```

Si tratta di un errore se il *tipo* è un tipo di valore Nullable.

Il modello di dichiarazione può essere usato per testare i valori dei tipi nullable: un valore di tipo `Nullable<T>` (o un oggetto boxed `T` ) corrisponde a un modello di tipo `T2 id` se il valore è non null e il tipo di `T2` è `T` o di un tipo o di un'interfaccia di base di `T` . Ad esempio, nel frammento di codice

```csharp
int? x = 3;
if (x is int v) { // code using v }
```

La condizione dell' `if` istruzione è in fase di `true` esecuzione e la variabile `v` include il valore `3` di tipo `int` all'interno del blocco.

### <a name="constant-pattern"></a>Criterio costante

```antlr
constant_pattern
    : shift_expression
    ;
```

Un criterio costante verifica il valore di un'espressione rispetto a un valore costante. La costante può essere qualsiasi espressione costante, ad esempio un valore letterale, il nome di una variabile dichiarata `const` o una costante di enumerazione o un' `typeof` espressione.

Se e *e* *c* sono di tipo integrale, il modello viene considerato corrispondente se il risultato dell'espressione `e == c` è `true` .

In caso contrario, il modello viene considerato corrispondente se `object.Equals(e, c)` restituisce `true` . In questo caso si tratta di un errore in fase di compilazione se il tipo statico di *e* non è *compatibile* con il tipo della costante.

### <a name="var-pattern"></a>Modello var

```antlr
var_pattern
    : 'var' simple_designation
    ;
```

Un'espressione *e* corrisponde a un *var_pattern* sempre. In altre parole, una corrispondenza con un *modello var* ha sempre esito positivo. Se il *simple_designation* è un identificatore, in fase di esecuzione il valore di *e* viene associato a una variabile locale appena introdotta. Il tipo della variabile locale è il tipo statico di *e*.

Si tratta di un errore se il nome viene `var` associato a un tipo.

## <a name="switch-statement"></a>Istruzioni switch

L' `switch` istruzione viene estesa in modo da selezionare per l'esecuzione il primo blocco a cui è associato un modello corrispondente all' *espressione switch*.

```antlr
switch_label
    : 'case' complex_pattern case_guard? ':'
    | 'case' constant_expression case_guard? ':'
    | 'default' ':'
    ;

case_guard
    : 'when' expression
    ;
```

L'ordine in cui vengono confrontati i modelli non è definito. Un compilatore è autorizzato a trovare una corrispondenza con i modelli non ordinati e a riutilizzare i risultati dei modelli già corrispondenti per calcolare il risultato della corrispondenza di altri criteri.

Se è presente un *case-Guard* , l'espressione è di tipo `bool` . Viene valutato come una condizione aggiuntiva che deve essere soddisfatta affinché il case venga considerato soddisfatto.

Se un *switch_label* non può avere alcun effetto in fase di esecuzione, non è un errore perché il relativo modello viene sostituito dai case precedenti. [TODO: è necessario essere più precisi sulle tecniche che il compilatore deve usare per raggiungere questo giudizio.]

Una variabile di pattern dichiarata in un *switch_label* viene assegnata in modo sicuro nel blocco case solo se il blocco case contiene esattamente una *switch_label*.

[TODO: è necessario specificare quando un *blocco switch* è raggiungibile.]

### <a name="scope-of-pattern-variables"></a>Ambito delle variabili del modello

L'ambito di una variabile dichiarata in un modello è il seguente:

- Se il modello è un'etichetta case, l'ambito della variabile è il *blocco case*.

In caso contrario, la variabile viene dichiarata in un'espressione *is_pattern* e il relativo ambito è basato sul costrutto che racchiude immediatamente l'espressione contenente l'espressione *is_pattern* , come indicato di seguito:

- Se l'espressione è in un'espressione lambda con corpo di espressione, il relativo ambito è il corpo dell'espressione lambda.
- Se l'espressione si trova in una proprietà o in un metodo con corpo di espressione, il relativo ambito è il corpo del metodo o della proprietà.
- Se l'espressione si trova in una `when` clausola di una `catch` clausola, l'ambito è tale `catch` clausola.
- Se l'espressione si trova in un *iteration_statement*, il suo ambito è semplicemente tale istruzione.
- In caso contrario, se l'espressione è in un altro formato di istruzione, il relativo ambito è l'ambito che contiene l'istruzione.

Ai fini della determinazione dell'ambito, un *embedded_statement* viene considerato nel proprio ambito. Ad esempio, la grammatica per un *if_Statement* è

``` antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

Quindi, se l'istruzione controllata di un *if_Statement* dichiara una variabile di modello, il suo ambito è limitato a tale *embedded_statement*:

```csharp
if (x) M(y is var z);
```

In questo caso l'ambito di `z` è l'istruzione incorporata `M(y is var z);` .

Altri casi sono errori per altri motivi, ad esempio nel caso di un valore predefinito di un parametro o di un attributo, che sono entrambi un errore, perché tali contesti richiedono un'espressione costante.

> [In C# 7,3 sono stati aggiunti i seguenti contesti](../csharp-7.3/expression-variables-in-initializers.md) in cui è possibile dichiarare una variabile di modello:
> - Se l'espressione si trova in un *inizializzatore di Costruttore*, il relativo ambito è l' *inizializzatore del costruttore* e il corpo del costruttore.
> - Se l'espressione si trova in un inizializzatore di campo, il relativo ambito è il *equals_value_clause* in cui viene visualizzato.
> - Se l'espressione si trova in una clausola di query specificata per essere convertita nel corpo di un'espressione lambda, il suo ambito è semplicemente tale espressione.

## <a name="changes-to-syntactic-disambiguation"></a>Modifiche alla disambiguazione sintattica

Esistono situazioni che coinvolgono generics in cui la grammatica C# è ambigua e la specifica del linguaggio spiega come risolvere le ambiguità seguenti:

> #### <a name="7652-grammar-ambiguities"></a>ambiguità della grammatica 7.6.5.2
> Le produzioni per il *nome semplice* (§ 7.6.3) e l' *accesso ai membri* (§ 7.6.5) possono dare luogo a ambiguità nella grammatica per le espressioni. Ad esempio, l'istruzione:
> ```csharp
> F(G<A,B>(7));
> ```
> può essere interpretato come una chiamata a `F` con due argomenti, `G < A` e `B > (7)` . In alternativa, può essere interpretato come una chiamata a `F` con un argomento, che è una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento normale.

> Se una sequenza di token può essere analizzata (nel contesto) come *nome semplice* (§ 7.6.3), *accesso ai membri* (§ 7.6.5) o *puntatore-membro-Access* (§ 18.5.2) che termina con un *tipo-argument-list* (§ 4.4.1), viene esaminato il token immediatamente successivo al token di chiusura `>` . Se è uno dei
> ```none
> (  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
> ```
> quindi, l' *elenco di argomenti di tipo* viene mantenuto come parte del *nome semplice*, l' *accesso ai membri* o l'accesso ai membri del *puntatore* e qualsiasi altra possibile analisi della sequenza di token viene scartata. In caso contrario, l' *elenco di argomenti di tipo* non viene considerato parte dell'accesso con *nome semplice*, *accesso ai membri* o > *puntatore al membro*, anche se non è possibile analizzare la sequenza di token. Si noti che queste regole non vengono applicate durante l'analisi di un *elenco di argomenti di tipo* in uno *spazio dei nomi o un nome di tipo* (§ 3,8). L'istruzione
> ```csharp
> F(G<A,B>(7));
> ```
> in base a questa regola, sarà interpretato come una chiamata a `F` con un argomento, che è una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento normale. Istruzioni
> ```csharp
> F(G < A, B > 7);
> F(G < A, B >> 7);
> ```
> ogni oggetto verrà interpretato come una chiamata a `F` con due argomenti. L'istruzione
> ```csharp
> x = F < A > +y;
> ```
> verrà interpretato come un operatore minore di, un operatore maggiore di e un operatore unario più, come se l'istruzione fosse stata scritta `x = (F < A) > (+y)` , anziché come *nome semplice* con un *tipo-argument-list* seguito da un operatore Binary Plus. Nell'istruzione
> ```csharp
> x = y is C<T> + z;
> ```
> i token `C<T>` vengono interpretati come uno *spazio dei nomi o un nome di tipo* con un elenco di argomenti di *tipo*.

In C# 7 sono state introdotte alcune modifiche che rendono le regole di risoluzione dell'ambiguità non più sufficienti per gestire la complessità del linguaggio.

### <a name="out-variable-declarations"></a>Dichiarazioni di variabili out

È ora possibile dichiarare una variabile in un argomento out:

```csharp
M(out Type name);
```

Tuttavia, il tipo può essere generico: 

```csharp
M(out A<B> name);
```

Poiché la grammatica del linguaggio per l'argomento utilizza *Expression*, questo contesto è soggetto alla regola di risoluzione dell'ambiguità. In questo caso, la chiusura `>` è seguita da un *identificatore*, che non è uno dei token che consente di considerarlo come un elenco di *argomenti di tipo*. Viene pertanto proposto di **aggiungere *identificatore* al set di token che attiva la risoluzione dell'ambiguità in un *tipo-argument-list*.**

### <a name="tuples-and-deconstruction-declarations"></a>Tuple e dichiarazioni di decostruzione

Un valore letterale di tupla viene eseguito esattamente nello stesso problema. Si consideri l'espressione di tupla

```csharp
(A < B, C > D, E < F, G > H)
```

Con le precedenti regole C# 6 per l'analisi di un elenco di argomenti, questo verrebbe analizzato come una tupla con quattro elementi, a partire dal `A < B` primo. Tuttavia, quando viene visualizzato a sinistra di una decostruzione, si vuole che la risoluzione dell'ambiguità venga attivata dal token dell' *identificatore* , come descritto in precedenza:

```csharp
(A<B,C> D, E<F,G> H) = e;
```

Si tratta di una dichiarazione di decostruzione che dichiara due variabili, la prima delle quali è di tipo `A<B,C>` e denominata `D` . In altre parole, il valore letterale di tupla contiene due espressioni, ciascuna delle quali è un'espressione di dichiarazione.

Per semplicità della specifica e del compilatore, propongo che questo valore letterale di tupla venga analizzato come tupla con due elementi ovunque venga visualizzato (indipendentemente dal fatto che venga visualizzato sul lato sinistro di un'assegnazione). Si tratta di un risultato naturale della risoluzione dell'ambiguità descritta nella sezione precedente.

### <a name="pattern-matching"></a>Criteri di ricerca

Criteri di ricerca introduce un nuovo contesto in cui si verifica l'ambiguità del tipo di espressione. In precedenza, il lato destro di un `is` operatore era un tipo. A questo punto può essere un tipo o un'espressione e, se è un tipo, può essere seguito da un identificatore. Questo può, tecnicamente, modificare il significato del codice esistente:

```csharp
var x = e is T < A > B;
```

Questa operazione può essere analizzata in base alle regole di C# 6 come

```csharp
var x = ((e is T) < A) > B;
```

Tuttavia, in base alle regole di C# 7 (con la risoluzione dell'ambiguità proposta in precedenza) verrebbe analizzata come

```csharp
var x = e is T<A> B;
```

che dichiara una variabile `B` di tipo `T<A>` . Fortunatamente, i compilatori nativi e Roslyn hanno un bug in cui forniscono un errore di sintassi sul codice C# 6. Questa particolare modifica di rilievo non costituisce pertanto un problema.

La corrispondenza dei modelli introduce token aggiuntivi che dovrebbero guidare la risoluzione dell'ambiguità verso la selezione di un tipo. Gli esempi seguenti di codice C# 6 valido esistente verrebbero interrotti senza regole di risoluzione dell'ambiguità aggiuntive:

```csharp
var x = e is A<B> && f;            // &&
var x = e is A<B> || f;            // ||
var x = e is A<B> & f;             // &
var x = e is A<B>[];               // [
```

### <a name="proposed-change-to-the-disambiguation-rule"></a>Modifica proposta alla regola di risoluzione dell'ambiguità

Suggerisco di rivedere la specifica per modificare l'elenco di token distinguere da

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```

su

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [
```

E, in determinati contesti, consideriamo l' *identificatore* come token distinguere. Questi contesti sono i casi in cui la sequenza di token da ambiguità è immediatamente preceduta da una delle parole chiave `is` , o o si `case` `out` verifica durante l'analisi del primo elemento di un valore letterale di tupla (nel qual caso i token sono preceduti da `(` o `:` e l'identificatore è seguito da un `,` ) o da un elemento successivo di un valore letterale di tupla.

### <a name="modified-disambiguation-rule"></a>Regola di risoluzione dell'ambiguità modificata

La regola di risoluzione dell'ambiguità riveduta sarà simile alla seguente

> Se una sequenza di token può essere analizzata (nel contesto) come *nome semplice* (§ 7.6.3), *accesso ai membri* (§ 7.6.5) o *puntatore-membro-Access* (§ 18.5.2) che termina con un *tipo-argument-list* (§ 4.4.1), il token immediatamente dopo il `>` token di chiusura viene esaminato, per verificare se è
> - Uno tra `(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [` ; o
> - Uno degli operatori relazionali `<  >  <=  >=  is as` ; oppure
> - Parola chiave di query contestuale visualizzata all'interno di un'espressione di query. o
> - In determinati contesti consideriamo l' *identificatore* come token distinguere. Questi contesti sono i casi in cui la sequenza di token ambiguità è immediatamente preceduta da una delle parole chiave `is` , `case` o `out` , o si verifica durante l'analisi del primo elemento di un valore letterale di tupla (nel qual caso i token sono preceduti da `(` o `:` e l'identificatore è seguito da un `,` ) o da un elemento successivo di un valore letterale di tupla.
> 
> Se il token seguente è incluso in questo elenco oppure un identificatore in un contesto di questo tipo, il *tipo-argument-list* viene mantenuto come parte del *nome semplice*, l' *accesso ai membri* o l'  *accesso ai membri del puntatore* e qualsiasi altra possibile analisi della sequenza di token viene scartata.  In caso contrario, l' *elenco di argomenti di tipo* non viene considerato parte dell'accesso con *nome semplice*, *accesso ai membri* o *puntatore-membro*, anche se non è possibile analizzare la sequenza di token. Si noti che queste regole non vengono applicate durante l'analisi di un *elenco di argomenti di tipo* in uno *spazio dei nomi o un nome di tipo* (§ 3,8).

### <a name="breaking-changes-due-to-this-proposal"></a>Modifiche di rilievo dovute a questa proposta

Nessuna modifica di rilievo è nota a causa di questa regola di risoluzione dell'ambiguità proposta.

### <a name="interesting-examples"></a>Esempi interessanti

Ecco alcuni risultati interessanti di queste regole di risoluzione dell'ambiguità:

L'espressione `(A < B, C > D)` è una tupla con due elementi, ognuno dei quali è un confronto.

L'espressione `(A<B,C> D, E)` è una tupla con due elementi, il primo dei quali è un'espressione di dichiarazione.

La chiamata `M(A < B, C > D, E)` ha tre argomenti.

La chiamata `M(out A<B,C> D, E)` ha due argomenti, il primo dei quali è una `out` dichiarazione.

L'espressione `e is A<B> C` utilizza un'espressione di dichiarazione.

L'etichetta case `case A<B> C:` Usa un'espressione di dichiarazione.

## <a name="some-examples-of-pattern-matching"></a>Alcuni esempi di criteri di ricerca

### <a name="is-as"></a>Is-As

È possibile sostituire l'idioma

```csharp
var v = expr as Type;   
if (v != null) {
    // code using v
}
```

Con la forma leggermente più concisa e diretta

```csharp
if (expr is Type v) {
    // code using v
}
```

### <a name="testing-nullable"></a>Test Nullable

È possibile sostituire l'idioma

```csharp
Type? v = x?.y?.z;
if (v.HasValue) {
    var value = v.GetValueOrDefault();
    // code using value
}
```

Con la forma leggermente più concisa e diretta

```csharp
if (x?.y?.z is Type value) {
    // code using value
}
```

### <a name="arithmetic-simplification"></a>Semplificazione aritmetica

Si supponga di definire un set di tipi ricorsivi per rappresentare le espressioni (per una proposta separata):

```csharp
abstract class Expr;
class X() : Expr;
class Const(double Value) : Expr;
class Add(Expr Left, Expr Right) : Expr;
class Mult(Expr Left, Expr Right) : Expr;
class Neg(Expr Value) : Expr;
```

A questo punto è possibile definire una funzione per calcolare la derivata (non ridotta) di un'espressione:

```csharp
Expr Deriv(Expr e)
{
  switch (e) {
    case X(): return Const(1);
    case Const(*): return Const(0);
    case Add(var Left, var Right):
      return Add(Deriv(Left), Deriv(Right));
    case Mult(var Left, var Right):
      return Add(Mult(Deriv(Left), Right), Mult(Left, Deriv(Right)));
    case Neg(var Value):
      return Neg(Deriv(Value));
  }
}
```

Un semplificatore di espressioni illustra i modelli posizionali:

```csharp
Expr Simplify(Expr e)
{
  switch (e) {
    case Mult(Const(0), *): return Const(0);
    case Mult(*, Const(0)): return Const(0);
    case Mult(Const(1), var x): return Simplify(x);
    case Mult(var x, Const(1)): return Simplify(x);
    case Mult(Const(var l), Const(var r)): return Const(l*r);
    case Add(Const(0), var x): return Simplify(x);
    case Add(var x, Const(0)): return Simplify(x);
    case Add(Const(var l), Const(var r)): return Const(l+r);
    case Neg(Const(var k)): return Const(-k);
    default: return e;
  }
}
```
