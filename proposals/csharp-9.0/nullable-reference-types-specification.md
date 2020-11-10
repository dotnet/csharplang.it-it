---
ms.openlocfilehash: e6a784ef90308a5395c6b1db454a67d40c10e3e4
ms.sourcegitcommit: 00d9d791b6f1d9538a979a111e93cf935d9b6cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94423369"
---
# <a name="nullable-reference-types-specification"></a>Specifica di tipi di riferimento Nullable

***Questo è un lavoro in corso. diverse parti sono mancanti o incomplete.** _

Questa funzionalità aggiunge due nuovi tipi di tipi Nullable (tipi di riferimento nullable e tipi generici Nullable) ai tipi di valore Nullable esistenti e introduce un'analisi del flusso statica per finalità di sicurezza dei valori null.

## <a name="syntax"></a>Sintassi

### <a name="nullable-reference-types-and-nullable-type-parameters"></a>Tipi di riferimento nullable e parametri di tipo Nullable

I tipi di riferimento nullable e i parametri di tipo Nullable hanno la stessa sintassi della `T?` forma abbreviata di tipi di valore Nullable, ma non hanno una forma estesa corrispondente.

Ai fini della specifica, la `nullable_type` produzione corrente viene rinominata in e le `nullable_value_type` `nullable_reference_type` `nullable_type_parameter` produzioni e vengono aggiunte:

```antlr
type
    : value_type
    | reference_type
    | nullable_type_parameter
    | type_parameter
    | type_unsafe
    ;

reference_type
    : ...
    | nullable_reference_type
    ;

nullable_reference_type
    : non_nullable_reference_type '?'
    ;

non_nullable_reference_type
    : reference_type
    ;

nullable_type_parameter
    : non_nullable_non_value_type_parameter '?'
    ;

non_nullable_non_value_type_parameter
    : type_parameter
    ;
```

Il `non_nullable_reference_type` valore di in `nullable_reference_type` deve essere un tipo di riferimento non Nullable (classe, interfaccia, delegato o matrice).

`non_nullable_non_value_type_parameter`In `nullable_type_parameter` deve essere un parametro di tipo che non è vincolato a un tipo valore.

I tipi di riferimento nullable e i parametri di tipo nullable non possono essere presenti nelle posizioni seguenti:

- come classe o interfaccia di base
- come ricevitore di un `member_access`
- come `type` in un `object_creation_expression`
- come `delegate_type` in un `delegate_creation_expression`
- come `type` in un oggetto `is_expression` , un oggetto `catch_clause` o un oggetto `type_pattern`
- come `interface` in un nome di membro di interfaccia completo

Viene fornito un avviso in un oggetto `nullable_reference_type` e `nullable_type_parameter` in un contesto di annotazione _disabled * Nullable.

### <a name="class-and-class-constraint"></a>`class` e `class?` vincolo

Il `class` vincolo presenta una controparte Nullable `class?` :

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

È necessario creare un'istanza di un parametro di tipo vincolato a `class` (in un contesto di annotazione *abilitato* ) con un tipo di riferimento non null.

È possibile creare un'istanza di un parametro di tipo vincolato da `class?` (o `class` in un contesto di annotazione *disabilitato* ) con un tipo di riferimento nullable o non null.

Un avviso viene specificato in un `class?` vincolo in un contesto di annotazione *disabilitato* .

### <a name="notnull-constraint"></a>`notnull` vincolo

Un parametro di tipo vincolato a `notnull` non può essere un tipo Nullable (tipo di valore Nullable, tipo di riferimento nullable o parametro di tipo Nullable).

```antlr
primary_constraint
    : ...
    | 'notnull'
    ;
```

### <a name="default-constraint"></a>`default` vincolo

Il `default` vincolo può essere usato nell'override di un metodo o in un'implementazione esplicita per evitare ambiguità che `T?` significa "parametro di tipo Nullable" da "tipo di valore Nullable" ( `Nullable<T>` ). Mancanza del `default` vincolo `T?` , una sintassi in un'implementazione di override o esplicita verrà interpretata come `Nullable<T>`

Vedere https://github.com/dotnet/csharplang/blob/master/proposals/csharp-9.0/unconstrained-type-parameter-annotations.md#default-constraint

