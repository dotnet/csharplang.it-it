---
ms.openlocfilehash: fa3326bf69c83b6042b1db7b5567fd5c28d6f81a
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484524"
---
# <a name="callerargumentexpression"></a>CallerArgumentExpression

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Summary
[summary]: #summary

Consente agli sviluppatori di acquisire le espressioni passate a un metodo per consentire messaggi di errore migliori nelle API di diagnostica/test e ridurre le sequenze di tasti.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Quando la convalida di un'asserzione o un argomento ha esito negativo, lo sviluppatore vuole sapere quanto più possibile su dove e perché non è riuscito. Tuttavia, le API di diagnostica odierne non facilitano completamente questa operazione. Si consideri il seguente metodo:

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null);
    Debug.Assert(array.Length == 1);

    return array[0];
}
```

Quando una delle asserzioni ha esito negativo, nella traccia dello stack verranno specificati solo il nome del file, il numero di riga e il nome del metodo. Lo sviluppatore non sarà in grado di stabilire quale asserzione ha avuto esito negativo da queste informazioni: dovrà aprire il file e passare al numero di riga specificato per verificare che si è verificato un errore.

Questo è anche il motivo per cui i Framework di test devono fornire un'ampia gamma di metodi Assert. Con xUnit, `Assert.True` e `Assert.False` non vengono usati di frequente perché non forniscono un contesto sufficiente sulle operazioni non riuscite.

Anche se la situazione è un po' migliore per la convalida degli argomenti poiché i nomi degli argomenti non validi vengono visualizzati allo sviluppatore, lo sviluppatore deve passare manualmente questi nomi alle eccezioni. Se l'esempio precedente è stato riscritto in modo da usare la convalida degli argomenti tradizionale invece di `Debug.Assert`, l'aspetto sarà simile al seguente:

```csharp
T Single<T>(this T[] array)
{
    if (array == null)
    {
        throw new ArgumentNullException(nameof(array));
    }

    if (array.Length != 1)
    {
        throw new ArgumentException("Array must contain a single element.", nameof(array));
    }

    return array[0];
}
```

Si noti che `nameof(array)` necessario passare a ogni eccezione, sebbene sia già chiaro dal contesto quale argomento non è valido.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Negli esempi precedenti, includendo la stringa `"array != null"` o `"array.Length == 1"` nel messaggio di asserzione, lo sviluppatore può determinare le operazioni non riuscite. Immettere `CallerArgumentExpression`: si tratta di un attributo che può essere usato dal Framework per ottenere la stringa associata a un argomento specifico del metodo. Lo aggiungeremo a `Debug.Assert` in questo modo

```csharp
public static class Debug
{
    public static void Assert(bool condition, [CallerArgumentExpression("condition")] string message = null);
}
```

Il codice sorgente nell'esempio precedente rimane invariato. Tuttavia, il codice che il compilatore emette effettivamente corrisponde a

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null, "array != null");
    Debug.Assert(array.Length == 1, "array.Length == 1");

    return array[0];
}
```

Il compilatore riconosce in modo specifico l'attributo in `Debug.Assert`. Passa la stringa associata all'argomento a cui si fa riferimento nel costruttore dell'attributo (in questo caso, `condition`) nel sito di chiamata. Quando l'asserzione ha esito negativo, verrà visualizzata la condizione false per lo sviluppatore, che saprà quale operazione non è riuscita.

Per la convalida degli argomenti, l'attributo non può essere utilizzato direttamente, ma può essere utilizzato tramite una classe helper:

