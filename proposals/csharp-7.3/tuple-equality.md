---
ms.openlocfilehash: f238a711e710bbac7f5b7400fa938bd85dec00c6
ms.sourcegitcommit: 5278336b61519956240a6f7d83bcb4322019e032
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79485252"
---
# <a name="support-for--and--on-tuple-types"></a>Supporto per = = e! = sui tipi di tupla

Consenti espressioni `t1 == t2` dove `t1` e `t2` sono tuple o tipi di tupla Nullable della stessa cardinalità e li valutano approssimativamente come `temp1.Item1 == temp2.Item1 && temp1.Item2 == temp2.Item2` (presupponendo `var temp1 = t1; var temp2 = t2;`).

Al contrario, consente di `t1 != t2` e di valutarla come `temp1.Item1 != temp2.Item1 || temp1.Item2 != temp2.Item2`.

Nel caso nullable vengono utilizzati controlli aggiuntivi per `temp1.HasValue` e `temp2.HasValue`. Ad esempio, `nullableT1 == nullableT2` restituisce come `temp1.HasValue == temp2.HasValue ? (temp1.HasValue ? ... : true) : false`.

Quando un confronto con elementi restituisce un risultato non bool, ad esempio quando viene usato un `operator ==` o un `operator !=` non bool definito dall'utente o in un confronto dinamico, tale risultato verrà convertito in `bool` o eseguito tramite `operator true` o `operator false` per ottenere un `bool`. Il confronto della tupla termina sempre con la restituzione di un `bool`.

A partire C# da 7,2, tale codice genera un errore (`error CS0019: Operator '==' cannot be applied to operands of type '(...)' and '(...)'`), a meno che non esista un `operator==`definito dall'utente.

## <a name="details"></a>Dettagli

Quando si associa l'operatore `==` (o `!=`), le regole esistenti sono: (1) case dinamico, (2) risoluzione dell'overload e (3) hanno esito negativo.
Questa proposta aggiunge un case di tupla compreso tra (1) e (2): se entrambi gli operandi di un operatore di confronto sono Tuple (i tipi di tupla o sono valori letterali di tupla) e hanno cardinalità corrispondente, il confronto viene eseguito per elemento. Questa uguaglianza di tupla viene inoltre sollevata sulle tuple Nullable.

Entrambi gli operandi (e, nel caso dei valori letterali di tupla, i rispettivi elementi) vengono valutati in ordine da sinistra a destra. Ogni coppia di elementi viene quindi utilizzata come operandi per associare l'operatore `==` (o `!=`), in modo ricorsivo. Tutti gli elementi con tipo in fase di compilazione `dynamic` generano un errore. I risultati di questi confronti per elementi vengono utilizzati come operandi in una catena di operatori condizionali AND (or).

Nel contesto di `(int, (int, int)) t1, t2;`, ad esempio, `t1 == (1, (2, 3))` valuterà come `temp1.Item1 == temp2.Item1 && temp1.Item2.Item1 == temp2.Item2.Item1 && temp2.Item2.Item2 == temp2.Item2.Item2`.

Quando un valore letterale di tupla viene usato come operando (su entrambi i lati), riceve un tipo di tupla convertito formato dalle conversioni degli elementi che vengono introdotte quando si associa l'operatore `==` (o `!=`) elemento per elemento. 

In `(1L, 2, "hello") == (1, 2L, null)`, ad esempio, il tipo convertito per entrambi i valori letterali di tupla è `(long, long, string)` e il secondo valore letterale non ha un tipo naturale.


### <a name="deconstruction-and-conversions-to-tuple"></a>Decostruzione e conversioni in tupla
In `(a, b) == x`, il fatto che `x` possibile decostruire in due elementi non riveste un ruolo. Questo potrebbe essere in una proposta futura, anche se potrebbe generare domande su `x == y` (si tratta di un confronto semplice o di un confronto degli elementi e, in caso affermativo, usando la cardinalità?).
Analogamente, le conversioni in tupla non svolgono alcun ruolo.

