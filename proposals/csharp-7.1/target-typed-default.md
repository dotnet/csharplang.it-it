---
ms.openlocfilehash: 1852356b830e29c3537a4de559fef32fd2c2f8b6
ms.sourcegitcommit: f7952cdddf85316a4beec493a0ecc14fcb3241c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "79484986"
---
# <a name="target-typed-default-literal"></a>Valore letterale "default" tipizzato come destinazione

* [x] proposto
* Prototipo [x]
* Implementazione di [x]
* [] Specifica

## <a name="summary"></a>Summary
[summary]: #summary

La funzionalità `default` tipizzata come destinazione è una variante più breve dell'operatore `default(T)`, che consente di omettere il tipo. Il tipo viene dedotto da target-typing. A parte questo, si comporta come `default(T)`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

La motivazione principale è evitare di digitare informazioni ridondanti.

Ad esempio, quando si richiama `void Method(ImmutableArray<SomeType> array)`, il valore letterale *predefinito* consente `M(default)` al posto di `M(default(ImmutableArray<SomeType>))`.

Questa operazione è applicabile in diversi scenari, ad esempio:

- Dichiarazione di variabili locali (`ImmutableArray<SomeType> x = default;`)
- operazioni ternarie (`var x = flag ? default : ImmutableArray<SomeType>.Empty;`)
- restituzione di metodi e espressioni lambda (`return default;`)
- Dichiarazione dei valori predefiniti per i parametri facoltativi (`void Method(ImmutableArray<SomeType> arrayOpt = default)`)
- inclusione di valori predefiniti nelle espressioni di creazione di matrici (`var x = new[] { default, ImmutableArray.Create(y) };`)


## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Viene introdotta una nuova espressione, il valore letterale *predefinito* . Un'espressione con questa classificazione può essere convertita in modo implicito in qualsiasi tipo, mediante una *conversione di valore letterale predefinito*. 

L'inferenza del tipo per il valore letterale *predefinito* funziona come per il valore letterale *null* , con la differenza che è consentito qualsiasi tipo (non solo i tipi di riferimento).

Questa conversione genera il valore predefinito del tipo dedotto.

Il valore letterale *predefinito* può avere un valore costante, a seconda del tipo derivato. `const int x = default;` è quindi valido, ma `const int? y = default;` non lo è.

Il valore letterale *predefinito* può essere l'operando degli operatori di uguaglianza, purché l'altro operando abbia un tipo. Quindi `default == x` e `x == default` sono espressioni valide, ma `default == default` non è valido.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Uno svantaggio minore è che il valore letterale *predefinito* può essere utilizzato al posto di un valore letterale *null* nella maggior parte dei contesti. Due eccezioni sono `throw null;` e `null == null`, che sono consentite per il valore letterale *null* , ma non per il valore letterale *predefinito* .

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Ci sono due alternative da considerare:

- Status quo: la funzionalità non è giustificata per i propri meriti e gli sviluppatori continuano a usare l'operatore predefinito con un tipo esplicito.
- Estensione del valore letterale null: si tratta dell'approccio VB con `Nothing`. Potremmo consentire `int x = null;`.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [x] deve essere consentito per *impostazione predefinita* come operando di *è* o *come* operatori? Risposta: non consentire `default is T`, consentire `x is default`, consentire `default as RefType` (con avviso always-null)

## <a name="design-meetings"></a>Riunioni di progettazione

- [LDM 3/7/2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-07.md)
- [LDM 3/28/2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-28.md)
- [LDM 5/31/2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md#default-in-operators)