```csharp
public static class Verify
{
    public static void Argument(bool condition, string message, [CallerArgumentExpression("condition")] string conditionExpression = null)
    {
        if (!condition) throw new ArgumentException(message: message, paramName: conditionExpression);
    }

    public static void InRange(int argument, int low, int high,
        [CallerArgumentExpression("argument")] string argumentExpression = null,
        [CallerArgumentExpression("low")] string lowExpression = null,
        [CallerArgumentExpression("high")] string highExpression = null)
    {
        if (argument < low)
        {
            throw new ArgumentOutOfRangeException(paramName: argumentExpression,
                message: $"{argumentExpression} ({argument}) cannot be less than {lowExpression} ({low}).");
        }

        if (argument > high)
        {
            throw new ArgumentOutOfRangeException(paramName: argumentExpression,
                message: $"{argumentExpression} ({argument}) cannot be greater than {highExpression} ({high}).");
        }
    }

    public static void NotNull<T>(T argument, [CallerArgumentExpression("argument")] string argumentExpression = null)
        where T : class
    {
        if (argument == null) throw new ArgumentNullException(paramName: argumentExpression);
    }
}

T Single<T>(this T[] array)
{
    Verify.NotNull(array); // paramName: "array"
    Verify.Argument(array.Length == 1, "Array must contain a single element."); // paramName: "array.Length == 1"

    return array[0];
}

T ElementAt(this T[] array, int index)
{
    Verify.NotNull(array); // paramName: "array"
    // paramName: "index"
    // message: "index (-1) cannot be less than 0 (0).", or
    //          "index (6) cannot be greater than array.Length - 1 (5)."
    Verify.InRange(index, 0, array.Length - 1);

    return array[index];
}
```

Una proposta per aggiungere tale classe helper al Framework è in corso in https://github.com/dotnet/corefx/issues/17068. Se questa funzionalità del linguaggio è stata implementata, è possibile aggiornare la proposta per sfruttare i vantaggi di questa funzionalità.

### <a name="extension-methods"></a>Metodi di estensione

È possibile fare riferimento al parametro `this` in un metodo di estensione da `CallerArgumentExpression`. Ad esempio:

```csharp
public static void ShouldBe<T>(this T @this, T expected, [CallerArgumentExpression("this")] string thisExpression = null) {}

contestant.Points.ShouldBe(1337); // thisExpression: "contestant.Points"
```

`thisExpression` riceverà l'espressione corrispondente all'oggetto prima del punto. Se viene chiamato con la sintassi del metodo statico, ad esempio `Ext.ShouldBe(contestant.Points, 1337)`, si comporterà come se il primo parametro non venisse contrassegnato come `this`.

Deve essere sempre presente un'espressione corrispondente al parametro `this`. Anche se un'istanza di una classe chiama un metodo di estensione su se stesso, ad esempio `this.Single()` dall'interno di un tipo di raccolta, il `this` è richiesto dal compilatore in modo che `"this"` venga superato. Se questa regola viene modificata in futuro, è possibile provare a passare `null` o una stringa vuota.

### <a name="extra-details"></a>Dettagli aggiuntivi

- Analogamente agli altri attributi `Caller*`, ad esempio `CallerMemberName`, questo attributo può essere utilizzato solo su parametri con valori predefiniti.
- Sono consentiti più parametri contrassegnati con `CallerArgumentExpression`, come illustrato in precedenza.
- Lo spazio dei nomi dell'attributo verrà `System.Runtime.CompilerServices`.
- Se viene specificato `null` o una stringa che non è un nome di parametro (ad esempio `"notAParameterName"`), il compilatore passerà una stringa vuota.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

- Gli utenti che sanno come usare i decompilatori saranno in grado di visualizzare parte del codice sorgente nei siti di chiamata per i metodi contrassegnati con questo attributo. Questo potrebbe essere indesiderato o imprevisto per il software di origine chiusa.

- Sebbene questo non sia un difetto nella funzionalità stessa, è possibile che esista già un'API `Debug.Assert` che accetta solo una `bool`. Anche se l'overload che accetta un messaggio aveva il secondo parametro contrassegnato con questo attributo e reso facoltativo, il compilatore avrebbe comunque scelto il nessun messaggio nella risoluzione dell'overload. Pertanto, l'overload di nessun messaggio deve essere rimosso per sfruttare questa funzionalità, che potrebbe essere una modifica di rilievo binaria (anche se non di origine).

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

