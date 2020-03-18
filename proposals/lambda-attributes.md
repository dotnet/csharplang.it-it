---
ms.openlocfilehash: 9647fff40a1e45bef917f140612ae4e91abea958
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484573"
---
# <a name="lambda-attributes"></a>Attributi lambda

* [x] proposto
* [] Prototype
* [] Implementazione
* [] Specifica

## <a name="summary"></a>Summary
[summary]: #summary

Consente di applicare gli attributi alle espressioni lambda (e ai metodi anonimi) e ai parametri del metodo lambda/Anonimo, in quanto possono trovarsi nei metodi normali.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Due motivazioni principali:

1. Per fornire i metadati visibili agli analizzatori in fase di compilazione.
2. Per fornire i metadati visibili per la reflection e gli strumenti in fase di esecuzione.

Come esempio di (1): per il codice sensibile alle prestazioni, è utile poter avere un analizzatore che contrassegna quando le chiusure e i delegati vengono allocati per le espressioni lambda che chiudono lo stato.  Spesso uno sviluppatore di tale codice non potrà più acquisire alcuno stato, in modo che il compilatore possa generare un metodo statico e un delegato memorizzabile nella cache per il metodo. in caso contrario, lo sviluppatore assicurerà che l'unico stato chiuso venga `this`, consentendo al compilatore almeno di evitare l'allocazione di un oggetto di chiusura.  Tuttavia, senza il supporto del linguaggio per limitare ciò che può essere acquisito, è troppo facile per chiudere accidentalmente lo stato.  Sarebbe utile se uno sviluppatore potesse annotare le espressioni lambda con attributi per indicare lo stato di cui è consentita la chiusura, ad esempio:

```csharp
[CaptureNone] // can't close over any instance state
[CaptureThis] // can only capture `this` and no other instance state
[CaptureAny] // can close over any instance state
```

Un analizzatore può quindi essere scritto per contrassegnare quando lo stato viene acquisito in modo non corretto, ad esempio:

```csharp
var results = collection.Select([CaptureNone](i) => Process(item)); // Analyzer error: [CaptureNone] lambdas captures `this`
...
private U Process(T item) { ... }
```

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

- Utilizzando la stessa sintassi di attributo dei metodi normali, gli attributi possono essere applicati all'inizio di un metodo lambda o anonimo, ad esempio:

```csharp
[SomeAttribute(...)] () => { ... }
[SomeAttribute(...)] delegate (int i) { ... }
```

- Per evitare ambiguità in merito al fatto che un attributo venga applicato al metodo lambda o a uno degli argomenti, gli attributi possono essere utilizzati solo quando le parentesi sono utilizzate intorno a qualsiasi argomento, ad esempio:

```csharp
[SomeAttribute] i => { ... } // ERROR
[SomeAttribute] (i) => { ... } // Ok
[SomeAttribute] (int i) => { ... } // Ok
```

- Con i metodi anonimi, le parentesi non sono necessarie per applicare un attributo al metodo prima della parola chiave `delegate`, ad esempio:

```csharp
[SomeAttribute] delegate { ... } // Ok
[SomeAttribute] delegate (int i) => { ... } // Ok
```

- È possibile applicare più attributi, tramite la sintassi standard delimitato da virgole o tramite la sintassi dell'attributo completo, ad esempio:

```csharp
[FirstAttribute, SecondAttribute] (i) => { ... } // Ok
[FirstAttribute] [SecondAttribute] (i) => { .... } // Ok
```

- Gli attributi possono essere applicati ai parametri a un metodo anonimo o a un'espressione lambda, ma solo quando le parentesi vengono usate intorno a qualsiasi argomento, ad esempio:

```csharp
[SomeAttribute] i => { ... } // ERROR
([SomeAttribute] i) => { .... } // Ok
([SomeAttribute] int i) => { ... } // Ok
([SomeAttribute] i, [SomeOtherAttribute] j) => { ... } // Ok
```

- È possibile applicare più attributi ai parametri di un metodo anonimo o di un'espressione lambda, usando la sintassi delimitata da virgole o con attributi completi, ad esempio:

```csharp
([FirstAttribute, SecondAttribute] i) => { ... } // Ok
([FirstAttribute] [SecondAttribute] i) => { ... } // Ok
```

- gli attributi di destinazione `return`possono essere usati anche nelle espressioni lambda, ad esempio:

```csharp
([return: SomeAttribute] (i) => { ... }) // Ok
```

- Il compilatore genera gli attributi nel metodo e negli argomenti generati come per qualsiasi altro metodo.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

n/d

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

n/d

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

n/d

## <a name="design-meetings"></a>Riunioni di progettazione

n/d