### <a name="tuple-element-names"></a>Nomi di elementi di tupla

Quando si converte un valore letterale di tupla, viene visualizzato un avviso quando nel valore letterale è stato specificato un nome di elemento di tupla esplicito, ma non corrisponde al nome dell'elemento della tupla
Si usa la stessa regola nel confronto tra tuple, in modo che presupponendo `(int a, int b) t` venga visualizzato un avviso in caso di `d` in `t == (c, d: 0)`.

### <a name="non-bool-element-wise-comparison-results"></a>Risultati di confronto elemento non bool

Se un confronto a livello di elemento è dinamico in un'uguaglianza di tupla, viene usata una chiamata dinamica dell'operatore `false` e viene negato il tentativo di ottenere un `bool` e continuare con ulteriori confronti di elementi. 

Se un confronto con elementi restituisce un altro tipo non bool in un'uguaglianza di tupla, esistono due casi:
- Se il tipo non bool viene convertito in `bool`, viene applicata la conversione.
- Se non esiste una conversione di questo tipo, ma il tipo ha un operatore `false`, lo utilizzeremo e negherà il risultato.

In una tupla di disuguaglianza, valgono le stesse regole, con la differenza che verrà usato l'operatore `true` (senza negazione) invece dell'operatore `false`.

Tali regole sono simili alle regole necessarie per l'utilizzo di un tipo non bool in un'istruzione `if` e in altri contesti esistenti.

## <a name="evaluation-order-and-special-cases"></a>Ordine di valutazione e casi speciali
Il valore del lato sinistro viene valutato per primo, quindi il valore del lato destro, quindi i confronti elemento per elemento da sinistra a destra (incluse le conversioni e con inizio uscita in base alle regole esistenti per gli operatori condizionali e/o).

Ad esempio, se è presente una conversione dal tipo `A` al tipo `B` e un metodo `(A, A) GetTuple()`, la valutazione di `(new A(1), (new B(2), new B(3))) == (new B(4), GetTuple())` significa:
- `new A(1)`
- `new B(2)`
- `new B(3)`
- `new B(4)`
- `GetTuple()`
- vengono quindi valutate le conversioni e i confronti degli elementi e la logica condizionale (convertire `new A(1)` nel tipo `B`, quindi confrontarli con `new B(4)`e così via).

### <a name="comparing-null-to-null"></a>Confronto tra `null` e `null`

Si tratta di un caso speciale di confronti regolari, che si riferisce ai confronti di tupla. Il confronto tra `null == null` è consentito e i valori letterali `null` non ottengono alcun tipo.
Nell'uguaglianza della tupla, ciò significa che è consentito anche `(0, null) == (0, null)` e i valori letterali `null` e tupla non ottengono un tipo.

### <a name="comparing-a-nullable-struct-to-null-without-operator"></a>Confronto di uno struct Nullable per `null` senza `operator==`

Si tratta di un altro caso speciale dei confronti regolari, che viene riportato ai confronti di Tuple.
Se si dispone di un `struct S` senza `operator==`, il confronto `(S?)x == null` è consentito e viene interpretato come `((S?).x).HasValue`.
Nell'uguaglianza della tupla viene applicata la stessa regola, pertanto `(0, (S?)x) == (0, null)` è consentita.

## <a name="compatibility"></a>Compatibilità

Se un utente ha scritto tipi di `ValueTuple` personalizzati con un'implementazione dell'operatore di confronto, sarebbe stato prelevato in precedenza dalla risoluzione dell'overload. Tuttavia, poiché il nuovo caso di tupla precede la risoluzione dell'overload, questo caso verrà gestito con il confronto delle tuple invece di basarsi sul confronto definito dall'utente.

----

Si riferisce agli [operatori relazionali e di test del tipo](../../spec/expressions.md#relational-and-type-testing-operators) che si riferiscono a [#190](https://github.com/dotnet/csharplang/issues/190)
