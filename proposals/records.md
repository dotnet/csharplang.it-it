---
ms.openlocfilehash: 35f6836e20776450ce5f776e7fdb66ca634d04a0
ms.sourcegitcommit: 0f56445e250ddf82b88848b94c59870f13ab8ffc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84663267"
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
    | ':' class_type argument_list? interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}'
    | ';'
    ;
```

I tipi di record sono tipi di riferimento, simili a una dichiarazione di classe. È un errore che un record fornisca un oggetto `record_base` `argument_list` se `record_declaration` non contiene un oggetto `parameter_list` .

## <a name="members-of-a-record-type"></a>Membri di un tipo di record

Oltre ai membri dichiarati nel corpo del record, un tipo di record ha membri sintetizzati aggiuntivi.
I membri vengono sintetizzati a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo del record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.
Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.

I membri sintetizzati sono i seguenti:

### <a name="equality-members"></a>Membri di uguaglianza

I tipi di record producono implementazioni sintetizzate dei metodi seguenti, dove `T` è il tipo che lo contiene:
```C#
public override int GetHashCode();
public override bool Equals(object other);
public virtual bool Equals(T other);
```
`GetHashCode()`e `Equals(object other)` sono override dei metodi virtuali in `System.Object` .
Quando si esegue l'override di tutti i metodi delle classi di base intermedie che nascondono tali metodi viene ignorato.

Anche i tipi di record derivati eseguono l'override del `Equals(TBase other)` metodo da ogni tipo di record di base.

Il tipo di record sintetizza un'implementazione di `System.IEquatable<T>` che viene implementata in modo implicito da `Equals(T other)` Where `T` è il tipo che lo contiene.
I tipi di record non sintetizzano le implementazioni di `System.IEquatable<TBase>` per qualsiasi tipo di base `TBase` , anche se tali interfacce sono implementate dai tipi di record di base.

La classe di record di base sintetizza una `EqualityContract` Proprietà. La proprietà viene sottoposta a override nelle classi di record derivate. Le implementazioni sintetizzate restituiscono `typeof(T)` Where che `T` contiene il tipo.
```C#
protected virtual Type EqualityContract { get; }
```

Si tratta di un errore se le implementazioni di base di uno dei membri sottoposti a override sono sealed o non virtuali oppure non corrispondono alla firma e all'accessibilità previste.

`Equals(T other)`Restituisce true solo se si verificano tutti i termini seguenti:
- `other`non è `null` , e
- Per ogni campo dichiarato nel tipo di record, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo e
- Se è presente un tipo di record di base, il valore di `base.Equals(other)` ; in caso contrario, il valore di `EqualityContract.Equals(other.EqualityContract)` .

Le sostituzioni di `Equals(T other)` per i metodi di base, incluso `object.Equals(object other)` , eseguono l'equivalente di:
```C#
public override bool Equals(object other) => Equals(other as T);
```

`GetHashCode()`Restituisce il `int` risultato di una funzione deterministica che accetta i valori seguenti:
- Per ogni campo dichiarato nel tipo di record, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` dove `TN` è il tipo di campo e
- Se è presente un tipo di record di base, il valore di `base.GetHashCode()` ; in caso contrario, il valore di `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .

### <a name="copy-and-clone-members"></a>Copiare e clonare i membri

Un tipo di record contiene due membri di copia:

* Costruttore che accetta un solo argomento del tipo di record. Viene definito "costruttore di copia".
* Metodo "clone" dell'istanza virtuale senza parametri pubblico sintetizzato con un nome riservato dal compilatore

Lo scopo del costruttore di copia è copiare lo stato dal parametro alla nuova istanza creata. Questo costruttore non esegue gli inizializzatori di proprietà o campi di istanza presenti nella dichiarazione di record. Se il costruttore non è dichiarato in modo esplicito, un costruttore protetto verrà sintetizzato dal compilatore.
Per prima cosa, il costruttore deve eseguire la chiamata a un costruttore di copia della base o a un costruttore di oggetti senza parametri se il record eredita dall'oggetto. Se un costruttore di copia definito dall'utente utilizza un inizializzatore di costruttore implicito o esplicito che non soddisfa questo requisito, viene restituito un errore.
Dopo che un costruttore di copia di base viene richiamato, un costruttore di copia sintetizzato copia i valori per tutti i campi di istanza dichiarati in modo implicito o esplicito nel tipo di record.

Il metodo "clone" restituisce il risultato di una chiamata a un costruttore con la stessa firma del costruttore di copia. Il tipo restituito del metodo Clone è il tipo che lo contiene, a meno che non sia presente un metodo clone virtuale nella classe di base. In tal caso, il tipo restituito è il tipo contenitore corrente se la funzionalità "revariante Returns" è supportata e il tipo restituito di override in caso contrario. Il metodo Clone sintetizzato è un override del metodo clone del tipo di base se ne esiste uno. Se il metodo clone del tipo di base è sealed, viene generato un errore.

Se il record contenitore è astratto, anche il metodo Clone sintetizzato è astratto.

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
  Ogni funzione di accesso astratta ereditata "corrispondente" viene sottoposta a override. La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.

### <a name="deconstruct"></a>Decostruire

Un record posizionale sintetizza un metodo pubblico che restituisce un valore void denominato Deconstruct con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario. Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario. Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.

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

Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con modifiche nelle assegnazioni in `member_initializer_list` .

Un' `with` espressione valida ha un ricevitore con un tipo non void. Il tipo di ricevitore deve contenere un metodo "clone" del record sintetizzato accessibile.

Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo restituito del `Clone()` metodo.

Ogni `member_initializer` viene elaborato allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al valore restituito del metodo clone del record. Il metodo Clone viene eseguito una sola volta e le assegnazioni vengono elaborate in ordine lessicale.