### <a name="the-null-forgiving-operator"></a>Operatore che perdona i valori null

L'operatore post-correzione `!` è denominato operatore che perdona i valori null. Può essere applicato a un *primary_expression* o all'interno di un *null_conditional_expression* :

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;

null_forgiving_expression
    : primary_expression '!'
    ;

null_conditional_expression
    : primary_expression null_conditional_operations_no_suppression suppression?
    ;

null_conditional_operations_no_suppression
    : null_conditional_operations? '?' '.' identifier type_argument_list?
    | null_conditional_operations? '?' '[' argument_list ']'
    | null_conditional_operations '.' identifier type_argument_list?
    | null_conditional_operations '[' argument_list ']'
    | null_conditional_operations '(' argument_list? ')'
    ;

null_conditional_operations
    : null_conditional_operations_no_suppression suppression?
    ;

suppression
    : '!'
    ;
```

Ad esempio:

```csharp
var v = expr!;
expr!.M();
_ = a?.b!.c;
```

`primary_expression`E `null_conditional_operations_no_suppression` devono essere di un tipo Nullable.

L' `!` operatore di suffisso non ha alcun effetto in fase di esecuzione. restituisce il risultato dell'espressione sottostante. Il suo unico ruolo consiste nel modificare lo stato null dell'espressione in "not null" e per limitare gli avvisi in base al relativo utilizzo.

### <a name="nullable-compiler-directives"></a>Direttive del compilatore Nullable

`#nullable` le direttive controllano i contesti di annotazione e avviso Nullable.

```antlr
pp_directive
    : ...
    | pp_nullable
    ;

pp_nullable
    : whitespace? '#' whitespace? 'nullable' whitespace nullable_action (whitespace nullable_target)? pp_new_line
    ;

nullable_action
    : 'disable'
    | 'enable'
    | 'restore'
    ;

nullable_target
    : 'warnings'
    | 'annotations'
    ;
```

`#pragma warning` le direttive vengono espanse per consentire la modifica del contesto di avviso Nullable:

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace warning_action whitespace 'nullable'
    ;
```

Ad esempio:

```csharp
#pragma warning disable nullable
```

## <a name="nullable-contexts"></a>Contesti nullable

Ogni riga del codice sorgente ha un *contesto di annotazione Nullable* e un *contesto di avviso Nullable*. Questi controllano se le annotazioni Nullable hanno effetto e se vengono specificati avvisi di supporto dei valori null. Il contesto di annotazione di una determinata riga è *disabilitato* o *abilitato*. Il contesto di avviso di una determinata riga è *disabilitato* o *abilitato*.

Entrambi i contesti possono essere specificati a livello di progetto (all'esterno del codice sorgente C#) o in qualsiasi punto all'interno di un file di origine tramite `#nullable` direttive pre-processore. Se non vengono specificate impostazioni a livello di progetto, il valore predefinito è per entrambi i contesti da *disabilitare*.

La `#nullable` direttiva controlla l'annotazione e i contesti di avviso all'interno del testo di origine e hanno la precedenza sulle impostazioni a livello di progetto.

Una direttiva imposta i contesti che controlla per le righe di codice successive, fino a quando un'altra direttiva ne esegue l'override o fino alla fine del file di origine.

L'effetto delle direttive è il seguente:

- `#nullable disable`: Imposta l'annotazione nullable e i contesti di avviso su *disabled*
- `#nullable enable`: Imposta l'annotazione nullable e i contesti di avviso su *Enabled*
- `#nullable restore`: Ripristina i contesti di avviso e di annotazione Nullable nelle impostazioni del progetto
- `#nullable disable annotations`: Imposta il contesto di annotazione Nullable su *disabled*
- `#nullable enable annotations`: Imposta il contesto di annotazione Nullable su *Enabled*
- `#nullable restore annotations`: Ripristina il contesto di annotazione Nullable nelle impostazioni del progetto
- `#nullable disable warnings`: Imposta il contesto di avviso Nullable su *disabled*
- `#nullable enable warnings`: Imposta il contesto di avviso Nullable su *Enabled*
- `#nullable restore warnings`: Ripristina il contesto di avviso Nullable nelle impostazioni del progetto

