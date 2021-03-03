---
ms.openlocfilehash: fe9955c66ec231a85bc2058f044e8921fc2fbbc0
ms.sourcegitcommit: 3f8f57e294e2952af19a5231e6b9c7ff85d0ed56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101641747"
---
# <a name="introduction"></a>Introduzione

C#, pronunciato "See Sharp", è un linguaggio di programmazione semplice, moderno, orientato a oggetti e indipendente dai tipi. C# presenta le sue radici nella famiglia di linguaggi C e sarà immediatamente familiare ai programmatori C, C++ e Java. C# è standardizzato da ECMA International come ***ECMA-334** _ standard e ISO/IEC come lo standard _ *_iso/IEC 23270_**. Il compilatore C# di Microsoft per il .NET Framework è un'implementazione conforme di entrambi questi standard.

C# è un linguaggio orientato a oggetti, ma include anche il supporto per la programmazione ***orientata ai componenti***. La progettazione software contemporanea è basata in misura sempre maggiore su componenti software costituiti da pacchetti di funzionalità autonomi e autodescrittivi. L'aspetto chiave di tali componenti è che presentano un modello di programmazione con proprietà, metodi ed eventi. Presentano inoltre attributi che forniscono informazioni dichiarative sul componente. Questi componenti, infine, includono la propria documentazione. C# fornisce costrutti di linguaggio per supportare direttamente questi concetti, rendendo C# un linguaggio molto naturale in cui creare e utilizzare i componenti software.

Diverse funzionalità C# facilitano la costruzione di applicazioni solide e durevoli: ***Garbage Collection** _ recupera automaticamente la memoria occupata dagli oggetti inutilizzati; la _*_gestione delle eccezioni_*_ fornisce un approccio strutturato ed estendibile per il rilevamento e il ripristino degli errori. e la struttura di tipo **_indipendente dai tipi_* di linguaggio rende impossibile leggere le variabili non inizializzate, indicizzare le matrici oltre i limiti o per eseguire cast di tipo non verificati.

C# presenta un ***sistema di tipi unificato***. Tutti i tipi C#, inclusi i tipi di primitiva quali `int` e `double`, ereditano da un unico tipo `object` radice. Di conseguenza, tutti i tipi condividono un set di operazioni comuni e i valori dei diversi tipi possono essere archiviati, trasportati e gestiti in modo coerente. C#, inoltre, supporta sia i tipi riferimento sia i tipi valore definiti dall'utente, consentendo l'allocazione dinamica di oggetti e l'archiviazione inline di strutture leggere.

Per garantire che i programmi e le librerie C# possano evolversi nel tempo in modo compatibile, è stato enfatizzato il ***controllo delle versioni*** nella progettazione di c#. Molti linguaggi di programmazione prestano scarsa attenzione a questo aspetto e, di conseguenza, i programmi scritti in tali linguaggi si interrompono molto più spesso del necessario quando vengono introdotte nuove versioni delle librerie dipendenti. Gli aspetti della progettazione di C# che sono stati influenzati direttamente dalle considerazioni sul controllo delle versioni includono i `virtual` `override` modificatori e separati, le regole per la risoluzione dell'overload del metodo e il supporto per le dichiarazioni esplicite dei membri di interfaccia.

Nella parte restante di questo capitolo vengono descritte le funzionalità essenziali del linguaggio C#. Sebbene i capitoli successivi descrivano le regole e le eccezioni in modo orientato ai dettagli e talvolta matematico, questo capitolo si impegna per la chiarezza e la brevità a scapito della completezza. Lo scopo è fornire al lettore un'introduzione al linguaggio che faciliterà la scrittura dei primi programmi e la lettura dei capitoli successivi.

## <a name="hello-world"></a>Hello world

Il programma "Hello World" viene tradizionalmente usato per presentare un linguaggio di programmazione. Di seguito è riportato il programma Hello, World in C#:

```csharp
using System;

class Hello
{
    static void Main() {
        Console.WriteLine("Hello, World");
    }
}
```

I file di origine C# hanno in genere l'estensione `.cs`. Supponendo che il programma "Hello, World" sia archiviato nel file `hello.cs` , è possibile compilare il programma con il compilatore Microsoft C# utilizzando la riga di comando
```console
csc hello.cs
```
che produce un assembly eseguibile denominato `hello.exe` . L'output prodotto da questa applicazione quando viene eseguito è
```console
Hello, World
```

Il programma "Hello World" inizia con una direttiva `using` che fa riferimento allo spazio dei nomi `System`. Gli spazi dei nomi consentono di organizzare i programmi e le librerie C# in modo gerarchico. Gli spazi dei nomi contengono tipi e altri spazi dei nomi. Lo stazio dei nomi `System`, ad esempio, contiene diversi tipi, come la classe `Console` a cui viene fatto riferimento nel programma, e altri spazi dei nomi, come `IO` e `Collections`. Una direttiva `using` che fa riferimento a un determinato spazio dei nomi consente l'uso non qualificato dei tipi che sono membri di tale spazio dei nomi. Grazie alla direttiva `using`, il programma può usare `Console.WriteLine` come sintassi abbreviata per `System.Console.WriteLine`.

La classe `Hello` dichiarata dal programma "Hello World" ha un solo membro, ovvero il metodo denominato `Main`. Il `Main` metodo viene dichiarato con il `static` modificatore. Mentre i metodi di istanza possono fare riferimento a una particolare istanza dell'oggetto contenitore usando la parola chiave `this`, i metodi statici operano senza riferimento a un determinato oggetto. Per convenzione, un metodo statico denominato `Main` funge da punto di ingresso di un programma.

L'output del programma viene prodotto dal metodo `WriteLine` della classe `Console` nello spazio dei nomi `System`. Questa classe viene fornita dalle librerie di classi .NET Framework, che, per impostazione predefinita, fanno riferimento automaticamente al compilatore Microsoft C#. Si noti che in C# non è presente una libreria di runtime separata. Il .NET Framework è invece la libreria di runtime di C#.

## <a name="program-structure"></a>Struttura del programma

I concetti aziendali chiave in C# sono i ***programmi** _, gli _*_spazi dei nomi_*_, i _*_tipi_*_, _*_i membri_*_ e gli _*_assembly_*_. I programmi C# sono costituiti da uno o più file di origine. I programmi dichiarano i tipi, che contengono i membri e possono essere organizzati in spazi dei nomi. Le classi e le interfacce sono esempi di tipi. I campi, i metodi, le proprietà e gli eventi sono esempi di membri. Quando vengono compilati, i programmi C# vengono inseriti fisicamente in assembly. Gli assembly hanno in genere l'estensione di file `.exe` o `.dll` , a seconda che _**_ implementino o meno le _librerie_* *.

L'esempio:

```csharp
using System;

namespace Acme.Collections
{
    public class Stack
    {
        Entry top;

        public void Push(object data) {
            top = new Entry(top, data);
        }

        public object Pop() {
            if (top == null) throw new InvalidOperationException();
            object result = top.data;
            top = top.next;
            return result;
        }

        class Entry
        {
            public Entry next;
            public object data;
    
            public Entry(Entry next, object data) {
                this.next = next;
                this.data = data;
            }
        }
    }
}
```
dichiara una classe denominata `Stack` in uno spazio dei nomi denominato `Acme.Collections` . Il nome completo di questa classe è `Acme.Collections.Stack`. La classe contiene vari membri: un campo `top`, due metodi `Push` e `Pop` e una classe annidata `Entry`. La classe `Entry` contiene altri tre membri: un campo `next`, un campo `data` e un costruttore. Supponendo che il codice sorgente dell'esempio sia archiviato nel file `acme.cs`, la riga di comando

```console
csc /t:library acme.cs
```
compila l'esempio come libreria (codice senza un punto di ingresso `Main`) e genera un assembly denominato `acme.dll`.

Gli assembly contengono codice eseguibile sotto forma di istruzioni ***Intermediate Language** _ (il) e informazioni sui simboli nel formato _ *_Metadata_* *. Prima di essere eseguito, il codice IL presente in un assembly viene convertito automaticamente nel codice specifico del processore dal compilatore JIT (Just-In-Time) di .NET Common Language Runtime.

Poiché un assembly è un'unità autodescrittiva di funzionalità contenente codice e metadati, in C# non sono necessari file di intestazione e direttive `#include`. I membri e i tipi pubblici contenuti in un determinato assembly vengono resi disponibili in un programma C# semplicemente facendo riferimento a tale assembly durante la compilazione del programma. Questo programma usa ad esempio la classe `Acme.Collections.Stack` dell'assembly `acme.dll`:

```csharp
using System;
using Acme.Collections;

class Test
{
    static void Main() {
        Stack s = new Stack();
        s.Push(1);
        s.Push(10);
        s.Push(100);
        Console.WriteLine(s.Pop());
        Console.WriteLine(s.Pop());
        Console.WriteLine(s.Pop());
    }
}
```
Se il programma è archiviato nel file `test.cs` , quando `test.cs` viene compilato, è `acme.dll` possibile fare riferimento all'assembly usando l'opzione del compilatore `/r` :

```console
csc /r:acme.dll test.cs
```
In questo modo verrà creato un assembly eseguibile denominato `test.exe` che, quando viene eseguito, genera l'output:

```console
100
10
1
```
C# consente di archiviare il testo di origine di un programma in vari file di origine. Quando viene compilato un programma C# costituito da più file, tutti i file di origine vengono elaborati insieme e possono fare riferimento l'uno all'altro. A livello concettuale è come se tutti i file di origine fossero concatenati in un unico grande file prima di essere elaborati. Le dichiarazioni con prototipo non sono mai necessarie in C# perché, tranne che in rare eccezioni, l'ordine di dichiarazione non è significativo. C# non limita un file di origine alla dichiarazione di un solo tipo pubblico e non richiede che il nome del file di origine corrisponda a un tipo dichiarato nel file di origine.

## <a name="types-and-variables"></a>Tipi e variabili

Esistono due tipi di tipi in C#: ***tipi valore** _ e _ *_tipi di riferimento_* *. Le variabili dei tipi valore contengono direttamente i propri dati, mentre le variabili dei tipi riferimento archiviano i riferimenti ai propri dati, noti come oggetti. Con i tipi riferimento, due variabili possono fare riferimento allo stesso oggetto e di conseguenza le operazioni su una delle due variabili possono influire sull'oggetto a cui fa riferimento l'altra. Con i tipi valore, ogni variabile ha una propria copia dei dati e non è possibile che le operazioni su una variabile influiscano sull'altra (tranne nel caso delle variabili di parametro `ref` e `out`).

