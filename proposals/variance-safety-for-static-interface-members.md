---
ms.openlocfilehash: f1ab0e063cc8b7a44c70ea1e52027cda30e064b0
ms.sourcegitcommit: efac0d1e909d222dce5b18b10ae0d0b1d309b757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97502849"
---
# <a name="variance-safety-for-static-interface-members"></a>Sicurezza della varianza per i membri dell'interfaccia statica

## <a name="summary"></a>Summary

Consente ai membri statici non virtuali nelle interfacce di trattare i parametri di tipo nelle rispettive dichiarazioni come invarianti, indipendentemente dalla varianza dichiarata.

## <a name="motivation"></a>Motivazione


- https://github.com/dotnet/csharplang/issues/3275
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-24.md#interface-static-member-variance

È stata considerata la varianza nei `static` membri dell'interfaccia. Attualmente, per i parametri di tipo covariante usati in questi membri, devono seguire le regole standard complete della varianza, causando un'incoerenza con il modo in cui i `static` campi vengono gestiti rispetto alle `static` proprietà o ai metodi:

```cs
public interface I<out T>
{
    static Task<T> F = Task.FromResult(default(T)); // No problem
    static Task<T> P => Task.FromResult(default(T));   //CS1961
    static Task<T> M() => Task.FromResult(default(T));    //CS1961
    static event EventHandler<T> E; // CS1961
}
```

Poiché questi membri sono `static` e non virtuali, non sono presenti problemi di sicurezza: non è possibile derivare un membro Looser/più limitato in qualche modo, digitando l'interfaccia ed eseguendo l'override del membro.

## <a name="detailed-design"></a>Progettazione dettagliata

Ecco il contenuto proposto per la sezione relativa alla sicurezza Vaiance della specifica del linguaggio ( https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety) .
La modifica è l'aggiunta di "*queste restrizioni non si applicano a ocurrances di tipi nelle dichiarazioni di membri statici".* frase all'inizio della sezione. 

### <a name="variance-safety"></a>Sicurezza della varianza

L'occorrenza delle annotazioni di varianza nell'elenco dei parametri di tipo di un tipo limita le posizioni in cui i tipi possono essere presenti all'interno della dichiarazione del tipo.
*Queste restrizioni non si applicano a ocurrances di tipi nelle dichiarazioni di membri statici.*

Un tipo `T` è ***output-unsafe** _ se uno degli elementi seguenti include:

_  `T` è un parametro di tipo controvariante
*  `T` è un tipo di matrice con un tipo di elemento non sicuro di output
*  `T` è un tipo di interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` in cui per almeno uno `Ai` dei seguenti:
   * `Xi` è covariante o invariante e non `Ai` è sicuro per l'output.
   * `Xi` è controvariante o invariante ed `Ai` è indipendente dall'input.
   
Un tipo `T` è ***input-unsafe** _ se uno degli elementi seguenti include:

_  `T` è un parametro di tipo covariante
*  `T` è un tipo di matrice con un tipo di elemento non sicuro di input
*  `T` è un tipo di interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` in cui per almeno uno `Ai` dei seguenti:
   * `Xi` è covariante o invariante e non `Ai` è sicuro da input.
   * `Xi` è controvariante o invariante e non `Ai` è sicuro per l'output.

In modo intuitivo, un tipo non sicuro di output è vietato in una posizione di output e un tipo di input non sicuro è vietato in una posizione di input.

Un tipo è ***output-Safe** _ se non è unsafe di output, e _ *_input-safe_** se non è non sicuro da input.


## <a name="other-considerations"></a>Altre considerazioni

Si è inoltre preso in considerazione se questa situazione potrebbe interferire con alcuni altri miglioramenti che ci auguriamo di prendere in considerazione per i ruoli, le classi di tipi e le estensioni. Queste dovrebbero essere tutte: non sarà possibile reimpostare i membri statici esistenti in modo che siano virtuali per impostazione predefinita per le interfacce, perché questo potrebbe essere una modifica di rilievo a più livelli, anche senza modificare il comportamento di varianza.
