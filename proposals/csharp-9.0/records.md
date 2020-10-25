---
ms.openlocfilehash: 4e6c45b68b68a38a00295d990f2bf35f65a9c424
ms.sourcegitcommit: 0d9762aa9ff13f8018860c8fcb474dd69c3b69e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492632"
---

# <a name="records"></a>Record

Questa proposta tiene traccia delle specifiche per la funzionalità record di C# 9, come stabilito dal team di progettazione del linguaggio C#.

La sintassi per un record è la seguente:

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;

record_base
    : ':' class_type argument_list?
    | ':' interface_type_list
    | ':' class_type argument_list? ',' interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}' ';'?
    | ';'
    ;
```

I tipi di record sono tipi di riferimento, simili a una dichiarazione di classe. È un errore che un record fornisca un oggetto `record_base` `argument_list` se `record_declaration` non contiene un oggetto `parameter_list` .
Al massimo una dichiarazione di tipo parziale di un record parziale può fornire un `parameter_list` .

I parametri di record non possono usare i `ref` `out` `this` modificatori o (ma `in` `params` sono consentiti e).

## <a name="inheritance"></a>Ereditarietà

I record non possono ereditare dalle classi, a meno che la classe non sia `object` e le classi non possano ereditare dai record. I record possono ereditare da altri record.

## <a name="members-of-a-record-type"></a>Membri di un tipo di record

Oltre ai membri dichiarati nel corpo del record, un tipo di record ha membri sintetizzati aggiuntivi.
I membri vengono sintetizzati a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo del record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.
Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.
Un membro di un record denominato "clone" non è un errore.

I membri sintetizzati sono i seguenti:

### <a name="equality-members"></a>Membri di uguaglianza

Se il record è derivato da `object` , il tipo di record include una proprietà di sola lettura sintetizzata equivalente a una proprietà dichiarata come indicato di seguito:
```C#
Type EqualityContract { get; };
```
La proprietà è `private` se il tipo di record è `sealed` . In caso contrario, la proprietà è `virtual` e `protected` .
La proprietà può essere dichiarata in modo esplicito. Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .

Se il tipo di record è derivato da un tipo di record di base `Base` , il tipo di record include una proprietà di sola lettura sintetizzata equivalente a una proprietà dichiarata come indicato di seguito:
```C#
protected override Type EqualityContract { get; };
```

La proprietà può essere dichiarata in modo esplicito. Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` . Si verifica un errore se la proprietà sintetizzata o dichiarata in modo esplicito non esegue l'override di una proprietà con questa firma nel tipo di record, `Base` ad esempio se la proprietà non è presente in `Base` , o sealed o not Virtual e così via.
La proprietà sintetizzata restituisce `typeof(R)` dove `R` è il tipo di record.

Il tipo di record implementa `System.IEquatable<R>` e include un overload fortemente tipizzato sintetizzato di `Equals(R? other)` dove `R` è il tipo di record.
Il metodo è `public` e il metodo è `virtual` a meno che il tipo di record non sia `sealed` .
Il metodo può essere dichiarato in modo esplicito. Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .

Se `Equals(R? other)` è definito dall'utente (non sintetizzato) ma `GetHashCode` non è, viene generato un avviso.

```C#
public virtual bool Equals(R? other);
```
Il risultato della sintesi `Equals(R?)` restituisce `true` se e solo se ciascuno dei seguenti è `true` :
- `other` non è `null` , e
- Per ogni campo di istanza `fieldN` nel tipo di record non ereditato, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo e
- Se è presente un tipo di record di base, il valore di `base.Equals(other)` (una chiamata non virtuale a `public virtual bool Equals(Base? other)` ); in caso contrario, il valore di `EqualityContract == other.EqualityContract` .

Il tipo di record include gli `==` operatori e sintetizzati `!=` equivalenti agli operatori dichiarati come segue:
```C#
public static bool operator==(R? r1, R? r2)
    => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
public static bool operator!=(R? r1, R? r2)
    => !(r1 == r2);
```
Il `Equals` metodo chiamato dall' `==` operatore è il `Equals(R? other)` metodo specificato in precedenza. L' `!=` operatore delega all' `==` operatore. Se gli operatori sono dichiarati in modo esplicito, si tratta di un errore.
    