## <a name="nullability-of-types"></a>Supporto dei valori Null dei tipi

Un tipo specificato può avere uno dei tre nullabilities: *ignaro* , non *nullo* e *Nullable*.

I tipi che non *ammettono valori null* possono causare avvisi se `null` viene assegnato un valore potenziale. I tipi *ignari* e *Nullable* , tuttavia, sono " *assegnabili a null* " e possono avere `null` valori assegnati senza avvisi.

I valori dei tipi *ignari* e non *null* possono essere dereferenziati o assegnati senza avvisi. I valori dei tipi *Nullable* , tuttavia, sono " *null-Yielding* " e possono causare avvisi quando vengono dereferenziati o assegnati senza il controllo null appropriato.

Lo *stato null predefinito* di un tipo che cede un valore null è "Maybe null" o "Maybe default". Lo stato null predefinito di un tipo non null restituisce "not null".

Il tipo di tipo e il contesto di annotazione nullable in cui si verificano determinano il supporto di valori null:

- Un tipo di valore che non ammette valori null `S` è sempre non *null*
- Un tipo di valore Nullable `S?` è sempre *Nullable*
- Un tipo di riferimento senza annotazioni `C` in un contesto di annotazione *disabilitato* è *ignaro*
- Un tipo di riferimento non annotato `C` in un contesto di annotazione *abilitato* non è *null*
- Un tipo di riferimento Nullable ammette i `C?` *valori null* (ma è possibile che venga restituito un avviso in un contesto di annotazione *disabilitato* )

I parametri di tipo prendono inoltre in considerazione i vincoli:

- Parametro di tipo in `T` cui tutti i vincoli (se presenti) sono tipi nullable o il `class?` vincolo *ammette i valori null*
- Parametro di tipo `T` in cui almeno un vincolo è *ignaro* o non può essere *null* o uno dei `struct` `class` vincoli o o `notnull` è
    - *ignaro* in un contesto di annotazione *disabilitato*
    - non *ammette valori null* in un contesto di annotazione *abilitato*
- Un parametro di tipo Nullable ammette i `T?` *valori null* , ma un avviso viene restituito in un contesto di annotazione *disabilitato* se `T` non è un tipo di valore

### <a name="oblivious-vs-nonnullable"></a>E non nullable

Un `type` viene considerato che si verifica in un contesto di annotazione specificato quando l'ultimo token del tipo si trova all'interno di tale contesto.

Se un tipo di riferimento specificato `C` nel codice sorgente viene interpretato come ignaro o non nullo dipende dal contesto dell'annotazione di tale codice sorgente. Una volta stabilito, viene considerato parte di quel tipo e "lo sposta con esso", ad esempio durante la sostituzione di argomenti di tipo generico. È come se fosse presente un'annotazione come `?` nel tipo, ma invisibile.

## <a name="constraints"></a>Vincoli

I tipi di riferimento nullable possono essere utilizzati come vincoli generici.

`class?` è un nuovo vincolo che indica "probabile tipo di riferimento Nullable", mentre `class` in un contesto di annotazione *abilitato* denota "tipo di riferimento non null".

`default` è un nuovo vincolo che indica un parametro di tipo che non è noto come tipo di riferimento o di valore. Può essere utilizzato solo su metodi sottoposti a override e implementati in modo esplicito. Con questo vincolo, `T?` significa un parametro di tipo nullable, anziché essere una sintassi abbreviata per `Nullable<T>` .

`notnull` è un nuovo vincolo che indica un parametro di tipo che non ammette valori null.

Il supporto di valori null di un argomento di tipo o di un vincolo non ha alcun effetto sul fatto che il tipo soddisfi il vincolo, ad eccezione del caso in cui è già presente. i tipi di valore nullable non soddisfano il `struct` vincolo. Tuttavia, se l'argomento di tipo non soddisfa i requisiti di supporto dei valori null del vincolo, è possibile che venga fornito un avviso.

## <a name="null-state-and-null-tracking"></a>Stato null e rilevamento null

Ogni espressione in una determinata posizione di origine ha uno *stato null* , che indica se si ritiene che possa restituire potenzialmente null. Lo stato null può essere "not null", "Maybe null" o "Maybe default". Lo stato null viene utilizzato per determinare se è necessario che venga fornito un avviso sulle conversioni e le dereferenziazioni non sicure null.

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

