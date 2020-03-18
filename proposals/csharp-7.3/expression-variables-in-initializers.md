---
ms.openlocfilehash: a78567594d39fc4e204e12c4f2f247b8d6995c38
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484923"
---
# <a name="expression-variables-in-initializers"></a>Variabili di espressione negli inizializzatori

## <a name="summary"></a>Summary
[summary]: #summary

Si estendono le funzionalità introdotte C# in 7 per consentire espressioni contenenti variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in inizializzatori di campo, inizializzatori di proprietà, inizializzatori ctor e clausole di query.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Questa operazione completa un paio di bordi approssimativi rimasti nella C# lingua a causa della mancanza di tempo.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Viene rimossa la restrizione che impedisce la dichiarazione di variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in un inizializzatore ctor. Una variabile dichiarata è nell'ambito di tutto il corpo del costruttore.

Viene rimossa la restrizione che impedisce la dichiarazione di variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in un inizializzatore di campo o proprietà. Tale variabile dichiarata rientra nell'ambito dell'espressione di inizializzazione.

Viene rimossa la restrizione che impedisce la dichiarazione di variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in una clausola dell'espressione di query che viene convertita nel corpo di un'espressione lambda. Tale variabile dichiarata rientra nell'ambito di tale espressione della clausola di query.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

No.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

L'ambito appropriato per le variabili di espressione dichiarate in questi contesti non è ovvio e merita ulteriore discussione LDM.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [] Qual è l'ambito appropriato per queste variabili?

## <a name="design-meetings"></a>Riunioni di progettazione

No.
