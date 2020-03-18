---
ms.openlocfilehash: aecbf4298053debdb479ad3aaf6e3db468918455
ms.sourcegitcommit: 02b535d712cc9e022290e14d9e0324508b28f231
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "79485210"
---
# <a name="nullable-reference-types-specification"></a>Specifica di tipi di riferimento Nullable

Questo è un lavoro in corso. diverse parti sono mancanti o incomplete. ***

## <a name="syntax"></a>Sintassi

### <a name="nullable-reference-types"></a>Tipi riferimento nullable

I tipi di riferimento Nullable hanno la stessa sintassi `T?` la forma abbreviata di tipi di valore Nullable, ma non hanno un formato lungo corrispondente.

Ai fini della specifica, la produzione `nullable_type` corrente viene rinominata `nullable_value_type`e viene aggiunta una `nullable_reference_type` di produzione:

```antlr
reference_type
    : ...
    | nullable_reference_type
    ;
    
nullable_reference_type
    : non_nullable_reference_type '?'
    ;
    
non_nullable_reference_type
    : type
    ;
```

Il `non_nullable_reference_type` in un `nullable_reference_type` deve essere un tipo di riferimento non Nullable (classe, interfaccia, delegato o matrice) oppure un parametro di tipo vincolato a un tipo di riferimento non Nullable (tramite il vincolo `class` o una classe diversa da `object`).

I tipi di riferimento nullable non possono essere presenti nelle posizioni seguenti:

- come classe o interfaccia di base
- come ricevitore di un `member_access`
- come `type` in una `object_creation_expression`
- come `delegate_type` in una `delegate_creation_expression`
- come `type` in un `is_expression`, un `catch_clause` o un `type_pattern`
- come `interface` in un nome di membro di interfaccia completo

Viene fornito un avviso in un `nullable_reference_type` in cui il contesto di annotazione Nullable è disabilitato.

### <a name="nullable-class-constraint"></a>Vincolo di classe Nullable

Il vincolo `class` presenta una controparte Nullable `class?`:

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

### <a name="the-null-forgiving-operator"></a>Operatore che perdona i valori null

L'operatore `!` post-correzione è denominato operatore che perdona i valori null.

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;
    
null_forgiving_expression
    : primary_expression '!'
    ;
```

Il `primary_expression` deve essere di un tipo riferimento.  

L'operatore `!` suffisso non ha alcun effetto in fase di esecuzione. restituisce il risultato dell'espressione sottostante. Il suo unico ruolo è modificare lo stato null dell'espressione e limitare gli avvisi in base al relativo utilizzo.

### <a name="nullable-implicitly-typed-local-variables"></a>variabili locali tipizzate in modo implicito Nullable

`var` deduce un tipo con annotazioni per i tipi di riferimento.
Ad esempio, in `var s = "";` `var` viene dedotto come `string?`.

### <a name="nullable-compiler-directives"></a>Direttive del compilatore Nullable

`#nullable` direttive controllano i contesti di annotazione e avviso Nullable.

```antlr
pp_directive
    : ...
    | pp_nullable
    ;
    
pp_nullable
    : whitespace? '#' whitespace? 'nullable' whitespace nullable_action pp_new_line
    ;
    
nullable_action
    : 'disable'
    | 'enable'
    | 'restore'
    ;
```

le direttive `#pragma warning` vengono espanse per consentire la modifica del contesto di avviso nullable e per consentire l'abilitazione di singoli avvisi anche quando sono disabilitati per impostazione predefinita:

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace nullable_action whitespace 'nullable'
    ;

warning_action
    : ...
    | 'enable'
    ;
