---
ms.openlocfilehash: 405153448d0e3685d6f22725e00d75d9250b3e20
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484797"
---
# <a name="async-main"></a>Principale asincrono

* [x] proposto
* [] Prototype
* [] Implementazione
* [] Specifica

## <a name="summary"></a>Summary
[summary]: #summary

Consente di usare `await` nel metodo Main/EntryPoint di un'applicazione consentendo al EntryPoint di restituire `Task` / `Task<int>` e di essere contrassegnati come `async`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

È molto comune durante l'apprendimento C#, quando si scrivono utilità basate su console e quando si scrivono app di test di piccole dimensioni per chiamare e `await` `async` metodi da Main.  Attualmente si aggiunge un livello di complessità forzando l'esecuzione di tale `await`in un metodo asincrono separato, che fa sì che gli sviluppatori debbano scrivere uno standard come il seguente solo per iniziare:

```csharp
public static void Main()
{
    MainAsync().GetAwaiter().GetResult();
}

private static async Task MainAsync()
{
    ... // Main body here
}
```

È possibile rimuovere la necessità di questo standard e semplificarne l'avvio semplicemente consentendo la `async` principale, in modo che sia possibile utilizzare `await`s.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Le firme seguenti sono attualmente consentite entryPoints:

```csharp
static void Main()
static void Main(string[])
static int Main()
static int Main(string[])
```

Si estenderà l'elenco dei entryPoints consentiti in modo da includere:

```csharp
static Task Main()
static Task<int> Main()
static Task Main(string[])
static Task<int> Main(string[])
```

Per evitare i rischi di compatibilità, queste nuove firme verranno considerate entryPoints valide solo se non sono presenti overload del set precedente.
Il linguaggio/compilatore non richiede che il EntryPoint venga contrassegnato come `async`, anche se si prevede che la maggior parte degli usi venga contrassegnata come tale.

Quando uno di questi viene identificato come EntryPoint, il compilatore sintetizza un Metodo EntryPoint effettivo che chiama uno di questi metodi codificati:
- ```static Task Main()``` comporterà il compilatore che emette l'equivalente di ```private static void $GeneratedMain() => Main().GetAwaiter().GetResult();```
- ```static Task Main(string[])``` comporterà il compilatore che emette l'equivalente di ```private static void $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```
- ```static Task<int> Main()``` comporterà il compilatore che emette l'equivalente di ```private static int $GeneratedMain() => Main().GetAwaiter().GetResult();```
- ```static Task<int> Main(string[])``` comporterà il compilatore che emette l'equivalente di ```private static int $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```

Esempio di utilizzo:

```csharp
using System;
using System.Net.Http;

class Test
{
    static async Task Main(string[] args) =>
        Console.WriteLine(await new HttpClient().GetStringAsync(args[0]));
}
```

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Lo svantaggio principale è semplicemente l'ulteriore complessità del supporto di firme EntryPoint aggiuntive.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Altre varianti considerate:

Consentendo `async void`.  È necessario mantenerla identica per il codice che lo chiama direttamente, il che renderebbe difficile per un punto di ingresso generato la chiamata (nessuna attività restituita).  È possibile risolvere questo problema generando altri due metodi, ad esempio

```csharp
public static async void Main()
{
   ... // await code
}
```

diventa

```csharp
public static async void Main() => await $MainTask();

private static void $EntrypointMain() => Main().GetAwaiter().GetResult();

private static async Task $MainTask()
{
    ... // await code
}
```

Ci sono inoltre problemi a incoraggiare l'utilizzo di `async void`.

Usare "MainAsync" invece di "Main" come nome.  Sebbene sia consigliabile usare il suffisso asincrono per i metodi che restituiscono attività, si tratta principalmente della funzionalità della libreria, che non è la principale, e il supporto di nomi di EntryPoint aggiuntivi oltre a "Main" non vale la pena.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

n/d

## <a name="design-meetings"></a>Riunioni di progettazione

n/d