I tipi di valore di c# sono ulteriormente divisi in ***tipi semplici** _, _*_tipi enum_*_, _*_tipi di struct_*_ e _*_tipi nullable_*_ e i tipi di riferimento di c# sono ulteriormente divisi in _*_tipi di classe_*_, _*_tipi di interfaccia_*_, tipi di _*_matrici_*_ e _tipi delegati_ _ * * *.

La tabella seguente fornisce una panoramica del sistema di tipi di C#.

| __Categoria__    |                 | __Descrizione__ |
|-----------------|-----------------|-----------------|
| Tipi valore     | Tipi semplici    | Signed Integer: `sbyte`, `short`, `int`,`long` |
|                 |                 | Unsigned Integer: `byte`, `ushort`, `uint`,`ulong` |
|                 |                 | Caratteri Unicode: `char` |
|                 |                 | Virgola mobile IEEE: `float`, `double` |
|                 |                 | Decimale ad alta precisione: `decimal` |
|                 |                 | Booleano: `bool` |
|                 | Tipi enum      | Tipi definiti dall'utente nel formato `enum E {...}` |
|                 | Tipi struct    | Tipi definiti dall'utente nel formato `struct S {...}` |
|                 | Tipi nullable  | Estensioni di tutti gli altri tipi valore con un valore `null` |
| Tipi riferimento | Tipi di classe     | Classe di base principale di tutti gli altri tipi: `object` |
|                 |                 | Stringhe Unicode: `string` |
|                 |                 | Tipi definiti dall'utente nel formato `class C {...}` |
|                 | Tipi interfaccia | Tipi definiti dall'utente nel formato `interface I {...}` |
|                 | Tipi matrice     | Unidimensionale e multidimensionale, ad esempio `int[]` e `int[,]` |
|                 | Tipi delegato  | Tipi definiti dall'utente del modulo, ad esempio `delegate int  D(...)` |

Gli otto tipi integrali offrono supporto per i valori a 8, 16, 32 e 64 bit in formato con segno o senza segno.

I due tipi a virgola mobile, `float` e `double` , vengono rappresentati usando i formati IEEE 754 a precisione singola a 32 bit e a 64 bit.

Il tipo `decimal` è un tipo dati a 128 bit adatto per i calcoli finanziari e monetari.

Il tipo di C# `bool` viene usato per rappresentare valori booleani, ovvero valori che sono `true` o `false` .

Per l'elaborazione di caratteri e stringhe, in C# viene usata la codifica Unicode. Il tipo `char` rappresenta un'unità di codice UTF-16, mentre il tipo `string` rappresenta una sequenza di unità di codice UTF-16.

Nella tabella seguente sono riepilogati i tipi numerici di C#.


| __Categoria__      | __BITS__ | __Tipo__  | __Intervallo/precisione__ |
|-------------------|----------|-----------|---------------------|
| Integrale con segno   | 8        | `sbyte`   | -128... 127 |
|                   | 16       | `short`   | -32768... 32, 767 |
|                   | 32       | `int`     | -2147483648... 2, 147, 483, 647 |
|                   | 64       | `long`    | -9.223.372.036.854.775.808... 9, 223, 372, 036, 854, 775, 807 |
| Unsigned Integer | 8        | `byte`    | 0... 255 |
|                   | 16       | `ushort`  | 0... 65, 535 |
|                   | 32       | `uint`    | 0... 4, 294, 967, 295 |
|                   | 64       | `ulong`   | 0... 18, 446, 744, 073, 709, 551, 615 |
| Virgola mobile    | 32       | `float`   | 1,5 × 10 ^ − 45 a 3,4 × 10 ^ 38, precisione a 7 cifre |
|                   | 64       | `double`  | 5,0 × 10 ^ − 324 a 1,7 × 10 ^ 308, precisione di 15 cifre |
| Decimal           | 128      | `decimal` | 1,0 × 10 ^ − 28 a 7,9 × 10 ^ 28, precisione di 28 cifre |

I programmi C# usano le ***dichiarazioni di tipo*** per creare nuovi tipi. Una dichiarazione di tipo consente di specificare il nome e i membri del nuovo tipo. Cinque delle categorie di tipi di C# sono definibili dall'utente: tipi di classe, tipi di struct, tipi di interfaccia, tipi enum e tipi delegati.

Un tipo di classe definisce una struttura di dati che contiene membri dati (campi) e membri di funzione (metodi, proprietà e altri). I tipi classe supportano l'ereditarietà singola e il polimorfismo, meccanismi in base ai quali le classi derivate possono estendere e specializzare le classi di base.

Un tipo di struct è simile a un tipo di classe in quanto rappresenta una struttura con membri dati e membri di funzione. Tuttavia, a differenza delle classi, gli struct sono tipi valore e non richiedono l'allocazione dell'heap. I tipi struct non supportano l'ereditarietà specificata dall'utente. Tutti i tipi struct ereditano implicitamente dal tipo `object`.

Un tipo di interfaccia definisce un contratto come un set denominato di membri di funzioni pubbliche. Una classe o uno struct che implementa un'interfaccia deve fornire le implementazioni dei membri della funzione dell'interfaccia. Un'interfaccia può ereditare da più interfacce di base e una classe o uno struct può implementare più interfacce.

Un tipo delegato rappresenta riferimenti ai metodi con un elenco di parametri e un tipo restituito particolari. I delegati consentono di trattare i metodi come entità che è possibile assegnare a variabili e passare come parametri. I delegati sono simili al concetto di puntatori a funzione disponibili in altri linguaggi. A differenza dei puntatori a funzione, tuttavia, i delegati sono orientati agli oggetti e indipendenti dai tipi.

I tipi di classe, struct, interfaccia e delegato supportano tutti i generics, in base ai quali possono essere parametrizzati con altri tipi.

Un tipo enum è un tipo distinto con costanti denominate. Ogni tipo di enumerazione ha un tipo sottostante, che deve essere uno degli otto tipi integrali. Il set di valori di un tipo enum corrisponde al set di valori del tipo sottostante.

C# supporta matrici unidimensionali e multidimensionali di qualsiasi tipo. A differenza dei tipi elencati in precedenza, i tipi matrice non devono essere dichiarati prima dell'uso. Al contrario, i tipi matrice vengono costruiti facendo seguire a un nome di tipo delle parentesi quadre. Ad esempio, `int[]` è una matrice unidimensionale di `int` , `int[,]` è una matrice bidimensionale di `int` e `int[][]` è una matrice unidimensionale di matrici unidimensionali di `int` .

Anche i tipi nullable non devono essere dichiarati prima di poter essere usati. Per ogni tipo di valore non Nullable `T` esiste un tipo nullable corrispondente `T?` , che può avere un valore aggiuntivo `null` . Ad esempio, `int?` è un tipo che può ospitare qualsiasi Integer a 32 bit o il valore `null` .

Il sistema di tipi di C# è unificato in modo che un valore di qualsiasi tipo possa essere considerato come un oggetto. In C# ogni tipo deriva direttamente o indirettamente dal tipo classe `object` e `object` è la classe di base principale di tutti i tipi. I valori dei tipi riferimento vengono trattati come oggetti semplicemente visualizzando tali valori come tipi `object`. I valori dei tipi valore vengono trattati come oggetti eseguendo le operazioni ***Boxing** _ e _ *_unboxing *._* Nell'esempio seguente un valore `int` viene convertito in `object` e quindi convertito nuovamente in `int`.

```csharp
using System;

class Test
{
    static void Main() {
        int i = 123;
        object o = i;          // Boxing
        int j = (int)o;        // Unboxing
    }
}
```
Quando un valore di un tipo di valore viene convertito nel tipo `object` , viene allocata un'istanza dell'oggetto, denominata anche "box", per mantenere il valore e il valore viene copiato in tale casella. Viceversa, quando `object` viene eseguito il cast di un riferimento a un tipo valore, viene eseguito un controllo che l'oggetto a cui si fa riferimento è una casella del tipo di valore corretto e, se il controllo ha esito positivo, il valore nella casella viene copiato.

Il sistema di tipi unificato di C# indica efficacemente che i tipi di valore possono diventare oggetti "su richiesta". Grazie all'unificazione, le librerie generiche che usano il tipo `object` possono essere usate con entrambi i tipi riferimento e valore.

In C# sono disponibili diversi tipi di ***variabili***, inclusi campi, elementi matrice, variabili locali e parametri. Le variabili rappresentano i percorsi di archiviazione e ogni variabile dispone di un tipo che determina quali valori possono essere archiviati nella variabile, come illustrato nella tabella seguente.


| __Il tipo di variabile__    | __Contenuto possibile__ |
|-------------------------|-----------------------|
| Tipo valore non-nullable | Valore esattamente del tipo indicato |
| Tipo valore nullable     | Un valore null o un valore di quel tipo esatto |
| `object`                | Un riferimento null, un riferimento a un oggetto di qualsiasi tipo di riferimento o un riferimento a un valore boxed di qualsiasi tipo di valore |
| Tipo classe              | Un riferimento null, un riferimento a un'istanza di quel tipo di classe o un riferimento a un'istanza di una classe derivata da tale tipo di classe |
| Tipo interfaccia          | Un riferimento null, un riferimento a un'istanza di un tipo di classe che implementa tale tipo di interfaccia o un riferimento a un valore boxed di un tipo di valore che implementa tale tipo di interfaccia |
| Tipo matrice              | Un riferimento null, un riferimento a un'istanza di tale tipo di matrice o un riferimento a un'istanza di un tipo di matrice compatibile |
| Tipo delegato           | Un riferimento null o un riferimento a un'istanza del tipo delegato |

## <a name="expressions"></a>Espressioni

*Le **espressioni** _ sono costruite da _*_operandi_*_ e _*_operatori_*_. Gli operatori di un'espressione indicano le operazioni che devono essere eseguite sugli operandi. Alcuni esempi di operatori sono `+`, `-`, `_`, `/` e `new`, mentre i valori effettivi, i campi, le variabili locali e le espressioni sono esempi di operandi.

Quando un'espressione contiene più operatori, la ***precedenza** _ degli operatori controlla l'ordine in cui vengono valutati i singoli operatori. L'espressione `x + y _ z`, ad esempio, viene valutata come `x + (y * z)` poiché l'operatore `*` ha la precedenza sull'operatore `+`.

