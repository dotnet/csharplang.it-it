# <a name="arrays"></a><span data-ttu-id="f7962-101">Matrici</span><span class="sxs-lookup"><span data-stu-id="f7962-101">Arrays</span></span>

<span data-ttu-id="f7962-102">Una matrice è una struttura di dati che contiene un numero di variabili accessibili tramite indici calcolati.</span><span class="sxs-lookup"><span data-stu-id="f7962-102">An array is a data structure that contains a number of variables which are accessed through computed indices.</span></span> <span data-ttu-id="f7962-103">Le variabili contenute in una matrice, chiamata anche gli elementi della matrice, sono tutti dello stesso tipo, e questo tipo viene definito il tipo di elemento della matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-103">The variables contained in an array, also called the elements of the array, are all of the same type, and this type is called the element type of the array.</span></span>

<span data-ttu-id="f7962-104">Una matrice con una classificazione che determina il numero di indici associati a ogni elemento della matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-104">An array has a rank which determines the number of indices associated with each array element.</span></span> <span data-ttu-id="f7962-105">L'ordine delle matrici è detta anche le dimensioni della matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-105">The rank of an array is also referred to as the dimensions of the array.</span></span> <span data-ttu-id="f7962-106">Una matrice a un rango pari a uno viene definita un' ***matrici unidimensionali***.</span><span class="sxs-lookup"><span data-stu-id="f7962-106">An array with a rank of one is called a ***single-dimensional array***.</span></span> <span data-ttu-id="f7962-107">Una matrice con un numero di dimensioni superiore a uno viene chiamato un ***matrice multidimensionale***.</span><span class="sxs-lookup"><span data-stu-id="f7962-107">An array with a rank greater than one is called a ***multi-dimensional array***.</span></span> <span data-ttu-id="f7962-108">Matrici multidimensionali con dimensioni specifiche sono spesso detta matrici bidimensionali, le matrici tridimensionali ne e così via.</span><span class="sxs-lookup"><span data-stu-id="f7962-108">Specific sized multi-dimensional arrays are often referred to as two-dimensional arrays, three-dimensional arrays, and so on.</span></span>

<span data-ttu-id="f7962-109">Ogni dimensione della matrice è associata una lunghezza che è un numero intero maggiore o uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="f7962-109">Each dimension of an array has an associated length which is an integral number greater than or equal to zero.</span></span> <span data-ttu-id="f7962-110">Le lunghezze delle dimensioni non fanno parte del tipo di matrice, ma piuttosto vengono stabilite quando viene creata un'istanza del tipo di matrice in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f7962-110">The dimension lengths are not part of the type of the array, but rather are established when an instance of the array type is created at run-time.</span></span> <span data-ttu-id="f7962-111">La lunghezza di una dimensione determina l'intervallo valido degli indici per tale dimensione: Per una dimensione di lunghezza `N`, gli indici può essere compreso tra `0` a `N - 1` inclusivo.</span><span class="sxs-lookup"><span data-stu-id="f7962-111">The length of a dimension determines the valid range of indices for that dimension: For a dimension of length `N`, indices can range from `0` to `N - 1` inclusive.</span></span> <span data-ttu-id="f7962-112">Il numero totale di elementi in una matrice è il prodotto delle lunghezze di ogni dimensione della matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-112">The total number of elements in an array is the product of the lengths of each dimension in the array.</span></span> <span data-ttu-id="f7962-113">Se uno o più delle dimensioni della matrice ha una lunghezza pari a zero, la matrice viene considerata può essere vuoto.</span><span class="sxs-lookup"><span data-stu-id="f7962-113">If one or more of the dimensions of an array have a length of zero, the array is said to be empty.</span></span>

<span data-ttu-id="f7962-114">L'elemento di una matrice può essere di qualsiasi tipo, anche di tipo matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-114">The element type of an array can be any type, including an array type.</span></span>

## <a name="array-types"></a><span data-ttu-id="f7962-115">Tipi matrice</span><span class="sxs-lookup"><span data-stu-id="f7962-115">Array types</span></span>

<span data-ttu-id="f7962-116">Un tipo di matrice viene scritto come un *non_array_type* seguita da uno o più *rank_specifier*s:</span><span class="sxs-lookup"><span data-stu-id="f7962-116">An array type is written as a *non_array_type* followed by one or more *rank_specifier*s:</span></span>

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