```

Si noti che il nuovo form di `pragma_warning_body` utilizza `nullable_action`, non `warning_action`.

## <a name="nullable-contexts"></a>Contesti nullable

Ogni riga del codice sorgente ha un *contesto di annotazione Nullable* e un *contesto di avviso Nullable*. Questi controllano se le annotazioni Nullable hanno effetto e se vengono specificati avvisi di supporto dei valori null. Il contesto di annotazione di una determinata riga è *disabilitato* o *abilitato*. Il contesto di avviso di una determinata riga è *disabilitato* o *abilitato*.

Entrambi i contesti possono essere specificati a livello di progetto (all' C# esterno del codice sorgente) o in qualsiasi punto all'interno di un file di origine tramite `#nullable` direttive del pre-processore. Se non vengono specificate impostazioni a livello di progetto, il valore predefinito è per entrambi i contesti da *disabilitare*.

La direttiva `#nullable` controlla l'annotazione e i contesti di avviso all'interno del testo di origine e hanno la precedenza sulle impostazioni a livello di progetto.

Una direttiva imposta i contesti che controlla per le righe di codice successive, fino a quando un'altra direttiva ne esegue l'override o fino alla fine del file di origine.

L'effetto delle direttive è il seguente:

- `#nullable disable`: imposta l'annotazione nullable e i contesti di avviso su *disabled*
- `#nullable enable`: imposta l'annotazione nullable e i contesti di avviso su *Enabled*
- `#nullable restore`: Ripristina i contesti di avviso e di annotazione Nullable nelle impostazioni del progetto
- `#nullable disable annotations`: imposta il contesto di annotazione Nullable su *disabled*
- `#nullable enable annotations`: imposta il contesto di annotazione Nullable su *Enabled*
- `#nullable restore annotations`: Ripristina il contesto di annotazione Nullable nelle impostazioni del progetto
- `#nullable disable warnings`: imposta il contesto di avviso Nullable su *disabled*
- `#nullable enable warnings`: imposta il contesto di avviso Nullable su *Enabled*
- `#nullable restore warnings`: Ripristina il contesto di avviso Nullable nelle impostazioni del progetto

## <a name="nullability-of-types"></a>Supporto dei valori Null dei tipi

Un tipo specificato può avere uno dei quattro nullabilities: *ignaro*, non *null*, *Nullable* e *Unknown*. 

I tipi *sconosciuti* e non *null* possono causare avvisi se viene assegnato un potenziale valore `null`. I tipi *ignari* e *Nullable* , tuttavia, sono "*assegnabili a null*" e possono avere `null` valori assegnati senza avvisi. 

I tipi *ignari* e non *null* possono essere dereferenziati o assegnati senza avvisi. I valori di tipi *Nullable* e *Unknown* , tuttavia, sono "*null-Yielding*" e possono causare avvisi quando vengono dereferenziati o assegnati senza il controllo null appropriato. 

Lo *stato null predefinito* di un tipo che cede un valore null è "Maybe null". Lo stato null predefinito di un tipo non null restituisce "not null".

Il tipo di tipo e il contesto di annotazione nullable in cui si verificano determinano il supporto di valori null:

- Un tipo di valore che non può essere null `S` è sempre non *null*
- Un tipo di valore Nullable `S?` è sempre *Nullable*
- Un tipo di riferimento senza annotazioni `C` in un contesto di annotazione *disabilitato* è *ignaro*
- Un tipo di riferimento senza annotazioni `C` in un contesto di annotazione *abilitato* non può essere *null*
- Un tipo di riferimento Nullable `C?` *ammette i valori null* (ma è possibile che venga restituito un avviso in un contesto di annotazione *disabilitato* )

I parametri di tipo prendono inoltre in considerazione i vincoli:

- Un parametro di tipo `T` dove tutti i vincoli (se presenti) sono tipi che restituiscono valori null (*Nullable* e *Unknown*) o il vincolo `class?` è *sconosciuto*
- Parametro di tipo `T` dove almeno un vincolo è *ignaro* o non può essere *null* o uno dei vincoli `struct` o `class` è
    - *ignaro* in un contesto di annotazione *disabilitato*
    - non *ammette valori null* in un contesto di annotazione *abilitato*
