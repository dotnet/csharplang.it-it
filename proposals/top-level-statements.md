---
ms.openlocfilehash: 252b7d601ce2f1649b090dcfd97a3aea15d8e132
ms.sourcegitcommit: 356ee04506a2a82292be25d7b029e7ce2a39e63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82038235"
---
# <a name="top-level-statements"></a>Istruzioni di primo livello

* [x] Proposto
* [x] Prototipo: Avviato
* [x] Implementazione: Avviata
* [ ] Specifica: Non avviato

## <a name="summary"></a>Summary
[summary]: #summary

Consentire la verifica di una sequenza di *istruzioni* subito prima delle *namespace_member_declaration*di un *compilation_unit* (ad esempio un file di origine).

La semantica è che se tale sequenza di *istruzioni* è presente, la seguente dichiarazione di tipo, modulo il nome effettivo del tipo e il nome del metodo, verrebbe generato:

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

Vedere anche https://github.com/dotnet/csharplang/issues/3117.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

C'è una certa quantità di boilerplate che circonda anche il `Main` più semplice dei programmi, a causa della necessità di un metodo esplicito. Questo sembra intralciare l'apprendimento delle lingue e la chiarezza del programma. L'obiettivo principale della funzionalità è quindi quello di consentire i programmi c'è senza inutili boilerplate intorno a loro, per il bene degli studenti e la chiarezza del codice.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

### <a name="syntax"></a>Sintassi

L'unica sintassi aggiuntiva è consentire una sequenza di *istruzioni*s in un'unità di compilazione, appena prima del *namespace_member_declaration*s:

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

Solo un *compilation_unit* può avere *l'istruzione*s. 

Esempio:

``` c#
if (System.Environment.CommandLine.Length == 0
    || !int.TryParse(System.Environment.CommandLine, out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a>Semantics

Se sono presenti istruzioni di primo livello in qualsiasi unità di compilazione del programma, il `Main` significato `Program` è come se fossero combinate nel corpo del blocco di un metodo di una classe nello spazio dei nomi globale, come indicato di seguito:

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

Si noti che i nomi "Programma" e "Main" vengono utilizzati solo a scopo illustrativo, i nomi effettivi utilizzati dal compilatore dipendono dall'implementazione e né è possibile fare riferimento al tipo né al metodo per nome dal codice sorgente.

Il metodo viene designato come punto di ingresso del programma. I metodi dichiarati in modo esplicito che per convenzione potrebbero essere considerati come candidati di un punto di ingresso vengono ignorati. Un avviso viene segnalato quando ciò si verifica. È un errore `-main:<type>` specificare l'opzione del compilatore quando sono presenti istruzioni di primo livello.

Le operazioni asincrone sono consentite nelle istruzioni di primo livello al grado in cui sono consentite nelle istruzioni all'interno di un normale metodo del punto di ingresso asincrono. Tuttavia, non sono `await` necessari, se le espressioni e altre operazioni asincrone vengono omesse, non viene generato alcun avviso.

La firma del metodo del punto di ingresso generato viene determinata in base alle operazioni utilizzate dalle istruzioni di primo livello come segue:
**Async-operations - Return-with-expression (Operazioni asincrone- Return-with-expression)** | **Presente** | **Assente**
----------------------------------------| -------------|-------------
**Presente** | ```static Task<int> Main()```| ```static Task Main()```
**Assente**  | ```static int Main()``` | ```static void Main()```

L'esempio precedente produrrebbe la seguente dichiarazione di metodo:The example above would yield the following `$Main` method declaration:

``` c#
static class $Program
{
    static void $Main()
    {
        if (System.Environment.CommandLine.Length == 0
            || !int.TryParse(System.Environment.CommandLine, out int n)
            || n < 0) return;
        Console.WriteLine(Fib(n).curr);
        
        (int curr, int prev) Fib(int i)
        {
            if (i == 0) return (1, 0);
            var (curr, prev) = Fib(i - 1);
            return (curr + prev, curr);
        }
    }
}
```

Allo stesso tempo, un esempio come questo:
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

produrrebbe:
``` c#
static class $Program
{
    static async Task $Main()
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

Un esempio come questo:
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

produrrebbe:
``` c#
static class $Program
{
    static async Task<int> $Main()
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
        return 0;
    }
}
```

E un esempio come questo:
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

produrrebbe:
``` c#
static class $Program
{
    static int $Main()
    {
        System.Console.WriteLine("Hi!");
        return 2;
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a>Ambito delle variabili locali di primo livello e delle funzioni locali

Anche se le funzioni e le variabili locali di primo livello sono "incapsulate" nel metodo del punto di ingresso generato, devono comunque essere nell'ambito in tutto il programma in ogni unità di compilazione.
Ai fini della valutazione del nome semplice, una volta raggiunto lo spazio dei nomi globale:
- In primo luogo, viene effettuato un tentativo di valutare il nome all'interno del metodo del punto di ingresso generato e solo se il tentativo non riesce 
- Viene eseguita la valutazione "regolare" all'interno della dichiarazione dello spazio dei nomi globale. 

Ciò potrebbe portare allo shadowing dei nomi degli spazi dei nomi e dei tipi dichiarati all'interno dello spazio dei nomi globale, nonché allo shadowing dei nomi importati.

Se la valutazione del nome semplice si verifica al di fuori delle istruzioni di primo livello e la valutazione produce una variabile locale di primo livello o una funzione, ciò dovrebbe causare un errore.

In questo modo proteggiamo la nostra futura capacità di affrontare https://github.com/dotnet/csharplang/issues/3117)meglio le "funzioni di primo livello" (scenario 2 in , e siamo in grado di dare diagnostica utile agli utenti che erroneamente credono che siano supportati.

