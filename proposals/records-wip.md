---
ms.openlocfilehash: 32682010c79073aabbc71ef9de433a7a76138c3f
ms.sourcegitcommit: c30039481ee8a75c3b3e4ddd369fdf8f84f8945b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82596757"
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

Il `attributes` non terminale consentirà anche un nuovo attributo contestuale `data`,.

Una classe (struct) dichiarata con un elenco `data` di parametri o un modificatore viene chiamata classe di record (struct di record), che è un tipo di record.

È un errore dichiarare un tipo di record senza un elenco di parametri e il `data` modificatore.

## <a name="members-of-a-record-type"></a>Membri di un tipo di record

Oltre ai membri dichiarati nel corpo della classe o dello struct, un tipo di record presenta i membri aggiuntivi seguenti:

### <a name="primary-constructor"></a>Costruttore primario

Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo. Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente. È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.

In fase di esecuzione il costruttore primario

1. esegue gli inizializzatori di campo di istanza visualizzati nel corpo della classe; quindi richiama il costruttore della classe base senza argomenti.

1. Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore

### <a name="properties"></a>Proprietà

Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value. Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con il nome e il tipo specificati viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:

Per uno struct di record o una classe di record:

* Viene creata una proprietà automatica Get-only pubblica. Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente. Ogni funzione di accesso get della proprietà astratta "corrispondente" ereditata viene sottoposta a override.

  * Questa proprietà è anche `initonly`, vale a dire che il campo sottostante può essere modificato nell'espressione [with](#With) sotto, se la `get` funzione di accesso corrispondente è accessibile

### <a name="equality-members"></a>Membri di uguaglianza

I tipi di record producono implementazioni sintetizzate per i metodi seguenti:

* `object.GetHashCode()`eseguire l'override di, a meno che non sia sealed o fornito dall'utente
* `object.Equals(object)`eseguire l'override di, a meno che non sia sealed o fornito dall'utente
* `T Equals(T)`Metodo, dove `T` è il tipo corrente

`T Equals(T)`viene specificato per eseguire l'uguaglianza dei valori, confrontando la proprietà con lo stesso nome di ogni parametro del costruttore primario per la proprietà corrispondente dell'altro tipo.
`object.Equals`esegue l'equivalente di

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a>Espressione `with`

Un' `with` espressione è una nuova espressione che usa la sintassi seguente.

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con le modifiche alle proprietà elencate in `anonymous_object_initializer`.

Un'espressione `with` valida ha un ricevitore con un tipo non void. Il tipo di ricevitore deve contenere un metodo di istanza senza parametri `Clone` accessibile denominato il cui tipo restituito deve essere il tipo di ricevitore o un tipo di base.

`with` Sul lato destro dell'espressione è presente una `anonymous_object_initializer` con una sequenza di assegnazioni, ognuna con una `initonly` proprietà (vedere [Proprietà](#Properties)) del tipo `Clone` restituito sul lato sinistro dell'assegnazione (come chiamata di proprietà) e un'espressione arbitraria sul lato destro, che è implicitamente convertibile nel tipo della proprietà.

La valutazione di un' `with` espressione chiama il `Clone` metodo esattamente una volta, quindi imposta il campo sottostante di ogni `initonly` proprietà nell'elenco di argomenti sul valore dell'espressione corrispondente, in ordine lessicale, usando il risultato della chiamata al `Clone` metodo come ricevitore. Il tipo dell' `with` espressione è uguale al tipo restituito del `Clone` metodo.