<span data-ttu-id="f7962-117">Oggetto *non_array_type* eventuale *tipo* vale a dire non è un *array_type*.</span><span class="sxs-lookup"><span data-stu-id="f7962-117">A *non_array_type* is any *type* that is not itself an *array_type*.</span></span>

<span data-ttu-id="f7962-118">Il numero di dimensioni di tipo matrice è dato dai più a sinistra *rank_specifier* nel *array_type*: Oggetto *rank_specifier* indica che la matrice è una matrice a un rango pari a uno più il numero di "`,`" i token nel *rank_specifier*.</span><span class="sxs-lookup"><span data-stu-id="f7962-118">The rank of an array type is given by the leftmost *rank_specifier* in the *array_type*: A *rank_specifier* indicates that the array is an array with a rank of one plus the number of "`,`" tokens in the *rank_specifier*.</span></span>

<span data-ttu-id="f7962-119">Il tipo di elemento di tipo matrice è il tipo che deriva dall'eliminazione di più a sinistra *rank_specifier*:</span><span class="sxs-lookup"><span data-stu-id="f7962-119">The element type of an array type is the type that results from deleting the leftmost *rank_specifier*:</span></span>

*  <span data-ttu-id="f7962-120">Un tipo di matrice nel formato `T[R]` è una matrice con rango `R` e un tipo di elemento non di matrice `T`.</span><span class="sxs-lookup"><span data-stu-id="f7962-120">An array type of the form `T[R]` is an array with rank `R` and a non-array element type `T`.</span></span>
*  <span data-ttu-id="f7962-121">Un tipo di matrice nel formato `T[R][R1]...[Rn]` è una matrice con rango `R` e un tipo di elemento `T[R1]...[Rn]`.</span><span class="sxs-lookup"><span data-stu-id="f7962-121">An array type of the form `T[R][R1]...[Rn]` is an array with rank `R` and an element type `T[R1]...[Rn]`.</span></span>

<span data-ttu-id="f7962-122">In effetti, il *rank_specifier*s vengono lette da sinistra a destra prima del tipo di elemento diverso da una matrice finale.</span><span class="sxs-lookup"><span data-stu-id="f7962-122">In effect, the *rank_specifier*s are read from left to right before the final non-array element type.</span></span> <span data-ttu-id="f7962-123">Il tipo `int[][,,][,]` è una matrice unidimensionale di bidimensionale di matrici tridimensionali `int`.</span><span class="sxs-lookup"><span data-stu-id="f7962-123">The type `int[][,,][,]` is a single-dimensional array of three-dimensional arrays of two-dimensional arrays of `int`.</span></span>

<span data-ttu-id="f7962-124">In fase di esecuzione, può essere un valore di tipo matrice `null` o un riferimento a un'istanza di quel tipo di matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-124">At run-time, a value of an array type can be `null` or a reference to an instance of that array type.</span></span>

### <a name="the-systemarray-type"></a><span data-ttu-id="f7962-125">Il tipo System. Array</span><span class="sxs-lookup"><span data-stu-id="f7962-125">The System.Array type</span></span>

<span data-ttu-id="f7962-126">Il tipo `System.Array` è il tipo di base astratto di tutti i tipi di matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-126">The type `System.Array` is the abstract base type of all array types.</span></span> <span data-ttu-id="f7962-127">Una conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) presente, da qualsiasi tipo di matrice `System.Array`e la conversione di un riferimento esplicito ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) esiste da `System.Array` in qualsiasi tipo di matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-127">An implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from any array type to `System.Array`, and an explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from `System.Array` to any array type.</span></span> <span data-ttu-id="f7962-128">Si noti che `System.Array` non è a sua volta un' *array_type*.</span><span class="sxs-lookup"><span data-stu-id="f7962-128">Note that `System.Array` is not itself an *array_type*.</span></span> <span data-ttu-id="f7962-129">Si tratta piuttosto di una *class_type* da cui tutti gli *array_type*derivano.</span><span class="sxs-lookup"><span data-stu-id="f7962-129">Rather, it is a *class_type* from which all *array_type*s are derived.</span></span>

<span data-ttu-id="f7962-130">In fase di esecuzione, un valore di tipo `System.Array` può essere `null` o un riferimento a un'istanza di qualsiasi tipo di matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-130">At run-time, a value of type `System.Array` can be `null` or a reference to an instance of any array type.</span></span>

