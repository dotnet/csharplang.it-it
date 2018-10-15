# <a name="unsafe-code"></a>Codice di tipo unsafe

Il linguaggio c#, come definito nei precedenti capitoli, differisce notevolmente dai linguaggi C e C++ nell'omissione dei puntatori come tipo di dati. Al contrario, c# fornisce riferimenti e la possibilità di creare gli oggetti gestiti da un garbage collector. Rende questa struttura, insieme ad altre funzionalità, in c# un linguaggio molto più sicuro rispetto a C o C++. Nel linguaggio core c# semplicemente non è possibile avere una variabile non inizializzata, un puntatore "inesatti" o un'espressione che indicizza una matrice oltre i limiti. Categorie di intere di bug riscontrati che in continuazione nei C e i programmi C++ in tal modo vengono eliminati.

Sebbene praticamente tutti i costrutti tipo puntatore in C o C++ ha un equivalente di tipo riferimento in c#, nonostante ciò, esistono situazioni in cui l'accesso ai tipi di puntatore diventa una necessità. Ad esempio, l'interazione con il sistema operativo sottostante, l'accesso a un dispositivo con mapping in memoria o implementare un algoritmo potrebbe non essere possibile o fattibile senza accesso a puntatori. Per soddisfare questa esigenza, c# offre la possibilità di scrivere ***codice unsafe***.

Nel codice di tipo unsafe è possibile dichiarare e utilizzare con i puntatori, eseguire le conversioni tra puntatori e i tipi integrali, per accettare l'indirizzo di variabili, e così via. In un certo senso, la scrittura di codice unsafe è molto simile a scrivere codice C all'interno di un programma c#.

Codice unsafe in realtà è una funzionalità "sicura" dal punto di vista di sviluppatori e utenti. Codice unsafe debba essere chiaramente contrassegnato con il modificatore `unsafe`, in modo che gli sviluppatori usare accidentalmente le funzionalità non sicure e il motore di esecuzione si impegna per assicurare che il codice unsafe non può essere eseguito in un ambiente non attendibile.

## <a name="unsafe-contexts"></a>Contesti non sicuri

Le funzionalità non sicure del linguaggio c# sono disponibili solo in contesti non sicuri. È stato introdotto un contesto unsafe, includendo un' `unsafe` modificatore nella dichiarazione di un tipo o membro, o tramite l'uso di un *unsafe_statement*:

*  Una dichiarazione di una classe, struct, interfaccia o delegato può includere un `unsafe` modificatore, in cui il l'intera estensione testuale di tale dichiarazione del tipo (incluso il corpo della classe, struct o interfaccia) viene considerato un contesto non sicuro.
*  Una dichiarazione di un campo, metodo, proprietà, eventi, l'indicizzatore, operatore, costruttore di istanza, distruttore o costruttore statico potrebbe includere un `unsafe` modificatore, in cui il l'intera estensione testuale che della dichiarazione del membro viene considerato un tipo unsafe contesto.
*  Un' *unsafe_statement* consente l'uso di un contesto unsafe all'interno di un *blocco*. L'intera estensione testuale dell'oggetto associato *blocco* viene considerato un contesto non sicuro.

Productions la grammatica associati vengono visualizzati sotto.

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

il `unsafe` modificatore specificato nella dichiarazione di struct fa sì che l'intera estensione testuale della dichiarazione di struct per diventare un contesto non sicuro. Di conseguenza, è possibile dichiarare la `Left` e `Right` campi di un tipo di puntatore. Nell'esempio precedente può anche essere scritta

```csharp
public struct Node
{
    public int Value;
    public unsafe Node* Left;
    public unsafe Node* Right;
}
```

In questo caso, il `unsafe` tali dichiarazioni essere considerato contesti non sicuri a causa dei modificatori nelle dichiarazioni di campo.

Oltre a stabilire un contesto unsafe, permettendo così l'utilizzo di tipi di puntatore, il `unsafe` modificatore non ha alcun effetto su un tipo o membro. Nell'esempio

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

il `unsafe` modificatore il `F` metodo nella `A` semplicemente fa sì che l'estensione testuale del `F` per diventare un contesto unsafe in cui è possono utilizzare le funzionalità del linguaggio non sicure. Nell'override di `F` nel `B`, non è necessario specificare di nuovo il `unsafe` modificatore, a meno che non, naturalmente, il `F` metodo nella `B` stesso deve avere accesso alle funzionalità di tipo unsafe.

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

In questo caso, poiché `F`della firma include un tipo di puntatore, può essere scritta solo in un contesto unsafe. Tuttavia, il contesto non sicuro può essere introdotta apportando entrambi l'intera classe poco sicuro, come nel caso in `A`, o includendo un' `unsafe` modificatore nella dichiarazione del metodo, come nel caso in `B`.

## <a name="pointer-types"></a>Tipi puntatore

