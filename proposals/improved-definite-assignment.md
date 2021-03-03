---
ms.openlocfilehash: 44980f4dadfb80a5ef1fccb09ef6490f80a12c5c
ms.sourcegitcommit: 3f8f57e294e2952af19a5231e6b9c7ff85d0ed56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101751673"
---
# <a name="improved-definite-assignment-analysis"></a>Analisi di assegnazione definita migliorata

## <a name="summary"></a>Riepilogo
L' [analisi di assegnazione definita](../spec/variables.md#definite-assignment) come specificato presenta alcuni gap che hanno causato l'inconveniente degli utenti. In particolare, scenari che coinvolgono il confronto con costanti booleane, accesso condizionale e Unione di valori null.

## <a name="related-discussions-and-issues"></a>Discussioni e problemi correlati
csharplang discussione di questa proposta: https://github.com/dotnet/csharplang/discussions/4240

Probabilmente è possibile trovare una decina di report per gli utenti tramite questa o query simili, ovvero cercare "assegnazione definita" invece di "CS0165" o cercare in csharplang.
https://github.com/dotnet/roslyn/issues?q=is%3Aclosed+is%3Aissue+label%3A%22Resolution-By+Design%22+cs0165

Sono stati inclusi problemi correlati negli scenari indicati di seguito per dare un'idea dell'effetto relativo di ogni scenario.

## <a name="scenarios"></a>Scenari

Come punto di riferimento, iniziamo con una nota "felice caso" che funziona in un'assegnazione definita e ammette i valori null.
```cs
#nullable enable

C c = new C();
if (c != null && c.M(out object obj0))
{
    obj0.ToString(); // ok
}

public class C
{
    public bool M(out object obj)
    {
        obj = new object();
        return true;
    }
}
```

### <a name="comparison-to-bool-constant"></a>Confronto con costante bool
- https://github.com/dotnet/csharplang/discussions/801
- https://github.com/dotnet/roslyn/issues/45582
  - Collegamenti a altri quattro problemi per cui gli utenti sono interessati.
```cs
if ((c != null && c.M(out object obj1)) == true)
{
    obj1.ToString(); // undesired error
}

if ((c != null && c.M(out object obj2)) is true)
{
    obj2.ToString(); // undesired error
}
```

### <a name="comparison-between-a-conditional-access-and-a-constant-value"></a>Confronto tra un accesso condizionale e un valore costante

- https://github.com/dotnet/roslyn/issues/33559
- https://github.com/dotnet/csharplang/discussions/4214
- https://github.com/dotnet/csharplang/issues/3659
- https://github.com/dotnet/csharplang/issues/3485
- https://github.com/dotnet/csharplang/issues/3659

Questo scenario è probabilmente quello più grande. Questa operazione è supportata in Nullable ma non nell'assegnazione definitiva.

```cs
if (c?.M(out object obj3) == true)
{
    obj3.ToString(); // undesired error
}
```

### <a name="conditional-access-coalesced-to-a-bool-constant"></a>Accesso condizionale Unito a una costante bool

- https://github.com/dotnet/csharplang/discussions/916
- https://github.com/dotnet/csharplang/issues/3365

Questo scenario è molto simile a quello precedente. Questa operazione è supportata anche in Nullable ma non nell'assegnazione definita.

```cs
if (c?.M(out object obj4) ?? false)
{
    obj4.ToString(); // undesired error
}
```

### <a name="conditional-expressions-where-one-arm-is-a-bool-constant"></a>Espressioni condizionali in cui un ARM è una costante bool
- https://github.com/dotnet/roslyn/issues/4272

Vale la pena sottolineare che è già presente un comportamento speciale quando l'espressione della condizione è costante (ad esempio `true ? a : b` ). Si visita solo in modo non condizionale il ARM indicato dalla condizione costante e si ignora l'altro ARM.

Si noti inoltre che questo scenario non è stato gestito in Nullable.

```cs
if (c != null ? c.M(out object obj4) : false)
{
    obj4.ToString(); // undesired error
}
```

# <a name="specification"></a>Specifiche

## <a name="-null-conditional-operator-expressions"></a>?. espressioni (operatore condizionale null)
Viene introdotta una nuova sezione **?. espressioni (operatore condizionale null)**. Per il contesto, vedere la specifica dell' [operatore condizionale null](../spec/expressions.md#null-conditional-operator) e [le regole di assegnazione definite](../spec/variables.md#precise-rules-for-determining-definite-assignment) .

Come per le regole di assegnazione definite sopra indicate, si fa riferimento a una variabile inizialmente non assegnata come *v*.