- Parametro di tipo Nullable `T?` dove almeno uno dei vincoli di `T`è *ignaro* o non può essere *null* o uno dei vincoli `struct` o `class`
    - *Nullable* in un contesto di annotazione *disabilitato* (ma viene restituito un avviso)
    - *Nullable* in un contesto di annotazione *abilitato*

Per un parametro di tipo `T`, `T?` è consentito solo se `T` è noto come tipo di valore o è noto come tipo di riferimento.

### <a name="oblivious-vs-nonnullable"></a>E non nullable

Si ritiene che un `type` venga eseguito in un determinato contesto di annotazione quando l'ultimo token del tipo è incluso in tale contesto.

Se un tipo di riferimento specificato `C` nel codice sorgente viene interpretato come ignaro o non nullo dipende dal contesto dell'annotazione di tale codice sorgente. Una volta stabilito, viene considerato parte di quel tipo e "lo sposta con esso", ad esempio durante la sostituzione di argomenti di tipo generico. È come se è presente un'annotazione come `?` sul tipo, ma invisibile.

## <a name="constraints"></a>Vincoli

I tipi di riferimento nullable possono essere utilizzati come vincoli generici. Inoltre `object` è ora valido come vincolo esplicito. L'assenza di un vincolo è ora equivalente a un vincolo di `object?` (anziché `object`), ma, a differenza di `object` prima di, `object?` non è vietato come vincolo esplicito.

`class?` è un nuovo vincolo che indica "probabile tipo di riferimento Nullable", mentre `class` denota "tipo di riferimento non null".

Il supporto di valori null di un argomento di tipo o di un vincolo non ha alcun effetto sul fatto che il tipo soddisfi il vincolo, ad eccezione del caso in cui è già presente. i tipi di valore nullable non soddisfano il vincolo `struct`. Tuttavia, se l'argomento di tipo non soddisfa i requisiti di supporto dei valori null del vincolo, è possibile che venga fornito un avviso.

## <a name="null-state-and-null-tracking"></a>Stato null e rilevamento null

Ogni espressione in una determinata posizione di origine ha uno *stato null*, che indica se si ritiene che possa restituire potenzialmente null. Lo stato null può essere "not null" o "Maybe null". Lo stato null viene utilizzato per determinare se è necessario che venga fornito un avviso sulle conversioni e le dereferenziazioni non sicure null.

### <a name="null-tracking-for-variables"></a>Rilevamento di valori null per le variabili

Per alcune espressioni che indicano variabili o proprietà, lo stato null viene rilevato tra le occorrenze, in base alle assegnazioni, i test eseguiti su di essi e il flusso di controllo tra di essi. Questa operazione è simile alla modalità di rilevamento dell'assegnazione definita per le variabili. Le espressioni registrate sono quelle con il formato seguente:

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

Dove gli identificatori denotano i campi o le proprietà.

***Descrivere le transizioni di stato null simili all'assegnazione definita***

### <a name="null-state-for-expressions"></a>Stato null per le espressioni

Lo stato null di un'espressione deriva dal formato e dal tipo e dallo stato null delle variabili in esso incluse.

### <a name="literals"></a>Valori letterali

Lo stato null di un valore letterale `null` è "Maybe null". Lo stato null di un valore letterale `default` che viene convertito in un tipo che non è un tipo di valore non nullo è "Maybe null". Lo stato null di qualsiasi altro valore letterale è "not null".

### <a name="simple-names"></a>Nomi semplici

Se una `simple_name` non è classificata come valore, il suo stato null è "not null". In caso contrario, si tratta di un'espressione rilevata e il relativo stato null è il relativo stato null rilevato in questa posizione di origine.

### <a name="member-access"></a>Accesso ai membri

Se una `member_access` non è classificata come valore, il suo stato null è "not null". In caso contrario, se si tratta di un'espressione rilevata, il relativo stato null è il relativo stato null rilevato in questa posizione di origine. In caso contrario, lo stato null è lo stato null predefinito del relativo tipo.

### <a name="invocation-expressions"></a>Espressioni di chiamata

Se un `invocation_expression` richiama un membro dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi. In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.

