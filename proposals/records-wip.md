---
ms.openlocfilehash: 258ae6865c5b2c3103a0cdf7e1e5a2cdee11e740
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281957"
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

## <a name="with-expression"></a>Espressione `with`

Un'espressione `with` è una nuova espressione che usa la sintassi seguente.

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

Un'espressione `with` consente "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con le modifiche alle proprietà elencate nel `anonymous_object_initializer`.

Un'espressione `with` valida ha un ricevitore con un tipo non void. Il tipo di ricevitore deve contenere un metodo di istanza accessibile denominato `With` con i parametri e il tipo restituiti appropriati. Se sono presenti più metodi di `With` senza override, è un errore. Se sono presenti più sostituzioni di `With`, deve essere presente un metodo di `With` non di override, ovvero il metodo di destinazione. In caso contrario, deve essere presente esattamente un `With` metodo.

Sul lato destro dell'espressione `with` è presente una `anonymous_object_initializer` con una sequenza di assegnazioni con un campo o una proprietà del ricevitore sul lato sinistro dell'assegnazione e un'espressione arbitraria sul lato destro, convertibile in modo implicito nel tipo della parte sinistra del lato sinistro.

Dato un metodo di `With` di destinazione, il tipo restituito deve essere il tipo del tipo di espressione del ricevitore o un tipo di base. Per ogni parametro del metodo `With`, deve essere presente un campo di istanza corrispondente accessibile o una proprietà leggibile per il tipo di ricevitore con lo stesso nome e lo stesso tipo. Ogni proprietà o campo nel lato destro dell'espressione with deve corrispondere anche a un parametro con lo stesso nome nel metodo `With`.

Dato un metodo di `With` valido, la valutazione di un'espressione `with` equivale a chiamare il metodo `With` con le espressioni nel `anonymous_object_initializer` sostituito per il parametro con lo stesso nome della proprietà sulla parte sinistra. Se non esiste una proprietà corrispondente per un determinato parametro nell'`anonymous_object_initializer`, l'argomento è la valutazione del campo o della proprietà con lo stesso nome nel destinatario.

L'ordine di valutazione degli effetti collaterali è il seguente, in cui ogni espressione viene valutata esattamente una volta:

1. Espressione ricevitore

2. Espressioni nel `anonymous_object_initializer`, in ordine lessicale

3. Valutazione delle proprietà corrispondenti ai parametri del metodo `With`, in ordine di definizione dei parametri del metodo di `With`.