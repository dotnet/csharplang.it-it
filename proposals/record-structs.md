---
ms.openlocfilehash: 519d546800218ac49ffdeb2d61cb311bbc57091c
ms.sourcegitcommit: 224a2ffa412eb2e7890a6b6e2c39d9ae93776a70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2021
ms.locfileid: "101103040"
---
# <a name="record-structs"></a>Struct di record

La sintassi per uno struct di record è la seguente:

```antlr
record_struct_declaration
    : attributes? struct_modifier* 'partial'? 'record' 'struct' identifier type_parameter_list?
      parameter_list? struct_interfaces? type_parameter_constraints_clause* record_struct_body
    ;

record_struct_body
    : struct_body
    | ';'
    ;
```

I tipi di struct di record sono tipi di valore, come altri tipi struct. Ereditano in modo implicito dalla classe `System.ValueType` .
I modificatori e i membri di uno struct di record sono soggetti alle stesse restrizioni di quelle degli struct (accessibilità sul tipo, modificatori sui membri, `base(...)` inizializzatori del costruttore di istanza, assegnazione definita per `this` nel costruttore, distruttori,...). Gli struct di record seguiranno anche le stesse regole degli struct per i costruttori di istanze senza parametri e gli inizializzatori di campo, ma in questo documento si presuppone che le restrizioni per gli struct vengano elevate in generale.

Vedere https://github.com/dotnet/csharplang/blob/master/spec/structs.md

Gli struct di record non possono usare il `ref` modificatore.

Al massimo una dichiarazione di tipo parziale di uno struct di record parziale può fornire un `parameter_list` .
Il `parameter_list` non può essere vuoto.

I parametri della struct di record non possono usare i `ref` `out` `this` modificatori o (ma `in` `params` sono consentiti e).

## <a name="members-of-a-record-struct"></a>Membri di uno struct di record

Oltre ai membri dichiarati nel corpo dello struct di record, un tipo di struct di record ha membri sintetizzati aggiuntivi.
I membri vengono sintetizzati, a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo della struct di record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.
Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.
Vedere https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading

Per un membro di uno struct di record il nome "clone" è un errore.

Non è possibile specificare un tipo unsafe per un campo di istanza di uno struct di record.

Uno struct di record non è autorizzato a dichiarare un distruttore.

I membri sintetizzati sono i seguenti:

### <a name="equality-members"></a>Membri di uguaglianza

I membri di uguaglianza sintetizzati sono simili a quelli di una classe di record ( `Equals` per questo tipo, `Equals` per il `object` tipo `==` e per `!=` gli operatori di questo tipo), \
eccetto la mancanza di `EqualityContract` , i controlli null o l'ereditarietà.

Lo struct di record implementa `System.IEquatable<R>` e include un overload fortemente tipizzato sintetizzato di `Equals(R other)` dove `R` è lo struct di record.
Il metodo è `public` .
Il metodo può essere dichiarato in modo esplicito. Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.

Se `Equals(R other)` è definito dall'utente (non sintetizzato) ma `GetHashCode` non è, viene generato un avviso.

```C#
public bool Equals(R other);
```

L'oggetto sintetizzato `Equals(R)` restituisce `true` se e solo se per ogni campo di istanza `fieldN` nello struct di record è il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo è `true` .

Lo struct di record include `==` gli operatori e sintetizzati `!=` equivalenti agli operatori dichiarati come segue:
```C#
public static bool operator==(R r1, R r2)
    => r1.Equals(r2);
public static bool operator!=(R r1, R r2)
    => !(r1 == r2);
```
Il `Equals` metodo chiamato dall' `==` operatore è il `Equals(R other)` metodo specificato in precedenza. L' `!=` operatore delega all' `==` operatore. Se gli operatori sono dichiarati in modo esplicito, si tratta di un errore.

Lo struct di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
public override bool Equals(object? obj);
```
Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore. L'override sintetizzato restituisce `other is R temp && Equals(temp)` dove `R` è lo struct di record.

Lo struct di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
public override int GetHashCode();
```
Il metodo può essere dichiarato in modo esplicito.

Viene segnalato un avviso se uno di `Equals(R)` e `GetHashCode()` viene dichiarato in modo esplicito, ma l'altro metodo non è esplicito.

L'override sintetizzato di `GetHashCode()` restituisce il `int` risultato della combinazione dei valori di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` per ogni campo di istanza `fieldN` con `TN` il tipo di `fieldN` .

Si consideri ad esempio il seguente struct di record:
```C#
record struct R1(T1 P1, T2 P2);
```

