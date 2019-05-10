---
ms.openlocfilehash: 75fcd5b00ea5cac218a9f7809c53b179df97825c
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488945"
---
# <a name="exceptions"></a>Eccezioni

Le eccezioni nel linguaggio c# forniscono un modo strutturato, uniforme e indipendente dai tipi di gestire a livello di sistema sia a livello di applicazione condizioni di errore. Il meccanismo delle eccezioni nel linguaggio c# è molto simile a quella di C++, con alcune importanti differenze:

*  In c#, tutte le eccezioni devono essere rappresentate da un'istanza di un tipo di classe derivato da `System.Exception`. In C++, qualsiasi valore di qualsiasi tipo è utilizzabile per rappresentare un'eccezione.
*  In c#, un blocco finally ([l'istruzione try](statements.md#the-try-statement)) può essere usato per scrivere codice di terminazione che viene eseguito in condizioni normali e condizioni eccezionali. Tale codice è difficile scrivere in C++ senza duplicazione del codice.
*  In c#, le eccezioni a livello di sistema, ad esempio overflow, divisione per zero e null dereferenzia ben definite, le classi di eccezioni ed Equilibra delle condizioni di errore a livello di applicazione.

## <a name="causes-of-exceptions"></a>Cause delle eccezioni

Eccezione può essere generata in due modi diversi.

*  Oggetto `throw` istruzione ([l'istruzione throw](statements.md#the-throw-statement)) genera un'eccezione immediatamente e in modo incondizionato. Controllo non raggiunga mai l'istruzione che segue immediatamente il `throw`.
*  Alcune condizioni eccezionali che si verificano durante l'elaborazione di istruzioni c# ed espressione generano un'eccezione in alcuni casi quando l'operazione non può essere completata normalmente. Ad esempio, un'operazione di divisione integer ([operatore di divisione](expressions.md#division-operator)) genera un `System.DivideByZeroException` se il denominatore è zero. Visualizzare [classi di eccezioni comuni](exceptions.md#common-exception-classes) per un elenco delle varie eccezioni che possono verificarsi in questo modo.

## <a name="the-systemexception-class"></a>La classe System. Exception

Il `System.Exception` classe è il tipo di base di tutte le eccezioni. Questa classe ha alcune proprietà importanti che condividono tutte le eccezioni:

*  `Message` è una proprietà di sola lettura di tipo `string` contenente una descrizione leggibile del motivo dell'eccezione.
*  `InnerException` è una proprietà di sola lettura di tipo `Exception`. Se il valore è diverso da null, fa riferimento all'eccezione che ha causato l'eccezione corrente, vale a dire, è stato generato l'eccezione corrente in un blocco catch gestisce le `InnerException`. In caso contrario, il relativo valore è null, che indica che questa eccezione non è stata causata da un'altra eccezione. Il numero di oggetti eccezione concatenate in questo modo può essere arbitrario.

Il valore di queste proprietà può essere specificato nelle chiamate al costruttore di istanza per `System.Exception`.

## <a name="how-exceptions-are-handled"></a>Gestione delle eccezioni

Le eccezioni vengono gestite da un `try` istruzione ([l'istruzione try](statements.md#the-try-statement)).

Quando si verifica un'eccezione, il sistema cerca più vicini `catch` clausola in grado di gestire l'eccezione, come determinato dal tipo di runtime dell'eccezione. In primo luogo, il metodo corrente viene cercato un livello lessicale di inclusione `try` istruzione e le clausole catch associata dell'istruzione try sono considerati nell'ordine. Se il problema persiste, il metodo che ha chiamato il metodo corrente viene cercato un livello lessicale di inclusione `try` istruzione che include il punto della chiamata al metodo corrente. Questa ricerca continua fino a quando un `catch` clausola viene trovata in grado di gestire l'eccezione corrente, assegnando una classe di eccezione che è della stessa classe o una classe base, del tipo di eccezione generata in fase di esecuzione. Oggetto `catch` clausola che non denominare una classe di eccezione può gestire tutte le eccezioni.

Una volta trovata una clausola catch corrispondente, il sistema si prepara al trasferimento di controllo per la prima istruzione della clausola catch. Prima dell'inizio dell'esecuzione della clausola catch, il sistema esegue prima, in ordine, qualsiasi `finally` clausole che erano associate a più istruzioni di prova annidati che da quello che ha generato l'eccezione.

Se non viene trovata alcuna clausola catch corrispondente, si verifica una delle seguenti operazioni:

*  Se la ricerca di una clausola catch raggiunge un costruttore statico ([costruttori statici](classes.md#static-constructors)) o un inizializzatore di campo statico, un `System.TypeInitializationException` viene generata in corrispondenza del punto che ha attivato la chiamata del costruttore statico. L'eccezione interna del `System.TypeInitializationException` contiene l'eccezione che è stata inizialmente generata.
*  Se la ricerca delle clausole catch corrispondenti raggiunge il codice che ha avviato il thread, viene terminata l'esecuzione del thread. L'impatto di tale azione è definito dall'implementazione.

Le eccezioni che si verificano durante l'esecuzione del distruttore sono nota particolare merita. Se si verifica un'eccezione durante l'esecuzione del distruttore e tale eccezione non rilevata, l'esecuzione del distruttore viene terminata e viene chiamato il distruttore della classe di base (se presente). Se non è presente alcuna classe di base (come nel caso del `object` tipo) o se non vi è alcun distruttore della classe base, l'eccezione viene eliminata.

## <a name="common-exception-classes"></a>Classi di eccezioni comuni

Le seguenti eccezioni vengono generate da determinate operazioni in c#.

|                                      |                |
|--------------------------------------|----------------|
| `System.ArithmeticException`         | Classe di base per le eccezioni che si verificano durante operazioni aritmetiche, quali `System.DivideByZeroException` e `System.OverflowException`. | 
| `System.ArrayTypeMismatchException`  | Generata quando un archivio in una matrice non riesce perché il tipo effettivo dell'elemento archiviato è incompatibile con il tipo effettivo della matrice. | 
| `System.DivideByZeroException`       | Generata quando si verifica un tentativo di dividere un valore integrale per zero. | 
| `System.IndexOutOfRangeException`    | Generata quando un tentativo di indicizzare una matrice tramite un indice minore di zero o supera i limiti della matrice. | 
| `System.InvalidCastException`        | Generata quando una conversione esplicita da un tipo di base o interfaccia a un tipo derivato non riesce in fase di esecuzione. | 
| `System.NullReferenceException`      | Generata quando un `null` riferimento viene usato in modo che l'oggetto di riferimento obbligatorio. | 
| `System.OutOfMemoryException`        | Generata quando si tenta di allocare la memoria (tramite `new`) ha esito negativo. | 
| `System.OverflowException`           | Generata quando si verifica un overflow di un'operazione aritmetica in un contesto `checked`. | 
| `System.StackOverflowException`      | Generata quando viene esaurito lo stack di esecuzione in un numero eccessivo di chiamate a metodi in sospeso; in genere indicativi di ricorsione troppo profonda o illimitata. | 
| `System.TypeInitializationException` | Generata quando un costruttore statico genera un'eccezione e nessun `catch` clausole esiste per intercettarla. | 
