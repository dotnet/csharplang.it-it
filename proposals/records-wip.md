---
ms.openlocfilehash: 1fb1f3b9025d65cb39f675e60bae1cb6415fc184
ms.sourcegitcommit: 88202acd40ca04b6e513ea1e5993625ee26fac3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2020
ms.locfileid: "81219628"
---
# <a name="records-work-in-progress"></a>Registra Lavoro in corso

A differenza delle altre proposte di record, questa non è una proposta in sé, ma un work-in-progress progettato per registrare le decisioni di progettazione del consenso per la funzione di registrazione. I dettagli delle specifiche verranno aggiunti in base alle esigenze per risolvere le domande.

La sintassi di un record viene proposta per essere aggiunta come segue:

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

Il `attributes` non terminale consentirà anche un `data`nuovo attributo contestuale, .

Una classe (struct) dichiarata con `data` un elenco di parametri o un modificatore è denominata classe di record (struttura di record), uno dei quali è un tipo di record.

È un errore dichiarare un tipo di record `data` senza un elenco di parametri e il modificatore.

## <a name="members-of-a-record-type"></a>Membri di un tipo di record

Oltre ai membri dichiarati nel corpo della classe o dello struct, un tipo di record dispone dei seguenti membri aggiuntivi:

### <a name="primary-constructor"></a>Costruttore primario

Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri value della dichiarazione del tipo. Questo è chiamato il costruttore primario per il tipo e fa sì che il costruttore di classe predefinito dichiarato in modo implicito, se presente, da eliminare. È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.

In fase di esecuzione il costruttore primario

1. esegue gli inizializzatori di campo di istanza che appaiono nel corpo della classe; e quindi richiama il costruttore della classe base senza argomenti.

1. inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri di valore (se queste proprietà sono fornite dal compilatore

### <a name="properties"></a>Proprietà

Per ogni parametro di record di una dichiarazione di tipo di record è presente un membro di proprietà pubblica corrispondente il cui nome e tipo sono tratti dalla dichiarazione del parametro di valore. Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con questo nome e tipo viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come segue:

Per una struttura di record o una classe di record:For a record struct or a record class:

* Viene creata una proprietà automatica get-only pubblica. Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente. Viene esegua l'override di ogni funzione di accesso get della proprietà astratta ereditata "corrispondente".

### <a name="equality-members"></a>Membri di uguaglianza

I tipi di record producono implementazioni sintetizzate per i metodi seguenti:Record types produce synthesized implementations for the following methods:

* `object.GetHashCode()`override, a meno che non sia sigillato o fornito dall'utente
* `object.Equals(object)`override, a meno che non sia sigillato o fornito dall'utente
* `T Equals(T)`metodo, `T` dove è il tipo corrente

`T Equals(T)`viene specificato per eseguire l'uguaglianza di valori, confrontando la proprietà con lo stesso nome di ogni parametro del costruttore primario con la proprietà corrispondente dell'altro tipo.
`object.Equals`esegue l'equivalente di

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a>Espressione `with`

Un'espressione `with` è una nuova espressione che utilizza la sintassi seguente.

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

Un'espressione `with` consente una "mutazione non distruttiva", progettata per produrre una `anonymous_object_initializer`copia dell'espressione del ricevitore con modifiche alle proprietà elencate nell'oggetto .

Un'espressione valida `with` ha un ricevitore con un tipo non void. Il tipo di ricevitore deve contenere un metodo di istanza senza parametri accessibile chiamato `Clone` il cui tipo restituito deve essere il tipo del tipo espresso del ricevitore o un tipo di base.

Sul lato destro `with` dell'espressione `anonymous_object_initializer` è un con una sequenza di assegnazioni con una proprietà di record generata dal compilatore del ricevitore sul lato sinistro dell'assegnazione e un'espressione arbitraria sul lato destro che è convertibile in modo implicito al tipo del lato sinistro.

La valutazione `with` di un'espressione `Clone` equivale a chiamare il metodo esattamente una volta e quindi a impostare il campo di supporto di `Clone` ogni proprietà di record nell'elenco di argomenti sull'espressione corrispondente, in ordine lessicale, utilizzando il risultato del metodo come ricevitore.