Per questo struct di record, i membri di uguaglianza sintetizzati sono simili ai seguenti:
```C#
struct R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    public T2 P2 { get; set; }
    public override bool Equals(object? obj) => obj is R1 temp && Equals(temp);
    public bool Equals(R1 other)
    {
        return
            EqualityComparer<T1>.Default.Equals(P1, other.P1) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public static bool operator==(R1 r1, R1 r2)
        => r1.Equals(r2);
    public static bool operator!=(R1 r1, R1 r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(
            EqualityComparer<T1>.Default.GetHashCode(P1),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}
```

### <a name="printing-members-printmembers-and-tostring-methods"></a>Membri di stampa: Metodi PrintMembers e ToString

Lo struct di record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
private bool PrintMembers(System.Text.StringBuilder builder);
```

Il metodo esegue le operazioni seguenti:
1. per ogni membro stampabile dello struct di record (campo pubblico non statico e membri di proprietà leggibili), aggiunge il nome del membro seguito da "=" seguito dal valore del membro separato con ",",
2. Restituisce true se lo struct di record dispone di membri stampabili.

Per un membro con un tipo di valore, il relativo valore viene convertito in una rappresentazione di stringa utilizzando il metodo più efficiente disponibile per la piattaforma di destinazione. Al momento, significa chiamare `ToString` prima di passare a `StringBuilder.Append` .

Il `PrintMembers` metodo può essere dichiarato in modo esplicito.
Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.

Lo struct di record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:
```C#
public override string ToString();
```

Il metodo può essere dichiarato in modo esplicito. Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.

Il metodo sintetizzato:
1. Crea un' `StringBuilder` istanza di,
2. Accoda il nome dello struct di record al generatore, seguito da "{",
3. richiama il metodo dello struct di record `PrintMembers` che lo assegna al generatore, seguito da "" se restituisce true.
4. Accoda "}",
5. Restituisce il contenuto del generatore con `builder.ToString()` .

Si consideri ad esempio il seguente struct di record:

``` csharp
record struct R1(T1 P1, T2 P2);
```

Per questo struct di record, i membri di stampa sintetizzati sono simili ai seguenti:

```C#
struct R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    public T2 P2 { get; set; }

    private bool PrintMembers(StringBuilder builder)
    {
        builder.Append(nameof(P1));
        builder.Append(" = ");
        builder.Append(this.P1); // or builder.Append(this.P1.ToString()); if P1 has a value type
        builder.Append(", ");

        builder.Append(nameof(P2));
        builder.Append(" = ");
        builder.Append(this.P2); // or builder.Append(this.P2.ToString()); if P2 has a value type

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
```

## <a name="positional-record-struct-members"></a>Membri struct dei record posizionali

Oltre ai membri precedenti, gli struct di record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.

### <a name="primary-constructor"></a>Costruttore primario

Uno struct di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo. Questo metodo è denominato costruttore primario per il tipo. È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nello struct.
Uno struct di record non è autorizzato a dichiarare un costruttore primario senza parametri.

Le dichiarazioni dei campi dell'istanza per uno struct di record possono includere inizializzatori di variabile.
Se non è presente alcun costruttore primario, gli inizializzatori di istanza vengono eseguiti come parte del costruttore senza parametri.
In caso contrario, in fase di esecuzione il costruttore primario esegue gli inizializzatori di istanza visualizzati nel corpo della struttura di record.

Se uno struct di record ha un costruttore primario, qualsiasi costruttore definito dall'utente, ad eccezione del costruttore di copia, deve disporre di un `this` inizializzatore di costruttore esplicito.

I parametri del costruttore primario e i membri dello struct di record sono inclusi nell'ambito all'interno degli inizializzatori dei campi o delle proprietà dell'istanza. I membri di istanza sarebbero un errore in queste posizioni (analogamente a come i membri dell'istanza sono inclusi nell'ambito negli inizializzatori di costruttori normali, ma un errore da usare), ma i parametri del costruttore primario sarebbero inclusi nell'ambito e utilizzabili e verrebbero nascosti ai membri. I membri statici sarebbero anche utilizzabili.

Se non viene letto un parametro del costruttore primario, viene generato un avviso.

Le regole assigment definite per i costruttori di istanze struct si applicano al costruttore primario di struct di record. Ad esempio, di seguito è riportato un errore:

```csharp
record struct Pos(int X) // definite assignment error in primary constructor
{
    private int x;
    public int X { get { return x; } set { x = value; } } = X;
}
```

### <a name="properties"></a>Proprietà

Per ogni parametro dello struct di record di una dichiarazione di struct di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.

Per uno struct di record:

* `get` `init` Viene creata una proprietà pubblica e automatica se lo struct di record ha un `readonly` modificatore `get` e `set` in caso contrario.
  Entrambi i tipi di funzioni di accesso set ( `set` e `init` ) sono considerati "corrispondenti". Quindi, l'utente può dichiarare una proprietà solo init al posto di un oggetto modificabile sintetizzato.
  Viene eseguito l'override di una proprietà ereditata `abstract` con tipo corrispondente.
  Si tratta di un errore se la proprietà ereditata non dispone `public` `get` di `set` / `init` funzioni di accesso e.
  La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.
  Gli attributi possono essere applicati alla proprietà automatica sintetizzata e al relativo campo sottostante usando `property:` `field:` le destinazioni o per gli attributi applicati in modo sintattico al parametro corrispondente dello struct di record.  

### <a name="deconstruct"></a>Decostruire

Uno struct di record posizionali con almeno un parametro sintetizza un metodo di istanza pubblico che restituisce void denominato `Deconstruct` con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario. Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario. Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.
Il metodo può essere dichiarato in modo esplicito. Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure è statica.

# <a name="allow-with-expression-on-structs"></a>Consenti `with` espressione sugli struct

È ora valido affinché il ricevitore in un' `with` espressione disponga di un tipo struct.

Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo del destinatario.

Per un ricevitore con tipo struct, il ricevitore viene prima copiato, quindi ogni `member_initializer` viene elaborato allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al risultato della conversione. Le assegnazioni vengono elaborate in ordine lessicale.

# <a name="improvements-on-records"></a>Miglioramenti ai record

## <a name="allow-record-class"></a>Consentire `record class`

La sintassi esistente per i tipi di record consente di usare lo `record class` stesso significato di `record` :

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' 'class'? identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;
```

## <a name="allow-user-defined-positional-members-to-be-fields"></a>Consenti i membri posizionali definiti dall'utente come campi

Vedere https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter

C'è un problema di compatibilità con la compatibilità, quindi è possibile eliminare questa funzionalità oppure è necessario implementarla rapidamente, come correzione di bug.

```csharp
public record Base
{
    public int Field;
}
public record Derived(int Field);
```

# <a name="allow-parameterless-constructors-and-member-initializers-in-structs"></a>Consenti costruttori e inizializzatori di membri senza parametri negli struct

In struct verranno supportati sia costruttori senza parametri che inizializzatori di membri.
Questa operazione verrà specificata in altri dettagli.

Note non elaborate:  
Consenti ctor senza parametri su struct e anche inizializzatori di campo (nessun rilevamento Runtime)  
Verranno enumerati gli scenari in cui gli inizializzatori non vengono valutati: matrici, generics, default...  
Si consideri la diagnostica per l'utilizzo di struct con un ctor senza parametri in alcuni di questi casi?  

# <a name="open-questions"></a>Domande aperte

- è consigliabile non consentire un costruttore definito dall'utente con una firma del costruttore di copia?
- confermare che si desidera impedire i membri denominati "clone".
- `with` sui generics? (può influire sulla progettazione per gli struct di record)
- Verificare che la logica sintetizzata `Equals` sia equivalente dal punto di vista funzionale all'implementazione di runtime, ad esempio float. Nan
- come riconoscere gli struct di record nei metadati (non è presente un metodo clone che è possibile utilizzare...)

## <a name="answered"></a>Rispose

- confermare che si vuole lasciare la progettazione PrintMembers (metodo separato che restituisce `bool` ) (risposta: Sì)
- confermare che non verrà consentito `record ref struct` (emettere con i `IEquatable<RefStruct>` campi di riferimento e) (risposta: Sì)
- confermare l'implementazione dei membri di uguaglianza. In alternativa `bool Equals(R other)` , gli operatori vengono sintetizzati `bool Equals(object? other)` e tutti semplicemente delegati a `ValueType.Equals` . (risposta: Sì)
- confermare che si desidera consentire gli inizializzatori di campo quando è presente un costruttore primario. Si vuole anche consentire i costruttori di struct senza parametri mentre ci troviamo (il problema dell'attivatore era apparentemente fisso)? (risposta: Sì, è necessario rivedere la specifica aggiornata in LDM)
- quanto si vuole dire sul `Combine` Metodo? (risposta: il minor possibile)
