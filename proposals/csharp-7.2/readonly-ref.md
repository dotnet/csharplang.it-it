---
ms.openlocfilehash: 39fb0aab5e8bb0d422f25fd2e92ab3d8256d3f59
ms.sourcegitcommit: b8f1103eb686c5d82e294837c9386d9b667da292
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "79485112"
---
# <a name="readonly-references"></a>Riferimenti di sola lettura

* [x] proposto
* Prototipo [x]
* Implementazione di [x]: avviata
* [] Specifica: non avviata

## <a name="summary"></a>Summary
[summary]: #summary

La funzionalità "riferimenti di sola lettura" è in realtà un gruppo di funzionalità che sfruttano l'efficienza del passaggio di variabili in base al riferimento, ma senza esporre i dati alle modifiche:  
- parametri `in`
- Valori restituiti `ref readonly`
- struct di `readonly`
- `ref`/metodi di estensione `in`
- variabili locali `ref readonly`
- `ref` espressioni condizionali

## <a name="passing-arguments-as-readonly-references"></a>Passaggio di argomenti come riferimenti di sola lettura.

Esiste una proposta esistente che tocca questo argomento https://github.com/dotnet/roslyn/issues/115 come un caso speciale di parametri ReadOnly senza passare a molti dettagli.
Qui voglio solo confermare che l'idea non è stata molto nuova.

### <a name="motivation"></a>Motivazione

