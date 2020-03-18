---
ms.openlocfilehash: 25756c1811d5e6dc97512ce70f99ab7fefa91c4a
ms.sourcegitcommit: 2a6dffb60718065ece95df75e1cc7eb509e48a8d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "79485238"
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

Il `attributes` non terminale consentirà anche un nuovo attributo contestuale, `data`.

Una classe (struct) dichiarata con un elenco di parametri o un modificatore `data` viene chiamata classe di record (struct di record), che è un tipo di record.

È un errore dichiarare un tipo di record senza un elenco di parametri e il modificatore `data`.

## <a name="members-of-a-record-type"></a>Membri di un tipo di record

Oltre ai membri dichiarati nel corpo della classe, un tipo di record presenta i membri aggiuntivi seguenti:

### <a name="primary-constructor"></a>Costruttore primario

Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo. Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore predefinito dichiarato in modo implicito. È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.
In fase di esecuzione il costruttore primario 

1. esegue gli inizializzatori di campo di istanza visualizzati nel corpo della classe; quindi richiama il costruttore della classe base senza argomenti.

1. Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore; vedere [Proprietà sintetizzate](#Synthesized Properties))


[] TODO: aggiungere la sintassi e la specifica della chiamata di base sulla scelta del costruttore di base tramite la risoluzione dell'overload

### <a name="properties"></a>Proprietà

Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value. Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con il nome e il tipo specificati viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:

Per uno struct di record o una classe di record:

* Viene creata una proprietà automatica Get-only pubblica. Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente. Ogni funzione di accesso get della proprietà astratta "corrispondente" ereditata viene sottoposta a override.

### <a name="equality-members"></a>Membri di uguaglianza

I tipi di record producono implementazioni sintetizzate per i metodi seguenti:

* `object.GetHashCode()` sostituzione, a meno che non sia sealed o fornito dall'utente
* `object.Equals(object)` sostituzione, a meno che non sia sealed o fornito dall'utente
* `T Equals(T)` metodo, dove `T` è il tipo corrente

`T Equals(T)` viene specificato per eseguire l'uguaglianza dei valori, confrontando la proprietà con lo stesso nome di ogni parametro del costruttore primario per la proprietà corrispondente dell'altro tipo.
`object.Equals` esegue l'equivalente di

```C#
override Equals(object o) => Equals(o as T);
```