Se il tipo di record è derivato da un tipo di record di base `Base` , il tipo di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
public sealed override bool Equals(Base? other);
```
Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore. Si tratta di un errore se il metodo non esegue l'override di un metodo con la stessa firma nel tipo di record `Base` , ad esempio se il metodo non è presente in `Base` , oppure sealed o non virtuale e così via.
L'override sintetizzato restituisce `Equals((object?)other)` .

Il tipo di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
public override bool Equals(object? obj);
```
Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore. Si tratta di un errore se il metodo non esegue l'override `object.Equals(object? obj)` , ad esempio a causa dello shadowing nei tipi di base intermedi e così via.
L'override sintetizzato restituisce `Equals(other as R)` dove `R` è il tipo di record.

Il tipo di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
public override int GetHashCode();
```
Il metodo può essere dichiarato in modo esplicito.
Si tratta di un errore se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` . Il metodo non viene sottoposta a override, `object.GetHashCode()` ad esempio a causa dello shadowing nei tipi di base intermedi e così via.
 
Viene segnalato un avviso se uno di `Equals(R?)` e `GetHashCode()` viene dichiarato in modo esplicito, ma l'altro metodo non è esplicito.

L'override sintetizzato di `GetHashCode()` restituisce `int` il risultato di una funzione deterministica che combina i valori seguenti:
- Per ogni campo di istanza `fieldN` nel tipo di record non ereditato, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` dove `TN` è il tipo di campo e
- Se è presente un tipo di record di base, il valore di `base.GetHashCode()` ; in caso contrario, il valore di `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .

Si considerino, ad esempio, i seguenti tipi di record:
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R3(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

Per questi tipi di record, i membri di uguaglianza sintetizzati sono simili ai seguenti:
```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    protected virtual Type EqualityContract => typeof(R1);
    public override bool Equals(object? obj) => Equals(obj as R1);
    public virtual bool Equals(R1? other)
    {
        return !(other is null) &&
            EqualityContract == other.EqualityContract &&
            EqualityComparer<T1>.Default.Equals(P1, other.P1);
    }
    public static bool operator==(R1? r1, R1? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R1? r1, R1? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(EqualityComparer<Type>.Default.GetHashCode(EqualityContract),
            EqualityComparer<T1>.Default.GetHashCode(P1));
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    protected override Type EqualityContract => typeof(R2);
    public override bool Equals(object? obj) => Equals(obj as R2);
    public sealed override bool Equals(R1? other) => Equals((object?)other);
    public virtual bool Equals(R2? other)
    {
        return base.Equals((R1?)other) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public static bool operator==(R2? r1, R2? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R2? r1, R2? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}

class R3 : R2, IEquatable<R3>
{
    public T3 P3 { get; init; }
    protected override Type EqualityContract => typeof(R3);
    public override bool Equals(object? obj) => Equals(obj as R3);
    public sealed override bool Equals(R2? other) => Equals((object?)other);
    public virtual bool Equals(R3? other)
    {
        return base.Equals((R2?)other) &&
            EqualityComparer<T3>.Default.Equals(P3, other.P3);
    }
    public static bool operator==(R3? r1, R3? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R3? r1, R3? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T3>.Default.GetHashCode(P3));
    }
}
```

### <a name="copy-and-clone-members"></a>Copiare e clonare i membri

Un tipo di record contiene due membri di copia:

* Costruttore che accetta un solo argomento del tipo di record. Viene definito "costruttore di copia".
* Metodo "clone" dell'istanza senza parametri pubblico sintetizzato con un nome riservato dal compilatore

Lo scopo del costruttore di copia è copiare lo stato dal parametro alla nuova istanza creata. Questo costruttore non esegue gli inizializzatori di proprietà o campi di istanza presenti nella dichiarazione di record. Se il costruttore non è dichiarato in modo esplicito, un costruttore verrà sintetizzato dal compilatore. Se il record è sealed, il costruttore sarà privato, in caso contrario verrà protetto.
Un costruttore di copia dichiarato in modo esplicito deve essere pubblico o protetto, a meno che il record non sia sealed.
Per prima cosa, il costruttore deve eseguire la chiamata a un costruttore di copia della base o a un costruttore di oggetti senza parametri se il record eredita dall'oggetto. Se un costruttore di copia definito dall'utente utilizza un inizializzatore di costruttore implicito o esplicito che non soddisfa questo requisito, viene restituito un errore.
Dopo che un costruttore di copia di base viene richiamato, un costruttore di copia sintetizzato copia i valori per tutti i campi di istanza dichiarati in modo implicito o esplicito nel tipo di record. L'unica presenza di un costruttore di copia, esplicito o implicito, non impedisce l'aggiunta automatica di un costruttore di istanza predefinito.

Se nel record di base è presente un metodo "clone" virtuale, il metodo "clone" sintetizzato ne esegue l'override e il tipo restituito del metodo è il tipo che lo contiene corrente se la funzionalità "ritorni covariante" è supportata e il tipo restituito di override in caso contrario. Se il metodo clone del record di base è sealed, viene generato un errore.
Se un metodo "clone" virtuale non è presente nel record di base, il tipo restituito del metodo Clone è il tipo che lo contiene e il metodo è virtuale, a meno che il record non sia sealed o abstract.
Se il record contenitore è astratto, anche il metodo Clone sintetizzato è astratto.
Se il metodo "clone" non è astratto, restituisce il risultato di una chiamata a un costruttore di copia. 


### <a name="printing-members-printmembers-and-tostring-methods"></a>Membri di stampa: Metodi PrintMembers e ToString

Se il record è derivato da `object` , il record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
bool PrintMembers(System.Text.StringBuilder builder);
```
Il metodo è `private` se il tipo di record è `sealed` . In caso contrario, il metodo è `virtual` e `protected` .

Il metodo si comporta come di seguito:
1. per ogni membro stampabile del record (campo pubblico non statico e membri di proprietà leggibili), aggiunge il nome di tale membro seguito da "=" seguito dal valore del membro: `this.member` , separato da ",",
2. Restituisce true se il record dispone di membri stampabili.

Se il tipo di record è derivato da un record di base `Base` , il record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
protected override bool PrintMembers(StringBuilder builder);
```

Se il record non dispone di membri stampabili, il metodo chiama il `PrintMembers` metodo di base con un argomento (il relativo `builder` parametro) e restituisce il risultato.

In caso contrario, il metodo:
1. chiama il `PrintMembers` metodo di base con un argomento (il relativo `builder` parametro),
2. Se il `PrintMembers` metodo ha restituito true, accodare "," al generatore,
3. per ogni membro stampabile del record, aggiunge il nome del membro seguito da "=" seguito dal valore del membro: `this.member` (o `this.member.ToString()` per i tipi di valore), separati da ",",
4. Restituisce true.

Il `PrintMembers` metodo può essere dichiarato in modo esplicito.
Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .

Il record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
public override string ToString();
```

Il metodo può essere dichiarato in modo esplicito. Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` . Il metodo non viene sottoposta a override, `object.ToString()` ad esempio a causa dello shadowing nei tipi di base intermedi e così via.

Il metodo sintetizzato:
1. Crea un' `StringBuilder` istanza di,
2. Accoda il nome del record al generatore, seguito da "{",
3. richiama il metodo del record `PrintMembers` che lo assegna al generatore, seguito da "" se restituisce true.
4. Accoda "}",
3. Restituisce il contenuto del generatore con `builder.ToString()` .

Si considerino, ad esempio, i seguenti tipi di record:

``` csharp
record R1(T1 P1);
record R2(T1 P1, T2 P2, T3 P3) : R1(P1);
```

Per questi tipi di record, i membri di stampa sintetizzati sarebbero simili ai seguenti:

```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    
    protected virtual bool PrintMembers(StringBuilder builder)
    {
        builder.Append(nameof(P1));
        builder.Append(" = ");
        builder.Append(this.P1); // or builder.Append(this.P1); if P1 has a value type
        
        return true;
    }
    
    public override string ToString()
    {
        var builder = new StringBuilder();
        builder.Append(nameof(R1));
        builder.Append(" { ");

        if (PrintMembers(builder))
            builder.Append(" ");

        builder.Append("}");
        return builder.ToString();
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    public T3 P3 { get; init; }
    
    protected override void PrintMembers(StringBuilder builder)
    {
        if (base.PrintMembers(builder))
            builder.Append(", ");
            
        builder.Append(nameof(P2));
        builder.Append(" = ");
        builder.Append(this.P2); // or builder.Append(this.P2); if P2 has a value type
        
        builder.Append(", ");
        
        builder.Append(nameof(P3));
        builder.Append(" = ");
        builder.Append(this.P3); // or builder.Append(this.P3); if P3 has a value type
        
        return true;
    }
    
    public override string ToString()
    {
        var builder = new StringBuilder();
        builder.Append(nameof(R2));
        builder.Append(" { ");

        if (PrintMembers(builder))
            builder.Append(" ");

        builder.Append("}");
        return builder.ToString();
    }
}
```

## <a name="positional-record-members"></a>Membri record posizionali

Oltre ai membri precedenti, i record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.

### <a name="primary-constructor"></a>Costruttore primario

Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo. Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente. È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.

In fase di esecuzione il costruttore primario

1. esegue gli inizializzatori di istanza visualizzati nel corpo della classe

1. richiama il costruttore della classe base con gli argomenti specificati nella `record_base` clausola, se presente

Se un record ha un costruttore primario, qualsiasi costruttore definito dall'utente, ad eccezione del costruttore di copia, deve disporre di un `this` inizializzatore di costruttore esplicito. 

I parametri del costruttore primario e i membri del record si trovano nell'ambito all'interno della `argument_list` clausola della `record_base` clausola e negli inizializzatori dei campi o delle proprietà dell'istanza. I membri di istanza sarebbero un errore in queste posizioni (analogamente a come i membri dell'istanza sono inclusi nell'ambito negli inizializzatori di costruttori normali, ma un errore da usare), ma i parametri del costruttore primario sarebbero inclusi nell'ambito e utilizzabili e verrebbero nascosti ai membri. I membri statici sarebbero anche utilizzabili, in modo analogo al funzionamento delle chiamate e degli inizializzatori di base nei costruttori normali. 

Le variabili di espressione dichiarate nell'oggetto sono incluse nell' `argument_list` ambito di `argument_list` . Si applicano le stesse regole di shadowing in un elenco di argomenti di un inizializzatore di costruttore normale.

### <a name="properties"></a>Proprietà

Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.

Per un record:

* `get` `init` Viene creata una proprietà pubblica e automatica (vedere Specifica della `init` funzione di accesso separata).
  Viene eseguito l'override di una proprietà ereditata `abstract` con tipo corrispondente.
  Si tratta di un errore se la proprietà ereditata non dispone `public` `get` di funzioni di accesso e sottoponibili a override `init` .
  La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.
  Gli attributi possono essere applicati alla proprietà automatica sintetizzata e al campo sottostante usando `property:` `field:` le destinazioni o per gli attributi applicati in modo sintattico al parametro record corrispondente.  

### <a name="deconstruct"></a>Decostruire

Un record posizionale con almeno un parametro sintetizza un metodo di istanza pubblico che restituisce void denominato Deconstruct con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario. Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario. Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.
Il metodo può essere dichiarato in modo esplicito. Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure è statica.

## <a name="with-expression"></a>Espressione `with`

Un' `with` espressione è una nuova espressione che usa la sintassi seguente.

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' '{' member_initializer_list? '}'
    ;

member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : identifier '=' expression
    ;
```
Un' `with` espressione non è consentita come istruzione.

Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con modifiche nelle assegnazioni in `member_initializer_list` .

Un' `with` espressione valida ha un ricevitore con un tipo non void. Il tipo di ricevitore deve essere un record.

Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo del destinatario.

Innanzitutto, viene richiamato il metodo "clone" del destinatario (specificato sopra) e il risultato viene convertito nel tipo del destinatario. Ognuna viene quindi `member_initializer` elaborata allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al risultato della conversione. Le assegnazioni vengono elaborate in ordine lessicale.
