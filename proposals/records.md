---
ms.openlocfilehash: ffa34ad55752197bc2d8fb6cac7759602a2672c9
ms.sourcegitcommit: 5c9b8f27bd8299c70e2f4205a46079a10cffce76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533346"
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
I membri vengono sintetizzati, a meno che un membro concreto (non astratto) accessibile con una firma "corrispondente" sia ereditato o dichiarato nel corpo del record. Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.

I membri sintetizzati sono i seguenti:

### <a name="equality-members"></a>Membri di uguaglianza

I tipi di record producono implementazioni sintetizzate per i metodi seguenti, dove `T` è il tipo che lo contiene:

* `object.GetHashCode()`override
* `object.Equals(object)`override
* `T Equals(T)`Metodo, dove `T` è il tipo corrente
* `Type EqualityContract`Proprietà solo Get

Se `object.GetHashCode()` o `object.Equals(object)` sono sealed, viene generato un errore.

`EqualityContract`è una proprietà dell'istanza virtuale che restituisce `typeof(T)` . Se il tipo di base definisce un oggetto `EqualityContract` , ne viene eseguito l'override nel record derivato. Se la base `EqualityContract` è sealed o non virtuale, viene generato un errore.

`T Equals(T)`viene specificato per eseguire l'uguaglianza dei valori `Equals` in modo che sia true solo se tutti i campi di istanza accessibili nel ricevitore sono uguali ai campi del parametro e è `this.EqualityContract` uguale a `other.EqualityContract` .

`object.Equals`esegue l'equivalente di

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a>Copiare e clonare i membri

Un tipo di record contiene due membri di copia sintetizzati:

* Costruttore protetto che accetta un solo argomento del tipo di record.
* Metodo "clone" dell'istanza virtuale senza parametri pubblico con un nome riservato dal compilatore

Il costruttore protetto viene definito "costruttore di copia" e il corpo sintetizzato copia i valori di tutti i campi di istanza accessibili nel tipo di input nei campi corrispondenti di `this` .

Il metodo "clone" restituisce il risultato di una chiamata a un costruttore con la stessa firma del costruttore di copia. Il tipo restituito del metodo Clone è il tipo che lo contiene, a meno che non sia presente un metodo clone virtuale nella classe di base. In tal caso, il tipo restituito è il tipo contenitore corrente se la funzionalità "revariante Returns" è supportata e il tipo restituito di override in caso contrario. Il metodo Clone sintetizzato è un override del metodo clone del tipo di base se ne esiste uno. Se il metodo clone del tipo di base è sealed, viene generato un errore.

Se il record contenitore è astratto, anche il metodo Clone sintetizzato è astratto.

## <a name="positional-record-members"></a>Membri record posizionali

Oltre ai membri precedenti, i record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.

### <a name="primary-constructor"></a>Costruttore primario

Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo. Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente. È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.

In fase di esecuzione il costruttore primario

1. esegue gli inizializzatori di istanza visualizzati nel corpo della classe

1. richiama il costruttore della classe base con gli argomenti specificati nella `record_base` clausola, se presente


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
