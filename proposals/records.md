---
ms.openlocfilehash: 1bf904965c2eacfdeb28aace93e1361881f1b0bd
ms.sourcegitcommit: 6c404867b9f1ab70c4c4960ddf821b52579ed1fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84305478"
---

# <a name="records-work-in-progress"></a>Record di lavoro in corso

Diversamente dalle altre proposte di record, non si tratta di una proposta di per sé, ma è stata progettata per registrare le decisioni di progettazione di consenso per la funzionalità dei record. I dettagli della specifica verranno aggiunti in modo necessario per risolvere le domande.

La sintassi per un record è proposta per essere aggiunta come indicato di seguito:

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      parameter_list? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      parameter_list? struct_interfaces? type_parameter_constraints_clauses? struct_body
    ;

class_body
    : '{' class_member_declarations? '}'
    | ';'
    ;

struct_body
    : '{' struct_members_declarations? '}'
    | ';'
    ;
```

Il `attributes` non terminale consentirà anche un nuovo attributo contestuale, `data` .

Una classe (struct) dichiarata con un elenco di parametri o un `data` modificatore viene chiamata classe di record (struct di record), che è un tipo di record.

È un errore dichiarare un tipo di record senza il `data` modificatore.

## <a name="members-of-a-record-type"></a>Membri di un tipo di record

Oltre ai membri dichiarati nel corpo della classe o dello struct, un tipo di record presenta i membri aggiuntivi seguenti:

### <a name="equality-members"></a>Membri di uguaglianza

I tipi di record producono implementazioni sintetizzate per i metodi seguenti:

* `object.GetHashCode()`eseguire l'override di, a meno che non sia sealed o fornito dall'utente
* `object.Equals(object)`eseguire l'override di, a meno che non sia sealed o fornito dall'utente
* `T Equals(T)`Metodo, dove `T` è il tipo corrente

`T Equals(T)`viene specificato per eseguire l'uguaglianza dei valori in modo che `Equals` sia true solo se tutti i campi di istanza dichiarati nel tipo di ricevitore sono uguali ai campi dell'altro tipo.

`object.Equals`esegue l'equivalente di

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a>Copiare e clonare i membri

Un tipo di record contiene due membri di copia sintetizzati se i metodi con la stessa firma non sono già stati dichiarati all'interno del tipo di record:

* Costruttore protetto che accetta un solo argomento del tipo di record.
* Metodo di istanza pubblico senza parametri denominato `Clone` che restituisce il tipo di record.

Il costruttore protetto viene definito "costruttore di copia" e il corpo sintetizzato copia i valori di tutti i campi di istanza dichiarati nel tipo di input nei campi corrispondenti di `this` .

Il `Clone` metodo restituisce il risultato di una chiamata a un costruttore con la stessa firma del costruttore di copia.

## <a name="positional-record-members"></a>Membri record posizionali

Oltre ai membri precedenti, i record con un elenco di parametri ("record posizionali") dispongono dei membri seguenti:

### <a name="primary-constructor"></a>Costruttore primario

Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo. Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente. È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.

In fase di esecuzione il costruttore primario

1. esegue gli inizializzatori di campo di istanza visualizzati nel corpo della classe; quindi richiama il costruttore della classe base senza argomenti.

1. Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore

### <a name="properties"></a>Proprietà

Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value. Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con il nome e il tipo specificati viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:

Per uno struct di record o una classe di record:

* `get` `init` Viene creata una proprietà pubblica e automatica (vedere Specifica della `init` funzione di accesso separata). Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente. Ogni funzione di accesso get della proprietà astratta "corrispondente" ereditata viene sottoposta a override.


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

Un' `with` espressione valida ha un ricevitore con un tipo non void. Il tipo di ricevitore deve contenere un metodo di istanza senza parametri accessibile denominato `Clone` il cui tipo restituito deve essere il tipo di ricevitore o un tipo di base.

Sul lato destro dell' `with` espressione è presente una `member_initializer_list` con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo restituito del `Clone()` metodo.

Ogni `member_initializer` viene elaborata allo stesso modo di un'assegnazione al campo o alla destinazione della proprietà sul valore restituito del `Clone()` metodo. Il `Clone()` metodo viene eseguito una sola volta e le assegnazioni vengono elaborate in ordine lessicale.
