---
ms.openlocfilehash: 138d66c669e5db527ffe24d2bbb004b5fd2f6db9
ms.sourcegitcommit: 00b0f04deb7ad7b7a1f16ed8e9ec40620d87e774
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250407"
---
# <a name="lambda-improvements"></a>Miglioramenti lambda

## <a name="summary"></a>Riepilogo
Modifiche proposte:
1. Consenti le espressioni lambda con attributi
2. Consenti le espressioni lambda con tipo restituito esplicito
3. Dedurre un tipo delegato naturale per le espressioni lambda e i gruppi di metodi

## <a name="motivation"></a>Motivazione
Il supporto per gli attributi sulle espressioni lambda fornirebbe parità con i metodi e le funzioni locali.

Il supporto per i tipi restituiti espliciti fornirebbe la simmetria con i parametri lambda in cui è possibile specificare tipi espliciti.
Consentire i tipi restituiti espliciti fornisce anche il controllo sulle prestazioni del compilatore nelle espressioni lambda nidificate in cui la risoluzione dell'overload deve associare il corpo dell'espressione lambda al momento per determinare la firma.

Un tipo naturale per le espressioni lambda e i gruppi di metodi consentirà più scenari in cui è possibile usare le espressioni lambda e i gruppi di metodi senza un tipo delegato esplicito, anche come inizializzatori nelle `var` dichiarazioni.