La maggior parte degli operatori può essere ***sottoposta a overload***. L'overload degli operatori consente di specificare implementazioni di operatori definite dall'utente per le operazioni in cui uno o entrambi gli operandi appartengono a un tipo struct o a una classe definita dall'utente.

Nella tabella seguente sono riepilogati gli operatori di C#, che elencano le categorie di operatori in ordine di precedenza dal più alto al più basso. Gli operatori nella stessa categoria hanno la stessa precedenza.


| __Categoria__                     | __Espressione__    | __Descrizione__ |
|----------------------------------|-------------------|-----------------|
| Principale                          | `x.m`             | Accesso ai membri |
|                                  | `x(...)`          | Chiamata a metodi e delegati |
|                                  | `x[...]`          | Accesso a matrici e indicizzatori |
|                                  | `x++`             | Post-incremento |
|                                  | `x--`             | Post-decremento |
|                                  | `new T(...)`      | Creazione di oggetti e delegati |
|                                  | `new T(...){...}` | creazione di oggetti con inizializzatore |
|                                  | `new {...}`       | inizializzatore di oggetti anonimo |
|                                  | `new T[...]`      | creazione di matrici |
|                                  | `typeof(T)`       | ottiene l'oggetto `System.Type` per `T` |
|                                  | `checked(x)`      | Valutare l'espressione in un contesto controllato (checked) |
|                                  | `unchecked(x)`    | Valutare l'espressione in un contesto non controllato (unchecked) |
|                                  | `default(T)`      | ottiene un valore predefinito di tipo `T` |
|                                  | `delegate {...}`  | Funzione anonima (metodo anonimo) |
| Unaria                            | `+x`              | Identità |
|                                  | `-x`              | Negazione |
|                                  | `!x`              | Negazione logica |
|                                  | `~x`              | Negazione bit per bit |
|                                  | `++x`             | Pre-incremento |
|                                  | `--x`             | Pre-decremento |
|                                  | `(T)x`            | converte in modo esplicito `x` al tipo `T` |
|                                  | `await x`         | attende in modo asincrono il completamento di `x` |
| Moltiplicazione                   | `x * y`           | Moltiplicazione |
|                                  | `x / y`           | Divisione |
|                                  | `x % y`           | Resto |
| Additive                         | `x + y`           | Addizione, concatenazione di stringhe, combinazione di delegati |
|                                  | `x - y`           | Sottrazione, rimozione di delegati |
| MAIUSC                            | `x << y`          | Spostamento a sinistra |
|                                  | `x >> y`          | Spostamento a destra |
| Operatori relazionali e operatori di test del tipo      | `x < y`           | Minore di |
|                                  | `x > y`           | Maggiore di |
|                                  | `x <= y`          | Minore o uguale a |
|                                  | `x >= y`          | Maggiore o uguale a |
|                                  | `x is T`          | restituisce `true` se `x` è un oggetto `T`, altrimenti `false` |
|                                  | `x as T`          | restituisce `x` tipizzato come `T` oppure `null` se `x` non è un oggetto `T` |
| Uguaglianza                         | `x == y`          | Uguale a      |
|                                  | `x != y`          | Diverso da |
| AND logico                      | `x & y`           | AND Integer bit per bit, AND logico booleano |
| XOR logico                      | `x ^ y`           | XOR Integer bit per bit, XOR logico booleano |
| OR logico                       | <code>x &#124; y</code> | OR Integer bit per bit, OR logico booleano |
| AND condizionale                  | `x && y`          | Valuta `y` solo se `x` è `true` |
| OR condizionale                   | <code>x &#124;&#124; y</code> | Valuta `y` solo se `x` è `false` |
| Null-coalescing                  | `x ?? y`          | Restituisce `y` se `x` è `null` , in `x` caso contrario |
| Condizionale                      | `x ? y : z`       | restituisce `y` se `x` è `true`, `z` se `x` è `false` |
| Assegnazione o funzione anonima | `x = y`           | Assegnazione |
|                                  | `x op= y`         | Assegnazione composta; gli operatori supportati `*=` `/=` `%=` `+=` `-=` sono `<<=` `>>=` `&=` `^=`<code>&#124;=</code> |
|                                  | `(T x) => y`      | Funzione anonima (espressione lambda) |

## <a name="statements"></a>Istruzioni

Le azioni di un programma vengono espresse mediante ***istruzioni***. C# supporta numerosi tipi di istruzioni, alcune delle quali sono definite in termini di istruzioni nidificate.

Un ***blocco*** consente di scrivere più istruzioni nei contesti in cui ne è consentita una sola. Un blocco è costituito da un elenco di istruzioni scritte tra i delimitatori `{` e `}`.

Le ***istruzioni di dichiarazione*** vengono usate per dichiarare le costanti e le variabili locali.

Le ***istruzioni di espressione*** vengono usate per valutare le espressioni. Le espressioni che possono essere utilizzate come istruzioni includono chiamate al metodo, allocazioni di oggetti utilizzando l' `new` operatore, assegnazioni utilizzando `=` e gli operatori di assegnazione composta, operazioni di incremento e decremento utilizzando gli `++` `--` operatori e e le espressioni await.

Le ***istruzioni di selezione*** vengono usate per selezionare una tra più istruzioni che è possibile eseguire sulla base del valore di alcune espressioni. In questo gruppo sono incluse le istruzioni `if` e `switch`.

Le ***istruzioni di iterazione*** vengono usate per eseguire ripetutamente un'istruzione incorporata. In questo gruppo sono incluse le istruzioni `while`, `do`, `for` e `foreach`.

Le ***istruzioni di spostamento*** vengono usate per trasferire il controllo. In questo gruppo sono incluse le istruzioni `break`, `continue`, `goto`, `throw`, `return` e `yield`.

L'istruzione `try`...`catch` viene usata per rilevare le eccezioni che si verificano durante l'esecuzione di un blocco, mentre l'istruzione `try`...`finally` viene usata per specificare il codice di finalizzazione che viene eseguito sempre, indipendentemente dal fatto che si sia verificata un'eccezione.

Le `checked` `unchecked` istruzioni e vengono utilizzate per controllare il contesto di controllo dell'overflow per le conversioni e le operazioni aritmetiche di tipo integrale.

L'istruzione `lock` viene usata per ottenere il blocco a esclusione reciproca per un oggetto specificato, eseguire un'istruzione e quindi rilasciare il blocco.

L'istruzione `using` viene usata per ottenere una risorsa, eseguire un'istruzione e quindi eliminare la risorsa.

Di seguito sono riportati alcuni esempi di ogni tipo di istruzione

__Dichiarazioni di variabili locali__

```csharp
static void Main() {
   int a;
   int b = 2, c = 3;
   a = 1;
   Console.WriteLine(a + b + c);
}
```


__Dichiarazione di costante locale__

```csharp
static void Main() {
    const float pi = 3.1415927f;
    const int r = 25;
    Console.WriteLine(pi * r * r);
}
```


__Istruzione di espressione__

```csharp
static void Main() {
    int i;
    i = 123;                // Expression statement
    Console.WriteLine(i);   // Expression statement
    i++;                    // Expression statement
    Console.WriteLine(i);   // Expression statement
}
```

__`if` istruzione__

```csharp
static void Main(string[] args) {
    if (args.Length == 0) {
        Console.WriteLine("No arguments");
    }
    else {
        Console.WriteLine("One or more arguments");
    }
}
```


__`switch` istruzione__

```csharp
static void Main(string[] args) {
    int n = args.Length;
    switch (n) {
        case 0:
            Console.WriteLine("No arguments");
            break;
        case 1:
            Console.WriteLine("One argument");
            break;
        default:
            Console.WriteLine("{0} arguments", n);
            break;
    }
}
```

__`while` istruzione__

```csharp
static void Main(string[] args) {
    int i = 0;
    while (i < args.Length) {
        Console.WriteLine(args[i]);
        i++;
    }
}
```


__`do` istruzione__

```csharp
static void Main() {
    string s;
    do {
        s = Console.ReadLine();
        if (s != null) Console.WriteLine(s);
    } while (s != null);
}
```

__`for` istruzione__

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        Console.WriteLine(args[i]);
    }
}
```

__`foreach` istruzione__

```csharp
static void Main(string[] args) {
    foreach (string s in args) {
        Console.WriteLine(s);
    }
}
```

__`break` istruzione__

```csharp
static void Main() {
    while (true) {
        string s = Console.ReadLine();
        if (s == null) break;
        Console.WriteLine(s);
    }
}
```

__`continue` istruzione__

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        if (args[i].StartsWith("/")) continue;
        Console.WriteLine(args[i]);
    }
}
```

__`goto` istruzione__

```csharp
static void Main(string[] args) {
    int i = 0;
    goto check;
    loop:
    Console.WriteLine(args[i++]);
    check:
    if (i < args.Length) goto loop;
}
```

__`return` istruzione__

```csharp
static int Add(int a, int b) {
    return a + b;
}

static void Main() {
    Console.WriteLine(Add(1, 2));
    return;
}
```

__`yield` istruzione__

```csharp
static IEnumerable<int> Range(int from, int to) {
    for (int i = from; i < to; i++) {
        yield return i;
    }
    yield break;
}

static void Main() {
    foreach (int x in Range(-10,10)) {
        Console.WriteLine(x);
    }
}
```

__`throw``try`istruzioni e__

```csharp
static double Divide(double x, double y) {
    if (y == 0) throw new DivideByZeroException();
    return x / y;
}

static void Main(string[] args) {
    try {
        if (args.Length != 2) {
            throw new Exception("Two numbers required");
        }
        double x = double.Parse(args[0]);
        double y = double.Parse(args[1]);
        Console.WriteLine(Divide(x, y));
    }
    catch (Exception e) {
        Console.WriteLine(e.Message);
    }
    finally {
        Console.WriteLine("Good bye!");
    }
}
```

__`checked``unchecked`istruzioni e__

```csharp
static void Main() {
    int i = int.MaxValue;
    checked {
        Console.WriteLine(i + 1);        // Exception
    }
    unchecked {
        Console.WriteLine(i + 1);        // Overflow
    }
}
```

__`lock` istruzione__

```csharp
class Account
{
    decimal balance;
    public void Withdraw(decimal amount) {
        lock (this) {
            if (amount > balance) {
                throw new Exception("Insufficient funds");
            }
            balance -= amount;
        }
    }
}
```

__`using` istruzione__

```csharp
static void Main() {
    using (TextWriter w = File.CreateText("test.txt")) {
        w.WriteLine("Line one");
        w.WriteLine("Line two");
        w.WriteLine("Line three");
    }
}
```