Prima di questa funzionalità C# non disponeva di un modo efficiente per esprimere il desiderio di passare le variabili struct nelle chiamate al metodo per le operazioni di sola lettura senza alcuna modifica. Il passaggio di un argomento per valore normale implica la copia, che aggiunge costi superflui.  Che consente agli utenti di usare l'argomento per-Ref passando e si basano su Commenti/documentazione per indicare che i dati non devono essere mutati dal chiamato. Non è una soluzione ideale per molti motivi.  
Gli esempi sono gli operatori matematici di tipo Vector/Matrix nelle librerie grafiche, come [XNA](https://msdn.microsoft.com/library/bb194944.aspx) , che hanno operandi di riferimento esclusivamente a causa di considerazioni sulle prestazioni. Nel compilatore Roslyn è presente codice che usa gli struct per evitare allocazioni e quindi li passa per riferimento per evitare di copiare i costi.

### <a name="solution-in-parameters"></a>Soluzione (parametri`in`)

Analogamente ai parametri `out`, `in` parametri vengono passati come riferimenti gestiti con garanzie aggiuntive da parte del chiamato.  
A differenza dei parametri di `out` che _devono_ essere assegnati dal chiamato prima di qualsiasi altro uso, non è possibile assegnare `in` parametri al chiamato.

Di conseguenza `in` parametri consentono l'efficacia del passaggio di argomenti indiretti senza esporre argomenti alle mutazioni da parte del chiamato.

### <a name="declaring-in-parameters"></a>Dichiarazione di parametri `in`

`in` parametri vengono dichiarati utilizzando `in` parola chiave come modificatore nella firma del parametro.

Per tutti gli scopi, il parametro `in` viene considerato come una variabile di `readonly`. La maggior parte delle restrizioni relative all'uso dei parametri `in` all'interno del metodo è identica a quella dei campi `readonly`.

> Un parametro `in` può infatti rappresentare un campo di `readonly`. La somiglianza delle restrizioni non è una coincidenza.

Per esempio, i campi di un parametro di `in` con un tipo struct sono ricorsivi in modo ricorsivo come `readonly` variabili.

```csharp
static Vector3 Add (in Vector3 v1, in Vector3 v2)
{
    // not OK!!
    v1 = default(Vector3);

    // not OK!!
    v1.X = 0;

    // not OK!!
    foo(ref v1.X);

    // OK
    return new Vector3(v1.X + v2.X, v1.Y + v2.Y, v1.Z + v2.Z);
}
```

- `in` parametri sono consentiti in qualsiasi punto in cui sono consentiti parametri ByVal comuni. Sono inclusi gli indicizzatori, gli operatori (incluse le conversioni), i delegati, le espressioni lambda, le funzioni locali.

> ```csharp
>  (in int x) => x                                                     // lambda expression  
>  TValue this[in TKey index];                                         // indexer
>  public static Vector3 operator +(in Vector3 x, in Vector3 y) => ... // operator
>  ```

- `in` non è consentita in combinazione con `out` o con qualsiasi elemento che `out` non è combinato con.

- Non è consentito eseguire l'overload in `ref`/`out`/differenze `in`.

- È consentita l'overload delle differenze comuni tra ByVal e `in`.

- Ai fini di OHI (overload, nascondiglio, implementazione) `in` si comporta in modo analogo a un parametro di `out`.
Si applicano tutte le stesse regole.
Ad esempio, il metodo di override dovrà corrispondere `in` parametri con `in` parametri di un tipo convertibile in identità.

- Per le conversioni di gruppi di delegati/lambda/metodi, `in` si comporta in modo analogo a un parametro di `out`.
Le espressioni lambda e i candidati per la conversione dei gruppi di metodi applicabili dovranno corrispondere `in` parametri del delegato di destinazione con `in` parametri di un tipo convertibile in identità.

- Ai fini della varianza generica, i parametri `in` sono non variant.

> Nota: non sono presenti avvisi nei parametri `in` con tipi di riferimento o primitivi.
Potrebbe essere inutile in generale, ma in alcuni casi l'utente deve/vuole passare le primitive come `in`. Esempi: override di un metodo generico come `Method(in T param)` quando `T` è stato sostituito da `int`o quando si hanno metodi come `Volatile.Read(in int location)`
>
> È preferibile disporre di un analizzatore in grado di avvisare in caso di uso inefficiente di parametri di `in`, ma le regole per tale analisi sarebbero troppo fuzzy per essere parte di una specifica del linguaggio.

### <a name="use-of-in-at-call-sites-in-arguments"></a>Uso di `in` nei siti di chiamata. (argomenti`in`)

Esistono due modi per passare argomenti a `in` parametri.

#### <a name="in-arguments-can-match-in-parameters"></a>`in` argomenti possono corrispondere `in` parametri:

Un argomento con un modificatore `in` nel sito di chiamata può corrispondere `in` parametri.

```csharp
int x = 1;

void M1<T>(in T x)
{
  // . . .
}

var x = M1(in x);  // in argument to a method

class D
{
    public string this[in Guid index];
}

D dictionary = . . . ;
var y = dictionary[in Guid.Empty]; // in argument to an indexer
```

- `in` argomento deve essere un lvalue _leggibile_ (*).
Esempio: `M1(in 42)` non è valido

> (*) Il concetto di [lvalue/rvalue](https://en.wikipedia.org/wiki/Value_(computer_science)#lrvalue) varia tra le lingue.  
Qui, da LValue, intendo un'espressione che rappresenta una posizione a cui è possibile fare riferimento direttamente.
E RValue indica un'espressione che produce un risultato temporaneo che non viene mantenuto autonomamente.  

- In particolare, è possibile passare `readonly` campi, `in` parametri o altre variabili formalmente `readonly` come argomenti `in`.
Esempio: `dictionary[in Guid.Empty]` è valido. `Guid.Empty` è un campo di sola lettura statico.

- `in` argomento deve avere identità del tipo _convertibile_ nel tipo di parametro.
Esempio: `M1<object>(in Guid.Empty)` non è valido. `Guid.Empty` non è _convertibile in identità_ in `object`

La motivazione per le regole precedenti è che `in` argomenti garantiscono l' _aliasing_ della variabile dell'argomento. Il chiamato riceve sempre un riferimento diretto alla stessa posizione rappresentata dall'argomento.

- in rari casi in cui gli argomenti di `in` devono essere distribuiti in modo stack a causa di espressioni `await` utilizzate come operandi della stessa chiamata, il comportamento è identico a quello degli argomenti `out` e `ref`. se la variabile non può essere distribuita in modo trasparente, viene restituito un errore.

Esempi:
1. `M1(in staticField, await SomethingAsync())` valido.
`staticField` è un campo statico a cui è possibile accedere più di una volta senza effetti collaterali osservabili. È quindi possibile fornire sia l'ordine degli effetti collaterali che i requisiti di aliasing.
2. `M1(in RefReturningMethod(), await SomethingAsync())` produrrà un errore.
`RefReturningMethod()` è un `ref` metodo restituito. Una chiamata al metodo può avere effetti collaterali osservabili, pertanto deve essere valutata prima dell'operando `SomethingAsync()`. Tuttavia, il risultato della chiamata è un riferimento che non può essere mantenuto nell'`await` punto di sospensione che rende impossibile il requisito di riferimento diretto.

> Nota: gli errori di distribuzione dello stack sono considerati limitazioni specifiche dell'implementazione. Pertanto non hanno effetto sulla risoluzione dell'overload o sull'inferenza lambda.

#### <a name="ordinary-byval-arguments-can-match-in-parameters"></a>Gli argomenti ByVal comuni possono corrispondere `in` parametri:

Gli argomenti regolari senza modificatori possono corrispondere a `in` parametri. In tal caso, gli argomenti hanno gli stessi vincoli rilassati di un normale argomento ByVal.

La motivazione di questo scenario è che `in` parametri nelle API possono comportare inconvenienti per l'utente quando non è possibile passare gli argomenti come risultati di riferimento diretto, ad esempio valori letterali, calcolati o `await`ed argomenti che hanno tipi più specifici.  
Tutti questi casi hanno una soluzione semplice per archiviare il valore dell'argomento in una lingua locale temporanea di tipo appropriato e passare tale oggetto locale come argomento `in`.  
Per ridurre la necessità del compilatore di codice standard, è possibile eseguire la stessa trasformazione, se necessario, quando `in` modificatore non è presente nel sito di chiamata.  

Inoltre, in alcuni casi, ad esempio la chiamata di operatori o i metodi di estensione `in`, non esiste alcun modo sintattico per specificare `in`. Questo solo richiede la specifica del comportamento degli argomenti ByVal comuni quando corrispondono `in` parametri.

In particolare:

- il passaggio di RValues è valido.
Un riferimento a un oggetto temporaneo viene passato in questo caso.
Esempio:
```csharp
Print("hello");      // not an error.

void Print<T>(in T x)
{
  //. . .
}
```

- sono consentite conversioni implicite.

> Si tratta in realtà di un caso speciale di passaggio di un RValue  

In tal caso, viene passato un riferimento a un valore convertito temporaneo.
Esempio:
```csharp
Print<int>(Short.MaxValue)     // not an error.
```

- nel caso di un destinatario di un metodo di estensione `in`, anziché `ref` metodi di estensione, sono consentite le _conversioni_ di RValues o implicite.
In tal caso, viene passato un riferimento a un valore convertito temporaneo.
Esempio:
```csharp
public static IEnumerable<T> Concat<T>(in this (IEnumerable<T>, IEnumerable<T>) arg)  => . . .;

("aa", "bb").Concat<char>()    // not an error.
```
Altre informazioni su `ref`/`in` metodi di estensione sono fornite più avanti in questo documento.

- la distribuzione di argomenti a causa di `await` operandi potrebbe riportare "per valore", se necessario.
Negli scenari in cui non è possibile fornire un riferimento diretto all'argomento a causa del `await` intermedio, viene invece distribuita una copia del valore dell'argomento.  
Esempio:
```csharp
M1(RefReturningMethod(), await SomethingAsync())   // not an error.
```
Poiché il risultato di una chiamata di effetto collaterale è un riferimento che non può essere mantenuto tra `await` sospensione, viene invece mantenuto un oggetto temporaneo che contiene il valore effettivo (come in un caso di parametro ByVal comune).

#### <a name="omitted-optional-arguments"></a>Argomenti facoltativi omessi

È consentito che un parametro `in` specifichi un valore predefinito. Che rende facoltativo l'argomento corrispondente.

Se si omette l'argomento facoltativo nel sito di chiamata, il valore predefinito viene passato tramite un oggetto temporaneo.

```csharp
Print("hello");      // not an error, same as
Print("hello", c: Color.Black);

void Print(string s, in Color c = Color.Black)
{
    // . . .
}
```

### <a name="aliasing-behavior-in-general"></a>Comportamento degli alias in generale

Proprio come `ref` e `out` variabili, `in` le variabili sono riferimenti/alias alle posizioni esistenti.

Sebbene il chiamato non sia autorizzato a scrivere in essi, la lettura di un parametro `in` può osservare valori diversi come effetto collaterale di altre valutazioni.

Esempio:

```C#
static Vector3 v = Vector3.UnitY;

static void Main()
{
    Test(v);
}

static void Test(in Vector3 v1)
{
    Debug.Assert(v1 == Vector3.UnitY);
    // changes v1 deterministically (no races required)
    ChangeV();
    Debug.Assert(v1 == Vector3.UnitX);
}

static void ChangeV()
{
    v = Vector3.UnitX;
}
```

### <a name="in-parameters-and-capturing-of-local-variables"></a>`in` i parametri e l'acquisizione di variabili locali.  
Ai fini dell'acquisizione lambda/Async `in` parametri si comportano come `out` e `ref` parametri.

- Impossibile acquisire `in` parametri in una chiusura
- parametri di `in` non consentiti nei metodi iterator
- parametri di `in` non consentiti nei metodi asincroni

### <a name="temporary-variables"></a>Variabili temporanee.  
Alcuni usi del passaggio `in` parametro possono richiedere l'uso indiretto di una variabile locale temporanea:  
- `in` argomenti vengono sempre passati come alias diretti quando il sito di chiamata USA `in`. Il tipo temporaneo non viene mai usato in questo caso.
- non è necessario che gli argomenti `in` siano alias diretti quando il sito di chiamata non usa `in`. Quando argument non è un LValue, è possibile usare un oggetto temporaneo.
- `in` parametro può avere un valore predefinito. Quando l'argomento corrispondente viene omesso nel sito di chiamata, il valore predefinito viene passato tramite un oggetto temporaneo.
- `in` argomenti possono avere conversioni implicite, incluse quelle che non conservano l'identità. In questi casi viene usato un oggetto temporaneo.
- i ricevitori di chiamate struct ordinarie non possono essere scrivibili lvalue (**case esistente!** ). In questi casi viene usato un oggetto temporaneo.

Il tempo di vita dell'argomento temporaries corrisponde all'ambito più vicino a quello del sito di chiamata.

Il tempo di vita formale delle variabili temporanee è semanticamente significativo in scenari che coinvolgono l'analisi di escape delle variabili restituite per riferimento.

### <a name="metadata-representation-of-in-parameters"></a>Rappresentazione dei metadati di `in` parametri.
Quando `System.Runtime.CompilerServices.IsReadOnlyAttribute` viene applicato a un parametro ByRef, significa che il parametro è un parametro di `in`.

Inoltre, se il metodo è *astratto* o *virtuale*, la firma di tali parametri (e solo tali parametri) deve avere `modreq[System.Runtime.InteropServices.InAttribute]`.

**Motivazione**: questa operazione viene eseguita per assicurarsi che, in caso di override o implementazione del metodo, i parametri di `in` corrispondano.

Gli stessi requisiti si applicano ai metodi `Invoke` nei delegati.

**Motivazione**: ciò consente di garantire che i compilatori esistenti non possano semplicemente ignorare `readonly` durante la creazione o l'assegnazione di delegati.

## <a name="returning-by-readonly-reference"></a>Restituzione per riferimento di sola lettura.

### <a name="motivation"></a>Motivazione
La motivazione di questa funzionalità secondaria è approssimativamente simmetrica per i motivi della `in` parametri, evitando la copia, ma sul lato di ritorno. Prima di questa funzionalità, un metodo o un indicizzatore aveva due opzioni: 1) restituito per riferimento ed essere esposto a possibili mutazioni o 2) restituito per valore che comporta la copia.

