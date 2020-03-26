---
ms.openlocfilehash: 54ae4ffabde6dca49b7e6bfb626d65837eabc8f5
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281944"
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

Solo un *compilation_unit* può avere un' *istruzione*s. 

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

Se in qualsiasi unità di compilazione del programma sono presenti istruzioni di primo livello, il significato è come se fossero combinate nel corpo del blocco di un `Main` metodo di una classe `Program` nello spazio dei nomi globale, come indicato di seguito:

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

Si noti che i nomi "Program" e "Main" vengono utilizzati solo a scopo illustrativo, i nomi effettivi utilizzati dal compilatore sono dipendenti dall'implementazione, né il tipo, né il metodo a cui è possibile fare riferimento dal codice sorgente.

Il metodo viene designato come punto di ingresso del programma. I metodi dichiarati in modo esplicito che per convenzione potrebbero essere considerati come candidati di un punto di ingresso vengono ignorati. Quando si verifica un avviso, viene visualizzato un avviso. È un errore specificare `-main:<type>` opzione del compilatore quando sono presenti istruzioni di primo livello.

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

Allo stesso tempo, un esempio simile al seguente:
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

### <a name="scope-of-top-level-local-variables-and-local-functions"></a>Ambito delle variabili locali di primo livello e delle funzioni locali

Anche se le funzioni e le variabili locali di primo livello sono "incapsulate" nel metodo del punto di ingresso generato, devono ancora trovarsi nell'ambito del programma in ogni unità di compilazione.
Ai fini della valutazione con nome semplice, una volta raggiunto lo spazio dei nomi globale:
- Viene innanzitutto eseguito un tentativo di valutare il nome all'interno del metodo del punto di ingresso generato e solo se il tentativo ha esito negativo 
- Viene eseguita la valutazione "regular" all'interno della dichiarazione dello spazio dei nomi globale. 

Questo potrebbe causare l'ombreggiatura dei nomi degli spazi dei nomi e dei tipi dichiarati all'interno dello spazio dei nomi globale, nonché dello shadowing dei nomi importati.

Se la valutazione del nome semplice viene eseguita al di fuori delle istruzioni di primo livello e la valutazione restituisce una variabile o una funzione locale di primo livello, questo dovrebbe causare un errore.

In questo modo, proteggiamo la nostra futura capacità di gestire meglio le "funzioni di primo livello" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117)e in grado di fornire una diagnostica utile agli utenti che si ritengono erroneamente supportati.