Lo stato null per le variabili rilevate è "not null" in codice non eseguibile. Questo segue le altre decisioni riguardanti il codice non eseguibile, ad esempio considerando tutte le variabili locali da assegnare definitivamente.

***Descrivere le transizioni di stato null simili all'assegnazione definita** _

### <a name="null-state-for-expressions"></a>Stato null per le espressioni

Lo stato null di un'espressione deriva dal formato e dal tipo e dallo stato null delle variabili in esso incluse.

### <a name="literals"></a>Valori letterali

Lo stato null di un `null` `default` valore letterale e è "Maybe default". Lo stato null di qualsiasi altro valore letterale è "not null".

### <a name="simple-names"></a>Nomi semplici

Se un oggetto `simple_name` non è classificato come valore, il relativo stato null è "not null". In caso contrario, si tratta di un'espressione rilevata e il relativo stato null è il relativo stato null rilevato in questa posizione di origine.

### <a name="member-access"></a>Accesso ai membri

Se un oggetto `member_access` non è classificato come valore, il relativo stato null è "not null". In caso contrario, se si tratta di un'espressione rilevata, il relativo stato null è il relativo stato null rilevato in questa posizione di origine. In caso contrario, lo stato null è lo stato null predefinito del relativo tipo.

### <a name="invocation-expressions"></a>Espressioni di chiamata

Se un oggetto `invocation_expression` richiama un membro dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi. In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.

### <a name="element-access"></a>Accesso a elementi

Se un oggetto `element_access` richiama un indicizzatore dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi. In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.

### <a name="base-access"></a>Accesso di base

Se `B` denota il tipo di base del tipo di inclusione, `base.I` ha lo stesso stato null di `((B)this).I` e `base[E]` ha lo stesso stato null di `((B)this)[E]` .

### <a name="default-expressions"></a>Espressioni predefinite

`default(T)` ha lo stato null "not null" Se `T` è noto come tipo di valore non null. In caso contrario, lo stato del valore null è "Maybe default".

### <a name="null-conditional-expressions"></a>Espressioni condizionali null

Uno `null_conditional_expression` ha lo stato null "Maybe null".

### <a name="cast-expressions"></a>Espressioni cast

Se un'espressione cast `(T)E` richiama una conversione definita dall'utente, lo stato null dell'espressione sarà lo stato null predefinito per il relativo tipo. In caso contrario, se `T` è _nullable *, lo stato null è "Maybe null". In caso contrario, lo stato null corrisponde allo stato null di `E` .

***Questa operazione richiede upddating** _

### <a name="await-expressions"></a>Espressioni await

Lo stato null di `await E` è lo stato null predefinito del relativo tipo.

### <a name="the-as-operator"></a>Operatore `as`

Un' `as` espressione ha lo stato null "Maybe null".

### <a name="the-null-coalescing-operator"></a>Operatore di Unione null

`E1 ?? E2` ha lo stesso stato null di `E2`

### <a name="the-conditional-operator"></a>Operatore condizionale

Lo stato null di `E1 ? E2 : E3` è "not null" se lo stato null di `E2` e è `E3` "not null". In caso contrario, è "Maybe null".

### <a name="query-expressions"></a>Espressioni di query

Lo stato null di un'espressione di query è lo stato null predefinito del relativo tipo.

### <a name="assignment-operators"></a>Operatori di assegnazione

`E1 = E2` e `E1 op= E2` hanno lo stesso stato null di `E2` dopo l'applicazione di tutte le conversioni implicite.

### <a name="unary-and-binary-operators"></a>Operatori unari e binari

Se un operatore unario o binario richiama un operatore definito dall'utente dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi. In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.

_*_Si tratta di un'operazione speciale per binari `+` su stringhe e delegati?_*_

### <a name="expressions-that-propagate-null-state"></a>Espressioni che propagano lo stato null

`(E)`, `checked(E)` e `unchecked(E)` hanno tutti lo stesso stato null di `E` .

### <a name="expressions-that-are-never-null"></a>Espressioni che non sono mai null

Lo stato null dei seguenti form di espressione è sempre "not null":

