---
ms.openlocfilehash: 91afbc3e3412049cd183c36c8035f1862c520413
ms.sourcegitcommit: da1180f7eacdd5067b32d291a76e6764159e00fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "79485056"
---
# <a name="pattern-based-using-and-using-declarations"></a>"basato su modelli con" e "dichiarazioni using"

## <a name="summary"></a>Summary

Il linguaggio aggiungerà due nuove funzionalità relative all'istruzione `using` per semplificare la gestione delle risorse: `using` dovrebbe riconoscere un modello Disposable, oltre a `IDisposable` e aggiungere una dichiarazione `using` al linguaggio.

## <a name="motivation"></a>Motivazione

Il `using` istruzione è uno strumento efficace per la gestione delle risorse, ma richiede una certa cerimonia. I metodi con numerose risorse da gestire possono essere sintatticamente impantanati con una serie di istruzioni `using`. Questo fardello di sintassi è sufficiente per la maggior parte delle linee guida di stile di codifica in modo esplicito per questo scenario. 

La dichiarazione di `using` rimuove gran parte della cerimonia qui e C# ottiene la parità con altri linguaggi che includono i blocchi di gestione delle risorse. Inoltre, la `using` basata su modelli consente agli sviluppatori di espandere il set di tipi che possono partecipare qui. In molti casi, la necessità di creare tipi di wrapper che esistono solo per consentire l'utilizzo di valori in un'istruzione `using`. 

Insieme, queste funzionalità consentono agli sviluppatori di semplificare ed espandere gli scenari in cui è possibile applicare `using`.

## <a name="detailed-design"></a>Progettazione dettagliata 

### <a name="using-declaration"></a>dichiarazione using

Il linguaggio consentirà di aggiungere `using` a una dichiarazione di variabile locale. Tale dichiarazione avrà lo stesso effetto di dichiarare la variabile in un'istruzione `using` nello stesso percorso.

```csharp
if (...) 
{ 
   using FileStream f = new FileStream(@"C:\users\jaredpar\using.md");
   // statements
}

// Equivalent to 
if (...) 
{ 
   using (FileStream f = new FileStream(@"C:\users\jaredpar\using.md")) 
   {
    // statements
   }
}
```

La durata di un `using` local si estenderà alla fine dell'ambito in cui è dichiarata. Le variabili locali `using` verranno quindi eliminate nell'ordine inverso in cui sono dichiarate. 

```csharp
{ 
    using var f1 = new FileStream("...");
    using var f2 = new FileStream("..."), f3 = new FileStream("...");
    ...
    // Dispose f3
    // Dispose f2 
    // Dispose f1
}
```

Non sono previste restrizioni per `goto`o qualsiasi altro costrutto del flusso di controllo in faccia di una dichiarazione di `using`. Il codice agisce invece esattamente come per l'istruzione `using` equivalente:

```csharp
{
    using var f1 = new FileStream("...");
  target:
    using var f2 = new FileStream("...");
    if (someCondition) 
    {
        // Causes f2 to be disposed but has no effect on f1
        goto target;
    }
}
```

Una dichiarazione locale dichiarata in una `using` dichiarazione locale sarà in modo implicito di sola lettura. Corrisponde al comportamento delle variabili locali dichiarate in un'istruzione `using`. 

La grammatica del linguaggio per le dichiarazioni di `using` sarà la seguente:

```antlr
local-using-declaration:
  using type using-declarators

using-declarators:
  using-declarator
  using-declarators , using-declarator
  
using-declarator:
  identifier = expression
```

Limitazioni relative alla dichiarazione `using`:

- Potrebbe non essere visualizzato direttamente all'interno di un'etichetta di `case` ma deve trovarsi all'interno di un blocco all'interno dell'etichetta `case`.
- Potrebbe non essere incluso in una dichiarazione di variabile `out`. 
- Deve disporre di un inizializzatore per ogni dichiaratore.
- Il tipo locale deve essere convertibile in modo implicito per `IDisposable` o soddisfare il modello di `using`.

### <a name="pattern-based-using"></a>Basato su pattern con

Il linguaggio aggiungerà la nozione di modello Disposable, ovvero un tipo che dispone di un metodo di istanza accessibile `Dispose`. I tipi che soddisfano il modello Disposable possono partecipare a un'istruzione o a una dichiarazione `using` senza che sia necessario implementare `IDisposable`. 

```csharp
class Resource
{ 
    public void Dispose() { ... }
}

using (var r = new Resource())
{
    // statements
}
```

Ciò consentirà agli sviluppatori di sfruttare `using` in diversi nuovi scenari:

- `ref struct`: questi tipi non possono implementare interfacce oggi e pertanto non possono partecipare a istruzioni `using`.
- I metodi di estensione consentono agli sviluppatori di potenziare i tipi di altri assembly per partecipare alle istruzioni `using`.

Nel caso in cui un tipo possa essere convertito in modo implicito in `IDisposable` e si adatta anche al modello Disposable, sarà preferibile `IDisposable`. Sebbene questa operazione accetti l'approccio opposto di `foreach` (modello preferito rispetto all'interfaccia), è necessario per la compatibilità con le versioni precedenti.

Di seguito sono riportate le stesse restrizioni di un'istruzione `using` tradizionale: le variabili locali dichiarate nella `using` sono di sola lettura, un valore `null` non causerà la generazione di un'eccezione e così via... La generazione del codice sarà diversa solo perché non sarà presente un cast da `IDisposable` prima di chiamare Dispose:

```csharp
{
      Resource r = new Resource();
      try {
            // statements
      }
      finally {
            if (resource != null) resource.Dispose();
      }
}
```

Per adattarsi al modello Disposable, il metodo `Dispose` deve essere accessibile senza parametri e deve avere un tipo `void` restituito. Non sono previste altre restrizioni. Questo significa esplicitamente che è possibile usare i metodi di estensione.

## <a name="considerations"></a>Considerazioni

### <a name="case-labels-without-blocks"></a>etichette case senza blocchi

Una `using declaration` non è valida direttamente all'interno di un'etichetta `case` a causa di complicazioni relative alla durata effettiva. Una possibile soluzione è semplicemente assegnarle la stessa durata di un `out var` nello stesso percorso. È stata considerata la complessità aggiuntiva all'implementazione della funzionalità e la facilità di gestione (solo aggiungere un blocco all'etichetta `case`) non giustificava l'esecuzione di questa route.

## <a name="future-expansions"></a>Espansioni future

### <a name="fixed-locals"></a>variabili locali fisse

Un'istruzione `fixed` dispone di tutte le proprietà di `using` istruzioni che hanno motivato la possibilità di avere `using` variabili locali. È necessario considerare l'estensione di questa funzionalità anche a `fixed` variabili locali. Le regole di durata e di ordinamento dovrebbero essere valide anche per `using` e `fixed`.