## <a name="classes-and-objects"></a>Classi e oggetti

*Le **classi** _ sono i più importanti dei tipi di C#. Una classe è una struttura di dati che combina in una singola unità lo stato (campi) e le azioni (metodi e altri membri di funzione). Una classe fornisce una definizione per le _*_istanze_*_ create dinamicamente della classe, note anche come _*_oggetti_*_. Le classi supportano l' _*_ereditarietà_*_ e il _*_polimorfismo_*_, meccanismi in base ai quali _*_le classi derivate_*_ possono estendere e specializzare _le classi di base_* *.

Le nuove classi vengono create tramite dichiarazioni di classe. Una dichiarazione di classe inizia con un'intestazione che specifica gli attributi e i modificatori della classe, il nome della classe, la classe di base (se disponibile) e le interfacce implementate dalla classe. L'intestazione è seguita dal corpo della classe, costituito da un elenco di dichiarazioni di membro scritte tra i delimitatori `{` e `}`.

Di seguito è riportata una dichiarazione di una classe semplice denominata `Point`:

```csharp
public class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
Le istanze delle classi vengono create usando l'operatore `new`, che alloca memoria per una nuova istanza, richiama un costruttore per inizializzare l'istanza e restituisce un riferimento all'istanza. Le istruzioni seguenti creano due `Point` oggetti e archiviano i riferimenti a tali oggetti in due variabili:

```csharp
Point p1 = new Point(0, 0);
Point p2 = new Point(10, 20);
```
La memoria occupata da un oggetto viene recuperata automaticamente quando l'oggetto non è più in uso. In C# non è possibile, né necessario, deallocare oggetti in modo esplicito.

### <a name="members"></a>Membri

I membri di una classe sono _i_ membri **statici*** *. I primi appartengono a classi, mentre i secondi appartengono a oggetti, ovvero a istanze di classi.

Nella tabella seguente viene fornita una panoramica dei tipi di membri che possono essere contenuti in una classe.


| __Membro__   | __Descrizione__ |
|------------  |-----------------|
| Costanti    | Valori costanti associati alla classe |
| Campi       | Variabili della classe |
| Metodi      | Calcoli e azioni che possono essere eseguiti dalla classe |
| Proprietà   | Azioni associate alla lettura e alla scrittura di proprietà denominate della classe |
| Indicizzatori     | Azioni associate all'indicizzazione di istanze della classe, come una matrice |
| Eventi       | Notifiche che possono essere generate dalla classe |
| Operatori    | Conversioni e operatori di espressione supportati dalla classe |
| Costruttori | Azioni necessarie per inizializzare istanze della classe o la classe stessa |
| Distruttori  | Azioni da eseguire prima che istanze della classe vengano eliminate in modo permanente |
| Tipi        | Tipi annidati dichiarati dalla classe |

### <a name="accessibility"></a>Accessibilità

A ogni membro di una classe è associata una caratteristica di accessibilità, che controlla le aree di testo del programma in grado di accedere al membro. Esistono cinque diverse forme di accessibilità, Questi report sono riepilogati nella tabella seguente.


| __Accessibilità__    | __Significato__ |
|----------------------|-----------------|
| `public`             | Accesso non limitato |
| `protected`          | Accesso limitato a questa classe o alle classi derivate da questa classe |
| `internal`           | Accesso limitato a questo programma |
| `protected internal` | Accesso limitato a questo programma o alle classi derivate da questa classe |
| `private`            | Accesso limitato a questa classe |

### <a name="type-parameters"></a>Parametri di tipo

Una definizione di classe può specificare un set di parametri di tipo se si fa seguire il nome della classe da un elenco di nomi di parametri di tipo, racchiuso tra parentesi uncinate. I parametri di tipo possono essere quindi usati nel corpo delle dichiarazioni di classe per definire i membri della classe. Nell'esempio seguente i parametri di tipo di `Pair` sono `TFirst` e `TSecond`:

```csharp
public class Pair<TFirst,TSecond>
{
    public TFirst First;
    public TSecond Second;
}
```
Un tipo di classe dichiarato per accettare parametri di tipo prende il nome di tipo di classe generico. Possono essere generici anche i tipi struct, interfaccia e delegato.

Quando si usa la classe generica, è necessario specificare argomenti di tipo per ogni parametro di tipo:

```csharp
Pair<int,string> pair = new Pair<int,string> { First = 1, Second = "two" };
int i = pair.First;     // TFirst is int
string s = pair.Second; // TSecond is string
```
Un tipo generico con argomenti di tipo forniti, come `Pair<int,string>` sopra, viene definito tipo costruito.

### <a name="base-classes"></a>Classi di base

Una dichiarazione di classe può specificare una classe di base se si fa seguire il nome della classe e i parametri di tipo dai due punti e dal nome della classe di base. L'omissione della specifica della classe di base equivale alla derivazione dal tipo `object`. Nell'esempio seguente la classe di base di `Point3D` è `Point` e quella di `Point` è `object`:

```csharp
public class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

public class Point3D: Point
{
    public int z;

    public Point3D(int x, int y, int z): base(x, y) {
        this.z = z;
    }
}
```
Una classe eredita i membri della relativa classe di base. L'ereditarietà indica che una classe contiene in modo implicito tutti i membri della relativa classe di base, ad eccezione dell'istanza e dei costruttori statici e dei distruttori della classe di base. Una classe derivata può aggiungere nuovi membri a quelli ereditati, ma non può rimuovere la definizione di un membro ereditato. Nell'esempio precedente `Point3D` eredita i campi `x` e `y` da `Point` e ogni istanza di `Point3D` contiene tre campi: `x`, `y` e `z`.

Un tipo di classe viene implicitamente convertito in uno dei relativi tipi di classe di base. Una variabile di un tipo di classe, quindi, può fare riferimento a un'istanza della classe o a un'istanza di una classe derivata. Nel caso delle dichiarazioni di classe precedenti, ad esempio, una variabile di tipo `Point` può fare riferimento a `Point` o `Point3D`:

```csharp
Point a = new Point(10, 20);
Point b = new Point3D(10, 20, 30);
```

### <a name="fields"></a>Campi

Un campo è una variabile associata a una classe o a un'istanza di una classe.

Un campo dichiarato con il `static` modificatore definisce un ***campo statico***. che identifica esattamente una posizione di memoria. Indipendentemente dal numero di istanze di una classe create, esiste una sola copia di un campo statico.

Un campo dichiarato senza il `static` modificatore definisce un ***campo di istanza***. Ogni istanza di una classe contiene una copia separata di tutti i campi di istanza della classe.

Nell'esempio seguente ogni istanza della classe `Color` include una copia distinta dei campi di istanza `r`, `g` e `b`, mentre esiste una sola copia dei campi statici `Black`, `White`, `Red`, `Green` e `Blue`.

```csharp
public class Color
{
    public static readonly Color Black = new Color(0, 0, 0);
    public static readonly Color White = new Color(255, 255, 255);
    public static readonly Color Red = new Color(255, 0, 0);
    public static readonly Color Green = new Color(0, 255, 0);
    public static readonly Color Blue = new Color(0, 0, 255);
    private byte r, g, b;

    public Color(byte r, byte g, byte b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }
}
```
Come illustrato nell'esempio precedente, i ***campi di sola lettura*** possono essere dichiarati con un modificatore `readonly`. L'assegnazione a un `readonly` campo può essere eseguita solo come parte della dichiarazione del campo o in un costruttore della stessa classe.

### <a name="methods"></a>Metodi

Un ***Method** _ è un membro che implementa un calcolo o un'azione che può essere eseguita da un oggetto o da una classe. I _*_metodi statici_*_ sono accessibili tramite la classe. _ I *_metodi di istanza_** sono accessibili tramite istanze della classe.

I metodi hanno un elenco (possibilmente vuoto) di ***parametri** _, che rappresentano valori o riferimenti a variabili passati al metodo, e un _tipo_ _ * restituito * *, che specifica il tipo di valore calcolato e restituito dal metodo. Il tipo restituito di un metodo è `void` se non restituisce alcun valore.

Come i tipi, anche i metodi possono avere un set di parametri di tipo per i quali è necessario specificare argomenti di tipo quando vengono chiamati. A differenza dei tipi, gli argomenti di tipo possono essere spesso dedotti dagli argomenti di una chiamata al metodo e non devono essere assegnati in modo esplicito.

La ***firma*** di un metodo deve essere univoca nell'ambito della classe in cui viene dichiarato il metodo. La firma di un metodo è costituita dal nome del metodo, dal numero di parametri di tipo e dal numero, dai modificatori e dai tipi dei rispettivi parametri. Nella firma di un metodo non è incluso il tipo restituito.

#### <a name="parameters"></a>Parametri

I parametri consentono di passare ai metodi valori o riferimenti a variabili. I parametri di un metodo ottengono i valori effettivi dagli ***argomenti*** specificati quando viene richiamato il metodo. Esistono quattro tipi di parametri: parametri di valore, parametri di riferimento, i parametri di output e matrici di parametri.

Un ***parametro di valore*** consente di passare parametri di input. Corrisponde a una variabile locale che ottiene il valore iniziale dall'argomento passato per il parametro. Eventuali modifiche a un parametro di valore non interessano l'argomento passato per il parametro.

I parametri di valore possono essere facoltativi specificando un valore predefinito. In questo caso gli argomenti corrispondenti possono essere omessi.

Un ***parametro di riferimento*** consente di passare parametri di input e di output. L'argomento passato per un parametro di riferimento deve essere una variabile e, durante l'esecuzione del metodo, il parametro di riferimento rappresenta la stessa posizione di memoria della variabile di argomento. Un parametro di riferimento viene dichiarato con il modificatore `ref`. Nell'esempio seguente viene illustrato l'uso di parametri `ref`.

```csharp
using System;

class Test
{
    static void Swap(ref int x, ref int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    static void Main() {
        int i = 1, j = 2;
        Swap(ref i, ref j);
        Console.WriteLine("{0} {1}", i, j);            // Outputs "2 1"
    }
}
```
Un ***parametro di output*** consente di passare parametri di input. Un parametro di output è simile a un parametro di riferimento, da cui differisce perché il valore iniziale dell'argomento fornito dal chiamante non è importante. Un parametro di output viene dichiarato con il modificatore `out`. Nell'esempio seguente viene illustrato l'uso di parametri `out`.

```csharp
using System;

class Test
{
    static void Divide(int x, int y, out int result, out int remainder) {
        result = x / y;
        remainder = x % y;
    }

