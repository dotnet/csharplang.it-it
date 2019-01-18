---
ms.openlocfilehash: 155c1beecddfdfcce2e7948bcb8d6b80428fbd7a
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229648"
---
# <a name="arrays"></a>Matrici

Una matrice è una struttura di dati che contiene un numero di variabili accessibili tramite indici calcolati. Le variabili contenute in una matrice, chiamata anche gli elementi della matrice, sono tutti dello stesso tipo, e questo tipo viene definito il tipo di elemento della matrice.

Una matrice con una classificazione che determina il numero di indici associati a ogni elemento della matrice. L'ordine delle matrici è detta anche le dimensioni della matrice. Una matrice a un rango pari a uno viene definita un' ***matrici unidimensionali***. Una matrice con un numero di dimensioni superiore a uno viene chiamato un ***matrice multidimensionale***. Matrici multidimensionali con dimensioni specifiche sono spesso detta matrici bidimensionali, le matrici tridimensionali ne e così via.

Ogni dimensione della matrice è associata una lunghezza che è un numero intero maggiore o uguale a zero. Le lunghezze delle dimensioni non fanno parte del tipo di matrice, ma piuttosto vengono stabilite quando viene creata un'istanza del tipo di matrice in fase di esecuzione. La lunghezza di una dimensione determina l'intervallo valido degli indici per tale dimensione: Per una dimensione di lunghezza `N`, gli indici può essere compreso tra `0` a `N - 1` inclusivo. Il numero totale di elementi in una matrice è il prodotto delle lunghezze di ogni dimensione della matrice. Se uno o più delle dimensioni della matrice ha una lunghezza pari a zero, la matrice viene considerata può essere vuoto.

L'elemento di una matrice può essere di qualsiasi tipo, anche di tipo matrice.

## <a name="array-types"></a>Tipi matrice

Un tipo di matrice viene scritto come un *non_array_type* seguita da uno o più *rank_specifier*s:

```antlr
array_type
    : non_array_type rank_specifier+
    ;

non_array_type
    : type
    ;

rank_specifier
    : '[' dim_separator* ']'
    ;

dim_separator
    : ','
    ;
```

Oggetto *non_array_type* eventuale *tipo* vale a dire non è un *array_type*.

Il numero di dimensioni di tipo matrice è dato dai più a sinistra *rank_specifier* nel *array_type*: Oggetto *rank_specifier* indica che la matrice è una matrice a un rango pari a uno più il numero di "`,`" i token nel *rank_specifier*.

Il tipo di elemento di tipo matrice è il tipo che deriva dall'eliminazione di più a sinistra *rank_specifier*:

*  Un tipo di matrice nel formato `T[R]` è una matrice con rango `R` e un tipo di elemento non di matrice `T`.
*  Un tipo di matrice nel formato `T[R][R1]...[Rn]` è una matrice con rango `R` e un tipo di elemento `T[R1]...[Rn]`.

In effetti, il *rank_specifier*s vengono lette da sinistra a destra prima del tipo di elemento diverso da una matrice finale. Il tipo `int[][,,][,]` è una matrice unidimensionale di bidimensionale di matrici tridimensionali `int`.

In fase di esecuzione, può essere un valore di tipo matrice `null` o un riferimento a un'istanza di quel tipo di matrice.

### <a name="the-systemarray-type"></a>Il tipo System. Array

Il tipo `System.Array` è il tipo di base astratto di tutti i tipi di matrice. Una conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) presente, da qualsiasi tipo di matrice `System.Array`e la conversione di un riferimento esplicito ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) esiste da `System.Array` in qualsiasi tipo di matrice. Si noti che `System.Array` non è a sua volta un' *array_type*. Si tratta piuttosto di una *class_type* da cui tutti gli *array_type*derivano.

In fase di esecuzione, un valore di tipo `System.Array` può essere `null` o un riferimento a un'istanza di qualsiasi tipo di matrice.

### <a name="arrays-and-the-generic-ilist-interface"></a>Matrici e l'interfaccia IList generica

Una matrice unidimensionale `T[]` implementa l'interfaccia `System.Collections.Generic.IList<T>` (`IList<T>` breve) e le relative interfacce di base. Di conseguenza, è presente una conversione implicita da `T[]` a `IList<T>` e le relative interfacce di base. Inoltre, se è una conversione implicita del riferimento da `S` al `T` quindi `S[]` implementa `IList<T>` ed è presente una conversione implicita del riferimento da `S[]` a `IList<T>` e rispettive interfacce di base ( [Le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)). Se è presente una conversione esplicita di riferimenti dal `S` al `T` non vi è una conversione esplicita di riferimenti da `S[]` a `IList<T>` e le relative interfacce di base ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)). Ad esempio:
```csharp
using System.Collections.Generic;

class Test
{
    static void Main() {
        string[] sa = new string[5];
        object[] oa1 = new object[5];
        object[] oa2 = sa;

        IList<string> lst1 = sa;                    // Ok
        IList<string> lst2 = oa1;                   // Error, cast needed
        IList<object> lst3 = sa;                    // Ok
        IList<object> lst4 = oa1;                   // Ok

        IList<string> lst5 = (IList<string>)oa1;    // Exception
        IList<string> lst6 = (IList<string>)oa2;    // Ok
    }
}
```