### <a name="element-access"></a>Accesso agli elementi

Se un `element_access` richiama un indicizzatore dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi. In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.

### <a name="base-access"></a>Accesso di base

Se `B` indica il tipo di base del tipo di inclusione, `base.I` ha lo stesso stato null di `((B)this).I` e `base[E]` ha lo stesso stato null di `((B)this)[E]`.

### <a name="default-expressions"></a>Espressioni predefinite

`default(T)` ha lo stato null "non null" se `T` è noto come tipo di valore che non ammette valori null. In caso contrario, lo stato null è "Maybe null".

### <a name="null-conditional-expressions"></a>Espressioni condizionali null

Uno `null_conditional_expression` ha lo stato null "Maybe null".

### <a name="cast-expressions"></a>Espressioni cast

Se un'espressione cast `(T)E` richiama una conversione definita dall'utente, lo stato null dell'espressione è lo stato null predefinito per il relativo tipo. In caso contrario, se `T` restituisce null (*Nullable* o *Unknown*), lo stato null è "Maybe null". In caso contrario, lo stato null corrisponde allo stato null del `E`.

### <a name="await-expressions"></a>Espressioni await

Lo stato null del `await E` è lo stato null predefinito del relativo tipo.

### <a name="the-as-operator"></a>Operatore `as`

Un'espressione `as` ha lo stato null "Maybe null".

### <a name="the-null-coalescing-operator"></a>Operatore di Unione null

`E1 ?? E2` ha lo stesso stato null di `E2`

### <a name="the-conditional-operator"></a>Operatore condizionale

Lo stato null del `E1 ? E2 : E3` è "not null" se lo stato null sia di `E2` che di `E3` sono "not null". In caso contrario, è "Maybe null".

### <a name="query-expressions"></a>Espressioni di query

Lo stato null di un'espressione di query è lo stato null predefinito del relativo tipo.

### <a name="assignment-operators"></a>Operatori di assegnazione

`E1 = E2` e `E1 op= E2` hanno lo stesso stato null di `E2` dopo l'applicazione di tutte le conversioni implicite.

### <a name="unary-and-binary-operators"></a>Operatori unari e binari

Se un operatore unario o binario richiama un operatore definito dall'utente dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi. In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.

***Si tratta di un'operazione speciale per la `+` binaria su stringhe e delegati?***

### <a name="expressions-that-propagate-null-state"></a>Espressioni che propagano lo stato null

`(E)`, `checked(E)` e `unchecked(E)` hanno tutti lo stesso stato null `E`.

### <a name="expressions-that-are-never-null"></a>Espressioni che non sono mai null

Lo stato null dei seguenti form di espressione è sempre "not null":

- `this` accesso
- stringhe interpolate
- espressioni di `new` (oggetto, delegato, oggetto anonimo e espressioni di creazione di matrici)
- Espressioni `typeof`
- Espressioni `nameof`
- funzioni anonime (metodi anonimi ed espressioni lambda)
- espressioni con indulgenza null
- Espressioni `is`

## <a name="type-inference"></a>Inferenza del tipo

### <a name="type-inference-for-var"></a>Inferenza del tipo per `var`

Il tipo dedotto per le variabili locali dichiarate con `var` viene informato dallo stato null dell'espressione di inizializzazione.

```csharp
var x = E;
```

Se il tipo di `E` è un tipo di riferimento Nullable `C?` e lo stato null di `E` è "not null", il tipo dedotto per `x` è `C`. In caso contrario, il tipo dedotto è il tipo di `E`.

Il supporto di valori null del tipo dedotto per `x` viene determinato come descritto in precedenza, in base al contesto di annotazione del `var`, come se il tipo fosse stato specificato in modo esplicito in tale posizione.

### <a name="type-inference-for-var"></a>Inferenza del tipo per `var?`

Il tipo dedotto per le variabili locali dichiarate con `var?` è indipendente dallo stato null dell'espressione di inizializzazione.

```csharp
var? x = E;
```