### <a name="solution-ref-readonly-returns"></a>Soluzione (`ref readonly` restituisce)  
La funzionalità consente a un membro di restituire le variabili in base al riferimento senza esporle alle mutazioni.

### <a name="declaring-ref-readonly-returning-members"></a>Dichiarazione di `ref readonly` la restituzione di membri

Una combinazione di modificatori `ref readonly` sulla firma restituita viene utilizzata per indicare che il membro restituisce un riferimento di sola lettura.

Per tutti gli scopi, un membro `ref readonly` viene considerato come una variabile `readonly`, simile a `readonly` campi e parametri `in`.

Ad esempio, i campi di `ref readonly` membro che dispone di un tipo struct sono ricorsivi in modo ricorsivo come `readonly` variabili. -È consentito passarli come argomenti `in`, ma non come argomenti `ref` o `out`.

```csharp
ref readonly Guid Method1()
{
}

Method2(in Method1()); // valid. Can pass as `in` argument.

Method3(ref Method1()); // not valid. Cannot pass as `ref` argument
```

- gli `ref readonly` restituiti sono consentiti nelle stesse posizioni in cui sono consentiti `ref` returni.
Sono inclusi gli indicizzatori, i delegati, le espressioni lambda, le funzioni locali.

- Non è consentito eseguire l'overload di `ref`/`ref readonly`/differenze.

