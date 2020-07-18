---
ms.openlocfilehash: 01ebf5caf071467496d4ac1c2664f64b790525cf
ms.sourcegitcommit: 8087d27db178039c2dc5fbfa6b3ac9cde8cd2ad2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451320"
---
# <a name="unsafe-code"></a>Codice di tipo unsafe

Il linguaggio C# di base, come definito nei capitoli precedenti, differisce in particolare da C e C++ nell'omissione dei puntatori come tipo di dati. C# fornisce invece riferimenti e la possibilità di creare oggetti gestiti da un Garbage Collector. Questo progetto, associato ad altre funzionalità, rende C# un linguaggio molto più sicuro rispetto a C o C++. Nel linguaggio C# di base non è semplicemente possibile avere una variabile non inizializzata, un puntatore "penzoloni" o un'espressione che indicizza una matrice oltre i limiti. Vengono quindi eliminate le intere categorie di bug che periodicamente affliggono i programmi C e C++.

Sebbene praticamente ogni costrutto di tipo di puntatore in C o C++ includa una controparte del tipo di riferimento in C#, tuttavia, esistono situazioni in cui l'accesso ai tipi di puntatore diventa una necessità. Ad esempio, l'interazione con il sistema operativo sottostante, l'accesso a un dispositivo mappato alla memoria o l'implementazione di un algoritmo critico per il tempo potrebbe non essere possibile o praticabile senza l'accesso ai puntatori. Per rispondere a questa esigenza, C# consente di scrivere codice di tipo ***unsafe***.

In codice unsafe è possibile dichiarare e operare sui puntatori, per eseguire conversioni tra puntatori e tipi integrali, per prendere l'indirizzo delle variabili e così via. In un certo senso, la scrittura di codice non sicuro è molto simile alla scrittura di codice C in un programma C#.

Il codice di tipo unsafe è infatti una funzionalità "sicura" dal punto di vista di sviluppatori e utenti. Il codice di tipo unsafe deve essere contrassegnato chiaramente con il modificatore `unsafe` , in modo che gli sviluppatori non possano usare accidentalmente funzionalità non sicure e il motore di esecuzione funzioni per garantire che il codice non sicuro non possa essere eseguito in un ambiente non attendibile.

## <a name="unsafe-contexts"></a>Contesti non sicuri

Le funzionalità unsafe di C# sono disponibili solo in contesti non sicuri. Un contesto unsafe viene introdotto includendo un `unsafe` modificatore nella dichiarazione di un tipo o di un membro oppure utilizzando un *unsafe_statement*:

*  Una dichiarazione di una classe, una struttura, un'interfaccia o un delegato può includere un `unsafe` modificatore, nel qual caso l'intero extent testuale della dichiarazione del tipo (incluso il corpo della classe, dello struct o dell'interfaccia) viene considerato un contesto non sicuro.
*  Una dichiarazione di un campo, un metodo, una proprietà, un evento, un indicizzatore, un operatore, un costruttore di istanza, un distruttore o un costruttore statico può includere un `unsafe` modificatore, nel qual caso l'intero extent testuale della dichiarazione del membro viene considerato un contesto non sicuro.
*  Un *unsafe_statement* consente l'utilizzo di un contesto non sicuro all'interno di un *blocco*. L'intero extent testuale del *blocco* associato viene considerato un contesto non sicuro.

Le produzioni di grammatica associate sono illustrate di seguito.

```antlr
class_modifier_unsafe
    : 'unsafe'
    ;

struct_modifier_unsafe
    : 'unsafe'
    ;

interface_modifier_unsafe
    : 'unsafe'
    ;

delegate_modifier_unsafe
    : 'unsafe'
    ;

field_modifier_unsafe
    : 'unsafe'
    ;

method_modifier_unsafe
    : 'unsafe'
    ;

property_modifier_unsafe
    : 'unsafe'
    ;

event_modifier_unsafe
    : 'unsafe'
    ;

indexer_modifier_unsafe
    : 'unsafe'
    ;

operator_modifier_unsafe
    : 'unsafe'
    ;

constructor_modifier_unsafe
    : 'unsafe'
    ;

destructor_declaration_unsafe
    : attributes? 'extern'? 'unsafe'? '~' identifier '(' ')' destructor_body
    | attributes? 'unsafe'? 'extern'? '~' identifier '(' ')' destructor_body
    ;

static_constructor_modifiers_unsafe
    : 'extern'? 'unsafe'? 'static'
    | 'unsafe'? 'extern'? 'static'
    | 'extern'? 'static' 'unsafe'?
    | 'unsafe'? 'static' 'extern'?
    | 'static' 'extern'? 'unsafe'?
    | 'static' 'unsafe'? 'extern'?
    ;

embedded_statement_unsafe
    : unsafe_statement
    | fixed_statement
    ;

unsafe_statement
    : 'unsafe' block
    ;
```

Nell'esempio

```csharp
public unsafe struct Node
{
    public int Value;
    public Node* Left;
    public Node* Right;
}
```

il `unsafe` modificatore specificato nella dichiarazione struct causa l'intero extent testuale della dichiarazione struct a diventare un contesto non sicuro. È quindi possibile dichiarare i `Left` campi e in modo che `Right` siano di tipo puntatore. È possibile scrivere anche l'esempio precedente

```csharp
public struct Node
{
    public int Value;
    public unsafe Node* Left;
    public unsafe Node* Right;
}
```

In questo caso, i `unsafe` modificatori nelle dichiarazioni di campo fanno sì che tali dichiarazioni vengano considerate contesti non sicuri.

Oltre a stabilire un contesto non sicuro, consentendo così l'uso di tipi di puntatore, il `unsafe` modificatore non ha alcun effetto su un tipo o un membro. Nell'esempio

```csharp
public class A
{
    public unsafe virtual void F() {
        char* p;
        ...
    }
}

public class B: A
{
    public override void F() {
        base.F();
        ...
    }
}
```

il `unsafe` modificatore del `F` metodo in `A` comporta semplicemente l'estensione testuale di `F` per diventare un contesto non sicuro in cui è possibile utilizzare le funzionalità non sicure del linguaggio. Nell'override di `F` in `B` , non è necessario specificare di nuovo il `unsafe` modificatore, a meno che, naturalmente, il `F` metodo di `B` per sé non debba accedere a funzionalità non sicure.

La situazione è leggermente diversa quando un tipo di puntatore fa parte della firma del metodo

```csharp
public unsafe class A
{
    public virtual void F(char* p) {...}
}

public class B: A
{
    public unsafe override void F(char* p) {...}
}
```

Qui, poiché la `F` firma include un tipo di puntatore, può essere scritta solo in un contesto unsafe. Tuttavia, il contesto unsafe può essere introdotto rendendo l'intera classe unsafe, come nel caso di `A` o includendo un `unsafe` modificatore nella dichiarazione del metodo, come nel caso di `B` .

## <a name="pointer-types"></a>Tipi puntatore

In un contesto unsafe, un *tipo* ([tipi](types.md)) può essere un *pointer_type* nonché un *value_type* o un *reference_type*. Tuttavia, un *pointer_type* può essere usato anche in un' `typeof` espressione ([espressioni di creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) al di fuori di un contesto non sicuro perché tale utilizzo non è sicuro.

```antlr
type_unsafe
    : pointer_type
    ;
```

Un *pointer_type* viene scritto come *unmanaged_type* o parola chiave `void` , seguito da un `*` token:

```antlr
pointer_type
    : unmanaged_type '*'
    | 'void' '*'
    ;

unmanaged_type
    : type
    ;
```

Il tipo specificato prima `*` di in un tipo di puntatore è denominato ***tipo referente*** del tipo di puntatore. Rappresenta il tipo della variabile a cui fa riferimento un valore del tipo di puntatore.

A differenza dei riferimenti (valori dei tipi di riferimento), i puntatori non vengono rilevati dal Garbage Collector, il Garbage Collector non conosce i puntatori e i dati a cui puntano. Per questo motivo un puntatore non può puntare a un riferimento o a uno struct che contiene riferimenti e il tipo referente di un puntatore deve essere un *unmanaged_type*.

Un *unmanaged_type* è un tipo che non è un tipo *reference_type* o costruito e non contiene *reference_type* o campi di tipo costruiti a qualsiasi livello di annidamento. In altre parole, un *unmanaged_type* è uno dei seguenti:

*  `sbyte`, `byte` , `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` o `bool` .
*  Qualsiasi *enum_type*.
*  Qualsiasi *pointer_type*.
*  Qualsiasi *struct_type* definito dall'utente che non sia un tipo costruito e contiene solo campi di *unmanaged_type*.

La regola intuitiva per la combinazione di puntatori e riferimenti è che i riferimenti di riferimenti (oggetti) sono consentiti per contenere i puntatori, ma i riferimenti ai puntatori non possono contenere riferimenti.

Nella tabella seguente sono riportati alcuni esempi di tipi di puntatore:

| __Esempio__ | __Descrizione__                               |
|-------------|-----------------------------------------------|
| `byte*`     | Puntatore a `byte`                             |
| `char*`     | Puntatore a `char`                             |
| `int**`     | Puntatore a puntatore a`int`                   |
| `int*[]`    | Matrice unidimensionale di puntatori a`int` |
| `void*`     | Puntatore al tipo sconosciuto                       |

Per un'implementazione specifica, tutti i tipi di puntatore devono avere le stesse dimensioni e la stessa rappresentazione.

Diversamente da C e C++, quando più puntatori vengono dichiarati nella stessa dichiarazione, in C# l'oggetto `*` viene scritto solo con il tipo sottostante, non come un punteggiatore di prefisso per ogni nome di puntatore. Ad esempio:

```csharp
int* pi, pj;    // NOT as int *pi, *pj;
```

Il valore di un puntatore di tipo `T*` rappresenta l'indirizzo di una variabile di tipo `T` . `*`Per accedere a questa variabile, è possibile usare l'operatore di riferimento indiretto del puntatore ([riferimento indiretto del puntatore](unsafe-code.md#pointer-indirection)). Ad esempio, data una variabile `P` di tipo `int*` , l'espressione `*P` indica la `int` variabile trovata in corrispondenza dell'indirizzo contenuto in `P` .

Come un riferimento a un oggetto, un puntatore può essere `null` . L'applicazione dell'operatore di riferimento indiretto a un `null` puntatore comporta un comportamento definito dall'implementazione. Un puntatore con valore `null` è rappresentato da All-bits-zero.

Il `void*` tipo rappresenta un puntatore a un tipo sconosciuto. Poiché il tipo referente è sconosciuto, non è possibile applicare l'operatore di riferimento indiretto a un puntatore di tipo `void*` , né eseguire operazioni aritmetiche su un puntatore di questo tipo. Tuttavia, `void*` è possibile eseguire il cast di un puntatore di tipo a qualsiasi altro tipo di puntatore (e viceversa).

I tipi di puntatore sono una categoria separata di tipi. A differenza dei tipi di riferimento e dei tipi di valore, i tipi di puntatore non ereditano da `object` e non esistono conversioni tra i tipi di puntatore e `object` . In particolare, la conversione boxing e unboxing ([Boxing e](types.md#boxing-and-unboxing)unboxing) non sono supportate per i puntatori. Tuttavia, le conversioni sono consentite tra tipi di puntatore diversi e tra tipi di puntatore e tipi integrali. Questa operazione è descritta in [conversioni di puntatori](unsafe-code.md#pointer-conversions).

Non è possibile usare un *pointer_type* come argomento di tipo ([tipi costruiti](types.md#constructed-types)) e l'inferenza del tipo ([inferenza](expressions.md#type-inference)del tipo) ha esito negativo in chiamate a metodi generici che avrebbero dedotto un argomento di tipo come tipo di puntatore.

Un *pointer_type* può essere utilizzato come tipo di campo volatile ([campi volatili](classes.md#volatile-fields)).

Sebbene i puntatori possano essere passati `ref` come `out` parametri o, questo può causare un comportamento non definito, poiché il puntatore può essere impostato in modo da puntare a una variabile locale che non esiste più quando il metodo chiamato restituisce oppure l'oggetto fisso a cui è stato usato per puntare, non è più fisso. Ad esempio:

```csharp
using System;

class Test
{
    static int value = 20;

    unsafe static void F(out int* pi1, ref int* pi2) {
        int i = 10;
        pi1 = &i;

        fixed (int* pj = &value) {
            // ...
            pi2 = pj;
        }
    }

    static void Main() {
        int i = 10;
        unsafe {
            int* px1;
            int* px2 = &i;

            F(out px1, ref px2);

            Console.WriteLine("*px1 = {0}, *px2 = {1}",
                *px1, *px2);    // undefined behavior
        }
    }
}
```

Un metodo può restituire un valore di un tipo e tale tipo può essere un puntatore. Se, ad esempio, si specifica un puntatore a una sequenza contigua di `int` , il numero di elementi di tale sequenza e un altro `int` valore, il metodo seguente restituisce l'indirizzo di tale valore nella sequenza, se viene rilevata una corrispondenza; in caso contrario, restituisce `null` :

```csharp
unsafe static int* Find(int* pi, int size, int value) {
    for (int i = 0; i < size; ++i) {
        if (*pi == value) 
            return pi;
        ++pi;
    }
    return null;
}
```

In un contesto non sicuro, sono disponibili diversi costrutti per operare sui puntatori:

*  L' `*` operatore può essere utilizzato per eseguire un riferimento indiretto del puntatore ([riferimento indiretto](unsafe-code.md#pointer-indirection)).
*  L' `->` operatore può essere usato per accedere a un membro di uno struct tramite un puntatore ([accesso al membro del puntatore](unsafe-code.md#pointer-member-access)).
*  L' `[]` operatore può essere utilizzato per indicizzare un puntatore ([accesso all'elemento del puntatore](unsafe-code.md#pointer-element-access)).
*  L' `&` operatore può essere utilizzato per ottenere l'indirizzo di una variabile ([l'operatore address-of](unsafe-code.md#the-address-of-operator)).
*  Gli `++` `--` operatori e possono essere utilizzati per incrementare e decrementare i puntatori ([incremento e decremento del puntatore](unsafe-code.md#pointer-increment-and-decrement)).
*  Gli `+` `-` operatori e possono essere utilizzati per eseguire l'aritmetica dei puntatori ([aritmetica del puntatore](unsafe-code.md#pointer-arithmetic)).
*  Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `>=` possono essere usati per confrontare i puntatori ([confronto tra](unsafe-code.md#pointer-comparison)puntatori).
*  L' `stackalloc` operatore può essere utilizzato per allocare memoria dallo stack di chiamate ([buffer a dimensione fissa](unsafe-code.md#fixed-size-buffers)).
*  L' `fixed` istruzione può essere utilizzata per correggere temporaneamente una variabile in modo che sia possibile ottenere l'indirizzo ([istruzione fixed](unsafe-code.md#the-fixed-statement)).

## <a name="fixed-and-moveable-variables"></a>Variabili fisse e mobili

L'operatore address-of ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) e l' `fixed` istruzione ([istruzione fixed](unsafe-code.md#the-fixed-statement)) dividono le variabili in due categorie: ***variabili fisse*** e ***variabili mobili***.

Le variabili fisse si trovano in percorsi di archiviazione che non sono interessati dall'operazione del Garbage Collector. Esempi di variabili fisse includono variabili locali, parametri di valore e variabili create mediante la dereferenziazione dei puntatori. D'altra parte, le variabili mobili si trovano in posizioni di archiviazione soggette alla rilocazione o all'eliminazione da parte del Garbage Collector. (Esempi di variabili mobili includono campi in oggetti ed elementi di matrici).

L' `&` operatore ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) consente di ottenere l'indirizzo di una variabile fissa senza restrizioni. Tuttavia, poiché una variabile mobile è soggetta alla rilocazione o all'eliminazione da parte del Garbage Collector, l'indirizzo di una variabile mobile può essere ottenuto solo utilizzando un' `fixed` istruzione ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) e tale indirizzo rimane valido solo per la durata dell' `fixed` istruzione.

In termini precisi, una variabile fissa è una delle seguenti:

*  Variabile risultante da un *simple_name* ([nomi semplici](expressions.md#simple-names)) che fa riferimento a una variabile locale o a un parametro di valore, a meno che la variabile non venga acquisita da una funzione anonima.
*  Variabile risultante da un *member_access* ([accesso ai membri](expressions.md#member-access)) del form `V.I` , dove `V` è una variabile fissa di un *struct_type*.
*  Variabile risultante da un *pointer_indirection_expression* ([riferimento indiretto del puntatore](unsafe-code.md#pointer-indirection)) del form `*P` , un *pointer_member_access* (accesso al membro del[puntatore](unsafe-code.md#pointer-member-access)) del form `P->I` o un *pointer_element_access* (accesso all'[elemento del puntatore](unsafe-code.md#pointer-element-access)) del form `P[E]` .

Tutte le altre variabili sono classificate come variabili mobili.

Si noti che un campo statico è classificato come variabile mobile. Si noti inoltre che `ref` un `out` parametro o è classificato come variabile mobile, anche se l'argomento specificato per il parametro è una variabile fissa. Si noti infine che una variabile prodotta mediante la dereferenziazione di un puntatore viene sempre classificata come variabile fissa.

## <a name="pointer-conversions"></a>Conversioni puntatore

In un contesto non sicuro, il set di conversioni implicite disponibili ([conversioni implicite](conversions.md#implicit-conversions)) viene esteso in modo da includere le seguenti conversioni implicite del puntatore:

*  Da qualsiasi *pointer_type* al tipo `void*` .
*  Dal `null` valore letterale a qualsiasi *pointer_type*.

Inoltre, in un contesto unsafe, il set di conversioni esplicite disponibili ([conversioni esplicite](conversions.md#explicit-conversions)) viene esteso in modo da includere le seguenti conversioni esplicite del puntatore:

*  Da qualsiasi *pointer_type* a qualsiasi altra *pointer_type*.
*  Da `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` o `ulong` a qualsiasi *pointer_type*.
*  Da qualsiasi *pointer_type* a `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` o `ulong` .

Infine, in un contesto unsafe, il set di conversioni implicite standard ([conversioni implicite standard](conversions.md#standard-implicit-conversions)) include la seguente conversione del puntatore:

*  Da qualsiasi *pointer_type* al tipo `void*` .

Le conversioni tra due tipi di puntatore non modificano mai il valore effettivo del puntatore. In altre parole, una conversione da un tipo di puntatore a un altro non ha alcun effetto sull'indirizzo sottostante fornito dal puntatore.

Quando un tipo di puntatore viene convertito in un altro, se il puntatore risultante non è allineato correttamente per il tipo puntato, il comportamento non è definito se il risultato è dereferenziato. In generale, il concetto "allineato correttamente" è transitivo: se un puntatore al tipo `A` è correttamente allineato per un puntatore al tipo `B` , che, a sua volta, è allineato correttamente per un puntatore al tipo `C` , un puntatore al tipo `A` è correttamente allineato per un puntatore al tipo `C` .

Si consideri il caso seguente in cui si accede a una variabile con un tipo tramite un puntatore a un tipo diverso:

```csharp
char c = 'A';
char* pc = &c;
void* pv = pc;
int* pi = (int*)pv;
int i = *pi;         // undefined
*pi = 123456;        // undefined
```

Quando un tipo di puntatore viene convertito in un puntatore a byte, il risultato fa riferimento al byte più basso risolto della variabile. Gli incrementi successivi del risultato, fino alla dimensione della variabile, restituiscono puntatori ai byte rimanenti della variabile. Ad esempio, il metodo seguente visualizza ciascuno degli otto byte in un valore Double come valore esadecimale:

```csharp
using System;

class Test
{
    unsafe static void Main() {
      double d = 123.456e23;
        unsafe {
           byte* pb = (byte*)&d;
            for (int i = 0; i < sizeof(double); ++i)
               Console.Write("{0:X2} ", *pb++);
            Console.WriteLine();
        }
    }
}
```

Naturalmente, l'output prodotto dipende da un tipo di output.

I mapping tra i puntatori e i numeri interi vengono definiti dall'implementazione. Tuttavia, nelle architetture della CPU 32 * e a 64 bit con uno spazio degli indirizzi lineare, le conversioni di puntatori a o da tipi integrali si comportano in genere esattamente come le conversioni dei `uint` valori o, `ulong` rispettivamente, verso o da tali tipi integrali.

### <a name="pointer-arrays"></a>Matrici puntatore

In un contesto non sicuro è possibile costruire matrici di puntatori. Nelle matrici di puntatore sono consentite solo alcune conversioni applicabili ad altri tipi di matrice:

*  La conversione implicita dei riferimenti ([conversioni implicite dei riferimenti](conversions.md#implicit-reference-conversions)) da qualsiasi *array_type* a `System.Array` e le interfacce implementate si applica anche alle matrici del puntatore. Tuttavia, qualsiasi tentativo di accedere agli elementi della matrice tramite `System.Array` o le interfacce implementate genererà un'eccezione in fase di esecuzione, perché i tipi di puntatore non sono convertibili in `object` .
*  Le conversioni di riferimenti implicite ed esplicite ([conversioni implicite](conversions.md#implicit-reference-conversions)dei riferimenti, conversioni di [riferimenti esplicite](conversions.md#explicit-reference-conversions)) da un tipo di matrice unidimensionale `S[]` a `System.Collections.Generic.IList<T>` e le interfacce di base generiche non si applicano mai alle matrici di puntatore, perché i tipi di puntatore non possono essere utilizzati come argomenti di tipo e non esistono conversioni dai tipi puntatore ai tipi non puntatore.
*  La conversione esplicita del riferimento ([conversioni esplicite dei riferimenti](conversions.md#explicit-reference-conversions)) da `System.Array` e le interfacce implementate in qualsiasi *array_type* si applicano alle matrici di puntatore.
*  Le conversioni esplicite dei riferimenti ([conversioni esplicite di riferimento](conversions.md#explicit-reference-conversions)) da `System.Collections.Generic.IList<S>` e le relative interfacce di base a un tipo di matrice unidimensionale `T[]` non si applicano mai alle matrici del puntatore, perché i tipi di puntatore non possono essere utilizzati come argomenti di tipo e non ci sono conversioni dai tipi di puntatore ai tipi non puntatore.

Queste restrizioni indicano che l'espansione per l' `foreach` istruzione sulle matrici descritte nell' [istruzione foreach](statements.md#the-foreach-statement) non può essere applicata alle matrici di puntatore. Al contrario, un'istruzione foreach nel formato

```csharp
foreach (V v in x) embedded_statement
```

Se il tipo di `x` è un tipo di matrice del form `T[,,...,]` , `N` è il numero di dimensioni meno 1 e `T` o `V` è un tipo di puntatore, viene espanso usando i cicli for annidati come segue:

```csharp
{
    T[,,...,] a = x;
    for (int i0 = a.GetLowerBound(0); i0 <= a.GetUpperBound(0); i0++)
    for (int i1 = a.GetLowerBound(1); i1 <= a.GetUpperBound(1); i1++)
    ...
    for (int iN = a.GetLowerBound(N); iN <= a.GetUpperBound(N); iN++) {
        V v = (V)a.GetValue(i0,i1,...,iN);
        embedded_statement
    }
}
```

Le variabili `a` , `i0` , `i1` ,..., `iN` non sono visibili o accessibili a `x` oppure il *embedded_statement* o qualsiasi altro codice sorgente del programma. La variabile `v` è di sola lettura nell'istruzione incorporata. Se non è presente una conversione esplicita ([conversioni di puntatore](unsafe-code.md#pointer-conversions)) da `T` (il tipo di elemento) a `V` , viene generato un errore e non vengono eseguiti altri passaggi. Se `x` ha il valore `null` , `System.NullReferenceException` viene generata un'eccezione in fase di esecuzione.

## <a name="pointers-in-expressions"></a>Puntatori nelle espressioni

In un contesto unsafe, un'espressione può restituire un risultato di un tipo di puntatore, ma all'esterno di un contesto non sicuro si tratta di un errore in fase di compilazione perché un'espressione sia di tipo puntatore. In termini precisi, all'esterno di un contesto non sicuro si verifica un errore in fase di compilazione se qualsiasi *simple_name* ([nomi semplici](expressions.md#simple-names)), *member_access* ([accesso ai membri](expressions.md#member-access)), *invocation_expression* ([espressioni di chiamata](expressions.md#invocation-expressions)) o *element_access* ([accesso agli elementi](expressions.md#element-access)) è di tipo puntatore.

In un contesto non sicuro, le produzioni *primary_no_array_creation_expression* ([espressioni primarie](expressions.md#primary-expressions)) e *unary_expression* ([operatori unari](expressions.md#unary-operators)) consentono i seguenti costrutti aggiuntivi:

```antlr
primary_no_array_creation_expression_unsafe
    : pointer_member_access
    | pointer_element_access
    | sizeof_expression
    ;

unary_expression_unsafe
    : pointer_indirection_expression
    | addressof_expression
    ;
```

Questi costrutti sono descritti nelle sezioni riportate di seguito. La precedenza e l'associatività degli operatori unsafe sono implicite nella grammatica.

### <a name="pointer-indirection"></a>Riferimento indiretto a puntatore

Un *pointer_indirection_expression* è costituito da un asterisco ( `*` ) seguito da un *unary_expression*.

```antlr
pointer_indirection_expression
    : '*' unary_expression
    ;
```

L'operatore unario `*` denota il riferimento indiretto del puntatore e viene usato per ottenere la variabile a cui punta un puntatore. Il risultato della valutazione di `*P` , dove `P` è un'espressione di un tipo di puntatore `T*` , è una variabile di tipo `T` . Si tratta di un errore in fase di compilazione per applicare l'operatore unario `*` a un'espressione di tipo `void*` o a un'espressione che non è di tipo puntatore.

L'effetto dell'applicazione dell'operatore unario `*` a un `null` puntatore è definito dall'implementazione. In particolare, non esiste alcuna garanzia che questa operazione generi un'eccezione `System.NullReferenceException` .

Se al puntatore è stato assegnato un valore non valido, il comportamento dell'operatore unario non `*` è definito. Tra i valori non validi per la dereferenziazione di un puntatore dall'operatore unario `*` si intende un indirizzo allineato in modo non appropriato per il tipo a cui punta (vedere l'esempio nelle [conversioni dei puntatori](unsafe-code.md#pointer-conversions)) e l'indirizzo di una variabile dopo la fine del relativo ciclo di vita.

Ai fini dell'analisi di assegnazione definita, una variabile prodotta dalla valutazione di un'espressione del form `*P` viene considerata inizialmente assegnata (le[variabili assegnate inizialmente](variables.md#initially-assigned-variables)).

### <a name="pointer-member-access"></a>Accesso ai membri del puntatore

Un *pointer_member_access* è costituito da un *primary_expression*, seguito da un `->` token "", seguito da un *identificatore* e da un *type_argument_list*facoltativo.

```antlr
pointer_member_access
    : primary_expression '->' identifier
    ;
```

In un accesso ai membri di un puntatore del form `P->I` , `P` deve essere un'espressione di un tipo di puntatore diverso da `void*` e `I` deve indicare un membro accessibile del tipo a cui `P` punta.

Un accesso al membro del puntatore del form `P->I` viene valutato esattamente come `(*P).I` . Per una descrizione dell'operatore di riferimento indiretto del puntatore ( `*` ), vedere riferimento [indiretto del puntatore](unsafe-code.md#pointer-indirection). Per una descrizione dell'operatore di accesso ai membri ( `.` ), vedere [accesso ai membri](expressions.md#member-access).

Nell'esempio

```csharp
using System;

struct Point
{
    public int x;
    public int y;

    public override string ToString() {
        return "(" + x + "," + y + ")";
    }
}

class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            p->x = 10;
            p->y = 20;
            Console.WriteLine(p->ToString());
        }
    }
}
```

l' `->` operatore viene usato per accedere ai campi e richiamare un metodo di uno struct tramite un puntatore. Poiché l'operazione `P->I` è esattamente equivalente a `(*P).I` , è `Main` possibile che sia stato scritto anche il metodo:

```csharp
class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            (*p).x = 10;
            (*p).y = 20;
            Console.WriteLine((*p).ToString());
        }
    }
}
```

### <a name="pointer-element-access"></a>Accesso agli elementi del puntatore

Un *pointer_element_access* è costituito da un *primary_no_array_creation_expression* seguito da un'espressione racchiusa tra " `[` " e " `]` ".

```antlr
pointer_element_access
    : primary_no_array_creation_expression '[' expression ']'
    ;
```

Nell'accesso a un elemento puntatore del form `P[E]` , `P` deve essere un'espressione di un tipo di puntatore diverso da `void*` e `E` deve essere un'espressione che può essere convertita in modo implicito in `int` , `uint` , `long` o `ulong` .

L'accesso di un elemento puntatore al form `P[E]` viene valutato esattamente come `*(P + E)` . Per una descrizione dell'operatore di riferimento indiretto del puntatore ( `*` ), vedere riferimento [indiretto del puntatore](unsafe-code.md#pointer-indirection). Per una descrizione dell'operatore di addizione del puntatore ( `+` ), vedere [aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic).

Nell'esempio

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) p[i] = (char)i;
        }
    }
}
```

l'accesso a un elemento del puntatore viene usato per inizializzare il buffer di caratteri in un `for` ciclo. Poiché l'operazione `P[E]` è esattamente equivalente a `*(P + E)` , è possibile che sia stata scritta anche l'esempio seguente:

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) *(p + i) = (char)i;
        }
    }
}
```

L'operatore di accesso dell'elemento puntatore non verifica la presenza di errori fuori limite e il comportamento quando si accede a un elemento fuori limite non è definito. Corrisponde a C e C++.

### <a name="the-address-of-operator"></a>Operatore address-of

Un *addressof_expression* è costituito da una e commerciale ( `&` ) seguita da una *unary_expression*.

```antlr
addressof_expression
    : '&' unary_expression
    ;
```

Data un'espressione `E` che è di un tipo `T` ed è classificata come variabile fissa ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)), il costrutto `&E` Calcola l'indirizzo della variabile fornita da `E` . Il tipo del risultato è `T*` ed è classificato come valore. Si verifica un errore in fase di compilazione se `E` non viene classificato come una variabile, se `E` è classificato come variabile locale di sola lettura o se `E` denota una variabile mobile. Nell'ultimo caso, è possibile usare un'istruzione fixed ([istruzione fixed](unsafe-code.md#the-fixed-statement)) per correggere temporaneamente la variabile prima di ottenerne l'indirizzo. Come indicato nell' [accesso ai membri](expressions.md#member-access), al di fuori di un costruttore di istanza o di un costruttore statico per uno struct o una classe che definisce un `readonly` campo, il campo viene considerato un valore, non una variabile. Di conseguenza, non è possibile eseguire il relativo indirizzo. Analogamente, non è possibile eseguire l'indirizzo di una costante.

L' `&` operatore non richiede che il relativo argomento venga assegnato definitivamente, ma dopo un' `&` operazione la variabile a cui viene applicato l'operatore viene considerata definitivamente assegnata nel percorso di esecuzione in cui si verifica l'operazione. È responsabilità del programmatore garantire che la corretta inizializzazione della variabile venga effettivamente eseguita in questa situazione.

Nell'esempio

```csharp
using System;

class Test
{
    static void Main() {
        int i;
        unsafe {
            int* p = &i;
            *p = 123;
        }
        Console.WriteLine(i);
    }
}
```

`i`viene considerato definitivamente assegnato dopo l' `&i` operazione utilizzata per inizializzare `p` . L'assegnazione a `*p` in effetti Inizializza `i` , ma l'inclusione di questa inizializzazione è responsabilità del programmatore e non si verificherà alcun errore in fase di compilazione se l'assegnazione è stata rimossa.

Le regole di assegnazione definitiva per l' `&` operatore esistono in modo tale che l'inizializzazione ridondante delle variabili locali possa essere evitata. Molte API esterne, ad esempio, accettano un puntatore a una struttura compilata dall'API. Le chiamate a tali API in genere passano l'indirizzo di una variabile di struct locale e senza la regola è necessaria l'inizializzazione ridondante della variabile struct.

### <a name="pointer-increment-and-decrement"></a>Incremento e decremento dei puntatori

In un contesto non sicuro, `++` è possibile applicare gli operatori e (operatori di `--` [incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators) e [operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)) a variabili puntatore di tutti i tipi ad eccezione di `void*` . Pertanto, per ogni tipo di puntatore `T*` , gli operatori seguenti sono definiti in modo implicito:

```csharp
T* operator ++(T* x);
T* operator --(T* x);
```

Gli operatori producono gli stessi risultati `x + 1` rispettivamente di e `x - 1` ([aritmetica del puntatore](unsafe-code.md#pointer-arithmetic)). In altre parole, per una variabile puntatore di tipo `T*` , l' `++` operatore aggiunge `sizeof(T)` all'indirizzo contenuto nella variabile e l' `--` operatore sottrae `sizeof(T)` dall'indirizzo contenuto nella variabile.

Se un'operazione di incremento o di decremento di un puntatore causa un overflow del dominio del tipo di puntatore, il risultato è definito dall'implementazione, ma non vengono generate eccezioni.

### <a name="pointer-arithmetic"></a>Puntatore aritmetico

In un contesto non sicuro, gli `+` `-` operatori e (operatore di[addizione](expressions.md#addition-operator) e [sottrazione](expressions.md#subtraction-operator)) possono essere applicati ai valori di tutti i tipi di puntatore eccetto `void*` . Pertanto, per ogni tipo di puntatore `T*` , gli operatori seguenti sono definiti in modo implicito:

```csharp
T* operator +(T* x, int y);
T* operator +(T* x, uint y);
T* operator +(T* x, long y);
T* operator +(T* x, ulong y);

T* operator +(int x, T* y);
T* operator +(uint x, T* y);
T* operator +(long x, T* y);
T* operator +(ulong x, T* y);

T* operator -(T* x, int y);
T* operator -(T* x, uint y);
T* operator -(T* x, long y);
T* operator -(T* x, ulong y);

long operator -(T* x, T* y);
```

Data un'espressione `P` di un tipo di puntatore `T*` e un'espressione `N` di tipo `int` , `uint` , `long` o `ulong` , le espressioni `P + N` e `N + P` calcolano il valore del puntatore di tipo `T*` risultante dall'aggiunta `N * sizeof(T)` all'indirizzo fornito da `P` . Analogamente, l'espressione `P - N` Calcola il valore del puntatore di tipo `T*` risultante dalla sottrazione `N * sizeof(T)` dall'indirizzo fornito da `P` .

Date due espressioni, `P` e `Q` , di un tipo di puntatore `T*` , l'espressione `P - Q` Calcola la differenza tra gli indirizzi specificati da `P` e `Q` e quindi divide la differenza in base a `sizeof(T)` . Il tipo del risultato è sempre `long` . In effetti, `P - Q` viene calcolata come `((long)(P) - (long)(Q)) / sizeof(T)` .

Ad esempio:

```csharp
using System;

class Test
{
    static void Main() {
        unsafe {
            int* values = stackalloc int[20];
            int* p = &values[1];
            int* q = &values[15];
            Console.WriteLine("p - q = {0}", p - q);
            Console.WriteLine("q - p = {0}", q - p);
        }
    }
}
```

che produce l'output:

```console
p - q = -14
q - p = 14
```

Se un'operazione aritmetica del puntatore causa un overflow del dominio del tipo di puntatore, il risultato viene troncato in modo definito dall'implementazione, ma non vengono generate eccezioni.

### <a name="pointer-comparison"></a>Confronto tra puntatori

In un contesto non sicuro `==` `!=` `<` `>` è possibile applicare gli operatori,,,, `<=` e `=>` ([operatori relazionali e di test dei tipi](expressions.md#relational-and-type-testing-operators)) a valori di tutti i tipi di puntatore. Gli operatori di confronto del puntatore sono:

```csharp
bool operator ==(void* x, void* y);
bool operator !=(void* x, void* y);
bool operator <(void* x, void* y);
bool operator >(void* x, void* y);
bool operator <=(void* x, void* y);
bool operator >=(void* x, void* y);
```

Poiché esiste una conversione implicita da qualsiasi tipo di puntatore al `void*` tipo, gli operandi di qualsiasi tipo di puntatore possono essere confrontati usando questi operatori. Gli operatori di confronto confrontano gli indirizzi forniti dai due operandi come se fossero interi senza segno.

### <a name="the-sizeof-operator"></a>Operatore sizeof

L'operatore `sizeof` restituisce il numero di byte occupati da una variabile di un tipo specificato. Il tipo specificato come operando `sizeof` deve essere un *unmanaged_type* ([tipi puntatore](unsafe-code.md#pointer-types)).

```antlr
sizeof_expression
    : 'sizeof' '(' unmanaged_type ')'
    ;
```

Il risultato dell' `sizeof` operatore è un valore di tipo `int` . Per determinati tipi predefiniti, l' `sizeof` operatore restituisce un valore costante, come illustrato nella tabella seguente.


| __Espressione__   | __Risultato__ |
|------------------|------------|
| `sizeof(sbyte)`  | `1`        |
| `sizeof(byte)`   | `1`        |
| `sizeof(short)`  | `2`        |
| `sizeof(ushort)` | `2`        |
| `sizeof(int)`    | `4`        |
| `sizeof(uint)`   | `4`        |
| `sizeof(long)`   | `8`        |
| `sizeof(ulong)`  | `8`        |
| `sizeof(char)`   | `2`        |
| `sizeof(float)`  | `4`        |
| `sizeof(double)` | `8`        |
| `sizeof(bool)`   | `1`        |

Per tutti gli altri tipi, il risultato dell' `sizeof` operatore è definito dall'implementazione ed è classificato come valore, non come costante.

L'ordine in cui i membri vengono compressi in uno struct non è specificato.

Ai fini dell'allineamento, potrebbe essere presente una spaziatura senza nome all'inizio di uno struct, all'interno di uno struct e alla fine dello struct. Il contenuto dei bit utilizzati come spaziatura interna è indeterminato.

Quando viene applicato a un operando con tipo struct, il risultato è il numero totale di byte in una variabile di quel tipo, inclusa la spaziatura interna.

## <a name="the-fixed-statement"></a>Istruzione fixed

In un contesto non sicuro, la produzione di *embedded_statement* ([istruzioni](statements.md)) consente un costrutto aggiuntivo, `fixed` ovvero l'istruzione, che viene usata per "correggere" una variabile mobile in modo che l'indirizzo rimanga costante per la durata dell'istruzione.

```antlr
fixed_statement
    : 'fixed' '(' pointer_type fixed_pointer_declarators ')' embedded_statement
    ;

fixed_pointer_declarators
    : fixed_pointer_declarator (','  fixed_pointer_declarator)*
    ;

fixed_pointer_declarator
    : identifier '=' fixed_pointer_initializer
    ;

fixed_pointer_initializer
    : '&' variable_reference
    | expression
    ;
```

Ogni *fixed_pointer_declarator* dichiara una variabile locale della *pointer_type* specificata e inizializza la variabile locale con l'indirizzo calcolato dalla *fixed_pointer_initializer*corrispondente. Una variabile locale dichiarata in un' `fixed` istruzione è accessibile in qualsiasi *fixed_pointer_initializer*s che si verifica a destra della dichiarazione della variabile e nella *embedded_statement* dell' `fixed` istruzione. Una variabile locale dichiarata da un' `fixed` istruzione è considerata di sola lettura. Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare questa variabile locale (tramite assegnazione o gli `++` `--` operatori e) o di passarla come `ref` `out` parametro o.

Un *fixed_pointer_initializer* può essere uno dei seguenti:

*  Il token " `&` " seguito da un *variable_reference* ([regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment)) a una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) di un tipo non gestito `T` , purché il tipo `T*` sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione. In questo caso, l'inizializzatore calcola l'indirizzo della variabile specificata e la variabile rimane in un indirizzo fisso per la durata dell' `fixed` istruzione.
*  Espressione di un *array_type* con gli elementi di un tipo non gestito `T` , purché il tipo `T*` sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione. In questo caso, l'inizializzatore calcola l'indirizzo del primo elemento nella matrice e l'intera matrice rimane in un indirizzo fisso per la durata dell' `fixed` istruzione. Se l'espressione di matrice è null o se la matrice ha zero elementi, l'inizializzatore calcola un indirizzo uguale a zero.
*  Espressione di tipo `string` , purché il tipo `char*` sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione. In questo caso, l'inizializzatore calcola l'indirizzo del primo carattere nella stringa e l'intera stringa rimane in un indirizzo fisso per la durata dell' `fixed` istruzione. Il comportamento dell' `fixed` istruzione è definito dall'implementazione se l'espressione stringa è null.
*  *Simple_name* o *member_access* che fa riferimento a un membro del buffer a dimensione fissa di una variabile mobile, purché il tipo del membro del buffer a dimensione fissa sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione. In questo caso, l'inizializzatore calcola un puntatore al primo elemento del buffer a dimensione fissa ([buffer a dimensione fissa nelle espressioni](unsafe-code.md#fixed-size-buffers-in-expressions)) e il buffer a dimensione fissa rimane sempre in corrispondenza di un indirizzo fisso per la durata dell' `fixed` istruzione.

Per ogni indirizzo calcolato da un *fixed_pointer_initializer* l' `fixed` istruzione garantisce che la variabile a cui fa riferimento l'indirizzo non sia soggetta alla rilocazione o all'eliminazione da parte del Garbage Collector per la durata dell' `fixed` istruzione. Se, ad esempio, l'indirizzo calcolato da un *fixed_pointer_initializer* fa riferimento a un campo di un oggetto o di un elemento di un'istanza di matrice, l' `fixed` istruzione garantisce che l'istanza dell'oggetto contenitore non venga rilocata o eliminata durante la durata dell'istruzione.

È responsabilità del programmatore garantire che i puntatori creati dalle `fixed` istruzioni non sopravvivano oltre l'esecuzione di tali istruzioni. Se, ad esempio, i puntatori creati dalle `fixed` istruzioni vengono passati alle API esterne, è responsabilità del programmatore garantire che le API non mantengano memoria di questi puntatori.

Gli oggetti fissi possono causare la frammentazione dell'heap, perché non possono essere spostati. Per questo motivo, gli oggetti devono essere corretti solo quando sono assolutamente necessari e quindi solo per la quantità di tempo più breve possibile.

L'esempio:

```csharp
class Test
{
    static int x;
    int y;

    unsafe static void F(int* p) {
        *p = 1;
    }

    static void Main() {
        Test t = new Test();
        int[] a = new int[10];
        unsafe {
            fixed (int* p = &x) F(p);
            fixed (int* p = &t.y) F(p);
            fixed (int* p = &a[0]) F(p);
            fixed (int* p = a) F(p);
        }
    }
}
```

vengono illustrati diversi utilizzi dell' `fixed` istruzione. La prima istruzione corregge e ottiene l'indirizzo di un campo statico, la seconda corregge e ottiene l'indirizzo di un campo di istanza e la terza istruzione corregge e ottiene l'indirizzo di un elemento di matrice. In ogni caso sarebbe stato un errore usare l' `&` operatore regolare poiché le variabili sono tutte classificate come variabili mobili.

La quarta `fixed` istruzione nell'esempio precedente produce un risultato simile al terzo.

Questo esempio di `fixed` istruzione USA `string` :

```csharp
class Test
{
    static string name = "xx";

    unsafe static void F(char* p) {
        for (int i = 0; p[i] != '\0'; ++i)
            Console.WriteLine(p[i]);
    }

    static void Main() {
        unsafe {
            fixed (char* p = name) F(p);
            fixed (char* p = "xx") F(p);
        }
    }
}
```

In una matrice di contesto unsafe gli elementi delle matrici unidimensionali vengono archiviati in ordine di indice crescente, iniziando con index `0` e terminando con index `Length - 1` . Per le matrici multidimensionali, gli elementi di matrice vengono archiviati in modo che gli indici della dimensione più a destra vengano aumentati per primi, quindi la dimensione a sinistra successiva e così via verso sinistra. All'interno di un' `fixed` istruzione che ottiene un puntatore `p` a un'istanza di matrice `a` , i valori del puntatore compresi tra `p` per `p + a.Length - 1` rappresentare gli indirizzi degli elementi nella matrice. Analogamente, le variabili che variano da `p[0]` per `p[a.Length - 1]` rappresentare gli elementi della matrice effettivi. Dato il modo in cui vengono archiviate le matrici, è possibile considerare una matrice di qualsiasi dimensione come se fosse lineare.

Ad esempio:

```csharp
using System;

class Test
{
    static void Main() {
        int[,,] a = new int[2,3,4];
        unsafe {
            fixed (int* p = a) {
                for (int i = 0; i < a.Length; ++i)    // treat as linear
                    p[i] = i;
            }
        }

        for (int i = 0; i < 2; ++i)
            for (int j = 0; j < 3; ++j) {
                for (int k = 0; k < 4; ++k)
                    Console.Write("[{0},{1},{2}] = {3,2} ", i, j, k, a[i,j,k]);
                Console.WriteLine();
            }
    }
}
```

che produce l'output:

```console
[0,0,0] =  0 [0,0,1] =  1 [0,0,2] =  2 [0,0,3] =  3
[0,1,0] =  4 [0,1,1] =  5 [0,1,2] =  6 [0,1,3] =  7
[0,2,0] =  8 [0,2,1] =  9 [0,2,2] = 10 [0,2,3] = 11
[1,0,0] = 12 [1,0,1] = 13 [1,0,2] = 14 [1,0,3] = 15
[1,1,0] = 16 [1,1,1] = 17 [1,1,2] = 18 [1,1,3] = 19
[1,2,0] = 20 [1,2,1] = 21 [1,2,2] = 22 [1,2,3] = 23
```

Nell'esempio

```csharp
class Test
{
    unsafe static void Fill(int* p, int count, int value) {
        for (; count != 0; count--) *p++ = value;
    }

    static void Main() {
        int[] a = new int[100];
        unsafe {
            fixed (int* p = a) Fill(p, 100, -1);
        }
    }
}
```

`fixed`viene utilizzata un'istruzione per correggere una matrice in modo che l'indirizzo possa essere passato a un metodo che accetta un puntatore.

Nell’esempio:

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    Font f;

    unsafe static void Main()
    {
        Test test = new Test();
        test.f.size = 10;
        fixed (char* p = test.f.name) {
            PutString("Times New Roman", p, 32);
        }
    }
}
```

viene utilizzata un'istruzione fixed per correggere un buffer a dimensione fissa di uno struct, in modo che l'indirizzo possa essere utilizzato come puntatore.

Un `char*` valore prodotto mediante la correzione di un'istanza di stringa fa sempre riferimento a una stringa con terminazione null. All'interno di un'istruzione fixed che ottiene un puntatore `p` a un'istanza di stringa `s` , i valori del puntatore che variano da `p` per `p + s.Length - 1` rappresentare gli indirizzi dei caratteri nella stringa e il valore del puntatore `p + s.Length` punta sempre a un carattere null (il carattere con valore `'\0'` ).

La modifica di oggetti di tipo gestito tramite puntatori fissi può comportare un comportamento non definito. Poiché, ad esempio, le stringhe non sono modificabili, è responsabilità del programmatore garantire che i caratteri a cui fa riferimento un puntatore a una stringa fissa non vengano modificati.

La terminazione null automatica delle stringhe è particolarmente utile quando si chiamano API esterne che prevedono stringhe di tipo "C". Si noti, tuttavia, che un'istanza di stringa può contenere caratteri null. Se sono presenti caratteri null, la stringa viene visualizzata troncata quando viene considerata come terminazione null `char*` .

## <a name="fixed-size-buffers"></a>Buffer a dimensione fissa

I buffer a dimensione fissa vengono usati per dichiarare le matrici inline "in stile C" come membri degli struct e sono particolarmente utili per l'interazione con le API non gestite.

### <a name="fixed-size-buffer-declarations"></a>Dichiarazioni di buffer a dimensione fissa

Un ***buffer a dimensione fissa*** è un membro che rappresenta lo spazio di archiviazione per un buffer a lunghezza fissa di variabili di un determinato tipo. Una dichiarazione di buffer a dimensione fissa introduce uno o più buffer a dimensione fissa di un determinato tipo di elemento. I buffer a dimensione fissa sono consentiti solo nelle dichiarazioni struct e possono verificarsi solo in contesti non sicuri ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).

```antlr
struct_member_declaration_unsafe
    : fixed_size_buffer_declaration
    ;

fixed_size_buffer_declaration
    : attributes? fixed_size_buffer_modifier* 'fixed' buffer_element_type fixed_size_buffer_declarator+ ';'
    ;

fixed_size_buffer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'unsafe'
    ;

buffer_element_type
    : type
    ;

fixed_size_buffer_declarator
    : identifier '[' constant_expression ']'
    ;
```

Una dichiarazione di buffer a dimensione fissa può includere un set di attributi ([attributi](attributes.md)), un `new` modificatore ([modificatori](classes.md#modifiers)), una combinazione valida dei quattro modificatori di accesso ([parametri di tipo e vincoli](classes.md#type-parameters-and-constraints)) e un `unsafe` modificatore ([contesti non sicuri](unsafe-code.md#unsafe-contexts)). Gli attributi e i modificatori si applicano a tutti i membri dichiarati dalla dichiarazione del buffer a dimensione fissa. È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di buffer a dimensione fissa.

Una dichiarazione di buffer a dimensione fissa non è consentita per includere il `static` modificatore.

Il tipo di elemento buffer di una dichiarazione di buffer a dimensione fissa specifica il tipo di elemento dei buffer introdotti dalla dichiarazione. Il tipo di elemento del buffer deve essere uno dei tipi predefiniti,,,, `sbyte` `byte` `short` `ushort` `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` o `bool` .

Il tipo di elemento del buffer è seguito da un elenco di dichiaratori di buffer a dimensione fissa, ciascuno dei quali introduce un nuovo membro. Un dichiaratore di buffer a dimensione fissa è costituito da un identificatore che assegna un nome al membro, seguito da un'espressione costante racchiusa tra `[` `]` token e. L'espressione costante indica il numero di elementi nel membro introdotto dal dichiaratore di buffer a dimensione fissa. Il tipo dell'espressione costante deve essere convertibile in modo implicito nel tipo `int` e il valore deve essere un numero intero positivo diverso da zero.

Gli elementi di un buffer a dimensione fissa sono sicuramente disposti in sequenza in memoria.

Una dichiarazione di buffer a dimensione fissa che dichiara più buffer a dimensione fissa equivale a più dichiarazioni di una singola dichiarazione di buffer a dimensione fissa con gli stessi attributi e tipi di elemento. Ad esempio:

```csharp
unsafe struct A
{
   public fixed int x[5], y[10], z[100];
}
```

equivale a

```csharp
unsafe struct A
{
   public fixed int x[5];
   public fixed int y[10];
   public fixed int z[100];
}
```

### <a name="fixed-size-buffers-in-expressions"></a>Buffer a dimensione fissa nelle espressioni

La ricerca di membri ([operatori](expressions.md#operators)) di un membro del buffer a dimensione fissa procede esattamente come la ricerca dei membri di un campo.

È possibile fare riferimento a un buffer a dimensione fissa in un'espressione utilizzando un *simple_name* ([inferenza del tipo](expressions.md#type-inference)) o una *member_access* ([controllo in fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Quando si fa riferimento a un membro del buffer a dimensione fissa come nome semplice, l'effetto è identico a quello di un accesso ai membri del form `this.I` , dove `I` è il membro del buffer a dimensione fissa.

In un accesso ai membri del form `E.I` , se `E` è di un tipo struct e una ricerca di membri di `I` in quel tipo di struct identifica un membro a dimensione fissa, `E.I` viene valutato un Classificato come segue:

*  Se l'espressione non `E.I` viene eseguita in un contesto non sicuro, si verifica un errore in fase di compilazione.
*  Se `E` è classificato come valore, si verifica un errore in fase di compilazione.
*  In caso contrario, se `E` è una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) e l'espressione `E.I` non è un *fixed_pointer_initializer* ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)), si verifica un errore in fase di compilazione.
*  In caso contrario, `E` fa riferimento a una variabile fissa e il risultato dell'espressione è un puntatore al primo elemento del membro del buffer `I` a dimensione fissa in `E` . Il risultato è di tipo `S*` , dove `S` è il tipo di elemento di `I` ed è classificato come valore.

È possibile accedere agli elementi successivi del buffer a dimensione fissa usando le operazioni del puntatore dal primo elemento. A differenza dell'accesso alle matrici, l'accesso agli elementi di un buffer a dimensione fissa è un'operazione non sicura e non è selezionato intervallo.

Nell'esempio seguente viene dichiarato e utilizzato uno struct con un membro del buffer a dimensione fissa.

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    unsafe static void Main()
    {
        Font f;
        f.size = 10;
        PutString("Times New Roman", f.name, 32);
    }
}
```

### <a name="definite-assignment-checking"></a>Controllo di assegnazione definito

I buffer a dimensione fissa non sono soggetti al controllo di assegnazione definito ([assegnazione definita](variables.md#definite-assignment)) e i membri del buffer a dimensione fissa vengono ignorati ai fini del controllo di assegnazione definito delle variabili di tipo struct.

Quando la variabile di struct contenitore più esterna di un membro del buffer a dimensione fissa è una variabile statica, una variabile di istanza di un'istanza di classe o un elemento di matrice, gli elementi del buffer a dimensione fissa vengono inizializzati automaticamente sui valori predefiniti ([valori predefiniti](variables.md#default-values)). In tutti gli altri casi, il contenuto iniziale di un buffer a dimensione fissa non è definito.

## <a name="stack-allocation"></a>Allocazione nello stack

In un contesto unsafe, una dichiarazione di variabile locale ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) può includere un inizializzatore di allocazione dello stack che alloca memoria dallo stack di chiamate.

```antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

Il *unmanaged_type* indica il tipo degli elementi che verranno archiviati nel percorso appena allocato e l' *espressione* indica il numero di questi elementi. Insieme, questi specificano le dimensioni di allocazione richieste. Poiché le dimensioni di un'allocazione dello stack non possono essere negative, si verifica un errore in fase di compilazione per specificare il numero di elementi come *constant_expression* che restituisce un valore negativo.

Un inizializzatore di allocazione dello stack del form `stackalloc T[E]` richiede `T` che sia un tipo non gestito ([tipi puntatore](unsafe-code.md#pointer-types)) e `E` che sia un'espressione di tipo `int` . Il costrutto alloca `E * sizeof(T)` byte dallo stack di chiamate e restituisce un puntatore di tipo `T*` al nuovo blocco allocato. Se `E` è un valore negativo, il comportamento non è definito. Se `E` è zero, non viene eseguita alcuna allocazione e il puntatore restituito è definito dall'implementazione. Se la memoria disponibile non è sufficiente per allocare un blocco della dimensione specificata, `System.StackOverflowException` viene generata un'eccezione.

Il contenuto della memoria appena allocata non è definito.

Gli inizializzatori di allocazione dello stack non sono consentiti nei `catch` `finally` blocchi o ([istruzione try](statements.md#the-try-statement)).

Non è possibile liberare esplicitamente la memoria allocata utilizzando `stackalloc` . Tutti i blocchi di memoria allocati dello stack creati durante l'esecuzione di un membro di funzione vengono eliminati automaticamente quando il membro della funzione restituisce. Corrisponde alla `alloca` funzione, un'estensione comunemente trovata nelle implementazioni C e C++.

Nell'esempio

```csharp
using System;

class Test
{
    static string IntToString(int value) {
        int n = value >= 0? value: -value;
        unsafe {
            char* buffer = stackalloc char[16];
            char* p = buffer + 16;
            do {
                *--p = (char)(n % 10 + '0');
                n /= 10;
            } while (n != 0);
            if (value < 0) *--p = '-';
            return new string(p, 0, (int)(buffer + 16 - p));
        }
    }

    static void Main() {
        Console.WriteLine(IntToString(12345));
        Console.WriteLine(IntToString(-999));
    }
}
```

un `stackalloc` inizializzatore viene usato nel `IntToString` metodo per allocare un buffer di 16 caratteri nello stack. Il buffer viene rimosso automaticamente quando il metodo restituisce un risultato.

## <a name="dynamic-memory-allocation"></a>Allocazione di memoria dinamica

Ad eccezione dell' `stackalloc` operatore, C# non fornisce costrutti predefiniti per la gestione della memoria non sottoposta a Garbage Collection. Tali servizi vengono in genere forniti mediante il supporto di librerie di classi o importati direttamente dal sistema operativo sottostante. Ad esempio, la `Memory` classe seguente illustra il modo in cui è possibile accedere alle funzioni dell'heap di un sistema operativo sottostante da C#:

```csharp
using System;
using System.Runtime.InteropServices;

public static unsafe class Memory
{
    // Handle for the process heap. This handle is used in all calls to the
    // HeapXXX APIs in the methods below.
    private static readonly IntPtr s_heap = GetProcessHeap();

    // Allocates a memory block of the given size. The allocated memory is
    // automatically initialized to zero.
    public static void* Alloc(int size)
    {
        void* result = HeapAlloc(s_heap, HEAP_ZERO_MEMORY, (UIntPtr)size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Copies count bytes from src to dst. The source and destination
    // blocks are permitted to overlap.
    public static void Copy(void* src, void* dst, int count)
    {
        byte* ps = (byte*)src;
        byte* pd = (byte*)dst;
        if (ps > pd)
        {
            for (; count != 0; count--) *pd++ = *ps++;
        }
        else if (ps < pd)
        {
            for (ps += count, pd += count; count != 0; count--) *--pd = *--ps;
        }
    }

    // Frees a memory block.
    public static void Free(void* block)
    {
        if (!HeapFree(s_heap, 0, block)) throw new InvalidOperationException();
    }

    // Re-allocates a memory block. If the reallocation request is for a
    // larger size, the additional region of memory is automatically
    // initialized to zero.
    public static void* ReAlloc(void* block, int size)
    {
        void* result = HeapReAlloc(s_heap, HEAP_ZERO_MEMORY, block, (UIntPtr)size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Returns the size of a memory block.
    public static int SizeOf(void* block)
    {
        int result = (int)HeapSize(s_heap, 0, block);
        if (result == -1) throw new InvalidOperationException();
        return result;
    }

    // Heap API flags
    private const int HEAP_ZERO_MEMORY = 0x00000008;

    // Heap API functions
    [DllImport("kernel32")]
    private static extern IntPtr GetProcessHeap();

    [DllImport("kernel32")]
    private static extern void* HeapAlloc(IntPtr hHeap, int flags, UIntPtr size);

    [DllImport("kernel32")]
    private static extern bool HeapFree(IntPtr hHeap, int flags, void* block);

    [DllImport("kernel32")]
    private static extern void* HeapReAlloc(IntPtr hHeap, int flags, void* block, UIntPtr size);

    [DllImport("kernel32")]
    private static extern UIntPtr HeapSize(IntPtr hHeap, int flags, void* block);
}
```

Di seguito è riportato un esempio che usa la `Memory` classe:

```csharp
class Test
{
    static unsafe void Main()
    {
        byte* buffer = null;
        try
        {
            const int Size = 256;
            buffer = (byte*)Memory.Alloc(Size);
            for (int i = 0; i < Size; i++) buffer[i] = (byte)i;
            byte[] array = new byte[Size];
            fixed (byte* p = array) Memory.Copy(buffer, p, Size);
            for (int i = 0; i < Size; i++) Console.WriteLine(array[i]);
        }
        finally
        {
            if (buffer != null) Memory.Free(buffer);
        }
    }
}
```

Nell'esempio vengono allocati 256 byte di memoria tramite `Memory.Alloc` e viene inizializzato il blocco di memoria con valori che aumentano da 0 a 255. Viene quindi allocata una matrice di byte di elementi 256 e viene utilizzato `Memory.Copy` per copiare il contenuto del blocco di memoria nella matrice di byte. Infine, il blocco di memoria viene liberato usando `Memory.Free` e il contenuto della matrice di byte viene restituito nella console.