In un contesto unsafe, un *tipo* ([tipi](types.md)) può essere una *pointer_type* nonché un *value_type* o una *reference_type* . Tuttavia, un *pointer_type* può essere usato anche un `typeof` espressione ([espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) di fuori di un contesto non sicuro in quanto tale utilizzo non unsafe.

```antlr
type_unsafe
    : pointer_type
    ;
```

Oggetto *pointer_type* viene scritto come un' *unmanaged_type* o la parola chiave `void`, seguito da un `*` token:

```antlr
pointer_type
    : unmanaged_type '*'
    | 'void' '*'
    ;

unmanaged_type
    : type
    ;
```

Il tipo specificato prima la `*` un puntatore di tipo viene chiamato il ***tipo referente*** del tipo puntatore. Rappresenta il tipo della variabile a cui fa riferimento un valore del tipo di puntatore.

A differenza dei riferimenti (valori dei tipi di riferimento), i puntatori non vengono rilevati dal garbage collector, ovvero il garbage collector non ha una conoscenza di puntatori e i dati a cui puntano. Per questo motivo un puntatore non è consentito in modo che punti a un riferimento o a una struttura che contiene i riferimenti e deve essere di tipo referente di un puntatore a un *unmanaged_type*.

Un' *unmanaged_type* è qualsiasi tipo che non è un *reference_type* o tipo costruito e non contiene *reference_type* o costruito i campi di tipo a qualsiasi livello di annidamento. In altre parole, un' *unmanaged_type* è uno dei seguenti:

*  `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, o `bool`.
*  Eventuali *enum_type*.
*  Eventuali *pointer_type*.
*  Qualsiasi definiti dall'utente *struct_type* che non è un tipo costruito e contiene i campi del *unmanaged_type*solo.

La regola intuitiva per la combinazione di puntatori e riferimenti è che sono consentiti referenti dei riferimenti (oggetti) che contiene i puntatori, ma non possono contenere riferimenti referenti di puntatori.

Nella tabella seguente sono indicati alcuni esempi di tipi di puntatore:

| __Esempio__ | __Descrizione__                               |
|-------------|-----------------------------------------------|
| `byte*`     | Puntatore a `byte`                             |
| `char*`     | Puntatore a `char`                             |
| `int**`     | Puntatore a puntatore a `int`                   |
| `int*[]`    | Matrice unidimensionale di puntatori a `int` |
| `void*`     | Puntatore a tipo sconosciuto                       |

Per una determinata implementazione, tutti i tipi di puntatore devono avere le stesse dimensioni e rappresentazione.

Diversamente da C e C++, quando vengono dichiarati più puntatori nella stessa dichiarazione, in c# il `*` viene scritto insieme al tipo sottostante, non come un segno di punteggiatura di prefisso su ciascun nome di puntatore. Esempio:

```csharp
int* pi, pj;    // NOT as int *pi, *pj;
```

Il valore di un puntatore di tipo `T*` rappresenta l'indirizzo di una variabile di tipo `T`. L'operatore di riferimento indiretto puntatore `*` ([riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection)) può essere utilizzato per accedere a questa variabile. Si consideri ad esempio una variabile `P` typu `int*`, l'espressione `*P` denota il `int` variabile trovata in corrispondenza dell'indirizzo contenuto in `P`.

Ad esempio un riferimento all'oggetto, potrebbe essere un puntatore `null`. Si applica l'operatore di riferimento indiretto a un `null` puntatore produce un comportamento definito dall'implementazione. Un puntatore con valore `null` è rappresentato da tutti i bit a zero.

Il `void*` tipo rappresenta un puntatore a un tipo sconosciuto. Poiché il tipo referente è sconosciuto, l'operatore di riferimento indiretto non può essere applicato a un puntatore di tipo `void*`, né può essere eseguite le operazioni aritmetiche su tale puntatore. Tuttavia, un puntatore di tipo `void*` può eseguire il cast a qualsiasi altro tipo di puntatore (e viceversa).

Tipi di puntatori sono una categoria di tipi separata. A differenza dei tipi di riferimento e tipi di valore, tipi di puntatore non ereditano da `object` e non esiste alcuna conversione tra tipi di puntatore e `object`. In particolare, la conversione boxing e unboxing ([conversioni Boxing e unboxing](types.md#boxing-and-unboxing)) non sono supportati per i puntatori. Tuttavia, sono consentite le conversioni tra tipi di puntatore diversi e tra i tipi di puntatore e tipi integrali. Come descritto nella [conversioni di puntatori](unsafe-code.md#pointer-conversions).

Oggetto *pointer_type* non può essere usato come argomento di tipo ([tipi costruiti](types.md#constructed-types)) e l'inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) ha esito negativo nelle chiamate di metodo generico sono inferire un tipo di argomento sia un tipo di puntatore.

Oggetto *pointer_type* può essere usato come tipo di un campo volatile ([campi volatili](classes.md#volatile-fields)).

Anche se i puntatori possono essere passati come `ref` o `out` parametri, tale operazione può causare un comportamento non definito, poiché il puntatore può anche essere impostati in modo che punti a una variabile locale che non esiste più quando il metodo chiamato termina oppure l'oggetto predefinito a cui si usato per puntare, non è stato risolto. Ad esempio:

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

Un metodo può restituire un valore di tipo e tale tipo può essere un puntatore. Ad esempio, quando si riceve un puntatore a una sequenza di contigui `int`s, numero di elementi della sequenza e un altro `int` valore, il metodo seguente restituisce l'indirizzo di tale valore in tale sequenza, se esiste una corrispondenza; in caso contrario, restituisce `null`:

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

In un contesto unsafe, sono disponibili per l'uso di puntatori di diversi costrutti:

*  Il `*` operatore può essere utilizzato per eseguire riferimento indiretto al puntatore ([riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection)).
*  Il `->` operatore può essere utilizzato per accedere a un membro di uno struct tramite un puntatore ([accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)).
*  Il `[]` operatore può essere utilizzato per indicizzare un puntatore ([accesso all'elemento puntatore](unsafe-code.md#pointer-element-access)).
*  Il `&` operatore può essere utilizzato per ottenere l'indirizzo di una variabile ([l'operatore address-of](unsafe-code.md#the-address-of-operator)).
*  Il `++` e `--` operatori possono essere usato per i puntatori di incremento e decremento ([puntatore incremento e decremento](unsafe-code.md#pointer-increment-and-decrement)).
*  Il `+` e `-` operatori possono essere utilizzati per eseguire l'aritmetica dei puntatori ([aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic)).
*  Il `==`, `!=`, `<`, `>`, `<=`, e `=>` operatori possono essere utilizzati per confrontare i puntatori ([confronto tra puntatori](unsafe-code.md#pointer-comparison)).
*  Il `stackalloc` operatore può essere utilizzato per allocare memoria dallo stack di chiamate ([buffer a dimensione fissa](unsafe-code.md#fixed-size-buffers)).
*  Il `fixed` istruzione può essere utilizzata per risolvere temporaneamente una variabile in modo che il relativo indirizzo può essere ottenuto ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)).

## <a name="fixed-and-moveable-variables"></a>Variabili fisse e mobili

L'operatore address-of ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) e il `fixed` istruzione ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) consentono di dividere le variabili in due categorie: ***fissa variabili***e ***spostabile variabili***.

Variabili fisse si trovano in posizioni di archiviazione che non sono interessati dall'operazione di garbage collector. (Esempi di variabili fisse includono le variabili locali e parametri con valori di variabili create da dereferenziazione dei puntatori). D'altra parte, le variabili mobili si trovano in posizioni di archiviazione che sono soggetti a spostamenti o eliminazione tramite garbage collector. (Esempi di variabili mobili includono campi negli elementi di matrici e oggetti).

Il `&` operatore ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) consente l'indirizzo di una variabile fissa devono essere ottenuti senza restrizioni. Tuttavia, poiché una variabile mobile è soggetti a spostamenti o eliminazione tramite garbage collector, l'indirizzo di una variabile mobile può essere ottenuta solo tramite un `fixed` istruzione ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) e tale indirizzo rimane valida solo per la durata di tale `fixed` istruzione.

Più precisamente, una variabile fissa è uno dei seguenti:

*  Una variabile risultante da un *simple_name* ([nomi semplici](expressions.md#simple-names)) che fa riferimento a una variabile locale o un parametro di valore, a meno che la variabile viene acquisita da una funzione anonima.
*  Una variabile risultante da un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `V.I`, dove `V` variabile fisse di un *struct_type*.
*  Una variabile risultante da un *pointer_indirection_expression* ([riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection)) nel formato `*P`, un *pointer_member_access* ([Accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)) nel formato `P->I`, o un *pointer_element_access* ([accesso all'elemento puntatore](unsafe-code.md#pointer-element-access)) nel formato `P[E]`.

Tutte le altre variabili sono classificate come spostabile.

Si noti che un campo statico viene classificato come una variabile mobile. Si noti anche che un `ref` o `out` parametro viene classificato come una variabile mobile, anche se l'argomento specificato per il parametro è una variabile fissa. Infine, si noti che una variabile prodotta da dereferenzia un puntatore sia sempre classificata come una variabile fissa.

## <a name="pointer-conversions"></a>Conversioni puntatore

In un contesto unsafe, il set di conversioni implicite disponibili ([conversioni implicite](conversions.md#implicit-conversions)) è stato esteso per includere le seguenti conversioni di puntatori implicite:

*  Da qualsiasi *pointer_type* al tipo `void*`.
*  Dal `null` qualsiasi valore letterale *pointer_type*.

Inoltre, in un contesto unsafe, il set di conversioni esplicite ([conversioni esplicite](conversions.md#explicit-conversions)) è stato esteso per includere le seguenti conversioni di puntatori esplicite:

*  Da qualsiasi *pointer_type* a qualsiasi altra *pointer_type*.
*  Dal `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, o `ulong` a qualsiasi *pointer_type*.
*  Da qualsiasi *pointer_type* al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, o `ulong`.

Infine, in un contesto unsafe, il set di conversioni implicite ([conversioni implicite](conversions.md#standard-implicit-conversions)) include le seguenti conversioni puntatore:

*  Da qualsiasi *pointer_type* al tipo `void*`.

Le conversioni tra due tipi di puntatore non modificano mai il valore del puntatore effettivo. In altre parole, una conversione da un tipo puntatore a un altro non ha effetto sull'indirizzo specificato dal puntatore sottostante.

Quando un tipo puntatore viene convertito in un altro, se il puntatore risultante non è correttamente allineato per il tipo di riferimento, il comportamento è definito se il risultato è dereferenziato. In generale, il concetto di "correttamente allineato" è transitivo: se un puntatore al tipo `A` viene correttamente allineato per un puntatore al tipo `B`che, a sua volta, viene correttamente allineato per un puntatore al tipo `C`, quindi un puntatore a tipo `A`è correttamente allineato per un puntatore al tipo `C`.

Si consideri il caso seguente in cui una variabile con un tipo è accessibile tramite un puntatore a un tipo diverso:

```csharp
char c = 'A';
char* pc = &c;
void* pv = pc;
int* pi = (int*)pv;
int i = *pi;         // undefined
*pi = 123456;        // undefined
```

Quando un tipo puntatore viene convertito in un puntatore al byte, i punti di risultato per il byte più basso indirizzato della variabile. Incrementi successivi del risultato, fino alla dimensione della variabile, restituiscono puntatori ai byte rimanenti di tale variabile. Ad esempio, il metodo seguente consente di visualizzare ognuno degli otto byte in un valore double come valore esadecimale:

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

Naturalmente, l'output del programma dipende dall'ordine dei byte.

Mapping tra i puntatori e i numeri interi vengono definiti dall'implementazione. Tuttavia, in 32 * e architetture della CPU a 64 bit con uno spazio degli indirizzi lineare, le conversioni dei puntatori verso o dai tipi integrali si comportano in genere esattamente come le conversioni delle `uint` o `ulong` valori, rispettivamente, da o verso tali tipi integrali.

### <a name="pointer-arrays"></a>Matrici di puntatori

In un contesto unsafe, è possibile costruire le matrici di puntatori. Solo alcune delle conversioni che si applicano ad altri tipi di matrice sono consentite nelle matrici di puntatore:

*  La conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) da qualsiasi *array_type* a `System.Array` e le interfacce implementate anche si applica alle matrici di puntatori. Tuttavia, qualsiasi tentativo di accedere a elementi della matrice tramite `System.Array` o le interfacce implementate comporterà un'eccezione in fase di esecuzione, come i tipi di puntatore non sono convertibili in `object`.
*  Conversioni di riferimenti di implicita ed esplicita ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions), [le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) da un tipo di matrice unidimensionale `S[]` a `System.Collections.Generic.IList<T>` e le interfacce di base generiche senza mai applicano alle matrici di puntatori, poiché i tipi di puntatore non possono essere utilizzati come argomenti tipo e non sono presenti conversioni da tipi di puntatori a tipi non puntatore.
*  La conversione di riferimenti esplicita ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) da `System.Array` e le interfacce implementate in qualsiasi *array_type* si applica alle matrici di puntatori.
*  Le conversioni di riferimento esplicito ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) da `System.Collections.Generic.IList<S>` e interfacce di base a un tipo di matrice unidimensionale `T[]` mai si applica alle matrici di puntatori, poiché i tipi di puntatore non possono essere usati come argomenti tipo e non sono presenti conversioni da tipi di puntatori a tipi non puntatore.