- Se la possibilità di visualizzare il codice sorgente nei siti di chiamata per i metodi che usano questo attributo dimostra che si tratta di un problema, è possibile fare in modo che gli effetti dell'attributo siano espliciti. Gli sviluppatori la consentiranno di usare un attributo `[assembly: EnableCallerArgumentExpression]` a livello di assembly che inserisce `AssemblyInfo.cs`.
  - Se gli effetti dell'attributo non sono abilitati, la chiamata ai metodi contrassegnati con l'attributo non è un errore, in modo da consentire ai metodi esistenti di utilizzare l'attributo e mantenere la compatibilità con l'origine. Tuttavia, l'attributo viene ignorato e il metodo viene chiamato con qualsiasi valore predefinito fornito.

```csharp
// Assembly1

void Foo(string bar); // V1
void Foo(string bar, string barExpression = "not provided"); // V2
void Foo(string bar, [CallerArgumentExpression("bar")] string barExpression = "not provided"); // V3

// Assembly2

Foo(a); // V1: Compiles to Foo(a), V2, V3: Compiles to Foo(a, "not provided")
Foo(a, "provided"); // V2, V3: Compiles to Foo(a, "provided")

// Assembly3

[assembly: EnableCallerArgumentExpression]

Foo(a); // V1: Compiles to Foo(a), V2: Compiles to Foo(a, "not provided"), V3: Compiles to Foo(a, "a")
Foo(a, "provided"); // V2, V3: Compiles to Foo(a, "provided")
```

- Per evitare che si verifichi un[ drawbacks] [problema di compatibilità binaria] ogni volta che si desidera aggiungere nuove informazioni sul chiamante a `Debug.Assert`, una soluzione alternativa consiste nell'aggiungere uno struct `CallerInfo` al Framework che contiene tutte le informazioni necessarie sul chiamante.

```csharp
struct CallerInfo
{
    public string MemberName { get; set; }
    public string TypeName { get; set; }
    public string Namespace { get; set; }
    public string FullTypeName { get; set; }
    public string FilePath { get; set; }
    public int LineNumber { get; set; }
    public int ColumnNumber { get; set; }
    public Type Type { get; set; }
    public MethodBase Method { get; set; }
    public string[] ArgumentExpressions { get; set; }
}

[Flags]
enum CallerInfoOptions
{
    MemberName = 1, TypeName = 2, ...
}

public static class Debug
{
    public static void Assert(bool condition,
        // If a flag is not set here, the corresponding CallerInfo member is not populated by the caller, so it's
        // pay-for-play friendly.
        [CallerInfo(CallerInfoOptions.FilePath | CallerInfoOptions.Method | CallerInfoOptions.ArgumentExpressions)] CallerInfo callerInfo = default(CallerInfo))
    {
        string filePath = callerInfo.FilePath;
        MethodBase method = callerInfo.Method;
        string conditionExpression = callerInfo.ArgumentExpressions[0];
        ...
    }
}

class Bar
{
    void Foo()
    {
        Debug.Assert(false);

        // Translates to:

        var callerInfo = new CallerInfo();
        callerInfo.FilePath = @"C:\Bar.cs";
        callerInfo.Method = MethodBase.GetCurrentMethod();
        callerInfo.ArgumentExpressions = new string[] { "false" };
        Debug.Assert(false, callerInfo);
    }
}
```

Questa operazione è stata proposta in origine in https://github.com/dotnet/csharplang/issues/87.

Questo approccio presenta alcuni svantaggi:

- Nonostante sia facile da pagare, è possibile specificare quali proprietà sono necessarie, ma è comunque possibile danneggiare significativamente le prestazioni allocando una matrice per le espressioni/chiamate `MethodBase.GetCurrentMethod` anche quando l'asserzione passa.

- Inoltre, anche se il passaggio di un nuovo flag all'attributo `CallerInfo` non è una modifica di rilievo, `Debug.Assert` non sarà garantita la ricezione del nuovo parametro dai siti di chiamata compilati in base a una versione precedente del metodo.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

TBD

## <a name="design-meetings"></a>Riunioni di progettazione

N/D
