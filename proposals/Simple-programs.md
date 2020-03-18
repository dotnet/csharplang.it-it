---
ms.openlocfilehash: b9697fc1d772ba59ed3b1de339a5a3d4eb24b1bd
ms.sourcegitcommit: 36b028f4d6e88bd7d4a843c6d384d1b63cc73334
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "79485224"
---
# <a name="simple-programs"></a>Programmi semplici

* [x] proposto
* [x] prototipo: avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Summary
[summary]: #summary

Consente di eseguire una sequenza di *istruzioni* immediatamente prima del *namespace_member_declaration*s di un *compilation_unit* , ad esempio un file di origine.

La semantica è che, se è presente una sequenza di *istruzioni* di questo tipo, viene emessa la seguente dichiarazione di tipo, modulo del nome del tipo effettivo e del nome del metodo:

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

Esiste una certa quantità di standard che circonda anche il più semplice dei programmi, a causa della necessità di un metodo esplicito `Main`. Si tratta di una soluzione di apprendimento della lingua e di chiarezza del programma. L'obiettivo principale della funzionalità è quindi consentire C# l'uso di programmi privi di standard superflui per gli studenti e la chiarezza del codice.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

### <a name="syntax"></a>Sintassi

L'unica sintassi aggiuntiva consente di eseguire una sequenza di *istruzioni*in un'unità di compilazione, immediatamente prima del *namespace_member_declaration*s:

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

In tutti gli oggetti tranne uno *compilation_unit* l' *istruzione*s deve essere tutti dichiarazioni di funzioni locali. 

Esempio:

``` c#
// File 1 - any statements
if (args.Length == 0
    || !int.TryParse(args[0], out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

// File 2 - only local functions
(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a>Semantics

Se in qualsiasi unità di compilazione del programma sono presenti istruzioni di primo livello, il significato è come se fossero combinate nel corpo del blocco di un `Main` metodo di una classe `Program` nello spazio dei nomi globale, come indicato di seguito:

``` c#
static class Program
{
    static async Task Main()
    {
        // File 1 statements
        // File 2 local functions
        // ...
    }
}
```

Si noti che i nomi "Program" e "Main" vengono utilizzati solo a scopo illustrativo, i nomi effettivi utilizzati dal compilatore sono dipendenti dall'implementazione, né il tipo, né il metodo a cui è possibile fare riferimento dal codice sorgente.

Il metodo viene designato come punto di ingresso del programma. I metodi dichiarati in modo esplicito che per convenzione potrebbero essere considerati come candidati di un punto di ingresso vengono ignorati. Quando si verifica un avviso, viene visualizzato un avviso. È un errore specificare `-main:<type>` opzione del compilatore.

Se una qualsiasi unità di compilazione dispone di istruzioni diverse dalle dichiarazioni di funzione locali, le istruzioni di tale unità di compilazione vengono eseguite per prime. In questo modo, è lecito per le funzioni locali in un file fare riferimento a variabili locali in un'altra. L'ordine dei contributi di istruzioni (che sarebbero tutte funzioni locali) da altre unità di compilazione non è definito.

Le operazioni asincrone sono consentite nelle istruzioni di primo livello fino al grado in cui sono consentite nelle istruzioni all'interno di un normale metodo del punto di ingresso asincrono. Tuttavia, non sono necessari, se `await` espressioni e altre operazioni asincrone vengono omesse, non viene generato alcun avviso. La firma del metodo del punto di ingresso generato è invece equivalente a 
``` c#
    static void Main()
```

Nell'esempio precedente viene restituita la dichiarazione di metodo `$Main` seguente:

``` c#
static class $Program
{
    static void $Main()
    {
        // Statements from File 1
        if (args.Length == 0
            || !int.TryParse(args[0], out int n)
            || n < 0) return;
        Console.WriteLine(Fib(n).curr);
        
        // Local functions from File 2
        (int curr, int prev) Fib(int i)
        {
            if (i == 0) return (1, 0);
            var (curr, prev) = Fib(i - 1);
            return (curr + prev, curr);
        }
    }
}
```

Allo stesso tempo, un esempio simile al seguente:
``` c#
// File 1
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

produrrebbe:
``` c#
static class $Program
{
    static async Task $Main()
    {
        // Statements from File 1
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a>Ambito delle variabili locali di primo livello e delle funzioni locali

Anche se le funzioni e le variabili locali di primo livello sono "sottoposte a incapsulamento" nel metodo del punto di ingresso generato, devono essere ancora nell'ambito dell'intero programma.
Ai fini della valutazione con nome semplice, una volta raggiunto lo spazio dei nomi globale:
- Viene innanzitutto eseguito un tentativo di valutare il nome all'interno del metodo del punto di ingresso generato e solo se il tentativo ha esito negativo 
- Viene eseguita la valutazione "regular" all'interno della dichiarazione dello spazio dei nomi globale. 

Questo potrebbe causare l'ombreggiatura dei nomi degli spazi dei nomi e dei tipi dichiarati all'interno dello spazio dei nomi globale, nonché dello shadowing dei nomi importati.

Se la valutazione del nome semplice viene eseguita al di fuori delle istruzioni di primo livello e la valutazione restituisce una variabile o una funzione locale di primo livello, questo dovrebbe causare un errore.

In questo modo, proteggiamo la nostra futura capacità di gestire meglio le "funzioni di primo livello" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117)e in grado di fornire una diagnostica utile agli utenti che si ritengono erroneamente supportati.