Queste restrizioni significa che l'espansione per il `foreach` istruzione sulle matrici come descritto in [l'istruzione foreach](statements.md#the-foreach-statement) non può essere applicato alle matrici di puntatori. Al contrario, un'istruzione foreach del form

```csharp
foreach (V v in x) embedded_statement
```

in cui il tipo della `x` è un tipo di matrice nel formato `T[,,...,]`, `N` è il numero di dimensioni meno 1 e `T` o `V` è un tipo di puntatore, viene espanso con cicli for annidati, come indicato di seguito:

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

Le variabili `a`, `i0`, `i1`,..., `iN` non sono visibile e accessibile al `x` o il *embedded_statement* o qualsiasi altro codice sorgente del programma. La variabile `v` è di sola lettura in un'istruzione incorporata. Se non esiste una conversione esplicita ([conversioni di puntatori](unsafe-code.md#pointer-conversions)) da `T` (tipo di elemento) per `V`, viene generato un errore e viene eseguita alcuna operazione ulteriormente. Se `x` ha il valore `null`, un `System.NullReferenceException` viene generata un'eccezione in fase di esecuzione.

## <a name="pointers-in-expressions"></a>Puntatori nelle espressioni

In un contesto unsafe, un'espressione può restituire un risultato di un tipo di puntatore, ma all'esterno di un contesto non sicuro è un errore in fase di compilazione per un'espressione di tipo puntatore. Più precisamente, all'esterno di un contesto non sicuro in fase di compilazione si verifica un errore se eventuali *simple_name* ([nomi semplici](expressions.md#simple-names)), *member_access* ([accesso ai membri ](expressions.md#member-access)), *invocation_expression* ([espressioni di chiamata](expressions.md#invocation-expressions)), oppure *element_access* ([accesso all'elemento](expressions.md#element-access)) è di tipo puntatore.

In un contesto unsafe, il *primary_no_array_creation_expression* ([espressioni primarie](expressions.md#primary-expressions)) e *unary_expression* ([operatori unari](expressions.md#unary-operators)) Productions consentono i seguenti costrutti aggiuntivi:

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

Questi costrutti sono descritte nelle sezioni seguenti. La precedenza e associatività degli operatori di tipo unsafe è implicita la grammatica.

### <a name="pointer-indirection"></a>Riferimento indiretto a puntatore

Oggetto *pointer_indirection_expression* costituito da un asterisco (`*`) seguito da una *unary_expression*.

```antlr
pointer_indirection_expression
    : '*' unary_expression
    ;
```

Operatore unario `*` operatore denota riferimento indiretto al puntatore e viene usato per ottenere la variabile a cui punta il puntatore. Il risultato della valutazione `*P`, dove `P` è un'espressione di tipo puntatore `T*`, è una variabile di tipo `T`. Tratta di un errore in fase di compilazione per applicare l'operatore unario `*` operatore da un'espressione di tipo `void*` o su un'espressione che non è di tipo puntatore.

L'effetto dell'applicazione con operatore unario `*` operatore da un `null` puntatore è definito dall'implementazione. In particolare, non c'è garanzia che questa operazione genera un `System.NullReferenceException`.

Se è stato assegnato un valore non valido per il puntatore, il comportamento dell'operatore unario `*` operatore non è definito. Tra i valori non validi per dereferenzia un puntatore da operatore unario `*` operatore sono un indirizzo allineato in modo non appropriato per il tipo a cui (vedere l'esempio nella [conversioni di puntatori](unsafe-code.md#pointer-conversions)) e l'indirizzo di una variabile dopo il fine della sua durata.

Ai fini dell'assegnazione definita analisi, una variabile prodotti dalla valutazione di un'espressione nel formato `*P` viene considerato come inizialmente assegnato ([inizialmente assegnata variabili](variables.md#initially-assigned-variables)).

### <a name="pointer-member-access"></a>Accesso ai membri del puntatore

Oggetto *pointer_member_access* è costituito da un *primary_expression*, seguito da un "`->`" token, seguito da un *identificatore* e un facoltativo*type_argument_list*.

```antlr
pointer_member_access
    : primary_expression '->' identifier
    ;
```

In un accesso ai membri del puntatore del form `P->I`, `P` deve essere diverso da un'espressione di tipo puntatore `void*`, e `I` deve indicare un membro accessibile del tipo a cui `P` punti.

Un accesso ai membri del puntatore del form `P->I` viene valutata esattamente come `(*P).I`. Per una descrizione dell'operatore di riferimento indiretto del puntatore (`*`), vedere [riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection). Per una descrizione dell'operatore di accesso membri (`.`), vedere [l'accesso ai membri](expressions.md#member-access).

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

il `->` operatore viene usato per accedere ai campi e richiamare un metodo di uno struct tramite un puntatore. Poiché l'operazione `P->I` equivale esattamente alla `(*P).I`, il `Main` possibile scrivere metodo altrettanto bene:

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

### <a name="pointer-element-access"></a>Accesso agli elementi di puntatore

Oggetto *pointer_element_access* costituito da un *primary_no_array_creation_expression* seguita da un'espressione racchiusa tra "`[`"e"`]`".

```antlr
pointer_element_access
    : primary_no_array_creation_expression '[' expression ']'
    ;
```

Nell'accesso all'elemento puntatore nel formato `P[E]`, `P` deve essere diverso da un'espressione di tipo puntatore `void*`, e `E` deve essere un'espressione che può essere convertita in modo implicito in `int`, `uint`, `long`, o `ulong`.

Accesso all'elemento un puntatore nel formato `P[E]` viene valutata esattamente come `*(P + E)`. Per una descrizione dell'operatore di riferimento indiretto del puntatore (`*`), vedere [riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection). Per una descrizione dell'operatore di addizione puntatore (`+`), vedere [aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic).

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

accesso all'elemento un puntatore utilizzato per inizializzare il buffer di caratteri in un `for` ciclo. Poiché l'operazione `P[E]` equivale esattamente alla `*(P + E)`, nell'esempio è stato altrettanto bene sono stati scritti:

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

L'operatore di accesso di elemento di puntatore non controlla oltre i limiti per gli errori e il comportamento quando si accede a un non compresi nell'intervallo elemento è definito. Questo è lo stesso come C e C++.

### <a name="the-address-of-operator"></a>L'operatore address-of

Un' *addressof_expression* costituito da una e commerciale (`&`) seguito da una *unary_expression*.

```antlr
addressof_expression
    : '&' unary_expression
    ;
```

Data un'espressione `E` che è di tipo `T` ed è classificato come una variabile fissa ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)), il costrutto `&E` calcola l'indirizzo della variabile specificata dalla `E`. Il tipo del risultato è `T*` ed è classificato come un valore. Si verifica un errore in fase di compilazione se `E` non è classificato come una variabile, se `E` viene classificato come una variabile locale di sola lettura, o se `E` denota una variabile mobile. Nell'ultimo caso, un'istruzione fixed ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) può essere utilizzato per temporaneamente "risolvere" la variabile prima di ottenerne l'indirizzo. Come indicato nella [accesso al membro](expressions.md#member-access), all'esterno di un costruttore di istanza o un costruttore statico per uno struct o una classe che definisce un `readonly` campo, tale campo viene considerato come un valore, non una variabile. Di conseguenza, non è possibile accettarne l'indirizzo. Analogamente, l'indirizzo di una costante non può essere accettato.

Il `&` operatore non richiede il relativo argomento sia assegnata, ma segue un `&` operazione, la variabile a cui viene applicato l'operatore viene considerata assegnata nel percorso di esecuzione in cui l'operazione viene eseguita. È responsabilità del programmatore assicurare che l'inizializzazione corretta della variabile vengono effettivamente eseguite in questa situazione.

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

`i` viene considerata assegnata seguendo la `&i` operazione consente di inizializzare `p`. L'assegnazione al `*p` Inizializza in vigore `i`, ma l'inclusione di questa inizializzazione è responsabilità del programmatore e si verificherebbe alcun errore in fase di compilazione se è stata rimossa l'assegnazione.

Le regole di assegnazione definite per il `&` operatore esiste in modo tale che può essere evitato ridondante inizializzazione delle variabili locali. Ad esempio, molte API esterne accettano un puntatore a una struttura di cui viene compilato tramite l'API. Le chiamate a tali API in genere passano l'indirizzo di una variabile locale di struct e senza la regola, l'inizializzazione con ridondanza della variabile di uno struct sarebbero necessario.

### <a name="pointer-increment-and-decrement"></a>Decremento e incremento di puntatore

In un contesto unsafe, il `++` e `--` operatori ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators) e [incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)) possono essere applicati a puntatore le variabili di tutti i tipi eccetto `void*`. Pertanto, per ogni tipo di puntatore `T*`, gli operatori seguenti vengono definiti in modo implicito:

```csharp
T* operator ++(T* x);
T* operator --(T* x);
```

Gli operatori generano gli stessi risultati `x + 1` e `x - 1`, rispettivamente ([aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic)). In altre parole, per una variabile puntatore di tipo `T*`, il `++` operatore aggiunge `sizeof(T)` all'indirizzo contenuto nella variabile e il `--` operatore sottrae `sizeof(T)` dall'indirizzo contenuto nella variabile.

Se un incremento o decremento operazione supera il dominio del tipo di puntatore, il risultato è definito dall'implementazione, ma non verranno generate eccezioni.

### <a name="pointer-arithmetic"></a>Puntatore aritmetico

In un contesto unsafe, il `+` e `-` operatori ([operatore di addizione](expressions.md#addition-operator) e [operatore di sottrazione](expressions.md#subtraction-operator)) possono essere applicati ai valori di tutti i tipi di puntatore, ad eccezione `void*`. Pertanto, per ogni tipo di puntatore `T*`, gli operatori seguenti vengono definiti in modo implicito:

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

Data un'espressione `P` di un tipo di puntatore `T*` e un'espressione `N` typu `int`, `uint`, `long`, o `ulong`, le espressioni `P + N` e `N + P` calcolo il valore del puntatore di tipo `T*` risultante dalla somma `N * sizeof(T)` all'indirizzo specificato da `P`. Analogamente, l'espressione `P - N` calcola il valore del puntatore di tipo `T*` risultante dalla sottrazione `N * sizeof(T)` rispetto all'indirizzo specificato da `P`.

Due espressioni, specificate `P` e `Q`, di un tipo di puntatore `T*`, l'espressione `P - Q` calcola la differenza tra gli indirizzi forniti dagli `P` e `Q` e quindi divide tale differenza per `sizeof(T)`. Il tipo del risultato è sempre `long`. In effetti `P - Q` viene calcolato come `((long)(P) - (long)(Q)) / sizeof(T)`.

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

l'output ottenuto sarà:

```
p - q = -14
q - p = 14
```

Se un'operazione aritmetica puntatore supera il dominio del tipo di puntatore, il risultato viene troncato in modo definito dall'implementazione, ma non verranno generate eccezioni.

### <a name="pointer-comparison"></a>Confronto tra puntatori

In un contesto unsafe, il `==`, `!=`, `<`, `>`, `<=`, e `=>` operatori ([relazionali e operatori di test del tipo](expressions.md#relational-and-type-testing-operators)) possono essere applicati ai valori di tutti tipi di puntatore. Gli operatori di confronto del puntatore sono:

```csharp
bool operator ==(void* x, void* y);
bool operator !=(void* x, void* y);
bool operator <(void* x, void* y);
bool operator >(void* x, void* y);
bool operator <=(void* x, void* y);
bool operator >=(void* x, void* y);
```

Poiché esiste una conversione implicita da qualsiasi tipo di puntatore per il `void*` tipo, gli operandi di tipo puntatore può essere confrontato utilizzando questi operatori. Gli operatori di confronto confrontano l'indirizzi specificati da due operandi come se fossero unsigned integer.

### <a name="the-sizeof-operator"></a>L'operatore sizeof

Il `sizeof` operatore restituisce il numero di byte occupati da una variabile di un determinato tipo. Il tipo specificato come un operando `sizeof` deve essere un *unmanaged_type* ([tipi di puntatore](unsafe-code.md#pointer-types)).

```antlr
sizeof_expression
    : 'sizeof' '(' unmanaged_type ')'
    ;
```

Il risultato del `sizeof` operatore è un valore di tipo `int`. Per determinati i tipi predefiniti, il `sizeof` operatore restituisce un valore costante, come illustrato nella tabella seguente.


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

Per tutti gli altri tipi, il risultato del `sizeof` l'operatore definito dall'implementazione ed è classificato come un valore, non una costante.

L'ordine in cui i membri vengono raggruppati in uno struct non è specificato.

Ai fini dell'allineamento, potrebbe esserci riempimento all'inizio di uno struct, all'interno di uno struct e alla fine dello struct senza nome. Il contenuto dei bit usato come spaziatura interna è indeterminato.

Quando applicato a un operando con il tipo di struct, il risultato è il numero totale di byte in una variabile di quel tipo, include eventuali spaziature interne.

## <a name="the-fixed-statement"></a>L'istruzione fixed

In un contesto unsafe, il *embedded_statement* ([istruzioni](statements.md)) produzione consente di creare un costrutto aggiuntivo, il `fixed` istruzione in cui viene utilizzata una variabile mobile "corretti" in modo che relativo indirizzo rimane costante per la durata dell'istruzione.

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

Ogni *fixed_pointer_declarator* dichiara una variabile locale di dato *pointer_type* e inizializza la variabile locale con l'indirizzo calcolata dal corrispondente *fixed_ pointer_initializer*. Una variabile locale dichiarata un `fixed` è accessibile in qualsiasi istruzione *fixed_pointer_initializer*s che si verificano a destra della dichiarazione della variabile nelle *embedded_statement* del `fixed` istruzione. Una variabile locale dichiarata da un `fixed` istruzione viene considerata di sola lettura. Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare questa variabile locale (tramite assegnazione o il `++` e `--` operators) o passarlo come una `ref` o `out` parametro.

Oggetto *fixed_pointer_initializer* può essere uno dei seguenti:

*  Il token "`&`" seguita da un *variable_reference* ([regole Precise per la determinazione di assegnazione certa](variables.md#precise-rules-for-determining-definite-assignment)) a una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) di un tipo non gestito `T`, purché il tipo `T*` implicita al tipo di puntatore fornito nel `fixed` istruzione. In questo caso, l'inizializzatore calcola l'indirizzo della variabile specificata e la variabile è garantita per rimanere in un indirizzo fisso per la durata del `fixed` istruzione.
*  Un'espressione di un' *array_type* con elementi di un tipo non gestito `T`, purché il tipo `T*` implicita al tipo di puntatore fornito nel `fixed` istruzione. In questo caso, l'inizializzatore calcola l'indirizzo del primo elemento nella matrice e l'intera matrice devono restare in un indirizzo fisso per la durata del `fixed` istruzione. Se l'espressione di matrice è null o se la matrice con zero elementi, l'inizializzatore calcola un indirizzo uguale a zero.
*  Un'espressione di tipo `string`, purché il tipo `char*` implicita al tipo di puntatore fornito nel `fixed` istruzione. In questo caso, l'inizializzatore calcola l'indirizzo del primo carattere nella stringa e l'intera stringa devono restare in un indirizzo fisso per la durata del `fixed` istruzione. Il comportamento del `fixed` istruzione è definito dall'implementazione se l'espressione di stringa è null.
*  Oggetto *simple_name* oppure *member_access* che fa riferimento a un membro del buffer a dimensione fissa di una variabile spostabile, purché il tipo di membro di buffer a dimensione fissa è implicitamente convertibile nel tipo di puntatore fornito nel `fixed` istruzione. In questo caso, l'inizializzatore calcola un puntatore al primo elemento del buffer a dimensione fissa ([buffer a dimensione fissa in espressioni](unsafe-code.md#fixed-size-buffers-in-expressions)), e il buffer a dimensione fissa è garantito a rimanere in un indirizzo fisso per la durata della `fixed`istruzione.

Per ogni indirizzo calcolata da una *fixed_pointer_initializer* il `fixed` istruzione garantisce che la variabile fa riferimento l'indirizzo non soggetti a spostamenti o eliminazione tramite garbage collector per la durata del `fixed` istruzione. Ad esempio, se l'indirizzo calcolata da una *fixed_pointer_initializer* fa riferimento a un campo di un oggetto o un elemento di un'istanza di matrice, il `fixed` istruzione garantisce che l'istanza dell'oggetto contenitore non viene rilocato o eliminato nel corso della durata dell'istruzione.

È responsabilità del programmatore per assicurarsi che i puntatori creato da `fixed` istruzioni non sopravvivono oltre l'esecuzione di tali istruzioni. Ad esempio, quando i puntatori creata da `fixed` istruzioni vengono passate per le API esterne, è responsabilità del programmatore assicurare che le API venga mantenuto nessuno di questi puntatori.

Oggetti fissi possono provocare la frammentazione dell'heap (perché non possono essere spostati). Per questo motivo, gli oggetti devono essere corretto solo quando strettamente necessario e solo per minor tempo possibile.

L'esempio

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

viene illustrato come utilizzare diverse di `fixed` istruzione. La prima istruzione correzioni e ottiene l'indirizzo di un campo statico, la seconda istruzione consente di risolvere e ottiene l'indirizzo di un campo di istanza e la terza istruzione correzioni e ottiene l'indirizzo di un elemento della matrice. In ogni caso sarebbe stato possibile usare la normale `&` operatore poiché tutte le variabili sono classificate come variabili spostabile.

Il quarto `fixed` istruzione nell'esempio precedente produce un risultato simile al terzo.

In questo esempio del `fixed` istruzione Usa `string`:

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

In un contesto unsafe elementi della matrice di matrici unidimensionali vengono archiviati in ordine crescente di indice, partire dall'indice `0` fino all'indice `Length - 1`. Per le matrici multidimensionali, matrice gli elementi vengono archiviati in modo che gli indici della dimensione a destra vengono incrementati in primo luogo, quindi successivo a sinistra, dimensione, e così via, a sinistra. All'interno di un `fixed` istruzione che consente di ottenere un puntatore `p` a un'istanza di matrice `a`, i valori di puntatore compreso `p` a `p + a.Length - 1` rappresentano indirizzi degli elementi nella matrice. Analogamente, le variabili comprese tra `p[0]` a `p[a.Length - 1]` rappresentano gli elementi di matrice effettive. Dato il modo in cui sono archiviate le matrici, è possibile considerare una matrice di qualsiasi dimensione come se fosse lineare.

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

l'output ottenuto sarà:

```
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

un `fixed` istruzione viene utilizzata per risolvere una matrice in modo che il relativo indirizzo può essere passato a un metodo che accetta un puntatore.

Nell’esempio

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

Per correggere un buffer a dimensione fissa di uno struct, in modo relativo indirizzo può essere utilizzato come indicatore di misura viene utilizzata un'istruzione fissa.

Oggetto `char*` valore generato tramite la correzione di un'istanza di stringa sempre punta a una stringa con terminazione null. All'interno di un'istruzione fixed che consente di ottenere un puntatore `p` a un'istanza di stringa `s`, i valori di puntatore compreso tra `p` al `p + s.Length - 1` rappresentano indirizzi dei caratteri nella stringa e il valore del puntatore `p + s.Length` fa sempre riferimento a un carattere null (il carattere con valore `'\0'`).

La modifica di oggetti di tipo gestito tramite puntatori fissi può determina un comportamento non definito. Ad esempio, poiché le stringhe sono modificabili, è compito del programmatore garantire che i caratteri di cui viene fatto riferimento da un puntatore a una stringa fissa non vengono modificati.

La terminazione null automatico delle stringhe è particolarmente utile quando si chiamano le API esterne che sono previste stringhe "C-style". Si noti tuttavia che un'istanza di stringa può contenere caratteri null. Se sono presenti caratteri null, la stringa risulterà troncata quando viene trattata come una terminazione null `char*`.

## <a name="fixed-size-buffers"></a>Buffer a dimensione fissa

Buffer a dimensione fissa vengono usate per dichiarare "C style" matrici in linea come membri di strutture e sono principalmente utili per interfacciarsi con le API non gestite.

### <a name="fixed-size-buffer-declarations"></a>Dichiarazioni di buffer a dimensione fissa

Oggetto ***buffer a dimensione fissa*** è un membro che rappresenta l'archiviazione per un buffer di lunghezza fissa di variabili di un determinato tipo. Una dichiarazione di buffer di dimensioni fisse introduce uno o più buffer a dimensione fissa di un tipo di elemento specificato. Buffer a dimensione fissa sono consentiti solo nelle dichiarazioni di struct e possono verificarsi solo in contesti non sicuri ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).

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

Una dichiarazione del buffer a dimensione fissa può includere un set di attributi ([attributi](attributes.md)), un `new` modificatore ([modificatori](classes.md#modifiers)), una combinazione valida dei quattro modificatori di accesso ([tipo i parametri e i vincoli](classes.md#type-parameters-and-constraints)) e un' `unsafe` modificatore ([contesti non sicuri](unsafe-code.md#unsafe-contexts)). Gli attributi e modificatori si applicano a tutti i membri dichiarati nella dichiarazione del buffer di dimensione fissa. È un errore per lo stesso modificatore venga visualizzato più volte in una dichiarazione di buffer di dimensione fissa.

Una dichiarazione del buffer a dimensione fissa non è consentita includere il `static` modificatore.

Il tipo di elemento del buffer di una dichiarazione di buffer di dimensione fissa specifica il tipo di elemento di buffer introdotte dalla dichiarazione. Il tipo di elemento del buffer deve essere uno dei tipi predefiniti `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, o `bool`.

Il tipo di elemento del buffer è seguito da un elenco di dichiaratori di buffer a dimensione fissa, ognuno dei quali presenta un nuovo membro. Un dichiaratore di buffer a dimensione fissa è costituito da un identificatore che denomina il membro, seguito da un'espressione costante racchiuso tra parentesi `[` e `]` i token. L'espressione costante indica il numero di elementi nel membro introdotte da tale dichiaratore di buffer a dimensione fissa. Il tipo dell'espressione costante deve essere convertibile in modo implicito nel tipo `int`, e il valore deve essere un numero intero positivo diverso da zero.

Gli elementi di un buffer a dimensione fissa che potrebbero essere disposti in sequenza in memoria.

Una dichiarazione del buffer a dimensione fissa che dichiara più buffer a dimensione fissa è equivalente alla più dichiarazioni di una dichiarazione di buffer di dimensione fissa singola con gli stessi attributi e tipi di elemento. Esempio:

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

Ricerca di membri ([operatori](expressions.md#operators)) di dimensioni fisse membro buffer procede esattamente come ricerca di membri di un campo.

Buffer a dimensione fissa è reperibile in un'espressione che usa un' *simple_name* ([inferenza del tipo](expressions.md#type-inference)) o un *member_access* ([controllo della fase di compilazione risoluzione dell'overload dinamica](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Quando un membro del buffer a dimensione fissa viene fatto riferimento come un nome semplice, l'effetto è lo stesso dell'accesso ai membri del modulo `this.I`, dove `I` è membro di buffer a dimensione fissa.

In un accesso ai membri del modulo `E.I`, se `E` è di un tipo di struct e la ricerca dei membri `I` in quanto tipo struct identifica un membro di dimensione fissa, quindi `E.I` viene valutata e classificata come indicato di seguito:

*  Se l'espressione `E.I` non si verifica in un contesto unsafe, si verifica un errore in fase di compilazione.
*  Se `E` viene classificato come un valore, si verifica un errore in fase di compilazione.
*  In caso contrario, se `E` è una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) e l'espressione `E.I` non è un *fixed_pointer_initializer* ([fissi istruzione](unsafe-code.md#the-fixed-statement)), si verifica un errore in fase di compilazione.
*  In caso contrario, `E` fa riferimento a una variabile fissa e il risultato dell'espressione è un puntatore al primo elemento del membro di buffer a dimensione fissa `I` in `E`. Il risultato è di tipo `S*`, dove `S` è il tipo di elemento di `I`ed è classificato come un valore.

Gli elementi successivi del buffer a dimensione fissa sono accessibile mediante operazioni di puntatore tra il primo elemento. A differenza di accesso alle matrici, accesso agli elementi di un buffer a dimensione fissa è un'operazione non sicura e non è selezionata di intervallo.

Nell'esempio seguente viene dichiarata e utilizzata una struttura con un membro del buffer a dimensione fissa.

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

### <a name="definite-assignment-checking"></a>Controllo dell'assegnazione definita

Buffer a dimensione fissa non sono soggetti a controllo dell'assegnazione definita ([assegnazione certa](variables.md#definite-assignment)), e i membri di dimensione fissa del buffer vengono ignorati per scopi di assegnazione certa il controllo delle variabili di tipo struct.

Quando la variabile di uno struct che lo contiene più esterno di un membro di dimensione fissa del buffer è una variabile statica, una variabile di istanza di un'istanza della classe o un elemento di matrice, gli elementi di buffer a dimensione fissa vengono inizializzati automaticamente i valori predefiniti ([Valori predefiniti](variables.md#default-values)). In tutti gli altri casi, il contenuto iniziale di un buffer a dimensione fissa è indefinito.

## <a name="stack-allocation"></a>Allocazione nello stack

In un contesto unsafe, una dichiarazione di variabile locale ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) può includere un inizializzatore di allocazione dello stack che consente di allocare memoria dallo stack di chiamate.

```antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

Il *unmanaged_type* indica il tipo degli elementi che verranno archiviati nel percorso appena allocato, e il *espressione* indica il numero di questi elementi. Nel loro insieme, questi elementi specificano la dimensione di allocazione necessaria. Poiché le dimensioni di un'allocazione dello stack non possono essere negativa, è un errore in fase di compilazione per specificare il numero di elementi come un *constant_expression* che restituisce un valore negativo.

Un inizializzatore di allocazione dello stack del form `stackalloc T[E]` richiede `T` sia un tipo non gestito ([tipi di puntatore](unsafe-code.md#pointer-types)) e `E` essere un'espressione di tipo `int`. Alloca il costrutto `E * sizeof(T)` byte dalla chiamata dello stack e restituisce un puntatore di tipo `T*`, per il blocco appena allocato. Se `E` è un valore negativo, il comportamento sarà indefinito. Se `E` è uguale a zero, quindi viene eseguita alcuna allocazione e il puntatore restituito è definito dall'implementazione. Se non è disponibile memoria sufficiente per allocare un blocco della dimensione specificata, un `System.StackOverflowException` viene generata un'eccezione.

Il contenuto della memoria appena allocata è definito.

Gli inizializzatori di allocazione dello stack non sono consentiti nelle `catch` oppure `finally` blocchi ([l'istruzione try](statements.md#the-try-statement)).

Non è possibile liberare in modo esplicito la memoria allocata tramite `stackalloc`. Tutti i blocchi di memoria allocato nello stack creati durante l'esecuzione di un membro di funzione vengono eliminati automaticamente quando restituisce il membro di funzione. Corrisponde al `alloca` (funzione), un'estensione comunemente reperibile nelle implementazioni di C e C++.

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

una `stackalloc` inizializzatore viene utilizzato la `IntToString` metodo per allocare un buffer di 16 caratteri nello stack. Il buffer viene eliminato automaticamente quando il metodo restituisce.

## <a name="dynamic-memory-allocation"></a>Allocazione dinamica della memoria

Fatta eccezione per il `stackalloc` operatore, c# non offre costrutti predefiniti per la gestione della memoria raccolta non garbage. Tali servizi sono in genere forniti dal supporto di librerie di classi o importati direttamente dal sistema operativo sottostante. Ad esempio, il `Memory` classe riportata di seguito viene illustrato come le funzioni heap di un sistema operativo sottostante potrebbe essere possibile accedere da c#:

```csharp
using System;
using System.Runtime.InteropServices;

public unsafe class Memory
{
    // Handle for the process heap. This handle is used in all calls to the
    // HeapXXX APIs in the methods below.
    static int ph = GetProcessHeap();

    // Private instance constructor to prevent instantiation.
    private Memory() {}

    // Allocates a memory block of the given size. The allocated memory is
    // automatically initialized to zero.
    public static void* Alloc(int size) {
        void* result = HeapAlloc(ph, HEAP_ZERO_MEMORY, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Copies count bytes from src to dst. The source and destination
    // blocks are permitted to overlap.
    public static void Copy(void* src, void* dst, int count) {
        byte* ps = (byte*)src;
        byte* pd = (byte*)dst;
        if (ps > pd) {
            for (; count != 0; count--) *pd++ = *ps++;
        }
        else if (ps < pd) {
            for (ps += count, pd += count; count != 0; count--) *--pd = *--ps;
        }
    }

    // Frees a memory block.
    public static void Free(void* block) {
        if (!HeapFree(ph, 0, block)) throw new InvalidOperationException();
    }

    // Re-allocates a memory block. If the reallocation request is for a
    // larger size, the additional region of memory is automatically
    // initialized to zero.
    public static void* ReAlloc(void* block, int size) {
        void* result = HeapReAlloc(ph, HEAP_ZERO_MEMORY, block, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Returns the size of a memory block.
    public static int SizeOf(void* block) {
        int result = HeapSize(ph, 0, block);
        if (result == -1) throw new InvalidOperationException();
        return result;
    }

    // Heap API flags
    const int HEAP_ZERO_MEMORY = 0x00000008;

    // Heap API functions
    [DllImport("kernel32")]
    static extern int GetProcessHeap();

    [DllImport("kernel32")]
    static extern void* HeapAlloc(int hHeap, int flags, int size);

    [DllImport("kernel32")]
    static extern bool HeapFree(int hHeap, int flags, void* block);

    [DllImport("kernel32")]
    static extern void* HeapReAlloc(int hHeap, int flags, void* block, int size);

    [DllImport("kernel32")]
    static extern int HeapSize(int hHeap, int flags, void* block);
}
```

Un esempio che usa il `Memory` classe è indicata di seguito:

```csharp
class Test
{
    static void Main() {
        unsafe {
            byte* buffer = (byte*)Memory.Alloc(256);
            try {
                for (int i = 0; i < 256; i++) buffer[i] = (byte)i;
                byte[] array = new byte[256];
                fixed (byte* p = array) Memory.Copy(buffer, p, 256); 
            }
            finally {
                Memory.Free(buffer);
            }
            for (int i = 0; i < 256; i++) Console.WriteLine(array[i]);
        }
    }
}
```

Nell'esempio viene allocata 256 byte di memoria tramite `Memory.Alloc` e inizializza il blocco di memoria con i valori crescenti da 0 a 255. Quindi alloca una matrice di byte di 256 elementi e utilizza `Memory.Copy` per copiare il contenuto del blocco di memoria nella matrice di byte. Infine, il blocco di memoria viene liberato usando `Memory.Free` e il contenuto della matrice di byte viene visualizzati nella console.
