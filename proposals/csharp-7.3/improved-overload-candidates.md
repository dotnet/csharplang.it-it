---
ms.openlocfilehash: 833ea0469149cbd434e8950e844740a3adb4d386
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484594"
---
# <a name="improved-overload-candidates"></a>Miglioramento dei candidati per l'overload

## <a name="summary"></a>Summary
[summary]: #summary

Le regole di risoluzione dell'overload sono state aggiornate in C# quasi tutti gli aggiornamenti del linguaggio per migliorare l'esperienza dei programmatori, facendo in modo che le chiamate ambigue selezionino la scelta più ovvia. Questa operazione deve essere eseguita con attenzione per mantenere la compatibilità con le versioni precedenti, ma poiché in genere si risolverebbe un caso di errore di questo tipo, questi miglioramenti in genere funzionano bene.

1. Quando in un gruppo di metodi sono contenuti sia i membri di istanza che quelli statici, i membri dell'istanza vengono rimossi se richiamati senza il destinatario o il contesto di un'istanza e vengono rimossi i membri statici se richiamati con un destinatario dell'istanza. Quando non è presente alcun ricevitore, vengono inclusi solo i membri statici in un contesto statico, in caso contrario i membri statici e di istanza. Quando il ricevitore è ambiguamente un'istanza o un tipo a causa di una situazione di colore colore, sono inclusi entrambi. Un contesto statico, in cui non è possibile usare un destinatario implicito di questa istanza, include il corpo dei membri in cui non è definito alcun oggetto, ad esempio i membri statici, nonché le posizioni in cui non può essere usato, ad esempio inizializzatori di campo e inizializzatori di costruttori.
2. Quando un gruppo di metodi include alcuni metodi generici i cui argomenti di tipo non soddisfano i vincoli, questi membri vengono rimossi dal set di candidati.
3. Per la conversione di un gruppo di metodi, i metodi candidati il cui tipo restituito non corrisponde al quello del delegato vengono rimossi dal set.
