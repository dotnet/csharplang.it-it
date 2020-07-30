---
ms.openlocfilehash: 9712631e9f0a70d9a08624d0aa2053d8bcca525c
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87440758"
---
# <a name="nullable-constructor-analysis"></a>Analisi del Costruttore Nullable

Questa proposta è destinata a risolvere alcuni dei problemi in sospeso con l'analisi dei costruttori Nullable.

## <a name="how-it-works-currently"></a>Funzionamento attuale

L'analisi Nullable dei costruttori funziona essenzialmente eseguendo un passaggio di assegnazione definito e segnalando un avviso se un costruttore non Inizializza un membro del tipo di riferimento non Nullable (ad esempio, un campo, una proprietà automatica o un evento simile a un campo) in tutti i percorsi del codice. Il costruttore viene altrimenti considerato come un metodo ordinario a scopo di analisi. Questo approccio presenta alcuni problemi.

Il primo è che lo stato del flusso iniziale dei membri non è accurato:

```cs
public class C
{
    public string Prop { get; set; }
    public C() // we get no "uninitialized member" warning, as expected
    {
        Prop.ToString(); // unexpectedly, there is no "possible null receiver" warning here
        Prop = "";
    }
}
```

Un altro è che le istruzioni Asserts/' Throw ' non impediscono gli avvisi di inizializzazione del campo:

```cs
public class C
{
    public string Prop { get; set; }
    public C() // unexpected warning: 'Prop' is not initialized
    {
        Init();

        if (Prop is null)
        {
            throw new Exception();
        }
    }

    void Init()
    {
        Prop = "some default";
    }
}
```

## <a name="an-alternative-approach"></a>Un approccio alternativo

È possibile risolvere questo problema adottando invece un approccio simile a `[MemberNotNull]` Analysis, in cui i campi sono contrassegnati come "null" e viene visualizzato un avviso se si esce dal metodo quando un campo è ancora in uno stato Maybe-null. È possibile eseguire questa operazione introducendo le regole seguenti:

**Un costruttore su un tipo di riferimento senza inizializzatore** *o*  
**Un costruttore in un tipo di riferimento con un `: base(...)` inizializzatore** ha uno stato di flusso Nullable iniziale determinato da:
- Inizializzazione dei membri del tipo di base sullo stato dichiarato, poiché si prevede che il costruttore di base Inizializza i membri di base.
- Inizializzando quindi tutti i membri applicabili del tipo sullo stato assegnato assegnando un `default` valore letterale al membro. Un membro è applicabile se è un'istanza di e il costruttore analizzato è un'istanza o se il membro è statico e il costruttore analizzato è statico. 
  - Si prevede che il valore `default` letterale produca uno `NotNull` stato per i tipi di valore non nullable, uno `MaybeNull` stato per i tipi di riferimento o tipi di valore nullable e uno `MaybeDefault` stato per i generics non vincolati.
- Quindi, visitando gli inizializzatori per i membri applicabili, aggiornando di conseguenza lo stato del flusso.
  - Questo consente l'inizializzazione di alcuni membri di riferimento non nullable usando un inizializzatore di campo/proprietà e altri da inizializzare all'interno del costruttore.
  - Si prevede che il compilatore analizzerà e aggiornerà i dati di diagnostica sugli inizializzatori una volta, quindi copierà lo stato del flusso risultante come stato iniziale per ogni costruttore che non dispone di un `: this(...)` inizializzatore.

**Un costruttore su un tipo di valore con un `: this()` inizializzatore che fa riferimento al costruttore del tipo di valore predefinito** ha uno stato di flusso iniziale fornito inizializzando tutti i membri applicabili allo stato fornito assegnando un `default` valore letterale al membro.

**Un costruttore su un tipo di riferimento con un `: this(...)` inizializzatore** *o*  
**Un costruttore su un tipo di valore con un inizializzatore che non fa riferimento al costruttore del tipo di valore predefinito** *o*  
**Un costruttore in un tipo di valore senza inizializzatore** ha lo stesso stato del flusso Nullable iniziale come metodo normale.  
I membri hanno uno stato iniziale in base alle annotazioni dichiarate e agli attributi di supporto dei valori null. Nel caso dei tipi di valore, si prevede che l'analisi di assegnazione definita fornisca il livello di sicurezza desiderato quando non è presente alcun `: this(...)` inizializzatore. Questo comportamento è identico a quello esistente.

