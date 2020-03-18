---
ms.openlocfilehash: aa0c233e7739ae7a0a7752251aae6f89df18ba93
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484517"
---
# <a name="c-language-proposals"></a>C#Proposte di linguaggio

Le proposte per la lingua sono documenti che descrivono il pensiero attuale su una determinata funzionalità del linguaggio.

Le proposte possono essere *attive*, *inattive*, *rifiutate* o *eseguite*. Le proposte *attive* vengono archiviate direttamente nella cartella proposte, le proposte *inattive* e *rifiutate* vengono archiviate nelle sottocartelle [inattive](proposals/inactive) e [rifiutate](proposals/rejected) e le proposte *effettuate* vengono archiviate in una cartella corrispondente alla versione del linguaggio di cui fanno parte.

## <a name="lifetime-of-a-proposal"></a>Durata di una proposta

Una proposta inizia il suo ciclo di vita quando il team di progettazione della lingua decide che potrebbe dare un'aggiunta alla lingua di un giorno. In genere, l'avvio sarà *attivo*, ma se si vuole acquisire un'idea senza volerne lavorare ora, una proposta può anche iniziare nella sottocartella *inattiva* . Le proposte possono anche iniziare direttamente nello stato *rifiutato* , se si vuole creare un record di qualcosa che non si intende eseguire. Se, ad esempio, non è possibile implementare una richiesta comune e ricorrente, è possibile acquisirla come proposta rifiutata.

La proposta può iniziare come idea in un problema di [discussione](https://github.com/dotnet/csharplang/labels/Discussion)o potrebbe derivare da discussioni nella riunione di progettazione del linguaggio o arrivare da molti altri front. La cosa principale è che il team di progettazione ritiene che debba essere eseguita e che qualcuno sia disposto a scriverlo. In genere, un membro del team di progettazione del linguaggio si assegnerà come un campione per la funzionalità, rilevato da un [problema di campione](https://github.com/dotnet/csharplang/labels/Proposal%20champion). Il campione è responsabile dello sviluppo della proposta tramite il processo di progettazione.

Una proposta è *attiva* se si procede attraverso la progettazione e l'implementazione verso una versione futura. Una volta completata l' *operazione*, ovvero un'implementazione è stata unita in una versione e la funzionalità è stata specificata, viene spostata in una sottodirectory corrispondente al relativo rilascio.

Se una funzionalità non è probabilmente in grado di inserirla nella lingua, ad esempio perché non è fattibile, non sembra aggiungere un valore sufficiente o semplicemente non è adatto al linguaggio, verrà [rifiutato](proposals/rejected). Se una funzionalità ha una promessa ragionevole ma non è attualmente in fase di assegnazione di priorità, può essere dichiarata [inattiva](proposals/inactive) per evitare confusione nella cartella principale. Si tratta di una soluzione ottimale per le proposte inattive o rifiutate e per poter essere rilasciate in un secondo momento. Le categorie sono disponibili per riflettere le finalità di progettazione correnti.

## <a name="nature-of-a-proposal"></a>Natura di una proposta

Una proposta deve seguire il [modello proposto](proposal-template.md). Una proposta interessante dovrebbe essere la seguente:

- Adatta allo spirito generale e all'estetica del linguaggio.
- Non introduce una sintassi leggermente alternativa per le funzionalità esistenti.
- Aggiungere molto valore per un set di utenti chiaro.
- Non aggiungere significativamente alla complessità del linguaggio, soprattutto per i nuovi utenti.  

## <a name="discussion-of-proposals"></a>Discussione sulle proposte

Il feedback e la discussione si verificano nei [problemi di discussione](https://github.com/dotnet/csharplang/labels/Discussion). Quando viene aggiunta una nuova proposta alla cartella proposte, questa deve essere annunciata in un problema di discussione da parte dell'autore del campione o della proposta. 

 