- È consentita l'overload sulle differenze comuni di ByVal e `ref readonly` return.

- Ai fini di OHI (overload, occultamento, implementazione) `ref readonly` è simile ma distinto da `ref`.
Ad esempio, un metodo che esegue l'override di `ref readonly` uno, deve essere `ref readonly` e avere un tipo convertibile di identità.

- Ai fini delle conversioni di gruppi delegate/Lambda/Method, `ref readonly` è simile ma è diverso da `ref`.
Le espressioni lambda e i candidati per la conversione di gruppi di metodi applicabili devono corrispondere `ref readonly` ritorno del delegato di destinazione con `ref readonly` la restituzione del tipo che è convertibile in identità.

- Ai fini della varianza generica, `ref readonly` restituisce un risultato non variant.

> Nota: non sono presenti avvisi per `ref readonly` restituiti con tipi di riferimento o primitivi.
Potrebbe essere inutile in generale, ma in alcuni casi l'utente deve/vuole passare le primitive come `in`. Esempi: override di un metodo generico come `ref readonly T Method()` quando `T` è stato sostituito da `int`.
>
>È preferibile disporre di un analizzatore in grado di avvisare in caso di uso inefficiente di `ref readonly` restituisce, ma le regole per tale analisi sarebbero troppo fuzzy per essere parte di una specifica del linguaggio.

### <a name="returning-from-ref-readonly-members"></a>Restituzione da membri `ref readonly`
All'interno del corpo del metodo la sintassi è identica a quella dei normali ritorni Ref. Il `readonly` verrà dedotto dal metodo contenitore.

La motivazione è che `return ref readonly <expression>` è troppo lungo e consente solo la mancata corrispondenza della parte `readonly` che comporterebbe sempre errori.
Il `ref` è, tuttavia, necessario per garantire la coerenza con altri scenari in cui un elemento viene passato tramite un alias rigoroso rispetto al valore.

> A differenza del caso con `in` parametri, `ref readonly` restituisce mai un risultato tramite una copia locale. Considerato che la copia smetterebbe di esistere immediatamente dopo la restituzione di tale procedura sarebbe inutile e pericolosa. Pertanto `ref readonly` restituisce sempre riferimenti diretti.

Esempio:

```csharp
struct ImmutableArray<T>
{
    private readonly T[] array;

    public ref readonly T ItemRef(int i)
    {
        // returning a readonly reference to an array element
        return ref this.array[i];
    }
}

```

- Un argomento di `return ref` deve essere un LValue (**regola esistente**)
- Un argomento di `return ref` deve essere "safe to return" (**regola esistente**)
- In un membro `ref readonly` _non è necessario che_ un argomento di `return ref` sia scrivibile.
Ad esempio, un membro può restituire Ref, un campo ReadOnly o uno dei relativi parametri `in`.

### <a name="safe-to-return-rules"></a>Regole sicure per la restituzione.
Le normali regole sicure per la restituzione dei riferimenti verranno applicate anche ai riferimenti di sola lettura.

Si noti che è possibile ottenere un `ref readonly` da un normale `ref` locale/parametro/restituito, ma non viceversa. In caso contrario, la sicurezza del `ref readonly` restituisce viene dedotta allo stesso modo del normale `ref` restituisce.

Considerando che RValues può essere passato come parametro di `in` e restituito come `ref readonly` è necessaria una sola regola: **RValues non è sicuro da restituire per riferimento**.

> Si consideri la situazione in cui un RValue viene passato a un parametro di `in` tramite una copia e quindi restituito sotto forma di `ref readonly`. Nel contesto del chiamante il risultato di tale chiamata è un riferimento ai dati locali e, di conseguenza, non è sicuro restituire.
> Una volta che RValues non è sicuro, la regola esistente `#6` gestisce già questo caso.

Esempio:
```csharp
ref readonly Vector3 Test1()
{
    // can pass an RValue as "in" (via a temp copy)
    // but the result is not safe to return
    // because the RValue argument was not safe to return by reference
    return ref Test2(default(Vector3));
}

ref readonly Vector3 Test2(in Vector3 r)
{
    // this is ok, r is returnable
    return ref r;
}
```

Regole `safe to return` aggiornate:

1.  **i riferimenti alle variabili nell'heap possono essere restituiti in modo sicuro**
2.  **i parametri ref/in sono sicuri per restituire**
`in` i parametri possono essere restituiti solo come ReadOnly.
3.  i **parametri out possono essere restituiti in modo sicuro** , ma è necessario assegnarli definitivamente, come già nel caso attuale
4.  **i campi struct dell'istanza possono essere restituiti in modo sicuro finché il ricevitore è sicuro per la restituzione**
5.  **' This ' non è sicuro per restituire i membri struct**
6.  **un riferimento, restituito da un altro metodo, può essere restituito in modo sicuro se tutti i refs/outs passati al metodo come parametri formali erano sicuri da restituire.** 
*in particolare è irrilevante se il ricevitore è sicuro da restituire, indipendentemente dal fatto che il ricevitore sia uno struct, una classe o tipizzato come parametro di tipo generico.*
7.  **RValues non è sicuro restituire per riferimento.** 
in *particolare RValues sono sicure da passare come parametri in.*

> Nota: esistono regole aggiuntive relative alla sicurezza dei ritorni che entrano in gioco quando sono interessati tipi di riferimento e riassegnazioni Ref.
> Le regole si applicano ugualmente ai membri `ref` e `ref readonly` e pertanto non sono indicate in questo argomento.

### <a name="aliasing-behavior"></a>Comportamento degli alias.
`ref readonly` membri forniscono lo stesso comportamento di alias dei membri `ref` ordinari (ad eccezione di ReadOnly).
Per questo motivo, per l'acquisizione di espressioni lambda, async, iteratori, distribuzione dello stack e così via... si applicano le stesse restrizioni. cioè. a causa dell'impossibilità di acquisire i riferimenti effettivi e la natura degli effetti collaterali della valutazione dei membri, tali scenari non sono consentiti.

> È consentita e necessaria per eseguire una copia quando `ref readonly` return è un destinatario di metodi struct normali che accettano `this` come riferimento scrivibile normale. Storicamente, in tutti i casi in cui tali chiamate vengono applicate alla variabile ReadOnly, viene eseguita una copia locale.

### <a name="metadata-representation"></a>Rappresentazione dei metadati.
Quando `System.Runtime.CompilerServices.IsReadOnlyAttribute` viene applicato alla restituzione di un metodo ByRef returning, significa che il metodo restituisce un riferimento di sola lettura.

Inoltre, la firma del risultato di tali metodi (e solo quei metodi) deve avere `modreq[System.Runtime.CompilerServices.IsReadOnlyAttribute]`.

**Motivazione**: per garantire che i compilatori esistenti non possano semplicemente ignorare `readonly` durante la chiamata di metodi con `ref readonly` restituisce

## <a name="readonly-structs"></a>Struct di sola lettura
In breve, funzionalità che rende `this` parametro di tutti i membri di istanza di uno struct, ad eccezione dei costruttori, di un parametro di `in`.