In **corrispondenza di ogni ' Return ' esplicito o implicito in un costruttore**, viene visualizzato un avviso per ogni membro il cui stato di flusso è incompatibile con le relative annotazioni e attributi di supporto dei valori null. Un proxy ragionevole a questo scopo è: se l'assegnazione del membro a se stessa nel punto di ritorno genera un avviso di supporto di valori null, viene generato un avviso di supporto di valori null nel punto di ripristino.

In alcuni scenari è possibile che si verifichino molti avvisi per gli stessi membri. Come obiettivo "Stretch", penso che dovremmo considerare le "ottimizzazioni" seguenti:
- Se un membro ha uno stato di flusso incompatibile in tutti i punti restituiti in un costruttore applicabile, viene visualizzato un avviso sulla sintassi del nome del costruttore invece che su ogni punto restituito singolarmente.
- Se un membro ha uno stato di flusso incompatibile in tutti i punti restituiti in tutti i costruttori applicabili, viene visualizzato un avviso sulla dichiarazione del membro stesso.

## <a name="consequences-of-this-approach"></a>Conseguenze di questo approccio

```cs
public class C
{
    public string Prop { get; set; }
    public C()
    {
        Prop = null; // Warning: cannot assign null to 'Prop'
    } // Warning: Prop may be null when exiting 'C.C()'
    
    // This is consistent with currently shipped behavior:
    [MemberNotNull(nameof(Prop))]
    void M()
    {
        Prop = null; // Warning: cannot assign null to 'Prop'
    } // Warning: Prop may be null when exiting 'C.M()'
}
```

Lo scenario precedente produce più avvisi corrispondenti alla stessa proprietà. Se sono presenti più punti restituiti nel metodo, è possibile che vengano generati molti avvisi a seconda del numero di punti restituiti in cui un membro ha uno stato di flusso non valido.

Tuttavia, questo è coerente con il comportamento fornito per `[MemberNotNull]` `[NotNull]` gli attributi e: viene visualizzato un avviso quando si verifica un valore non valido e viene visualizzato un avviso quando si restituisce il punto in cui la variabile potrebbe contenere un valore non valido.

---

```cs
public class C
{
    public string Prop { get; set; }
    public C()
    {
        Prop.ToString(); // Warning: dereference of a possible null reference.
    } // No warning: Prop's flow state was 'promoted' to NotNull after dereference
    
    // This is also consistent with currently shipped behavior:
    [MemberNotNull(nameof(Prop))]
    void M()
    {
        Prop.ToString(); // Warning: dereference of a possible null reference.
    } // No warning: Prop's flow state was 'promoted' to NotNull after dereference
}
```

In questo scenario non viene mai effettivamente inizializzato `Prop` , ma sappiamo che se viene restituito normalmente da questo costruttore, è necessario che prop venga inizializzato in qualche modo. Questo avviso, anche se non è ideale, sembra essere appropriato per puntare l'utente verso la posizione in cui si è verificato il problema.

Analogamente, questo è coerente con il comportamento fornito di `[MemberNotNull]` e `[NotNull]` .

---

```cs
class C
{
    string Prop1 { get; set; }
    string Prop2 { get; set; }

    public C(bool a)
    {
        if (a)
        {
            Prop1 = "hello";
            return; // warning for Prop2
        }
        else
        {
            return; // warning for Prop1 and for Prop2
        }
    }
}
```

In questo scenario viene illustrata l'indipendenza degli avvisi a ogni punto di ripristino, nonché il modo in cui gli avvisi possono essere moltiplicati per un singolo membro all'interno di un singolo costruttore. È possibile che si verifichino metodi per ridurre la ridondanza degli avvisi, ma questo perfezionamento potrebbe essere più recente e migliorare `[MemberNotNull]` / `[NotNull]` l'analisi nello stesso momento. Per i costruttori con logica condizionale complessa, sembra essere un miglioramento per indicare "in questo caso, non è stato ancora inizializzato un elemento" e si dice semplicemente che "in questo punto non è stato inizializzato alcun elemento".