L'assegnazione `lst2 = oa1` genera un errore in fase di compilazione poiché la conversione da `object[]` a `IList<string>` è una conversione esplicita, implicita non. Il cast `(IList<string>)oa1` genererà un'eccezione viene generata in fase di esecuzione poiché `oa1` riferimenti un' `object[]` e non un `string[]`. Tuttavia il cast `(IList<string>)oa2` non genererà un'eccezione generata dal `oa2` riferimenti un `string[]`.

Ogni volta che è una conversione implicita o esplicita di riferimento da `S[]` al `IList<T>`, è inoltre disponibile una conversione esplicita di riferimenti dal `IList<T>` e interfacce di base relativi a `S[]` ([riferimento esplicito le conversioni](conversions.md#explicit-reference-conversions)).

Quando un tipo di matrice `S[]` implementa `IList<T>`, alcuni dei membri dell'interfaccia implementata può generare eccezioni. Il comportamento preciso dell'implementazione dell'interfaccia esula dall'ambito di questa specifica.

## <a name="array-creation"></a>Creazione di matrici

Le istanze di matrice vengono create da *array_creation_expression*s ([espressioni di creazione matrice](expressions.md#array-creation-expressions)) o, campo o dichiarazioni di variabili locali che includono un *array_initializer*([Gli inizializzatori di matrice](arrays.md#array-initializers)).

Quando viene creata un'istanza di matrice, il numero di dimensioni e la lunghezza di ogni dimensione vengono stabiliti e rimanere costante per l'intera durata dell'istanza. In altre parole, non è possibile modificare il rango di un'istanza esistente di matrice, né è possibile modificarne le dimensioni.

Un'istanza di matrice è sempre di tipo matrice. Il `System.Array` tipo è un tipo astratto che non è possibile creare un'istanza.

Gli elementi delle matrici create *array_creation_expression*s sempre vengono inizializzati sul valore predefinito ([i valori predefiniti](variables.md#default-values)).

## <a name="array-element-access"></a>Accesso agli elementi di matrice

Gli elementi della matrice sono accessibili tramite *element_access* espressioni ([Array access](expressions.md#array-access)) nel formato `A[I1, I2, ..., In]`, dove `A` è un'espressione di un tipo di matrice e ogni `Ix` è un espressione di tipo `int`, `uint`, `long`, `ulong`, o possono essere convertiti in modo implicito a uno o più di questi tipi. Il risultato di un accesso all'elemento di matrice è una variabile, vale a dire l'elemento della matrice selezionata per gli indici.

Gli elementi della matrice possono essere enumerati utilizzando un `foreach` istruzione ([l'istruzione foreach](statements.md#the-foreach-statement)).

## <a name="array-members"></a>Membri di matrici

Ogni tipo di matrice eredita i membri dichiarati di `System.Array` tipo.

## <a name="array-covariance"></a>Covarianza di matrici

Per due *reference_type*s `A` e `B`, se una conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) o esplicita ([ Le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) esistente dal `A` al `B`, quindi la stessa conversione riferimento esiste anche dal tipo di matrice `A[R]` per il tipo di matrice `B[R]`, dove `R` qualsiasi determinata *rank_specifier* (ma lo stesso per entrambi i tipi di matrice). Questa relazione è detta ***covarianza di matrici***. Covarianza di matrici in particolare significa che un valore di tipo matrice `A[R]` può essere effettivamente un riferimento a un'istanza di un tipo di matrice `B[R]`, a condizione che esiste una conversione implicita del riferimento da `B` a `A`.

A causa di covarianza di matrici, assegnazioni agli elementi delle matrici di tipi di riferimento includono un controllo di runtime che assicura che il valore assegnato all'elemento della matrice è effettivamente di un tipo consentito ([assegnamento semplice](expressions.md#simple-assignment)). Ad esempio:
```csharp
class Test
{
    static void Fill(object[] array, int index, int count, object value) {
        for (int i = index; i < index + count; i++) array[i] = value;
    }

    static void Main() {
        string[] strings = new string[100];
        Fill(strings, 0, 100, "Undefined");
        Fill(strings, 0, 10, null);
        Fill(strings, 90, 10, 0);
    }
}
```

L'assegnazione al `array[i]` nella `Fill` metodo in modo implicito include un controllo di runtime che garantisce che l'oggetto fa `value` può essere `null` o un'istanza compatibile con il tipo effettivo degli elementi di `array`. Nelle `Main`, le prime due chiamate di `Fill` abbia esito positivo, ma le cause di chiamata al terzo una `System.ArrayTypeMismatchException` eccezione dopo l'esecuzione della prima assegnazione a `array[i]`. L'eccezione si verifica perché boxed `int` non possono essere archiviati un `string` matrice.

Covarianza di matrici in particolare non si estende alle matrici di *value_type*s. Ad esempio, non esiste alcuna conversione e consente un' `int[]` deve essere trattato come un `object[]`.

## <a name="array-initializers"></a>Inizializzatori di matrice

Gli inizializzatori di matrice possono essere specificati nelle dichiarazioni di campo ([campi](classes.md#fields)), le dichiarazioni delle variabili locali ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) e le espressioni di creazione di matrici ([creazione di matrici le espressioni](expressions.md#array-creation-expressions)):

```antlr
array_initializer
    : '{' variable_initializer_list? '}'
    | '{' variable_initializer_list ',' '}'
    ;

variable_initializer_list
    : variable_initializer (',' variable_initializer)*
    ;

variable_initializer
    : expression
    | array_initializer
    ;
```

Un inizializzatore di matrice è costituito da una sequenza di inizializzatori racchiusi tra "`{`"e"`}`"token e separati da"`,`" token. Ogni inizializzatore di variabile è un'espressione o, nel caso di una matrice multidimensionale, un inizializzatore di matrice annidati.

Il contesto in cui viene usato un inizializzatore di matrice determina il tipo della matrice in fase di inizializzazione. In un'espressione di creazione della matrice, il tipo di matrice immediatamente precede l'inizializzatore, o viene dedotto dalle espressioni nell'inizializzatore di matrice. In un campo o una dichiarazione di variabile, il tipo di matrice è il tipo del campo o della variabile dichiarata. Quando un inizializzatore di matrice viene usato in un campo o una dichiarazione di variabile, ad esempio:
```csharp
int[] a = {0, 2, 4, 6, 8};
```
è sufficiente a sintassi abbreviata per un'espressione di creazione matrice equivalente:
```csharp
int[] a = new int[] {0, 2, 4, 6, 8};
```

Per una matrice unidimensionale, l'inizializzatore di matrice deve contenere una sequenza di espressioni di assegnazione compatibile con il tipo di elemento della matrice. Le espressioni di inizializzano gli elementi di matrice in ordine crescente, iniziando con l'elemento in corrispondenza dell'indice zero. Il numero di espressioni nell'inizializzatore di matrice determina la lunghezza dell'istanza della matrice da creare. Ad esempio, l'inizializzatore di matrice precedente crea un `int[]` istanza lunghezza 5 e quindi inizializza l'istanza con i valori seguenti:
```csharp
a[0] = 0; a[1] = 2; a[2] = 4; a[3] = 6; a[4] = 8;
```

Per una matrice multidimensionale, l'inizializzatore di matrice deve avere tutti i livelli di nidificazione come non vi sono dimensioni nella matrice. Il livello di annidamento più esterno corrisponde alla dimensione più a sinistra e il livello di nidificazione più interno corrisponde alla dimensione più a destra. La lunghezza di ogni dimensione della matrice è determinata dal numero di elementi a livello di nidificazione corrispondente nell'inizializzatore di matrice. Per ogni inizializzatore di matrice annidati, il numero di elementi deve essere lo stesso come altri inizializzatori di matrice allo stesso livello. Esempio:
```csharp
int[,] b = {{0, 1}, {2, 3}, {4, 5}, {6, 7}, {8, 9}};
```
Crea una matrice bidimensionale con una lunghezza pari a cinque per la dimensione più a sinistra e una lunghezza pari a due per la dimensione più a destra:
```csharp
int[,] b = new int[5, 2];
```
e quindi inizializza l'istanza della matrice con i valori seguenti:
```csharp
b[0, 0] = 0; b[0, 1] = 1;
b[1, 0] = 2; b[1, 1] = 3;
b[2, 0] = 4; b[2, 1] = 5;
b[3, 0] = 6; b[3, 1] = 7;
b[4, 0] = 8; b[4, 1] = 9;
```

Se una dimensione diversa da più a destra viene fornita con lunghezza zero, si presuppone che le dimensioni successive per anche avere lunghezza zero. Esempio:
```csharp
int[,] c = {};
```
Crea una matrice bidimensionale con una lunghezza pari a zero per più a sinistra e la dimensione più a destra:
```csharp
int[,] c = new int[0, 0];
```

Quando un'espressione di creazione della matrice include le lunghezze delle dimensioni esplicite sia un inizializzatore di matrice, le lunghezze devono essere espressioni costanti e il numero di elementi in ogni livello di nidificazione deve corrispondere alla lunghezza della dimensione corrispondente. Ecco alcuni esempi:
```csharp
int i = 3;
int[] x = new int[3] {0, 1, 2};        // OK
int[] y = new int[i] {0, 1, 2};        // Error, i not a constant
int[] z = new int[3] {0, 1, 2, 3};     // Error, length/initializer mismatch
```

A questo punto, l'inizializzatore per `y` comporta un errore in fase di compilazione perché l'espressione di lunghezza della dimensione non è una costante e l'inizializzatore per `z` comporta un errore in fase di compilazione perché la lunghezza e il numero di elementi di inizializzatore non accettare.