La richiesta di tipi delegati espliciti per le espressioni lambda e i gruppi di metodi è un punto di attrito per i clienti ed è diventato un ostacolo per lo stato di avanzamento in ASP.NET con lavoro recente in [MapAction](https://github.com/dotnet/aspnetcore/pull/29878).

[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) senza modifiche proposte ( `MapAction()` accetta un `System.Delegate` argomento):
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction((Func<Todo>)GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction((Func<Todo, Todo>)PostTodo);
```

[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) con tipi naturali per i gruppi di metodi:
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction(GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction(PostTodo);
```

[MapAction ASP.NET](https://github.com/dotnet/aspnetcore/pull/29878) con attributi e tipi naturali per le espressioni lambda:
```csharp
app.MapAction([HttpGet("/")] () => new Todo(Id: 0, Name: "Name"));
app.MapAction([HttpPost("/")] ([FromBody] Todo todo) => todo);
```

## <a name="attributes"></a>Attributi
Gli attributi possono essere aggiunti alle espressioni lambda.
```csharp
f = [MyAttribute] x => x;          // [MyAttribute]lambda
f = [MyAttribute] (int x) => x;    // [MyAttribute]lambda
f = [MyAttribute] static x => x;   // [MyAttribute]lambda
f = [return: MyAttribute] () => 1; // [return: MyAttribute]lambda
```

_Se gli attributi vengono aggiunti all'intera espressione, le parentesi devono essere necessarie per l'elenco di parametri? (Non `[MyAttribute] x => x` è consentito? In caso affermativo, cosa accade `[MyAttribute] static x => x` ?)_

Gli attributi possono essere aggiunti ai parametri lambda dichiarati con tipi espliciti.
```csharp
f = ([MyAttribute] x) => x;      // syntax error
f = ([MyAttribute] int x) => x;  // [MyAttribute]x
```

Gli attributi non sono supportati per i metodi anonimi dichiarati con la `delegate { }` sintassi.
```csharp
f = [MyAttribute] delegate { return 1; };         // syntax error
f = delegate ([MyAttribute] int x) { return x; }; // syntax error
```

Gli attributi nell'espressione lambda o nei parametri lambda verranno inviati ai metadati nel metodo che esegue il mapping all'espressione lambda.

In generale, i clienti non devono dipendere dal modo in cui le espressioni lambda e le funzioni locali vengono mappate dall'origine ai metadati. Il modo in cui vengono emesse le espressioni lambda e le funzioni locali può essere modificato tra le versioni del compilatore.

Le modifiche proposte sono destinate allo `Delegate` scenario guidato.
Deve essere valido per esaminare l'oggetto `MethodInfo` associato a un' `Delegate` istanza di per determinare la firma dell'espressione lambda o della funzione locale, inclusi eventuali attributi espliciti e metadati aggiuntivi emessi dal compilatore, ad esempio i parametri predefiniti.
Questo consente ai team come ASP.NET di rendere disponibili gli stessi comportamenti per le espressioni lambda e le funzioni locali come metodi normali.

## <a name="explicit-return-type"></a>Tipo restituito esplicito
È possibile specificare un tipo restituito esplicito dopo l'elenco di parametri.
```csharp
f = () : T => default;              // () : T
f = x : short => 1;                 // <unknown> : short
f = (ref int x) : ref int => ref x; // ref int : ref int
f = static _ : void => { };         // <unknown> : void
```

I tipi restituiti espliciti non sono supportati per i metodi anonimi dichiarati con la `delegate { }` sintassi.
```csharp
f = delegate : int { return 1; };         // syntax error
f = delegate (int x) : int { return x; }; // syntax error
```

## <a name="natural-delegate-type"></a>Tipo delegato naturale
Un'espressione lambda ha un tipo naturale se i tipi di parametri sono espliciti e il tipo restituito è esplicito o se è presente un tipo comune tra i tipi naturali di tutte le `return` espressioni nel corpo.

Il tipo naturale è un tipo delegato in cui i tipi di parametro sono i tipi di parametro lambda espliciti e il tipo restituito `R` è:
- Se il tipo restituito lambda è esplicito, viene usato il tipo.
- Se l'espressione lambda non ha espressioni restituite, il tipo restituito è `void` o `System.Threading.Tasks.Task` se `async` ;
- Se il tipo comune del tipo naturale di tutte le `return` espressioni nel corpo è il tipo `R0` , il tipo restituito è `R0` o `System.Threading.Tasks.Task<R0>` se `async` .

Un gruppo di metodi ha un tipo naturale se il gruppo di metodi contiene un solo metodo.

Un gruppo di metodi può fare riferimento ai metodi di estensione. In genere, la risoluzione del gruppo di metodi cerca i metodi di estensione in modo differito, solo scorrendo gli ambiti dello spazio dei nomi successivi fino a trovare i metodi di estensione che corrispondono al tipo Per determinare il tipo naturale, tuttavia, è necessaria la ricerca in tutti gli ambiti dello spazio dei nomi. _Per ridurre al minimo il binding non necessario, il tipo naturale dovrebbe essere calcolato solo nei casi in cui non esiste un tipo di destinazione, ovvero solo per calcolare il tipo naturale nei casi in cui è necessario._

Il tipo delegato per l'espressione lambda o il gruppo di metodi e i tipi di parametro `P1, ..., Pn` e il tipo restituito `R` è:
- Se un parametro o un valore restituito non è per valore o se sono presenti più di 16 parametri oppure se uno dei tipi di parametro o restituisce non sono argomenti di tipo validi (ad indicare `(int* p) => { }` ), il delegato è un `internal` tipo delegato anonimo sintetizzato con firma che corrisponde al gruppo di metodi o lambda e con i nomi di parametro `arg1, ..., argn` o `arg` se un singolo parametro;
- Se `R` è `void` , il tipo delegato è `System.Action<P1, ..., Pn>` ;
- in caso contrario, il tipo delegato è `System.Func<P1, ..., Pn, R>` .

`modopt()` o `modreq()` nella firma del gruppo di metodi vengono ignorati nel tipo delegato corrispondente.

Se due espressioni lambda o gruppi di metodi nella stessa compilazione richiedono tipi delegati sintetizzati con gli stessi tipi di parametro e modificatori e lo stesso tipo restituito e gli stessi modificatori, il compilatore utilizzerà lo stesso tipo delegato sintetizzato.

Le espressioni lambda o i gruppi di metodi con tipi naturali possono essere usati come inizializzatori nelle `var` dichiarazioni.

```csharp
var f1 = () => default;        // error: no natural type
var f2 = x => { };             // error: no natural type
var f3 = x => x;               // error: no natural type
var f4 = () => 1;              // System.Func<int>
var f5 = () : string => null;  // System.Func<string>
```

```csharp
static void F1() { }
static void F1<T>(this T t) { }
static void F2(this string s) { }

var f6 = F1;    // error: multiple methods
var f7 = "".F1; // System.Action
var f8 = F2;    // System.Action<string> 
```

I tipi delegati sintetizzati sono implicitamente co-e controvariante.
```csharp
var fA = (IEnumerable<string> e, ref int i) => { }; // void DA$(IEnumerable<string>, ref int);
fA = (IEnumerable<object> e, ref int i) => { };     // ok

var fB = (IEnumerable<object> e, ref int i) => { }; // void DB$(IEnumerable<object>, ref int);
fB = (IEnumerable<string> e, ref int i) => { };     // error: parameter type mismatch
```

### <a name="implicit-conversion-to-systemdelegate"></a>Conversione implicita in `System.Delegate`
Una conseguenza dell'inferenza di un tipo naturale è che le espressioni lambda e i gruppi di metodi con tipo naturale sono convertibili in modo implicito in `System.Delegate` .
```csharp
static void Invoke(Func<string> f) { }
static void Invoke(Delegate d) { }

static string GetString() => "";
static int GetInt() => 0;

Invoke(() => "");  // Invoke(Func<string>)
Invoke(() => 0);   // Invoke(Delegate) [new]

Invoke(GetString); // Invoke(Func<string>)
Invoke(GetInt);    // Invoke(Delegate) [new]
```

Se non è possibile dedurre un tipo naturale, non esiste alcuna conversione implicita in `System.Delegate` .
```csharp
static void Invoke(Delegate d) { }

Invoke(Console.WriteLine); // error: cannot to 'Delegate'; multiple candidate methods
Invoke(x => x);            // error: cannot to 'Delegate'; no natural type for 'x'
```

Per evitare una modifica di rilievo, verrà aggiornata la risoluzione dell'overload per preferire le espressioni e i delegati fortemente tipizzati `System.Delegate` .
_Nell'esempio seguente viene illustrata la regola di suddivisione delle espressioni lambda. Esiste un esempio equivalente per i gruppi di metodi?_
```csharp
static void Execute(Expression<Func<string>> e) { }
static void Execute(Delegate d) { }

static string GetString() => "";
static int GetInt() => 0;

Execute(() => "");  // Execute(Expression<Func<string>>) [tie-breaker]
Execute(() => 0);   // Execute(Delegate) [new]

Execute(GetString); // Execute(Delegate) [new]
Execute(GetInt);    // Execute(Delegate) [new]
```

## <a name="syntax"></a>Sintassi

```antlr
lambda_expression
  : attribute_list* modifier* lambda_parameters (':' type)? '=>' (block | body)
  ;

lambda_parameters
  : lambda_parameter
  | '(' (lambda_parameter (',' lambda_parameter)*)? ')'
  ;

lambda_parameter
  : identifier
  | (attribute_list* modifier* type)? identifier equals_value_clause?
  ;
```

_La `: type` sintassi del tipo restituito introduce ambiguità con `?:` che non possono essere risolte facilmente?_

_È necessario consentire gli attributi sui parametri senza tipi espliciti, ad esempio `([MyAttribute] x) => { }` ? Non sono consentiti modificatori sui parametri senza tipi espliciti, ad esempio `(ref x) => { }` ._

## <a name="design-meetings"></a>Riunioni di progettazione

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-03-03.md