Se il tipo `T` di `E` è un tipo di valore nullable o un tipo di riferimento Nullable, il tipo dedotto per `x` è `T`. In caso contrario, se `T` è un tipo di valore che non ammette valori null `S` il tipo dedotto è `S?`. In caso contrario, se `T` è un tipo di riferimento non null `C` il tipo dedotto è `C?`. In caso contrario, la dichiarazione non è valida.

Il supporto di valori null del tipo dedotto per `x` è sempre *Nullable*.

### <a name="generic-type-inference"></a>Inferenza del tipo generico

L'inferenza del tipo generico è stata migliorata per determinare se i tipi di riferimento derivati devono essere nullable o meno. Si tratta di un tentativo ottimale, che non è in grado di generare avvisi, ma può causare avvisi Nullable quando i tipi dedotti dell'overload selezionato vengono applicati agli argomenti.

L'inferenza del tipo non si basa sul contesto di annotazione dei tipi in ingresso. Viene invece dedotto un `type` che acquisisce il proprio contesto di annotazione da dove "sarebbe stato" se fosse stato espresso in modo esplicito. In questo modo viene sottolineato il ruolo di inferenza del tipo come praticità per gli elementi che potrebbero essere stati scritti.

Più precisamente, il contesto dell'annotazione per un argomento di tipo derivato è il contesto del token che sarebbe stato seguito dall'elenco di parametri di tipo `<...>`, ne era presente uno. ovvero il nome del metodo generico chiamato. Per le espressioni di query che vengono convertite in chiamate di questo tipo, il contesto viene tratto dalla parola chiave contestuale iniziale della clausola di query da cui viene generata la chiamata.

### <a name="the-first-phase"></a>Prima fase

I tipi di riferimento Nullable scorrono nei limiti delle espressioni iniziali, come descritto di seguito. Vengono inoltre introdotti due nuovi tipi di limiti, ovvero `null` e `default`. Il loro scopo è quello di eseguire le occorrenze di `null` o `default` nelle espressioni di input, che possono causare il Nullable di un tipo derivato, anche in caso contrario. Funziona anche per i tipi di *valore* Nullable, che sono stati migliorati per prelevare il "valore null" nel processo di inferenza.

La determinazione dei limiti da aggiungere nella prima fase viene migliorata nel modo seguente:

Se un argomento `Ei` ha un tipo di riferimento, il tipo `U` usato per l'inferenza dipende dallo stato null di `Ei` e dal tipo dichiarato:
- Se il tipo dichiarato è un tipo di riferimento non nullo `U0` o un tipo di riferimento Nullable `U0?`
    - Se lo stato null del `Ei` è "not null", `U` è `U0`
    - Se lo stato null del `Ei` è "Maybe null", `U` è `U0?`
- In caso contrario, se `Ei` ha un tipo dichiarato, `U` è quel tipo
- In caso contrario, se `Ei` è `null` quindi `U` è il limite speciale `null`
- In caso contrario, se `Ei` è `default` quindi `U` è il limite speciale `default`
- In caso contrario, non viene eseguita alcuna inferenza.

### <a name="exact-upper-bound-and-lower-bound-inferences"></a>Inferenze esatte, con associazione superiore e con associazione inferiore

In inferenza *dal* tipo `U` al tipo `V`, se `V` è un tipo *di* riferimento Nullable `V0?`, viene utilizzato `V0` anziché `V` nelle clausole seguenti.
- Se `V` è una delle variabili di tipo non fixed, `U` viene aggiunto come limite esatto, superiore o inferiore come prima
- In caso contrario, se `U` è `null` o `default`, non viene eseguita alcuna inferenza
- In caso contrario, se `U` è un tipo di riferimento Nullable `U0?`, `U0` viene utilizzato anziché `U` nelle clausole successive.

L'essenza è che i valori null che riguardano direttamente una delle variabili di tipo non fisso vengono conservati nei limiti. Per gli inferenza che rimaledicono ulteriormente i tipi di origine e di destinazione, d'altra parte, il supporto dei valori null viene ignorato. Può corrispondere o meno, ma in caso contrario, verrà emesso un avviso in un secondo momento se viene scelto e applicato l'overload.