    static void Main() {
        int res, rem;
        Divide(10, 3, out res, out rem);
        Console.WriteLine("{0} {1}", res, rem);    // Outputs "3 1"
    }
}
```
Una ***matrice di parametri*** consente di passare un numero variabile di argomenti a un metodo. Una matrice di parametri viene dichiarata con il modificatore `params`. Solo l'ultimo parametro di un metodo può essere costituito da una matrice di parametri, che deve essere sempre di tipo unidimensionale. I `Write` `WriteLine` metodi e della `System.Console` classe sono ottimi esempi di utilizzo delle matrici di parametri. Vengono dichiarati come illustrato di seguito.

```csharp
public class Console
{
    public static void Write(string fmt, params object[] args) {...}
    public static void WriteLine(string fmt, params object[] args) {...}
    ...
}
```
All'interno di un metodo, una matrice di parametri si comporta esattamente come un normale parametro di tipo matrice. In una chiamata di un metodo con una matrice di parametri, tuttavia, è possibile passare un singolo argomento di tipo matrice di parametri oppure un qualsiasi numero di argomenti di tipo elemento della matrice di parametri. Nel secondo caso, un'istanza di matrice viene automaticamente creata e inizializzata con gli argomenti specificati. Questo esempio

```csharp
Console.WriteLine("x={0} y={1} z={2}", x, y, z);
```
è equivalente alla sintassi seguente.

```csharp
string s = "x={0} y={1} z={2}";
object[] args = new object[3];
args[0] = x;
args[1] = y;
args[2] = z;
Console.WriteLine(s, args);
```

#### <a name="method-body-and-local-variables"></a>Corpo del metodo e variabili locali

Il corpo di un metodo specifica le istruzioni da eseguire quando viene richiamato il metodo.

Il corpo di un metodo può dichiarare variabili specifiche per la chiamata del metodo. Queste variabili prendono il nome di ***variabili locali***. Una dichiarazione di variabile locale specifica un nome di tipo, un nome di variabile e possibilmente un valore iniziale. Nell'esempio seguente viene dichiarata una variabile locale `i` con un valore iniziale pari a zero e una variabile locale `j` senza valore iniziale.

```csharp
using System;

class Squares
{
    static void Main() {
        int i = 0;
        int j;
        while (i < 10) {
            j = i * i;
            Console.WriteLine("{0} x {0} = {1}", i, j);
            i = i + 1;
        }
    }
}
```
In C# è necessario ***assegnare esplicitamente*** una variabile locale prima di poterne ottenere il valore. Se ad esempio nella dichiarazione della variabile locale `i` precedente non fosse stato incluso un valore iniziale, il compilatore avrebbe segnalato un errore ogni volta che la variabile `i` veniva usata, perché `i` non era assegnata esplicitamente in quei punti del programma.

Un metodo può usare istruzioni `return` per restituire il controllo al chiamante. In un metodo che restituisce `void`, le istruzioni `return` non possono specificare un'espressione. In un metodo che restituisce non- `void` , `return` le istruzioni devono includere un'espressione che calcola il valore restituito.

#### <a name="static-and-instance-methods"></a>Metodi statici e di istanza

Un metodo dichiarato con un `static` modificatore è un ***metodo statico***. Questo metodo non agisce su un'istanza specifica e può accedere direttamente solo a membri statici.

Un metodo dichiarato senza un `static` modificatore è un ***metodo di istanza***. Questo metodo agisce su un'istanza specifica e può accedere a membri statici e di istanza. L'istanza in cui è stato richiamato un metodo di istanza è accessibile in modo esplicito come `this`. È un errore fare riferimento a `this` in un metodo statico.

La classe `Entity` seguente contiene sia membri statici sia membri di istanza.

```csharp
class Entity
{
    static int nextSerialNo;
    int serialNo;

    public Entity() {
        serialNo = nextSerialNo++;
    }

    public int GetSerialNo() {
        return serialNo;
    }

    public static int GetNextSerialNo() {
        return nextSerialNo;
    }

    public static void SetNextSerialNo(int value) {
        nextSerialNo = value;
    }
}
```
Ogni istanza `Entity` contiene un numero di serie (e probabilmente anche altre informazioni non illustrate qui). Il costruttore `Entity` (simile a un metodo di istanza) inizializza la nuova istanza con il successivo numero di serie disponibile. Poiché il costruttore è un membro di istanza, è consentito accedere sia al campo di istanza `serialNo` sia al campo statico `nextSerialNo`.

I metodi statici `GetNextSerialNo` e `SetNextSerialNo` possono accedere al campo statico `nextSerialNo`, ma si verificherebbe un errore se accedessero direttamente al campo di istanza `serialNo`.

Nell'esempio seguente viene illustrato l'utilizzo della `Entity` classe.

```csharp
using System;

class Test
{
    static void Main() {
        Entity.SetNextSerialNo(1000);
        Entity e1 = new Entity();
        Entity e2 = new Entity();
        Console.WriteLine(e1.GetSerialNo());           // Outputs "1000"
        Console.WriteLine(e2.GetSerialNo());           // Outputs "1001"
        Console.WriteLine(Entity.GetNextSerialNo());   // Outputs "1002"
    }
}
```
Osservare come, mentre i metodi statici `SetNextSerialNo` e `GetNextSerialNo` vengono richiamati sulla classe, il metodo di istanza `GetSerialNo` viene richiamato su istanze della classe.

#### <a name="virtual-override-and-abstract-methods"></a>Metodi virtuali, di override e astratti

Quando una dichiarazione di metodo di istanza include un `virtual` modificatore, il metodo viene definito un ***metodo virtuale** _. Quando non `virtual` è presente alcun modificatore, viene definito un metodo _ *_non virtuale_* *.

Quando viene richiamato un metodo virtuale, il **tipo di runtime*** dell'istanza per cui viene eseguita la chiamata determina l'implementazione del metodo effettivo da richiamare. In una chiamata a un metodo non virtuale, il *_tipo_* _ in fase di compilazione * dell'istanza è il fattore determinante.

Un metodo virtuale può essere ***sottoposto a override*** in una classe derivata. Quando una dichiarazione di metodo di istanza include un `override` modificatore, il metodo esegue l'override di un metodo virtuale ereditato con la stessa firma. Mentre una dichiarazione di metodo virtuale introduce un nuovo metodo, una dichiarazione di metodo di override specializza un metodo virtuale ereditato esistente specificando una nuova implementazione del metodo.

Un metodo ***astratto*** è un metodo virtuale senza implementazione. Un metodo astratto viene dichiarato con il `abstract` modificatore ed è consentito solo in una classe che è anche dichiarata `abstract` . Un metodo astratto deve essere sottoposto a override in ogni classe derivata non astratta.

Nell'esempio seguente viene dichiarata una classe astratta, `Expression`, che rappresenta un nodo dell'albero delle espressioni, e tre classi derivate, `Constant`, `VariableReference` e `Operation`, che implementano i nodi dell'albero delle espressioni relativi a costanti, riferimenti a variabili e operazioni aritmetiche. (Questo è simile a, ma non deve essere confuso con i tipi di albero delle espressioni introdotti nei [tipi di albero delle espressioni](types.md#expression-tree-types)).

```csharp
using System;
using System.Collections;

public abstract class Expression
{
    public abstract double Evaluate(Hashtable vars);
}

public class Constant: Expression
{
    double value;

    public Constant(double value) {
        this.value = value;
    }

    public override double Evaluate(Hashtable vars) {
        return value;
    }
}

public class VariableReference: Expression
{
    string name;

    public VariableReference(string name) {
        this.name = name;
    }

    public override double Evaluate(Hashtable vars) {
        object value = vars[name];
        if (value == null) {
            throw new Exception("Unknown variable: " + name);
        }
        return Convert.ToDouble(value);
    }
}

public class Operation: Expression
{
    Expression left;
    char op;
    Expression right;

    public Operation(Expression left, char op, Expression right) {
        this.left = left;
        this.op = op;
        this.right = right;
    }

    public override double Evaluate(Hashtable vars) {
        double x = left.Evaluate(vars);
        double y = right.Evaluate(vars);
        switch (op) {
            case '+': return x + y;
            case '-': return x - y;
            case '*': return x * y;
            case '/': return x / y;
        }
        throw new Exception("Unknown operator");
    }
}
```
Le quattro classi precedenti possono essere usate per modellare espressioni aritmetiche. Usando istanze di queste classi, l'espressione `x + 3`, ad esempio, può essere rappresentata come illustrato di seguito.

```csharp
Expression e = new Operation(
    new VariableReference("x"),
    '+',
    new Constant(3));
```
Il metodo `Evaluate` di un'istanza `Expression` viene richiamato per valutare l'espressione specificata e generare un valore `double`. Il metodo accetta come argomento un oggetto `Hashtable` che contiene i nomi delle variabili (come chiavi delle voci) e valori (come valori delle voci). Il `Evaluate` metodo è un metodo astratto virtuale, ovvero le classi derivate non astratte devono eseguirne l'override per fornire un'implementazione effettiva.

L'implementazione di un valore `Constant` del metodo `Evaluate` restituisce semplicemente la costante memorizzata. L' `VariableReference` implementazione di un oggetto cerca il nome della variabile in Hashtable e restituisce il valore risultante. L'implementazione di un valore `Operation` valuta prima gli operandi sinistro e destro (richiamando in modo ricorsivo i metodi `Evaluate`) e quindi esegue l'operazione aritmetica specificata.

Il programma seguente usa le classi `Expression` per valutare l'espressione `x * (y + 2)` per valori diversi di `x` e `y`.

```csharp
using System;
using System.Collections;

class Test
{
    static void Main() {
        Expression e = new Operation(
            new VariableReference("x"),
            '*',
            new Operation(
                new VariableReference("y"),
                '+',
                new Constant(2)
            )
        );
        Hashtable vars = new Hashtable();
        vars["x"] = 3;
        vars["y"] = 5;
        Console.WriteLine(e.Evaluate(vars));        // Outputs "21"
        vars["x"] = 1.5;
        vars["y"] = 9;
        Console.WriteLine(e.Evaluate(vars));        // Outputs "16.5"
    }
}
```

#### <a name="method-overloading"></a>Overload di un metodo

Il metodo ***Overload** _ consente a più metodi nella stessa classe di avere lo stesso nome purché dispongano di firme univoche. Quando si compila una chiamata di un metodo di overload, il compilatore USA _ la *_risoluzione dell'overload_** per determinare il metodo specifico da richiamare. La risoluzione dell'overload trova il metodo che meglio corrisponde agli argomenti o segnala un errore se non riesce a trovare alcuna corrispondenza. Nell'esempio seguente viene illustrato il funzionamento effettivo della risoluzione dell'overload. Il commento relativo a ogni chiamata del metodo `Main` mostra il metodo effettivamente richiamato.

```csharp
class Test
{
    static void F() {
        Console.WriteLine("F()");
    }