### <a name="motivation"></a>Motivazione
Il compilatore deve presupporre che qualsiasi chiamata al metodo su un'istanza struct possa modificare l'istanza. In realtà, un riferimento scrivibile viene passato al metodo come `this` parametro e Abilita completamente questo comportamento. Per consentire le chiamate in `readonly` variabili, le chiamate vengono applicate alle copie temporanee. Questo potrebbe essere poco intuitivo e talvolta impone agli utenti di abbandonare `readonly` per motivi di prestazioni.  
Esempio: https://codeblog.jonskeet.uk/2014/07/16/micro-optimization-the-surprising-inefficiency-of-readonly-fields/

Dopo l'aggiunta del supporto per `in` parametri e `ref readonly` restituisce il problema della copia difensiva peggiorerà perché le variabili di sola lettura diventeranno più comuni.

### <a name="solution"></a>Soluzione
Consenti `readonly` modificatore per le dichiarazioni di struct che comporterebbe il `this` trattato come `in` parametro in tutti i metodi di istanza struct ad eccezione dei costruttori.

```csharp
static void Test(in Vector3 v1)
{
    // no need to make a copy of v1 since Vector3 is a readonly struct
    System.Console.WriteLine(v1.ToString());
}

readonly struct Vector3
{
    . . .

    public override string ToString()
    {
        // not OK!!  `this` is an `in` parameter
        foo(ref this.X);

        // OK
        return $"X: {X}, Y: {Y}, Z: {Z}";
    }
}
```

### <a name="restrictions-on-members-of-readonly-struct"></a>Restrizioni sui membri dello struct di sola lettura
- I campi di istanza di uno struct di sola lettura devono essere di sola lettura.  
**Motivazione:** è possibile scrivere solo all'esterno, ma non tramite i membri.
- Le proprietà autoproprietà dell'istanza di uno struct di sola lettura devono essere solo Get.  
**Motivazione:** conseguenza della restrizione sui campi di istanza.
- Lo struct di sola lettura non può dichiarare eventi simili a campi.  
**Motivazione:** conseguenza della restrizione sui campi di istanza.

### <a name="metadata-representation"></a>Rappresentazione dei metadati.
Quando `System.Runtime.CompilerServices.IsReadOnlyAttribute` viene applicato a un tipo di valore, significa che il tipo è un `readonly struct`.

In particolare:
-  L'identità del tipo di `IsReadOnlyAttribute` non è importante. Infatti, se necessario, può essere incorporato dal compilatore nell'assembly contenitore.

## <a name="refin-extension-methods"></a>`ref`/metodi di estensione `in`
Esiste effettivamente una proposta esistente (https://github.com/dotnet/roslyn/issues/165) e la richiesta pull del prototipo corrispondente (https://github.com/dotnet/roslyn/pull/15650).
Voglio solo confermare che questa idea non è completamente nuova. Tuttavia, è pertinente in questo caso poiché `ref readonly` rimuovono in modo elegante il problema più contenzioso su tali metodi, cosa fare con i ricevitori RValue.

L'idea generale è consentire ai metodi di estensione di prendere il `this` parametro per riferimento, purché il tipo sia noto come tipo di struct.

```csharp
public static void Extension(ref this Guid self)
{
    // do something
}
```

I motivi per la scrittura di tali metodi di estensione sono principalmente:  
1.  Evitare la copia quando Receiver è uno struct di grandi dimensioni
2.  Consenti la mutazione di metodi di estensione su struct

I motivi per cui non si vuole consentire questa impostazione sulle classi  
1.  Lo scopo è molto limitato.
2.  In questo modo si interrompe un'invarianza a lungo termine che una chiamata al metodo non può trasformare il ricevitore non`null` per diventare `null` dopo la chiamata.
> Attualmente, una variabile non`null` non può diventare `null` a meno che non venga assegnata o passata in _modo esplicito_ da `ref` o `out`.
> Che facilita significativamente la leggibilità o altre forme di analisi "può essere un valore null".
3.  Sarebbe difficile riconciliare con la semantica "Evaluate once" degli accessi condizionali null.
Esempio: `obj.stringField?.RefExtension(...)`-è necessario acquisire una copia di `stringField` per rendere il controllo null significativo, ma le assegnazioni a `this` all'interno di RefExtension non verranno riflesse nel campo.

La possibilità di dichiarare metodi di estensione su **struct** che accettano il primo argomento per riferimento è una richiesta di lunga durata. Una delle considerazioni di blocco è stata "cosa accade se il ricevitore non è un LValue?".

- C'è un precedente che qualsiasi metodo di estensione può essere chiamato anche come metodo statico (a volte è l'unico modo per risolvere l'ambiguità). Si impone che i ricevitori RValue non siano consentiti.
- D'altra parte, è necessario eseguire una chiamata in una copia in situazioni simili quando sono interessati i metodi di istanza struct.

Il motivo per cui esiste la "copia implicita" è dato dal fatto che la maggior parte dei metodi struct non modifica effettivamente lo struct e non è in grado di indicare. Quindi, la soluzione più pratica consisteva nel creare solo la chiamata a una copia, ma questa procedura è nota per danneggiare le prestazioni e causare bug.

A questo punto, con la disponibilità dei parametri di `in`, è possibile che un'estensione segnali lo scopo. Di conseguenza, l'enigma può essere risolto richiedendo la chiamata delle estensioni `ref` con ricevitori scrivibile mentre le estensioni `in` consentono la copia implicita, se necessario.

```csharp
// this can be called on either RValue or an LValue
public static void Reader(in this Guid self)
{
    // do something nonmutating.
    WriteLine(self == default(Guid));
}

// this can be called only on an LValue
public static void Mutator(ref this Guid self)
{
    // can mutate self
    self = new Guid();
}
```

### <a name="in-extensions-and-generics"></a>`in` estensioni e generics.
Lo scopo di `ref` metodi di estensione consiste nel modificare direttamente il ricevitore o richiamando membri mutanti. Sono pertanto consentite `ref this T` estensioni purché `T` sia vincolato a essere uno struct.

D'altra parte `in` metodi di estensione esistono in modo specifico per ridurre la copia implicita. Tuttavia, qualsiasi uso di un parametro di `in T` deve essere eseguito tramite un membro di interfaccia. Poiché tutti i membri di interfaccia sono considerati mutanti, qualsiasi utilizzo di questo tipo richiederebbe una copia. -Anziché ridurre la copia, l'effetto sarebbe l'opposto. Pertanto `in this T` non è consentito quando `T` è un parametro di tipo generico indipendentemente dai vincoli.

### <a name="valid-kinds-of-extension-methods-recap"></a>Tipi validi di metodi di estensione (riepilogo):
Sono ora consentite le seguenti forme di `this` dichiarazione in un metodo di estensione:
1) estensione ByVal `this T arg`-Regular. (**caso esistente**)
- T può essere qualsiasi tipo, inclusi i tipi di riferimento o parametri di tipo.
L'istanza sarà la stessa variabile dopo la chiamata.
Consente le conversioni implicite di questo tipo di _conversione di argomenti_ .
Può essere chiamato su RValues.