- `this` accesso
- stringhe interpolate
- `new` espressioni (oggetto, delegato, oggetto anonimo e espressioni di creazione di matrici)
- Espressioni `typeof`
- Espressioni `nameof`
- funzioni anonime (metodi anonimi ed espressioni lambda)
- espressioni con indulgenza null
- Espressioni `is`

### <a name="nested-functions"></a>Funzioni annidate

Le funzioni annidate (espressioni lambda e funzioni locali) vengono trattate come metodi, tranne che per quanto riguarda le variabili acquisite.
Lo stato iniziale di una variabile acquisita all'interno di un'espressione lambda o di una funzione locale è l'intersezione dello stato Nullable della variabile in tutti gli "utilizzi" di tale funzione o lambda nidificata. L'uso di una funzione locale è una chiamata a tale funzione o quando viene convertito in un delegato. L'uso di un'espressione lambda è il punto in cui è definito nell'origine.

## <a name="type-inference"></a>Inferenza del tipo

### <a name="nullable-implicitly-typed-local-variables"></a>variabili locali tipizzate in modo implicito Nullable

`var` deduce un tipo con annotazioni per i tipi di riferimento e i parametri di tipo che non sono limitati a essere un tipo valore.
Ad esempio:
- in `var s = "";` `var` viene dedotto come `string?` .
- in `var t = new T();` con un oggetto non vincolato `T` `var` viene dedotto come `T?` .

### <a name="generic-type-inference"></a>Inferenza del tipo generico

L'inferenza del tipo generico è stata migliorata per determinare se i tipi di riferimento derivati devono essere nullable o meno. Si tratta di un impegno ottimale. Può produrre avvisi relativi ai vincoli di supporto dei valori null e può causare avvisi Nullable quando i tipi dedotti dell'overload selezionato vengono applicati agli argomenti.

### <a name="the-first-phase"></a>Prima fase

I tipi di riferimento Nullable scorrono nei limiti delle espressioni iniziali, come descritto di seguito. Inoltre, `null` vengono introdotti due nuovi tipi di limiti, ovvero e `default` . Il loro scopo è quello di eseguire le occorrenze di `null` o `default` nelle espressioni di input, che possono causare il Nullable di un tipo derivato, anche in caso contrario. Funziona anche per i tipi nullable _value *, che sono stati migliorati per prelevare il "valore null" nel processo di inferenza.

La determinazione dei limiti da aggiungere nella prima fase viene migliorata nel modo seguente:

Se un argomento `Ei` ha un tipo di riferimento, il tipo `U` usato per l'inferenza dipende dallo stato null di e dal `Ei` tipo dichiarato:
- Se il tipo dichiarato è un tipo di riferimento non null `U0` o un tipo di riferimento Nullable, `U0?`
    - Se lo stato null di `Ei` è "not null", `U` sarà `U0`
    - Se lo stato null di `Ei` è "Maybe null", `U` sarà `U0?`
- In caso contrario `Ei` , se ha un tipo dichiarato, `U` è quel tipo
- In caso contrario, se `Ei` è `null` quindi `U` il limite speciale `null`
- In caso contrario, se `Ei` è `default` quindi `U` il limite speciale `default`
- In caso contrario, non viene eseguita alcuna inferenza.

### <a name="exact-upper-bound-and-lower-bound-inferences"></a>Inferenze esatte, con associazione superiore e con associazione inferiore

In inferenza *dal* tipo `U` *al* tipo `V` , se `V` è un tipo di riferimento Nullable `V0?` , `V0` viene usato anziché `V` nelle clausole seguenti.
- Se `V` è una delle variabili di tipo non fixed, `U` viene aggiunto come limite esatto, superiore o inferiore come prima
- In caso contrario, se `U` è `null` o `default` , non viene eseguita alcuna inferenza
- In caso contrario, se `U` è un tipo `U0?` di riferimento Nullable, `U0` viene utilizzato anziché `U` nelle clausole successive.

L'essenza è che i valori null che riguardano direttamente una delle variabili di tipo non fisso vengono conservati nei limiti. Per gli inferenza che rimaledicono ulteriormente i tipi di origine e di destinazione, d'altra parte, il supporto dei valori null viene ignorato. Può corrispondere o meno, ma in caso contrario, verrà emesso un avviso in un secondo momento se viene scelto e applicato l'overload.