### <a name="arrays-and-the-generic-ilist-interface"></a><span data-ttu-id="f7962-131">Matrici e l'interfaccia IList generica</span><span class="sxs-lookup"><span data-stu-id="f7962-131">Arrays and the generic IList interface</span></span>

<span data-ttu-id="f7962-132">Una matrice unidimensionale `T[]` implementa l'interfaccia `System.Collections.Generic.IList<T>` (`IList<T>` breve) e le relative interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="f7962-132">A one-dimensional array `T[]` implements the interface `System.Collections.Generic.IList<T>` (`IList<T>` for short) and its base interfaces.</span></span> <span data-ttu-id="f7962-133">Di conseguenza, è presente una conversione implicita da `T[]` a `IList<T>` e le relative interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="f7962-133">Accordingly, there is an implicit conversion from `T[]` to `IList<T>` and its base interfaces.</span></span> <span data-ttu-id="f7962-134">Inoltre, se è una conversione implicita del riferimento da `S` al `T` quindi `S[]` implementa `IList<T>` ed è presente una conversione implicita del riferimento da `S[]` a `IList<T>` e rispettive interfacce di base ( [Le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)).</span><span class="sxs-lookup"><span data-stu-id="f7962-134">In addition, if there is an implicit reference conversion from `S` to `T` then `S[]` implements `IList<T>` and there is an implicit reference conversion from `S[]` to `IList<T>` and its base interfaces ([Implicit reference conversions](conversions.md#implicit-reference-conversions)).</span></span> <span data-ttu-id="f7962-135">Se è presente una conversione esplicita di riferimenti dal `S` al `T` non vi è una conversione esplicita di riferimenti da `S[]` a `IList<T>` e le relative interfacce di base ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)).</span><span class="sxs-lookup"><span data-stu-id="f7962-135">If there is an explicit reference conversion from `S` to `T` then there is an explicit reference conversion from `S[]` to `IList<T>` and its base interfaces ([Explicit reference conversions](conversions.md#explicit-reference-conversions)).</span></span> <span data-ttu-id="f7962-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f7962-136">For example:</span></span>
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

<span data-ttu-id="f7962-137">L'assegnazione `lst2 = oa1` genera un errore in fase di compilazione poiché la conversione da `object[]` a `IList<string>` è una conversione esplicita, implicita non.</span><span class="sxs-lookup"><span data-stu-id="f7962-137">The assignment `lst2 = oa1` generates a compile-time error since the conversion from `object[]` to `IList<string>` is an explicit conversion, not implicit.</span></span> <span data-ttu-id="f7962-138">Il cast `(IList<string>)oa1` genererà un'eccezione viene generata in fase di esecuzione poiché `oa1` riferimenti un' `object[]` e non un `string[]`.</span><span class="sxs-lookup"><span data-stu-id="f7962-138">The cast `(IList<string>)oa1` will cause an exception to be thrown at run-time since `oa1` references an `object[]` and not a `string[]`.</span></span> <span data-ttu-id="f7962-139">Tuttavia il cast `(IList<string>)oa2` non genererà un'eccezione generata dal `oa2` riferimenti un `string[]`.</span><span class="sxs-lookup"><span data-stu-id="f7962-139">However the cast `(IList<string>)oa2` will not cause an exception to be thrown since `oa2` references a `string[]`.</span></span>