- `in this T self` - estensione di `in`.
T deve essere un tipo struct effettivo.
L'istanza sarà la stessa variabile dopo la chiamata.
Consente le conversioni implicite di questo tipo di _conversione di argomenti_ .
Può essere chiamato su RValues (può essere richiamato su Temp se necessario).

- `ref this T self` - estensione di `ref`.
T deve essere un tipo struct o un parametro di tipo generico vincolato per essere uno struct.
L'istanza può essere scritta dalla chiamata.
Consente solo le conversioni di identità.
Deve essere chiamato su LValue scrivibile. (mai richiamato tramite una temperatura temporanea).

## <a name="readonly-ref-locals"></a>Variabili locali Ref di sola lettura.

### <a name="motivation"></a>Motivazione.
Una volta introdotti `ref readonly` membri, è stato chiaro che è necessario associarli al tipo di locale appropriato. La valutazione di un membro può produrre o osservare gli effetti collaterali, pertanto se il risultato deve essere usato più di una volta, deve essere archiviato. Le impostazioni locali di `ref` comuni non sono utili perché non è possibile assegnarle un riferimento `readonly`.   

### <a name="solution"></a>Soluzione.
Consente la dichiarazione di `ref readonly` variabili locali. Si tratta di un nuovo tipo di variabili locali `ref` non scrivibile. Di conseguenza `ref readonly` variabili locali possono accettare riferimenti a variabili di sola lettura senza esporre tali variabili alle Scritture.

### <a name="declaring-and-using-ref-readonly-locals"></a>Dichiarazione e utilizzo di `ref readonly` variabili locali.