### <a name="fixing"></a>correzione di 

La specifica attualmente non esegue un processo valido per descrivere cosa accade quando più limiti sono convertibili tra loro identità, ma sono diversi. Questo problema può verificarsi tra `object` e `dynamic` , tra i tipi di tupla che differiscono solo per i nomi di elementi, tra i tipi costruiti e ora anche tra `C` e `C?` per i tipi di riferimento.

È inoltre necessario propagare "null" dalle espressioni di input al tipo di risultato.

Per gestire queste operazioni, è necessario aggiungere altre fasi per la correzione, che ora è:

1. Raccogliere tutti i tipi in tutti i limiti come candidati, rimuovendo `?` da tutti i tipi di riferimento Nullable
2. Elimina i candidati in base ai requisiti dei limiti esatti, inferiore e superiore (mantenimento `null` e `default` limiti)
3. Elimina i candidati che non dispongono di una conversione implicita a tutti gli altri candidati
4. Se i candidati rimanenti non hanno tutte le conversioni di identità tra loro, l'inferenza del tipo ha esito negativo
5. *Unisci* i candidati rimanenti come descritto di seguito
6. Se il candidato risultante è un tipo di riferimento o un tipo di valore non null e *tutti* i limiti esatti o *uno* dei limiti inferiori sono tipi di valore Nullable, tipi di riferimento Nullable `null` o `default` , `?` viene aggiunto al candidato risultante, rendendolo un tipo di valore o un tipo di riferimento Nullable.

L' *Unione* viene descritta tra due tipi candidati. Si tratta di transitive e commutative, quindi i candidati possono essere Uniti in qualsiasi ordine con lo stesso risultato finale. Non è definito se i due tipi candidati non sono convertibili in identità.

La funzione *merge* accetta due tipi candidati e una direzione ( *+* o *-* ):

- *Unisci* ( `T` , `T` , *d* ) = T
- *Merge* ( `S` , `T?` , *+* ) = *merge* ( `S?` , `T` , *+* ) = *merge* ( `S` , `T` , *+* )`?`
- *Merge* ( `S` , `T?` , *-* ) = *merge* ( `S?` , `T` , *-* ) = *merge* ( `S` , `T` , *-* )
- *Merge* ( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *+* ) = `C<` *merge* ( `S1` , `T1` , *D1* ) `,...,` *merge* ( `Sn` , `Tn` , *DN* ) `>` , *dove*
    - `di` = *+* Se il `i` parametro di tipo ' th di `C<...>` è covariante
    - `di` = *-* Se il `i` parametro di tipo ' th di `C<...>` è contra o invariante
- *Merge* ( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *-* ) = `C<` *merge* ( `S1` , `T1` , *D1* ) `,...,` *merge* ( `Sn` , `Tn` , *DN* ) `>` , *dove*
    - `di` = *-* Se il `i` parametro di tipo ' th di `C<...>` è covariante
    - `di` = *+* Se il `i` parametro di tipo ' th di `C<...>` è contra o invariante
- *Merge* ( `(S1 s1,..., Sn sn)` , `(T1 t1,..., Tn tn)` , *d* ) = `(` *merge* ( `S1` , `T1` , *d* ) `n1,...,` *merge* ( `Sn` , `Tn` , *d* ) `nn)` , *dove*
    - `ni` è assente se `si` e `ti` differiscono o se entrambi sono assenti
    - `ni``si`se `si` e `ti` sono uguali
- *Merge* ( `object` , `dynamic` ) = *merge* ( `dynamic` , `object` ) = `dynamic`

## <a name="warnings"></a>Avvisi

### <a name="potential-null-assignment"></a>Potenziale assegnazione null

### <a name="potential-null-dereference"></a>Potenziale dereferenziazione null

### <a name="constraint-nullability-mismatch"></a>Mancata corrispondenza dei valori null per i vincoli

### <a name="nullable-types-in-disabled-annotation-context"></a>Tipi nullable nel contesto di annotazione disabilitato

### <a name="override-and-implementation-nullability-mismatch"></a>Override e implementazione del supporto dei valori null non corrispondenti

## <a name="attributes-for-special-null-behavior"></a>Attributi per un comportamento null speciale

