---
ms.openlocfilehash: ac2b233eb703b5eea3bd2dfdbeeadd7494b0c695
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484671"
---
# <a name="non-trailing-named-arguments"></a>Argomenti denominati non finali

## <a name="summary"></a>Summary
[summary]: #summary
Consente di utilizzare gli argomenti denominati nella posizione non finale, purché vengano utilizzati nella posizione corretta. Ad esempio: `DoSomething(isEmployed:true, name, age);`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

La motivazione principale è evitare di digitare informazioni ridondanti. È comune denominare un argomento che è un valore letterale (ad esempio `null`, `true`) allo scopo di chiarire il codice, anziché passare gli argomenti non ordinati.
Attualmente non consentito (`CS1738`), a meno che non vengano denominati anche tutti gli argomenti seguenti.

```csharp
DoSomething(isEmployed:true, name, age); // currently disallowed, even though all arguments are in position
// CS1738 "Named argument specifications must appear after all fixed arguments have been specified"
```

Altri esempi:
```csharp
public void DoSomething(bool isEmployed, string personName, int personAge) { ... }

DoSomething(isEmployed:true, name, age); // currently CS1738, but would become legal
DoSomething(true, personName:name, age); // currently CS1738, but would become legal
DoSomething(name, isEmployed:true, age); // remains illegal
DoSomething(name, age, isEmployed:true); // remains illegal
DoSomething(true, personAge:age, personName:name); // already legal
```

Funziona anche con params:
```csharp
public class Task
{
    public static Task When(TaskStatus all, TaskStatus any, params Task[] tasks);
}
Task.When(all: TaskStatus.RanToCompletion, any: TaskStatus.Faulted, task1, task2)
```

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

In § 7.5.1 (elenchi di argomenti), la specifica attualmente indica:
> Un *argomento con* un *nome di argomento* viene definito argomento __denominato__, mentre un *argomento senza un* nome di *argomento* è un __argomento posizionale__. Per visualizzare un argomento posizionale dopo un argomento denominato in un *elenco di argomenti*, è un errore.

La proposta consiste nel rimuovere questo errore e aggiornare le regole per trovare il parametro corrispondente per un argomento (§ 7.5.1.1):

Argomenti nell'elenco di argomenti di costruttori di istanza, metodi, indicizzatori e delegati:
- [regole esistenti]
- Un argomento senza nome corrisponde a nessun parametro se è successivo a un argomento denominato out-of-position o a un argomento denominato params.

In particolare, questa operazione impedisce la chiamata di `void M(bool a = true, bool b = true, bool c = true, );` con `M(c: false, valueB);`. Il primo argomento viene utilizzato fuori posizione (l'argomento viene utilizzato nella prima posizione, ma il parametro denominato "c" si trova nella terza posizione), pertanto è necessario denominare gli argomenti seguenti.

In altre parole, gli argomenti denominati non finali sono consentiti solo quando il nome e la posizione determinano la ricerca dello stesso parametro corrispondente.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Questa proposta aggrava le sottigliezze esistenti con argomenti denominati nella risoluzione dell'overload. Ad esempio:

```csharp
void M(int x, int y) { }
void M<T>(T y, int x) { }

void M2()
{
    M(3, 4);
    M(y: 3, x: 4); // Invokes M(int, int)
    M(y: 3, 4); // Invokes M<T>(T, int)
}
```

È possibile ottenere questa situazione oggi cambiando i parametri:

```csharp
void M(int y, int x) { }
void M<T>(int x, T y) { }

void M2()
{
    M(3, 4);
    M(x: 3, y: 4); // Invokes M(int, int)
    M(3, y: 4); // Invokes M<T>(int, T)
}
```

Analogamente, se si dispone di due metodi `void M(int a, int b)` e `void M(int x, string y)`, il `M(x: 1, 2)` di chiamata errato produrrà una diagnostica basata sul secondo overload ("Impossibile convertire da' int ' a' String '"). Questo problema esiste già quando l'argomento denominato viene utilizzato in una posizione finale.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Ci sono due alternative da considerare:

- Status quo
- Fornire assistenza IDE per inserire tutti i nomi degli argomenti finali quando si digita un nome specifico al centro.

Entrambe le persone soffrono di un livello di dettaglio maggiore, perché introducono più argomenti denominati anche se è necessario solo un nome di un valore letterale all'inizio dell'elenco di argomenti.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a>Riunioni di progettazione
[ldm]: #ldm
La funzionalità è stata brevemente discussa in LDM il 16 maggio 2017, con l'approvazione in principio (OK per passare alla proposta/prototipo). È stato descritto anche brevemente il 28 giugno 2017.

Si riferisce alla discussione iniziale https://github.com/dotnet/csharplang/issues/518 si riferisce al problema sostenuto https://github.com/dotnet/csharplang/issues/570