La sintassi di tali variabili locali utilizza i modificatori `ref readonly` nel sito di dichiarazione (nell'ordine specifico). Analogamente alle variabili locali di `ref`, `ref readonly` variabili locali devono essere inizializzate come ref alla dichiarazione. Diversamente dalle normali variabili locali `ref`, `ref readonly` variabili locali possono fare riferimento a `readonly` lvalue, ad esempio `in` parametri, `readonly` campi, `ref readonly` metodi.

Per tutti gli scopi, un `ref readonly` local viene considerato come una variabile di `readonly`. La maggior parte delle restrizioni relative all'utilizzo è identica a quella di `readonly` campi o parametri di `in`.

Per esempio, i campi di un parametro di `in` con un tipo struct sono ricorsivi in modo ricorsivo come `readonly` variabili.   

```csharp
static readonly ref Vector3 M1() => . . .

static readonly ref Vector3 M1_Trace()
{
    // OK
    ref readonly var r1 = ref M1();

    // Not valid. Need an LValue
    ref readonly Vector3 r2 = ref default(Vector3);

    // Not valid. r1 is readonly.
    Mutate(ref r1);

    // OK.
    Print(in r1);

    // OK.
    return ref r1;
}
```

### <a name="restrictions-on-use-of-ref-readonly-locals"></a>Restrizioni sull'uso di variabili locali `ref readonly`
Ad eccezione della natura `readonly`, `ref readonly` le variabili locali comportano come normali `ref` variabili locali e sono soggette esattamente alle stesse restrizioni.  
Ad esempio, le restrizioni relative all'acquisizione nelle chiusure, dichiarando in `async` metodi o l'analisi `safe-to-return` si applicano ugualmente alle `ref readonly` variabili locali.

## <a name="ternary-ref-expressions-aka-conditional-lvalues"></a>Espressioni `ref` ternarie. (noto anche come "lvalue condizionale")

### <a name="motivation"></a>Motivazione
L'uso di `ref` e `ref readonly` variabili locali hanno esposto la necessità di inizializzare tali variabili locali con una o un'altra variabile di destinazione in base a una condizione.

Una soluzione alternativa tipica consiste nell'introdurre un metodo come:

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

Si noti che `Choice` non è una sostituzione esatta di una terna poiché _tutti_ gli argomenti devono essere valutati nel sito di chiamata, il che ha provocato un comportamento non intuitivo e bug.

Il codice seguente non funzionerà come previsto:

```csharp
    // will crash with NRE because 'arr[0]' will be executed unconditionally
    ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

### <a name="solution"></a>Soluzione
Consente un tipo speciale di espressione condizionale che restituisce un riferimento a uno degli argomenti LValue in base a una condizione.

### <a name="using-ref-ternary-expression"></a>Utilizzando `ref` espressione ternaria.

La sintassi per la versione `ref` di un'espressione condizionale è `<condition> ? ref <consequence> : ref <alternative>;`

Analogamente all'espressione condizionale ordinaria, solo `<consequence>` o `<alternative>` viene valutato a seconda del risultato dell'espressione della condizione booleana.

A differenza della normale espressione condizionale, `ref` espressione condizionale:
- richiede che `<consequence>` e `<alternative>` siano lvalue.
- `ref` espressione condizionale è LValue e
- `ref` espressione condizionale è scrivibile se sia `<consequence>` che `<alternative>` sono scrivibile lvalue

Esempi:  
`ref` ternaria è un LValue e, di conseguenza, può essere passato/assegnato/restituito per riferimento;
```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

Essendo un LValue, può anche essere assegnato a.
```csharp
     // assign to
     (arr != null ? ref arr[0]: ref otherArr[0]) = 1;

     // error. readOnlyField is readonly and thus conditional expression is readonly
     (arr != null ? ref arr[0]: ref obj.readOnlyField) = 1;
```

Può essere usato come ricevitore di una chiamata al metodo e ignorare la copia, se necessario.
```csharp
     // no copies
     (arr != null ? ref arr[0]: ref otherArr[0]).StructMethod();

     // invoked on a copy.
     // The receiver is `readonly` because readOnlyField is readonly.
     (arr != null ? ref arr[0]: ref obj.readOnlyField).StructMethod();

     // no copies. `ReadonlyStructMethod` is a method on a `readonly` struct
     // and can be invoked directly on a readonly receiver
     (arr != null ? ref arr[0]: ref obj.readOnlyField).ReadonlyStructMethod();
```

`ref` ternaria può essere usato anche in un contesto normale (non Ref).
```csharp
     // only an example
     // a regular ternary could work here just the same
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```

### <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

È possibile visualizzare due argomenti principali rispetto al supporto migliorato per i riferimenti e i riferimenti di sola lettura:

1) I problemi risolti qui sono molto vecchi. Perché non è possibile risolverli rapidamente, soprattutto perché non è utile per il codice esistente?

Come è stato C# trovato e .NET usato nei nuovi domini, alcuni problemi diventano più evidenti.  
Come esempi di ambienti più critici rispetto alla media dei sovraccarichi di calcolo, è possibile elencare

* scenari di cloud/Data Center in cui il calcolo viene fatturato e la velocità di risposta è un vantaggio competitivo.
* Giochi/VR/AR con requisiti di tempo reale per le latenze     

Questa funzionalità non sacrifica i punti di forza esistenti, ad esempio la indipendenza dai tipi, consentendo al tempo stesso di ridurre i sovraccarichi in alcuni scenari comuni.

2) È ragionevolmente possibile garantire che il chiamato venga riprodotto dalle regole quando si opta per i contratti `readonly`?

Quando si usa `out`si ha un trust simile. Un'implementazione non corretta di `out` può causare un comportamento non specificato, ma in realtà si verifica raramente.  

L'esecuzione delle regole di verifica formale che hanno familiarità con `ref readonly` potrebbe ridurre ulteriormente il problema di attendibilità.

### <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Il principale progetto in competizione è effettivamente "non fare nulla".

### <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

### <a name="design-meetings"></a>Riunioni di progettazione

https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-02-22.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-01.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-08-28.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-25.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-27.md