<span data-ttu-id="f7962-140">Ogni volta che è una conversione implicita o esplicita di riferimento da `S[]` al `IList<T>`, è inoltre disponibile una conversione esplicita di riferimenti dal `IList<T>` e interfacce di base relativi a `S[]` ([riferimento esplicito le conversioni](conversions.md#explicit-reference-conversions)).</span><span class="sxs-lookup"><span data-stu-id="f7962-140">Whenever there is an implicit or explicit reference conversion from `S[]` to `IList<T>`, there is also an explicit reference conversion from `IList<T>` and its base interfaces to `S[]` ([Explicit reference conversions](conversions.md#explicit-reference-conversions)).</span></span>

<span data-ttu-id="f7962-141">Quando un tipo di matrice `S[]` implementa `IList<T>`, alcuni dei membri dell'interfaccia implementata può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="f7962-141">When an array type `S[]` implements `IList<T>`, some of the members of the implemented interface may throw exceptions.</span></span> <span data-ttu-id="f7962-142">Il comportamento preciso dell'implementazione dell'interfaccia esula dall'ambito di questa specifica.</span><span class="sxs-lookup"><span data-stu-id="f7962-142">The precise behavior of the implementation of the interface is beyond the scope of this specification.</span></span>

## <a name="array-creation"></a><span data-ttu-id="f7962-143">Creazione di matrici</span><span class="sxs-lookup"><span data-stu-id="f7962-143">Array creation</span></span>

<span data-ttu-id="f7962-144">Le istanze di matrice vengono create da *array_creation_expression*s ([espressioni di creazione matrice](expressions.md#array-creation-expressions)) o, campo o dichiarazioni di variabili locali che includono un *array_initializer*([Gli inizializzatori di matrice](arrays.md#array-initializers)).</span><span class="sxs-lookup"><span data-stu-id="f7962-144">Array instances are created by *array_creation_expression*s ([Array creation expressions](expressions.md#array-creation-expressions)) or by field or local variable declarations that include an *array_initializer* ([Array initializers](arrays.md#array-initializers)).</span></span>

<span data-ttu-id="f7962-145">Quando viene creata un'istanza di matrice, il numero di dimensioni e la lunghezza di ogni dimensione vengono stabiliti e rimanere costante per l'intera durata dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="f7962-145">When an array instance is created, the rank and length of each dimension are established and then remain constant for the entire lifetime of the instance.</span></span> <span data-ttu-id="f7962-146">In altre parole, non è possibile modificare il rango di un'istanza esistente di matrice, né è possibile modificarne le dimensioni.</span><span class="sxs-lookup"><span data-stu-id="f7962-146">In other words, it is not possible to change the rank of an existing array instance, nor is it possible to resize its dimensions.</span></span>

<span data-ttu-id="f7962-147">Un'istanza di matrice è sempre di tipo matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-147">An array instance is always of an array type.</span></span> <span data-ttu-id="f7962-148">Il `System.Array` tipo è un tipo astratto che non è possibile creare un'istanza.</span><span class="sxs-lookup"><span data-stu-id="f7962-148">The `System.Array` type is an abstract type that cannot be instantiated.</span></span>

<span data-ttu-id="f7962-149">Gli elementi delle matrici create *array_creation_expression*s sempre vengono inizializzati sul valore predefinito ([i valori predefiniti](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="f7962-149">Elements of arrays created by *array_creation_expression*s are always initialized to their default value ([Default values](variables.md#default-values)).</span></span>

## <a name="array-element-access"></a><span data-ttu-id="f7962-150">Accesso agli elementi di matrice</span><span class="sxs-lookup"><span data-stu-id="f7962-150">Array element access</span></span>

<span data-ttu-id="f7962-151">Gli elementi della matrice sono accessibili tramite *element_access* espressioni ([Array access](expressions.md#array-access)) nel formato `A[I1, I2, ..., In]`, dove `A` è un'espressione di un tipo di matrice e ogni `Ix` è un espressione di tipo `int`, `uint`, `long`, `ulong`, o possono essere convertiti in modo implicito a uno o più di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="f7962-151">Array elements are accessed using *element_access* expressions ([Array access](expressions.md#array-access)) of the form `A[I1, I2, ..., In]`, where `A` is an expression of an array type and each `Ix` is an expression of type `int`, `uint`, `long`, `ulong`, or can be implicitly converted to one or more of these types.</span></span> <span data-ttu-id="f7962-152">Il risultato di un accesso all'elemento di matrice è una variabile, vale a dire l'elemento della matrice selezionata per gli indici.</span><span class="sxs-lookup"><span data-stu-id="f7962-152">The result of an array element access is a variable, namely the array element selected by the indices.</span></span>

<span data-ttu-id="f7962-153">Gli elementi della matrice possono essere enumerati utilizzando un `foreach` istruzione ([l'istruzione foreach](statements.md#the-foreach-statement)).</span><span class="sxs-lookup"><span data-stu-id="f7962-153">The elements of an array can be enumerated using a `foreach` statement ([The foreach statement](statements.md#the-foreach-statement)).</span></span>

## <a name="array-members"></a><span data-ttu-id="f7962-154">Membri di matrici</span><span class="sxs-lookup"><span data-stu-id="f7962-154">Array members</span></span>

<span data-ttu-id="f7962-155">Ogni tipo di matrice eredita i membri dichiarati di `System.Array` tipo.</span><span class="sxs-lookup"><span data-stu-id="f7962-155">Every array type inherits the members declared by the `System.Array` type.</span></span>

## <a name="array-covariance"></a><span data-ttu-id="f7962-156">Covarianza di matrici</span><span class="sxs-lookup"><span data-stu-id="f7962-156">Array covariance</span></span>

<span data-ttu-id="f7962-157">Per due *reference_type*s `A` e `B`, se una conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) o esplicita ([ Le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) esistente dal `A` al `B`, quindi la stessa conversione riferimento esiste anche dal tipo di matrice `A[R]` per il tipo di matrice `B[R]`, dove `R` qualsiasi determinata *rank_specifier* (ma lo stesso per entrambi i tipi di matrice).</span><span class="sxs-lookup"><span data-stu-id="f7962-157">For any two *reference_type*s `A` and `B`, if an implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) or explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from `A` to `B`, then the same reference conversion also exists from the array type `A[R]` to the array type `B[R]`, where `R` is any given *rank_specifier* (but the same for both array types).</span></span> <span data-ttu-id="f7962-158">Questa relazione è detta ***covarianza di matrici***.</span><span class="sxs-lookup"><span data-stu-id="f7962-158">This relationship is known as ***array covariance***.</span></span> <span data-ttu-id="f7962-159">Covarianza di matrici in particolare significa che un valore di tipo matrice `A[R]` può essere effettivamente un riferimento a un'istanza di un tipo di matrice `B[R]`, a condizione che esiste una conversione implicita del riferimento da `B` a `A`.</span><span class="sxs-lookup"><span data-stu-id="f7962-159">Array covariance in particular means that a value of an array type `A[R]` may actually be a reference to an instance of an array type `B[R]`, provided an implicit reference conversion exists from `B` to `A`.</span></span>

<span data-ttu-id="f7962-160">A causa di covarianza di matrici, assegnazioni agli elementi delle matrici di tipi di riferimento includono un controllo di runtime che assicura che il valore assegnato all'elemento della matrice è effettivamente di un tipo consentito ([assegnamento semplice](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="f7962-160">Because of array covariance, assignments to elements of reference type arrays include a run-time check which ensures that the value being assigned to the array element is actually of a permitted type ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="f7962-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f7962-161">For example:</span></span>
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

<span data-ttu-id="f7962-162">L'assegnazione al `array[i]` nella `Fill` metodo in modo implicito include un controllo di runtime che garantisce che l'oggetto fa `value` può essere `null` o un'istanza compatibile con il tipo effettivo degli elementi di `array`.</span><span class="sxs-lookup"><span data-stu-id="f7962-162">The assignment to `array[i]` in the `Fill` method implicitly includes a run-time check which ensures that the object referenced by `value` is either `null` or an instance that is compatible with the actual element type of `array`.</span></span> <span data-ttu-id="f7962-163">Nelle `Main`, le prime due chiamate di `Fill` abbia esito positivo, ma le cause di chiamata al terzo una `System.ArrayTypeMismatchException` eccezione dopo l'esecuzione della prima assegnazione a `array[i]`.</span><span class="sxs-lookup"><span data-stu-id="f7962-163">In `Main`, the first two invocations of `Fill` succeed, but the third invocation causes a `System.ArrayTypeMismatchException` to be thrown upon executing the first assignment to `array[i]`.</span></span> <span data-ttu-id="f7962-164">L'eccezione si verifica perché boxed `int` non possono essere archiviati un `string` matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-164">The exception occurs because a boxed `int` cannot be stored in a `string` array.</span></span>

<span data-ttu-id="f7962-165">Covarianza di matrici in particolare non si estende alle matrici di *value_type*s.</span><span class="sxs-lookup"><span data-stu-id="f7962-165">Array covariance specifically does not extend to arrays of *value_type*s.</span></span> <span data-ttu-id="f7962-166">Ad esempio, non esiste alcuna conversione e consente un' `int[]` deve essere trattato come un `object[]`.</span><span class="sxs-lookup"><span data-stu-id="f7962-166">For example, no conversion exists that permits an `int[]` to be treated as an `object[]`.</span></span>

## <a name="array-initializers"></a><span data-ttu-id="f7962-167">Inizializzatori di matrice</span><span class="sxs-lookup"><span data-stu-id="f7962-167">Array initializers</span></span>

<span data-ttu-id="f7962-168">Gli inizializzatori di matrice possono essere specificati nelle dichiarazioni di campo ([campi](classes.md#fields)), le dichiarazioni delle variabili locali ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) e le espressioni di creazione di matrici ([creazione di matrici le espressioni](expressions.md#array-creation-expressions)):</span><span class="sxs-lookup"><span data-stu-id="f7962-168">Array initializers may be specified in field declarations ([Fields](classes.md#fields)), local variable declarations ([Local variable declarations](statements.md#local-variable-declarations)), and array creation expressions ([Array creation expressions](expressions.md#array-creation-expressions)):</span></span>

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

<span data-ttu-id="f7962-169">Un inizializzatore di matrice è costituito da una sequenza di inizializzatori racchiusi tra "`{`"e"`}`"token e separati da"`,`" token.</span><span class="sxs-lookup"><span data-stu-id="f7962-169">An array initializer consists of a sequence of variable initializers, enclosed by "`{`" and "`}`" tokens and separated by "`,`" tokens.</span></span> <span data-ttu-id="f7962-170">Ogni inizializzatore di variabile è un'espressione o, nel caso di una matrice multidimensionale, un inizializzatore di matrice annidati.</span><span class="sxs-lookup"><span data-stu-id="f7962-170">Each variable initializer is an expression or, in the case of a multi-dimensional array, a nested array initializer.</span></span>

<span data-ttu-id="f7962-171">Il contesto in cui viene usato un inizializzatore di matrice determina il tipo della matrice in fase di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="f7962-171">The context in which an array initializer is used determines the type of the array being initialized.</span></span> <span data-ttu-id="f7962-172">In un'espressione di creazione della matrice, il tipo di matrice immediatamente precede l'inizializzatore, o viene dedotto dalle espressioni nell'inizializzatore di matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-172">In an array creation expression, the array type immediately precedes the initializer, or is inferred from the expressions in the array initializer.</span></span> <span data-ttu-id="f7962-173">In un campo o una dichiarazione di variabile, il tipo di matrice è il tipo del campo o della variabile dichiarata.</span><span class="sxs-lookup"><span data-stu-id="f7962-173">In a field or variable declaration, the array type is the type of the field or variable being declared.</span></span> <span data-ttu-id="f7962-174">Quando un inizializzatore di matrice viene usato in un campo o una dichiarazione di variabile, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f7962-174">When an array initializer is used in a field or variable declaration, such as:</span></span>
```csharp
int[] a = {0, 2, 4, 6, 8};
```
<span data-ttu-id="f7962-175">è sufficiente a sintassi abbreviata per un'espressione di creazione matrice equivalente:</span><span class="sxs-lookup"><span data-stu-id="f7962-175">it is simply shorthand for an equivalent array creation expression:</span></span>
```csharp
int[] a = new int[] {0, 2, 4, 6, 8};
```

<span data-ttu-id="f7962-176">Per una matrice unidimensionale, l'inizializzatore di matrice deve contenere una sequenza di espressioni di assegnazione compatibile con il tipo di elemento della matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-176">For a single-dimensional array, the array initializer must consist of a sequence of expressions that are assignment compatible with the element type of the array.</span></span> <span data-ttu-id="f7962-177">Le espressioni di inizializzano gli elementi di matrice in ordine crescente, iniziando con l'elemento in corrispondenza dell'indice zero.</span><span class="sxs-lookup"><span data-stu-id="f7962-177">The expressions initialize array elements in increasing order, starting with the element at index zero.</span></span> <span data-ttu-id="f7962-178">Il numero di espressioni nell'inizializzatore di matrice determina la lunghezza dell'istanza della matrice da creare.</span><span class="sxs-lookup"><span data-stu-id="f7962-178">The number of expressions in the array initializer determines the length of the array instance being created.</span></span> <span data-ttu-id="f7962-179">Ad esempio, l'inizializzatore di matrice precedente crea un `int[]` istanza lunghezza 5 e quindi inizializza l'istanza con i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="f7962-179">For example, the array initializer above creates an `int[]` instance of length 5 and then initializes the instance with the following values:</span></span>
```csharp
a[0] = 0; a[1] = 2; a[2] = 4; a[3] = 6; a[4] = 8;
```

<span data-ttu-id="f7962-180">Per una matrice multidimensionale, l'inizializzatore di matrice deve avere tutti i livelli di nidificazione come non vi sono dimensioni nella matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-180">For a multi-dimensional array, the array initializer must have as many levels of nesting as there are dimensions in the array.</span></span> <span data-ttu-id="f7962-181">Il livello di annidamento più esterno corrisponde alla dimensione più a sinistra e il livello di nidificazione più interno corrisponde alla dimensione più a destra.</span><span class="sxs-lookup"><span data-stu-id="f7962-181">The outermost nesting level corresponds to the leftmost dimension and the innermost nesting level corresponds to the rightmost dimension.</span></span> <span data-ttu-id="f7962-182">La lunghezza di ogni dimensione della matrice è determinata dal numero di elementi a livello di nidificazione corrispondente nell'inizializzatore di matrice.</span><span class="sxs-lookup"><span data-stu-id="f7962-182">The length of each dimension of the array is determined by the number of elements at the corresponding nesting level in the array initializer.</span></span> <span data-ttu-id="f7962-183">Per ogni inizializzatore di matrice annidati, il numero di elementi deve essere lo stesso come altri inizializzatori di matrice allo stesso livello.</span><span class="sxs-lookup"><span data-stu-id="f7962-183">For each nested array initializer, the number of elements must be the same as the other array initializers at the same level.</span></span> <span data-ttu-id="f7962-184">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f7962-184">The example:</span></span>
```csharp
int[,] b = {{0, 1}, {2, 3}, {4, 5}, {6, 7}, {8, 9}};
```
<span data-ttu-id="f7962-185">Crea una matrice bidimensionale con una lunghezza pari a cinque per la dimensione più a sinistra e una lunghezza pari a due per la dimensione più a destra:</span><span class="sxs-lookup"><span data-stu-id="f7962-185">creates a two-dimensional array with a length of five for the leftmost dimension and a length of two for the rightmost dimension:</span></span>
```csharp
int[,] b = new int[5, 2];
```
<span data-ttu-id="f7962-186">e quindi inizializza l'istanza della matrice con i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="f7962-186">and then initializes the array instance with the following values:</span></span>
```csharp
b[0, 0] = 0; b[0, 1] = 1;
b[1, 0] = 2; b[1, 1] = 3;
b[2, 0] = 4; b[2, 1] = 5;
b[3, 0] = 6; b[3, 1] = 7;
b[4, 0] = 8; b[4, 1] = 9;
```

<span data-ttu-id="f7962-187">Se una dimensione diversa da più a destra viene fornita con lunghezza zero, si presuppone che le dimensioni successive per anche avere lunghezza zero.</span><span class="sxs-lookup"><span data-stu-id="f7962-187">If a dimension other than the rightmost is given with length zero, the subsequent dimensions are assumed to also have length zero.</span></span> <span data-ttu-id="f7962-188">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f7962-188">The example:</span></span>
```csharp
int[,] c = {};
```
<span data-ttu-id="f7962-189">Crea una matrice bidimensionale con una lunghezza pari a zero per più a sinistra e la dimensione più a destra:</span><span class="sxs-lookup"><span data-stu-id="f7962-189">creates a two-dimensional array with a length of zero for both the leftmost and the rightmost dimension:</span></span>
```csharp
int[,] c = new int[0, 0];
```

<span data-ttu-id="f7962-190">Quando un'espressione di creazione della matrice include le lunghezze delle dimensioni esplicite sia un inizializzatore di matrice, le lunghezze devono essere espressioni costanti e il numero di elementi in ogni livello di nidificazione deve corrispondere alla lunghezza della dimensione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="f7962-190">When an array creation expression includes both explicit dimension lengths and an array initializer, the lengths must be constant expressions and the number of elements at each nesting level must match the corresponding dimension length.</span></span> <span data-ttu-id="f7962-191">Ecco alcuni esempi:</span><span class="sxs-lookup"><span data-stu-id="f7962-191">Here are some examples:</span></span>
```csharp
int i = 3;
int[] x = new int[3] {0, 1, 2};        // OK
int[] y = new int[i] {0, 1, 2};        // Error, i not a constant
int[] z = new int[3] {0, 1, 2, 3};     // Error, length/initializer mismatch
```

<span data-ttu-id="f7962-192">A questo punto, l'inizializzatore per `y` comporta un errore in fase di compilazione perché l'espressione di lunghezza della dimensione non è una costante e l'inizializzatore per `z` comporta un errore in fase di compilazione perché la lunghezza e il numero di elementi di inizializzatore non accettare.</span><span class="sxs-lookup"><span data-stu-id="f7962-192">Here, the initializer for `y` results in a compile-time error because the dimension length expression is not a constant, and the initializer for `z` results in a compile-time error because the length and the number of elements in the initializer do not agree.</span></span>