Viene introdotto il concetto di "Contains direttamente". Un'espressione *e* viene detto "contiene direttamente" una sottoespressione *e <sub>1</sub>* se si verifica una delle condizioni seguenti:
- *E* è *e <sub>1</sub>*. Ad esempio, `a?.b()` contiene direttamente l'espressione `a?.b()` .
- Se *e* è un'espressione tra parentesi `(E2)` e e *<sub>2</sub>* contengono direttamente *e <sub>1</sub>*.
- Se *e* è un'espressione di operatore con indulgenza null `E2!` e e *<sub>2</sub>* contengono direttamente *e <sub>1</sub>*.

Per un'espressione *e* del form `primary_expression null_conditional_operations` , lasciare che *e <sub>0</sub>* sia l'espressione ottenuta rimuovendo in modo testuale il leader? da ogni *null_conditional_operations* di *E* che ne hanno uno, come nella specifica collegata sopra.

Nelle sezioni successive si fa riferimento a *E <sub>0</sub>* come *controparte non condizionale* per l'espressione condizionale null. Si noti che alcune espressioni nelle sezioni successive sono soggette a regole aggiuntive che si applicano solo quando uno degli operandi contiene direttamente un'espressione condizionale null.

- Lo stato di assegnazione definito di *v* in qualsiasi punto all'interno di *E* corrisponde allo stato di assegnazione definito nel punto corrispondente all'interno di *E0*.
- Lo stato di assegnazione definito di *v* dopo *E* corrisponde allo stato di assegnazione definito di *v* dopo *primary_expression*.

### <a name="remarks"></a>Commenti
Viene usato il concetto di "Contains" direttamente per consentire di ignorare espressioni "wrapper" relativamente semplici durante l'analisi degli accessi condizionali confrontati con altri valori. Si prevede, ad esempio, che `((a?.b(out x))!) == true` si verifichi lo stesso stato del flusso `a?.b == true` in generale.

Quando si considera la possibilità di assegnare una variabile in un punto specifico all'interno di un'espressione condizionale null, si presuppone che tutte le operazioni precedenti condizionali null all'interno della stessa espressione condizionale null abbiano avuto esito positivo.

Ad esempio, data un'espressione condizionale `a?.b(out x)?.c(x)` , la controparte non condizionale è `a.b(out x).c(x)` . Se si vuole ottenere lo stato di assegnazione definito di `x` prima `?.c(x)` , ad esempio, si esegue un'analisi ipotetica di e si `a.b(out x)` Usa lo stato risultante come input per `?.c(x)` .

## <a name="boolean-constant-expressions"></a>Espressioni costanti booleane
Viene introdotta una nuova sezione "espressioni costanti booleane":

Per un'espressione *expr* in cui *expr* è un'espressione costante con un valore bool:
- Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
  - Se *expr* è un'espressione costante con valore *true* e lo stato di *v* prima di *expr* è "non assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely unsigned when false".
  - Se *expr* è un'espressione costante con valore *false* e lo stato di *v* prima di *expr* è "non assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely Assigned when true".

### <a name="remarks"></a>Commenti

Si presuppone che se un'espressione ha un valore costante bool `false` , ad esempio, è Impossibile raggiungere qualsiasi ramo che richiede che l'espressione restituisca `true` . Pertanto si presuppone che le variabili siano assegnate in modo sicuro in tali rami. Questa operazione si integra perfettamente con le modifiche delle specifiche per le espressioni, ad esempio `??` e `?:` , e consentendo numerosi scenari utili.

È anche importante notare che non ci si aspetta mai di trovarsi in uno stato condizionale *prima* di visitare un'espressione costante. Questo è il motivo per cui non si tiene conto di scenari quali "*expr* è un'espressione costante con valore *true* e lo stato di *v* prima di *expr* è" assegnato definitivamente se true ".