### <a name="fixing"></a>Fissaggio

La specifica attualmente non esegue un processo valido per descrivere cosa accade quando più limiti sono convertibili tra loro identità, ma sono diversi. Questo problema può verificarsi tra `object` e `dynamic`, tra i tipi di tupla che differiscono solo per i nomi degli elementi, tra i tipi costruiti e ora anche tra `C` e `C?` per i tipi di riferimento.

È inoltre necessario propagare "null" dalle espressioni di input al tipo di risultato. 

Per gestire queste operazioni, è necessario aggiungere altre fasi per la correzione, che ora è:

1. Raccogliere tutti i tipi in tutti i limiti come candidati, rimuovendo `?` da tutti i tipi di riferimento Nullable
2. Elimina i candidati in base ai requisiti dei limiti esatti, inferiore e superiore (mantenendo i limiti di `null` e `default`)
3. Elimina i candidati che non dispongono di una conversione implicita a tutti gli altri candidati
4. Se i candidati rimanenti non hanno tutte le conversioni di identità tra loro, l'inferenza del tipo ha esito negativo
5. *Unisci* i candidati rimanenti come descritto di seguito
6. Se il candidato risultante è un tipo di riferimento o un tipo di valore non null e *tutti* i limiti esatti o *uno* dei limiti inferiori sono tipi di valore Nullable, tipi di riferimento nullable, `null` o `default`, `?` viene aggiunto al candidato risultante, rendendolo un tipo di valore nullable o un tipo di riferimento.

L' *Unione* viene descritta tra due tipi candidati. Si tratta di transitive e commutative, quindi i candidati possono essere Uniti in qualsiasi ordine con lo stesso risultato finale. Non è definito se i due tipi candidati non sono convertibili in identità.

La funzione *merge* accetta due tipi candidati e una direzione ( *+* o *-* ):

- *Merge*(`T`, `T`, *d*) = t
- *Merge*(`S`, `T?`, *+* ) = *merge*(`S?`, `T`, *+* ) = *merge*(`S`, `T`, *+* )`?`
- *Merge*(`S`, `T?`, *-* ) = *merge*(`S?`, `T`, *-* ) = *merge*(`S`, `T`, *-* )
- *Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+* ) = `C<`*merge*(`S1`, `T1`, *d1*)`,...,`*merge*(`Sn`, `Tn`, *DN*)`>`, *dove*
    - `di` =  *+* se il parametro di tipo `i`' di `C<...>` è covariante
    - `di` =  *-* se il parametro di tipo `i`' di `C<...>` è contra-o invariante
- *Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-* ) = `C<`*merge*(`S1`, `T1`, *d1*)`,...,`*merge*(`Sn`, `Tn`, *DN*)`>`, *dove*
    - `di` =  *-* se il parametro di tipo `i`' di `C<...>` è covariante
    - `di` =  *+* se il parametro di tipo `i`' di `C<...>` è contra-o invariante
- *Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*merge*(`S1`, `T1`, *d*)`n1,...,`*merge*(`Sn`, `Tn`, *d*) `nn)`, *dove*
    - `ni` è assente se `si` e `ti` sono diverse o se entrambe sono assenti
    - `ni` viene `si` se `si` e `ti` sono uguali
- *Merge*(`object`, `dynamic`) = *merge*(`dynamic`, `object`) = `dynamic`

## <a name="warnings"></a>Avvisi

### <a name="potential-null-assignment"></a>Potenziale assegnazione null

### <a name="potential-null-dereference"></a>Potenziale dereferenziazione null

### <a name="constraint-nullability-mismatch"></a>Mancata corrispondenza dei valori null per i vincoli

### <a name="nullable-types-in-disabled-annotation-context"></a>Tipi nullable nel contesto di annotazione disabilitato

## <a name="attributes-for-special-null-behavior"></a>Attributi per un comportamento null speciale