    static void F(object x) {
        Console.WriteLine("F(object)");
    }

    static void F(int x) {
        Console.WriteLine("F(int)");
    }

    static void F(double x) {
        Console.WriteLine("F(double)");
    }

    static void F<T>(T x) {
        Console.WriteLine("F<T>(T)");
    }

    static void F(double x, double y) {
        Console.WriteLine("F(double, double)");
    }

    static void Main() {
        F();                 // Invokes F()
        F(1);                // Invokes F(int)
        F(1.0);              // Invokes F(double)
        F("abc");            // Invokes F(object)
        F((double)1);        // Invokes F(double)
        F((object)1);        // Invokes F(object)
        F<int>(1);           // Invokes F<T>(T)
        F(1, 1);             // Invokes F(double, double)
    }
}
```
Come illustrato nell'esempio, è sempre possibile selezionare un metodo specifico eseguendo in modo esplicito il cast degli argomenti ai tipi di parametro corretti e/o specificando in modo esplicito gli argomenti di tipo.

### <a name="other-function-members"></a>Altri membri di funzione

I membri che contengono codice eseguibile sono noti come ***membri funzione*** di una classe. Nella sezione precedente sono stati descritti i metodi, che costituiscono i membri di funzione principali. Questa sezione descrive gli altri tipi di membri di funzione supportati da C#: costruttori, proprietà, indicizzatori, eventi, operatori e distruttori.

Nel codice seguente viene illustrata una classe generica denominata `List<T>` , che implementa un elenco di oggetti espandibile. Nella classe sono contenuti alcuni esempi di membri di funzione più comuni.


```csharp
public class List<T> {
    // Constant...
    const int defaultCapacity = 4;

    // Fields...
    T[] items;
    int count;

    // Constructors...
    public List(int capacity = defaultCapacity) {
        items = new T[capacity];
    }

    // Properties...
    public int Count {
        get { return count; }
    }
    public int Capacity {
        get {
            return items.Length;
        }
        set {
            if (value < count) value = count;
            if (value != items.Length) {
                T[] newItems = new T[value];
                Array.Copy(items, 0, newItems, 0, count);
                items = newItems;
            }
        }
    }

    // Indexer...
    public T this[int index] {
        get {
            return items[index];
        }
        set {
            items[index] = value;
            OnChanged();
        }
    }

    // Methods...
    public void Add(T item) {
        if (count == Capacity) Capacity = count * 2;
        items[count] = item;
        count++;
        OnChanged();
    }
    protected virtual void OnChanged() {
        if (Changed != null) Changed(this, EventArgs.Empty);
    }
    public override bool Equals(object other) {
        return Equals(this, other as List<T>);
    }
    static bool Equals(List<T> a, List<T> b) {
        if (a == null) return b == null;
        if (b == null || a.count != b.count) return false;
        for (int i = 0; i < a.count; i++) {
            if (!object.Equals(a.items[i], b.items[i])) {
                return false;
            }
        }
        return true;
    }

    // Event...
    public event EventHandler Changed;

    // Operators...
    public static bool operator ==(List<T> a, List<T> b) {
        return Equals(a, b);
    }
    public static bool operator !=(List<T> a, List<T> b) {
        return !Equals(a, b);
    }
}
```

#### <a name="constructors"></a>Costruttori

C# supporta sia costruttori di istanza sia costruttori statici. Un ***costruttore di istanza** _ è un membro che implementa le azioni necessarie per inizializzare un'istanza di una classe. Un *_costruttore statico_** è un membro che implementa le azioni necessarie per inizializzare una classe quando viene caricata per la prima volta.

Un costruttore viene dichiarato come un metodo, senza tipo restituito e con lo stesso nome della classe in cui è contenuto. Se una dichiarazione di costruttore include un `static` modificatore, dichiara un costruttore statico. In caso contrario, dichiara un costruttore di istanza.

È possibile eseguire l'overload di costruttori di istanze. La classe `List<T>`, ad esempio, dichiara due costruttori di istanza, uno senza parametri e uno che accetta un parametro `int`. I costruttori di istanza vengono richiamati con l'operatore `new`. Nelle istruzioni seguenti vengono allocate due `List<string>` istanze di utilizzando ciascuno dei costruttori della `List` classe.

```csharp
List<string> list1 = new List<string>();
List<string> list2 = new List<string>(10);
```
A differenza di altri membri, i costruttori di istanza non vengono ereditati e una classe non può contenere costruttori di istanza diversi da quelli effettivamente dichiarati nella classe. Se per una classe non è specificato alcun costruttore di istanza, ne viene automaticamente fornito uno vuoto senza parametri.

#### <a name="properties"></a>Proprietà

***Properties** _ sono un'estensione naturale dei campi. Entrambi sono membri denominati con tipi associati e la sintassi per accedere ai campi e alle proprietà è identica. A differenza dei campi, tuttavia, le proprietà non denotano posizioni di memoria, Le proprietà hanno invece _ *_funzioni di accesso_** che specificano le istruzioni da eseguire quando i relativi valori vengono letti o scritti.

Una proprietà viene dichiarata come un campo, ad eccezione del fatto che la dichiarazione termina con una `get` funzione di accesso e/o una `set` funzione di accesso scritta tra i delimitatori `{` e `}` anziché terminare con un punto e virgola. Una proprietà con una funzione di `get` accesso e una `set` funzione di accesso è una **proprietà di lettura/scrittura** _, una proprietà che ha solo una `get` funzione di accesso è una _*_proprietà di sola lettura_*_ e una proprietà che ha solo una funzione di `set` accesso è una proprietà _ * di _sola scrittura_* *.

Una `get` funzione di accesso corrisponde a un metodo senza parametri con un valore restituito del tipo di proprietà. Fatta eccezione per la destinazione di un'assegnazione, quando si fa riferimento a una proprietà in un'espressione, `get` viene richiamata la funzione di accesso della proprietà per calcolare il valore della proprietà.

Una `set` funzione di accesso corrisponde a un metodo con un solo parametro denominato `value` e nessun tipo restituito. Quando si fa riferimento a una proprietà come destinazione di un'assegnazione o come operando di `++` o `--` , la `set` funzione di accesso viene richiamata con un argomento che fornisce il nuovo valore.

La classe `List<T>` dichiara due proprietà, `Count` e `Capacity`, che sono rispettivamente di sola lettura e di lettura/scrittura. Di seguito è riportato un esempio d'uso di queste proprietà.

```csharp
List<string> names = new List<string>();
names.Capacity = 100;            // Invokes set accessor
int i = names.Count;             // Invokes get accessor
int j = names.Capacity;          // Invokes get accessor
```
Come per i campi e i metodi, C# supporta sia proprietà di istanza sia proprietà statiche. Le proprietà statiche vengono dichiarate con il `static` modificatore e le proprietà dell'istanza vengono dichiarate senza di essa.

Le funzioni di accesso di una proprietà possono essere virtuali. Se una dichiarazione di proprietà contiene un modificatore `virtual`, `abstract` o `override`, questo viene applicato anche alle funzioni di accesso della proprietà.

#### <a name="indexers"></a>Indicizzatori

Un ***indicizzatore*** è un membro che consente di indicizzare gli oggetti esattamente come una matrice. Un indicizzatore viene dichiarato come una proprietà, ma a differenza di questa il nome del membro è `this` seguito da un elenco di parametri scritti tra i delimitatori `[` e `]`. I parametri sono disponibili nelle funzioni di accesso dell'indicizzatore. Analogamente alle proprietà, gli indicizzatori possono essere di lettura/scrittura, di sola lettura o di sola scrittura e le funzioni di accesso di un indicizzatore possono essere virtuali.

La classe `List` dichiara un indicizzatore di lettura/scrittura che accetta un parametro `int` e consente di indicizzare istanze `List` con valori `int`. Ad esempio:

```csharp
List<string> names = new List<string>();
names.Add("Liz");
names.Add("Martha");
names.Add("Beth");
for (int i = 0; i < names.Count; i++) {
    string s = names[i];
    names[i] = s.ToUpper();
}
```
Gli indicizzatori possono essere sottoposti a overload, ovvero una classe può dichiarare più indicizzatori purché includano un numero o tipi diversi di parametri.

#### <a name="events"></a>Eventi

Un ***evento*** è un membro che consente a una classe o a un oggetto di inviare notifiche. Un evento viene dichiarato come un campo, ad eccezione del fatto che la dichiarazione include una `event` parola chiave e il tipo deve essere un tipo delegato.

In una classe che dichiara un membro di evento, l'evento si comporta esattamente come un campo di un tipo delegato, purché l'evento non sia astratto e non dichiari funzioni di accesso. Il campo archivia un riferimento a un delegato che rappresenta i gestori eventi aggiunti all'evento. Se non sono presenti handle di evento, il campo è `null` .

La classe `List<T>` dichiara un singolo membro di evento denominato `Changed`, con cui si indica che un nuovo elemento è stato aggiunto all'elenco. L' `Changed` evento viene generato dal `OnChanged` metodo virtuale, che verifica prima di tutto se l'evento è `null` (ovvero non è presente alcun gestore). Generare un evento equivale a richiamare il delegato rappresentato dall'evento. Non sono quindi necessari speciali costrutti di linguaggio per generare eventi.

I client rispondono agli eventi tramite ***gestori eventi***, che possono essere aggiunti con l'operatore `+=` e rimossi con l'operatore `-=`. Nell'esempio seguente un gestore eventi viene aggiunto all'evento `Changed` di `List<string>`.

```csharp
using System;

class Test
{
    static int changeCount;

    static void ListChanged(object sender, EventArgs e) {
        changeCount++;
    }