## <a name="-null-coalescing-expressions-augment"></a>?? (espressioni con Unione null) potenziare
La sezione è stata ampliata [? espressioni (Unione di valori null)](../spec/variables.md#-null-coalescing-expressions) come indicato di seguito:

Per un'espressione *expr* nel formato `expr_first ?? expr_second` :
- ...
- Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
  - ...
  - Se *expr_first* contiene direttamente *un'espressione condizionale null e e* *v* viene assegnato definitivamente dopo la controparte non condizionale *e <sub>0</sub>*, lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo la *expr_second*.

### <a name="remarks"></a>Commenti
La regola precedente formalizza che per un'espressione come `a?.M(out x) ?? (x = false)` , `a?.M(out x)` è stato completamente valutato e ha prodotto un valore non null, nel qual caso `x` è stato assegnato oppure `x = false` è stato valutato, nel qual caso `x` è stato assegnato anche. Pertanto `x` viene sempre assegnato dopo questa espressione.

Questa operazione gestisce anche lo `dict?.TryGetValue(key, out var value) ?? false` scenario, osservando che la *versione di v* viene assegnata in modo definitivo dopo `dict.TryGetValue(key, out var value)` e *v* è "definitivamente assegnata quando true" dopo `false` e concludendo che *v* deve essere "assegnato definitivamente quando è true".

La formulazione più generale consente inoltre di gestire alcuni scenari più insoliti, ad esempio:
- `if (x?.M(out y) ?? (b && z.M(out y))) y.ToString();`
- `if (x?.M(out y) ?? z?.M(out y) ?? false) y.ToString();`

## <a name="-conditional-expressions"></a>espressioni?: (condizionale)
Sono state aggiunte le [**espressioni (condizionali)**](../spec/variables.md#-conditional-expressions) della sezione come segue:

Per un'espressione *expr* nel formato `expr_cond ? expr_true : expr_false` :
- ...
- Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
  - ...
  - Se lo stato di *v* dopo *expr_true* è "assegnato definitivamente quando è true" e lo stato di *v* dopo che *expr_false* è "assegnato definitivamente quando true", lo stato di *v* dopo *expr* è "definitely assegnata quando true".
  - Se lo stato di *v* dopo *expr_true* è "assegnato definitivamente quando è false" e lo stato di *v* dopo che *expr_false* è "assegnato definitivamente quando false", lo stato di *v* dopo *expr* è "definitely unsigned when false".

### <a name="remarks"></a>Commenti

In questo modo, quando entrambi i bracci di un'espressione condizionale generano uno stato condizionale, si uniscono gli Stati condizionali corrispondenti e lo si propaga invece di separare lo stato e consentire lo stato finale come non condizionale. Questo consente scenari come i seguenti:

```cs
bool b = true;
object x = null;
int y;
if (b ? x != null && Set(out y) : x != null && Set(out y))
{
  y.ToString();
}

bool Set(out int x) { x = 0; return true; }
```

Si tratta di uno scenario di nicchia che si compila senza errori nel compilatore nativo, ma è stato suddiviso in Roslyn per poter corrispondere alla specifica al momento. Vedere il [problema interno](http://vstfdevdiv:8080/DevDiv2/DevDiv/_workitems/edit/529603).

## <a name="-relational-equality-operator-expressions"></a>espressioni = =/! = (operatore di uguaglianza relazionale)
Vengono introdotte una nuova **espressione Section = =/! = (operatore di uguaglianza relazionale)**.

Si applicano le [regole generali per le espressioni con espressioni incorporate](../spec/variables.md#general-rules-for-expressions-with-embedded-expressions) , ad eccezione degli scenari descritti di seguito.

Per un'espressione *expr* del form `expr_first == expr_second` , dove `==` è un [operatore di confronto predefinito](../spec/expressions.md#relational-and-type-testing-operators) o un [operatore lifted](../spec/expressions.md#lifted-operators), lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
  - Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione costante con valore *null* e lo stato *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitivamente assegnato quando false".
  - Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione di un tipo di valore non nullable oppure un'espressione costante con un valore non null e lo stato di *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely Assigned when true".
  - Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *true*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito dopo *expr_first*.
  - Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *false*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo l'espressione di negazione logica `!expr_first` .

Per un'espressione *expr* del form `expr_first != expr_second` , dove `!=` è un [operatore di confronto predefinito](../spec/expressions.md#relational-and-type-testing-operators) o un [operatore lifted](../spec/expressions.md#lifted-operators), lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
  - Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione costante con valore *null* e lo stato *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely Assigned when true".
  - Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione di un tipo di valore non nullable oppure un'espressione costante con un valore non null e lo stato di *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely unsigned when false".
  - Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *true*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo l'espressione di negazione logica `!expr_first` .
  - Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *false*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito dopo *expr_first*.

Tutte le regole descritte in questa sezione sono commutative, vale a dire che se una regola viene applicata quando viene valutata nel modulo `expr_second op expr_first` , viene applicata anche nel formato `expr_first op expr_second` .

### <a name="remarks"></a>Commenti
L'idea generale espressa da queste regole è la seguente:
- Se un accesso condizionale viene confrontato con `null` , si sa che le operazioni si sono verificate definitivamente se il risultato del confronto è `false`
- Se un accesso condizionale viene confrontato con un tipo di valore non nullable o una costante non null, si sa che le operazioni si sono verificate definitivamente se il risultato del confronto è `true` .
- Poiché non è possibile considerare attendibili gli operatori definiti dall'utente per fornire risposte affidabili in cui è interessata la sicurezza dell'inizializzazione, le nuove regole si applicano solo quando un `==` / `!=` operatore predefinito è in uso.

È possibile che si desideri perfezionare queste regole in thread tramite lo stato condizionale presente alla fine di un accesso o di una chiamata a un membro. Questi scenari non si verificano realmente nell'assegnazione definitiva, ma si verificano in modo nullable in presenza di `[NotNullWhen(true)]` e attributi simili. Questa operazione richiede una gestione speciale per le `bool` costanti oltre alla semplice gestione delle `null` costanti/non-null.

Di seguito sono riportate alcune conseguenze di queste regole:
- `if (a?.b(out var x) == true)) x() else x();` errore nel ramo ' else '
- `if (a?.b(out var x) == 42)) x() else x();` errore nel ramo ' else '
- `if (a?.b(out var x) == false)) x() else x();` errore nel ramo ' else '
- `if (a?.b(out var x) == null)) x() else x();` si errore nel ramo ' then '
- `if (a?.b(out var x) != true)) x() else x();` si errore nel ramo ' then '
- `if (a?.b(out var x) != 42)) x() else x();` si errore nel ramo ' then '
- `if (a?.b(out var x) != false)) x() else x();` si errore nel ramo ' then '
- `if (a?.b(out var x) != null)) x() else x();` errore nel ramo ' else '

## <a name="is-operator-and-is-pattern-expressions"></a>`is` espressioni di operatore e `is` modello
Vengono introdotte una nuova sezione **`is` operatore e `is` espressioni di criteri**.

Per un'espressione *expr* del form `E is T` , dove *T* è qualsiasi tipo o modello
- Lo stato di assegnazione definito di *v* prima di *E* è lo stesso dello stato di assegnazione definito di *v* prima di *expr*.
- Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
  - Se *e* contiene direttamente un'espressione condizionale null e lo stato di *v* dopo la controparte non condizionale *e <sub>0</sub>* è "assegnato definitivamente" ed `T` è di qualsiasi tipo o di un modello che corrisponde solo a un input non null, lo stato di *v* dopo *expr* è "definitivamente assegnato se true".
  - Se *e* contiene direttamente un'espressione condizionale null e lo stato *v* dopo la controparte non condizionale *e <sub>0</sub>* è "assegnato definitivamente" ed `T` è un modello che corrisponde solo a un input null, lo stato di *v* dopo *expr* è "definitely unsigned when false".
  - Se *e* è di tipo booleano ed `T` è il criterio costante `true` , lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo E.
  - Se *e* è di tipo booleano ed `T` è il criterio costante `false` , lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo l'espressione di negazione logica `!expr` .
  - In caso contrario, se lo stato di assegnazione definito di *v* dopo E è "definitivamente assegnato", lo stato di assegnazione definito di *v* dopo *expr* sarà "assegnato definitivamente".

### <a name="remarks"></a>Commenti

Questa sezione è destinata a scenari simili, come nella `==` / `!=` sezione precedente.
Questa specifica non risolve i modelli ricorsivi, ad esempio `(a?.b(out x), c?.d(out y)) is (object, object)` . Questo supporto può rientrare in un secondo momento, se consentito.

# <a name="additional-scenarios"></a>Scenari aggiuntivi

Questa specifica non è attualmente indirizzata a scenari che coinvolgono espressioni di cambio di modello e istruzioni switch. Ad esempio:

```cs
_ = c?.M(out object obj4) switch
{
    not null => obj4.ToString() // undesired error
};
```

Sembra che il supporto per questa operazione potrebbe rientrare in un secondo momento, se consentito.

Sono state rilevate diverse categorie di bug per i valori null, che richiedono essenzialmente una maggiore complessità dell'analisi dei modelli. È probabile che qualsiasi decisione che aumenti l'assegnazione definitiva venga trasferita anche a Nullable.

https://github.com/dotnet/roslyn/issues/49353  
https://github.com/dotnet/roslyn/issues/46819  
https://github.com/dotnet/roslyn/issues/44127

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Sembra strano che l'analisi sia "raggiungibile" e abbia un riconoscimento speciale degli accessi condizionali, quando lo stato di analisi del flusso dovrebbe essere propagato in alto. Ci occupiamo di come una soluzione come questa possa intersecarsi in modo doloroso con possibili funzionalità del linguaggio future che eseguono controlli null.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Due alternative a questa proposta:
1. Introdurre "state when null" e "state when not null" per il linguaggio e il compilatore. Questa operazione è stata giudicata come un lavoro troppo impegnativo per gli scenari che si sta tentando di risolvere, ma è possibile implementare la proposta precedente e quindi passare a un modello "state when null/not null" in un secondo momento senza causare interruzioni.
2. Non eseguire alcuna operazione.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

Ci sono effetti sulle espressioni switch che è necessario specificare: https://github.com/dotnet/csharplang/discussions/4240#discussioncomment-343395

## <a name="design-meetings"></a>Riunioni di progettazione

https://github.com/dotnet/csharplang/discussions/4243