    static void Main() {
        List<string> names = new List<string>();
        names.Changed += new EventHandler(ListChanged);
        names.Add("Liz");
        names.Add("Martha");
        names.Add("Beth");
        Console.WriteLine(changeCount);        // Outputs "3"
    }
}
```
Negli scenari avanzati in cui è auspicabile controllare l'archiviazione sottostante di un evento, una dichiarazione di evento può fornire in modo esplicito le funzioni di accesso `add` e `remove`, simili alla funzione di accesso `set` di una proprietà.

#### <a name="operators"></a>Operatori

Un ***operatore*** è un membro che definisce il significato dell'applicazione di un particolare operatore di espressione alle istanze di una classe. È possibile definire tre tipi di operatori: unari, binari e di conversione. Tutti gli operatori devono essere dichiarati come `public` e `static`.

La classe `List<T>` dichiara due operatori, `operator==` e `operator!=`, attribuendo così un nuovo significato alle espressioni che applicano questi operatori alle istanze di `List`. In particolare, gli operatori definiscono l'uguaglianza di due `List<T>` istanze di confrontando ognuno degli oggetti contenuti usando i relativi `Equals` metodi. Nell'esempio seguente viene usato l'operatore `==` per confrontare due istanze di `List<int>`.

```csharp
using System;

class Test
{
    static void Main() {
        List<int> a = new List<int>();
        a.Add(1);
        a.Add(2);
        List<int> b = new List<int>();
        b.Add(1);
        b.Add(2);
        Console.WriteLine(a == b);        // Outputs "True"
        b.Add(3);
        Console.WriteLine(a == b);        // Outputs "False"
    }
}
```

Il primo `Console.WriteLine` restituisce `True` perché i due elenchi contengono lo stesso numero di oggetti con gli stessi valori e nello stesso ordine. Se in `List<T>` non fosse stato definito l'operatore `operator==`, il primo `Console.WriteLine` avrebbe restituito `False` perché `a` e `b` fanno riferimento a istanze di `List<int>` diverse.

#### <a name="destructors"></a>Distruttori

Un ***distruttore*** è un membro che implementa le azioni necessarie per distruggere un'istanza di una classe. I distruttori non possono avere parametri, non possono avere modificatori di accessibilità e non possono essere richiamati in modo esplicito. Il distruttore di un'istanza viene richiamato automaticamente durante Garbage Collection.

Per la Garbage Collector è consentita una latitudine ampia per decidere quando raccogliere oggetti ed eseguire distruttori. In particolare, la tempistica delle chiamate del distruttore non è deterministica e i distruttori possono essere eseguiti su qualsiasi thread. Per questi e altri motivi, le classi devono implementare i distruttori solo quando non sono realizzabili altre soluzioni.

L'istruzione `using` offre una soluzione più efficace per l'eliminazione di oggetti.

## <a name="structs"></a>Struct

Analogamente alle classi, i tipi ***struct*** sono strutture dati che possono contenere membri dati e membri funzione. A differenza delle classi, tuttavia, i tipi struct sono tipi valore e non richiedono l'allocazione dell'heap. Una variabile di un tipo struct archivia direttamente i relativi dati, mentre una variabile di un tipo classe archivia un riferimento a un oggetto allocato in modo dinamico. I tipi struct non supportano l'ereditarietà specificata dall'utente. Tutti i tipi struct ereditano implicitamente dal tipo `object`.

I tipi struct sono particolarmente utili per strutture dati di piccole dimensioni che hanno una semantica di valori. I numeri complessi, i punti di un sistema di coordinate o le coppie chiave-valore di un dizionario sono buoni esempi di struct. L'uso dei tipi struct al posto delle classi può determinare una notevole differenza riguardo al numero di allocazioni di memoria eseguite da un'applicazione. Il programma seguente, ad esempio, crea e inizializza una matrice di 100 punti. Con `Point` implementato come classe, vengono create istanze di 101 oggetti separati, uno per la matrice e uno per ciascuno dei 100 elementi.

```csharp
class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Test
{
    static void Main() {
        Point[] points = new Point[100];
        for (int i = 0; i < 100; i++) points[i] = new Point(i, i);
    }
}
```
In alternativa, è possibile creare `Point` uno struct.

```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
Viene ora creata un'istanza di un solo oggetto, quello relativo alla matrice, e le istanze di `Point` vengono memorizzate inline nella matrice.

Con l'operatore `new` vengono chiamati i costruttori di struct, ma questo non implica l'allocazione di memoria. Anziché allocare dinamicamente un oggetto e restituire un riferimento a quest'ultimo, un costruttore di struct restituisce semplicemente il valore del tipo struct (in genere una posizione temporanea nello stack), che viene quindi copiato in base alle esigenze.

Con le classi, due variabili possono fare riferimento allo stesso oggetto e pertanto le operazioni su una variabile possono influire sull'oggetto a cui fa riferimento l'altra. Con i tipi struct, ogni variabile ha una propria copia dei dati e le operazioni su una variabile non possono influire sull'altra. L'output prodotto dal frammento di codice seguente, ad esempio, dipende dalla presenza di `Point` una classe o di uno struct.

```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 20;
Console.WriteLine(b.x);
```
Se `Point` è una classe, l'output è `20` perché `a` e `b` fanno riferimento allo stesso oggetto. Se `Point` è uno struct, l'output è `10` perché l'assegnazione di `a` a `b` Crea una copia del valore e questa copia non è interessata dall'assegnazione successiva a `a.x` .

L'esempio precedente evidenzia due delle limitazioni dei tipi struct. In primo luogo, la copia di un intero tipo struct è in genere meno efficiente della copia di un riferimento all'oggetto. Di conseguenza, il passaggio dei parametri di assegnazione e valore può risultare molto più costoso con i tipi struct che con i tipi riferimento. In secondo luogo, ad eccezione dei parametri `ref` e `out`, non è possibile creare riferimenti ai tipi struct e questa condizione che ne limita l'uso in varie situazioni.

## <a name="arrays"></a>Matrici

***Array** _ è una struttura di dati che contiene un numero di variabili a cui si accede tramite indici calcolati. Le variabili contenute in una matrice, denominate anche _*_elementi_*_ della matrice, sono tutti dello stesso tipo e questo tipo è denominato _ *_tipo di elemento_** della matrice.

Poiché i tipi di matrice sono tipi di riferimento, la dichiarazione di una variabile di matrice si limita a riservare spazio per un riferimento a un'istanza di matrice. Le istanze di matrice effettive vengono create dinamicamente in fase di esecuzione usando l' `new` operatore. L' `new` operazione specifica la ***lunghezza*** della nuova istanza della matrice, che viene quindi corretta per la durata dell'istanza. Gli indici degli elementi di una matrice sono compresi tra `0` e `Length - 1`. L'operatore `new` inizializza automaticamente gli elementi di una matrice sul rispettivo valore predefinito che, ad esempio, equivale a zero per tutti i tipi numerici e a `null` per tutti i tipi di riferimento.

Nell'esempio seguente viene creata una matrice di elementi `int`, viene inizializzata la matrice e ne viene stampato il contenuto.

```csharp
using System;

class Test
{
    static void Main() {
        int[] a = new int[10];
        for (int i = 0; i < a.Length; i++) {
            a[i] = i * i;
        }
        for (int i = 0; i < a.Length; i++) {
            Console.WriteLine("a[{0}] = {1}", i, a[i]);
        }
    }
}
```
Questo esempio crea e opera su un ***Array unidimensionale** _. C# supporta anche _*_matrici multidimensionali_*_. Il numero di dimensioni di un tipo di matrice, noto anche come _ *_Rank_** del tipo di matrice, è uno più il numero di virgole scritte tra le parentesi quadre del tipo di matrice. Nell'esempio seguente vengono allocate una matrice unidimensionale, bidimensionale e tridimensionale.

```csharp
int[] a1 = new int[10];
int[,] a2 = new int[10, 5];
int[,,] a3 = new int[10, 5, 2];
```
La matrice `a1` contiene 10 elementi, la matrice `a2` contiene 50 (10 × 5) elementi e la `a3` matrice contiene 100 (10 × 5 × 2) elementi.

L'elemento di una matrice può essere di qualsiasi tipo, anche di tipo matrice. Una matrice con elementi di tipo matrice viene chiamata talvolta ***matrice di matrici***, poiché le lunghezze delle matrici elemento non devono essere tutte uguali. Nell'esempio seguente viene allocata una matrice di matrici di `int`:

```csharp
int[][] a = new int[3][];
a[0] = new int[10];
a[1] = new int[5];
a[2] = new int[20];
```
La prima riga crea una matrice con tre elementi, ognuno di tipo `int[]` e con un valore iniziale di `null`. Le righe successive inizializzano quindi i tre elementi con riferimenti a singole istanze di matrice di lunghezza variabile.

L' `new` operatore consente di specificare i valori iniziali degli elementi della matrice utilizzando un ***inizializzatore di matrice***, ovvero un elenco di espressioni scritte tra i delimitatori `{` e `}` . Nell'esempio seguente viene allocata e inizializzata una matrice `int[]` con tre elementi.

```csharp
int[] a = new int[] {1, 2, 3};
```
Si noti che la lunghezza della matrice viene dedotta dal numero di espressioni tra `{` e `}` . Per evitare di ridefinire il tipo di matrice, è possibile abbreviare le dichiarazioni di campo e variabile locale.

```csharp
int[] a = {1, 2, 3};
```
Entrambi gli esempi precedenti equivalgono a quello seguente:

```csharp
int[] t = new int[3];
t[0] = 1;
t[1] = 2;
t[2] = 3;
int[] a = t;
```
## <a name="interfaces"></a>Interfacce

Un'***interfaccia*** definisce un contratto che può essere implementato da classi e struct. Può contenere metodi, proprietà, eventi e indicizzatori. Un'interfaccia non fornisce le implementazioni dei membri che definisce, ma specifica semplicemente i membri che devono essere forniti dalle classi o dai tipi struct che la implementano.

Le interfacce possono usare l'***ereditarietà multipla***. Nell'esempio seguente l'interfaccia `IComboBox` eredita da `ITextBox` e `IListBox`.

```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

interface IComboBox: ITextBox, IListBox {}
```
Classi e struct possono implementare più interfacce. Nell'esempio seguente la classe `EditBox` implementa `IControl` e `IDataBound`.

```csharp
interface IDataBound
{
    void Bind(Binder b);
}

public class EditBox: IControl, IDataBound
{
    public void Paint() {...}
    public void Bind(Binder b) {...}
}
```
Quando una classe o un tipo struct implementa un'interfaccia specifica, le istanze di tale classe o struct possono essere convertite in modo implicito in quel tipo di interfaccia. Ad esempio:

```csharp
EditBox editBox = new EditBox();
IControl control = editBox;
IDataBound dataBound = editBox;
```
Nei casi in cui un'istanza non implementa staticamente un'interfaccia specifica è possibile usare cast di tipo dinamico. Ad esempio, le istruzioni seguenti usano i cast di tipo dinamico per ottenere le `IControl` implementazioni dell'interfaccia e dell'oggetto `IDataBound` . Poiché il tipo effettivo dell'oggetto è `EditBox` , il cast ha esito positivo.

```csharp
object obj = new EditBox();
IControl control = (IControl)obj;
IDataBound dataBound = (IDataBound)obj;
```
Nella classe precedente `EditBox` , il `Paint` metodo dell' `IControl` interfaccia e il metodo dell' `Bind` `IDataBound` interfaccia vengono implementati usando `public` i membri. C# supporta anche ***implementazioni esplicite di membri di interfaccia***, usando le quali la classe o lo struct può evitare di creare membri `public` . Un'implementazione esplicita di un membro dell'interfaccia viene scritta usando il nome completo del membro dell'interfaccia. La classe `EditBox` può ad esempio implementare i metodi `IControl.Paint` e `IDataBound.Bind` usando implementazioni esplicite di un membro dell'interfaccia, come indicato di seguito.

```csharp
public class EditBox: IControl, IDataBound
{
    void IControl.Paint() {...}
    void IDataBound.Bind(Binder b) {...}
}
```
È possibile accedere ai membri dell'interfaccia esplicita solo tramite il tipo di interfaccia. Ad esempio, l'implementazione di `IControl.Paint` fornita dalla classe precedente `EditBox` può essere richiamata solo convertendo prima il `EditBox` riferimento al `IControl` tipo di interfaccia.

```csharp
EditBox editBox = new EditBox();
editBox.Paint();                        // Error, no such method
IControl control = editBox;
control.Paint();                        // Ok
```

## <a name="enums"></a>Enumerazioni

Un ***tipo enum*** è un tipo valore distinto con un set di costanti denominate. Nell'esempio seguente viene dichiarato e usato un tipo enum denominato `Color` con tre valori costanti, `Red` , `Green` e `Blue` .

```csharp
using System;

enum Color
{
    Red,
    Green,
    Blue
}

class Test
{
    static void PrintColor(Color color) {
        switch (color) {
            case Color.Red:
                Console.WriteLine("Red");
                break;
            case Color.Green:
                Console.WriteLine("Green");
                break;
            case Color.Blue:
                Console.WriteLine("Blue");
                break;
            default:
                Console.WriteLine("Unknown color");
                break;
        }
    }

    static void Main() {
        Color c = Color.Red;
        PrintColor(c);
        PrintColor(Color.Blue);
    }
}
```
Ogni tipo di enumerazione ha un tipo integrale corrispondente denominato ***tipo sottostante*** del tipo enum. Un tipo enum che non dichiara in modo esplicito un tipo sottostante ha un tipo sottostante di `int` . Il formato di archiviazione e l'intervallo di valori possibili di un tipo enum sono determinati dal tipo sottostante. Il set di valori che un tipo enum può assumere non è limitato dai relativi membri enum. In particolare, è possibile eseguire il cast di qualsiasi valore del tipo sottostante di un'enumerazione al tipo enum ed è un valore valido distinto di tale tipo enum.

Nell'esempio seguente viene dichiarato un tipo enum denominato `Alignment` con un tipo sottostante di `sbyte` .

```csharp
enum Alignment: sbyte
{
    Left = -1,
    Center = 0,
    Right = 1
}
```
Come illustrato nell'esempio precedente, una dichiarazione di membro enum può includere un'espressione costante che specifica il valore del membro. Il valore costante per ogni membro enum deve essere compreso nell'intervallo del tipo sottostante dell'enumerazione. Quando una dichiarazione di un membro enum non specifica in modo esplicito un valore, al membro viene assegnato il valore zero (se è il primo membro del tipo enum) o il valore del membro enum testuale precedente, più uno.

I valori enum possono essere convertiti in valori integrali e viceversa usando i cast di tipo. Ad esempio:

```csharp
int i = (int)Color.Blue;        // int i = 2;
Color c = (Color)2;             // Color c = Color.Blue;
```
Il valore predefinito di qualsiasi tipo enum è il valore integrale zero convertito nel tipo enum. Nei casi in cui le variabili vengono inizializzate automaticamente su un valore predefinito, si tratta del valore assegnato alle variabili dei tipi enum. Affinché il valore predefinito di un tipo enum sia facilmente disponibile, il valore letterale viene `0` convertito in modo implicito in qualsiasi tipo enum. Di conseguenza, quanto riportato di seguito è consentito.

```csharp
Color c = 0;
```

## <a name="delegates"></a>Delegati

Un ***tipo delegato*** rappresenta riferimenti ai metodi con un elenco di parametri e un tipo restituito particolari. I delegati consentono di trattare i metodi come entità che è possibile assegnare a variabili e passare come parametri. I delegati sono simili al concetto di puntatori a funzione disponibili in altri linguaggi. A differenza dei puntatori a funzione, tuttavia, i delegati sono orientati agli oggetti e indipendenti dai tipi.

Nell'esempio seguente viene dichiarato e usato un tipo delegato denominato `Function`.

```csharp
using System;

delegate double Function(double x);

class Multiplier
{
    double factor;

    public Multiplier(double factor) {
        this.factor = factor;
    }

    public double Multiply(double x) {
        return x * factor;
    }
}

class Test
{
    static double Square(double x) {
        return x * x;
    }

    static double[] Apply(double[] a, Function f) {
        double[] result = new double[a.Length];
        for (int i = 0; i < a.Length; i++) result[i] = f(a[i]);
        return result;
    }

    static void Main() {
        double[] a = {0.0, 0.5, 1.0};
        double[] squares = Apply(a, Square);
        double[] sines = Apply(a, Math.Sin);
        Multiplier m = new Multiplier(2.0);
        double[] doubles =  Apply(a, m.Multiply);
    }
}
```
Un'istanza del tipo delegato `Function` può fare riferimento a qualsiasi metodo che accetta un argomento `double` e restituisce un valore `double`. Il `Apply` metodo applica un oggetto specificato `Function` agli elementi di un oggetto `double[]` , restituendo un oggetto `double[]` con i risultati. Nel metodo `Main`, `Apply` viene usato per applicare a `double[]` tre funzioni differenti.

Un delegato può fare riferimento a un metodo statico, come `Square` o `Math.Sin` nell'esempio precedente, o a un metodo di istanza, come `m.Multiply` nell'esempio precedente. Un delegato che fa riferimento a un metodo di istanza fa riferimento anche a un oggetto particolare. Quando il metodo di istanza viene richiamato tramite il delegato, l'oggetto diventa `this` nella chiamata.

I delegati possono essere creati anche mediante funzioni anonime, ovvero "metodi inline" creati in tempo reale. Le funzioni anonime possono vedere le variabili locali dei metodi circostanti. Quindi, l'esempio precedente può essere scritto più facilmente senza usare una `Multiplier` classe:

```csharp
double[] doubles =  Apply(a, (double x) => x * 2.0);
```
Una proprietà interessante e utile di un delegato consiste nel fatto che non conosce o non prende in considerazione la classe del metodo a cui fa riferimento. Ciò che conta è che il metodo a cui un delegato fa riferimento abbia gli stessi parametri e restituisca lo stesso tipo del delegato in questione.

## <a name="attributes"></a>Attributi

Tipi, membri e altre entità di un programma C# supportano modificatori che controllano alcuni aspetti del loro comportamento. L'accessibilità di un metodo, ad esempio, è controllata con i modificatori `public`, `protected`, `internal` e `private`. Il linguaggio C# generalizza questa funzionalità in modo che i tipi di informazioni dichiarative definiti dall'utente possano essere associati a entità di programma e recuperati in fase di esecuzione. I programmi specificano queste informazioni dichiarative aggiuntive definendo e usando ***attributi***.

Nell'esempio seguente viene dichiarato un attributo `HelpAttribute` che può essere inserito in entità di programma per fornire collegamenti alla documentazione correlata.

```csharp
using System;

public class HelpAttribute: Attribute
{
    string url;
    string topic;

    public HelpAttribute(string url) {
        this.url = url;
    }

    public string Url {
        get { return url; }
    }

    public string Topic {
        get { return topic; }
        set { topic = value; }
    }
}
```
Tutte le classi di attributi derivano dalla `System.Attribute` classe di base fornita dal .NET Framework. Gli attributi possono essere applicati specificandone il nome (con eventuali argomenti) tra parentesi quadre appena prima della dichiarazione associata. Se il nome di un attributo termina in `Attribute` , tale parte del nome può essere omessa quando si fa riferimento all'attributo. L'attributo `HelpAttribute`, ad esempio, può essere usato come illustrato di seguito.

```csharp
[Help("http://msdn.microsoft.com/.../MyClass.htm")]
public class Widget
{
    [Help("http://msdn.microsoft.com/.../MyClass.htm", Topic = "Display")]
    public void Display(string text) {}
}
```
Questo esempio connette un oggetto `HelpAttribute` alla `Widget` classe e un altro `HelpAttribute` al `Display` metodo nella classe. I costruttori pubblici di una classe di attributo controllano le informazioni che devono essere specificate quando l'attributo è associato a un'entità di programma. È possibile specificare informazioni aggiuntive facendo riferimento a proprietà di lettura/scrittura pubbliche della classe di attributo, ad esempio il riferimento alla proprietà `Topic` precedente.

Nell'esempio seguente viene illustrato come recuperare le informazioni sugli attributi per un'entità programma specificata in fase di esecuzione tramite reflection.

```csharp
using System;
using System.Reflection;

class Test
{
    static void ShowHelp(MemberInfo member) {
        HelpAttribute a = Attribute.GetCustomAttribute(member,
            typeof(HelpAttribute)) as HelpAttribute;
        if (a == null) {
            Console.WriteLine("No help for {0}", member);
        }
        else {
            Console.WriteLine("Help for {0}:", member);
            Console.WriteLine("  Url={0}, Topic={1}", a.Url, a.Topic);
        }
    }

    static void Main() {
        ShowHelp(typeof(Widget));
        ShowHelp(typeof(Widget).GetMethod("Display"));
    }
}
```
Se un attributo viene richiesto tramite reflection, il costruttore della classe di attributo viene richiamato con le informazioni specificate nell'origine del programma e viene restituita l'istanza dell'attributo risultante. Se sono state fornite informazioni aggiuntive tramite proprietà, queste vengono impostate sui valori specificati prima che venga restituita l'istanza dell'attributo.
