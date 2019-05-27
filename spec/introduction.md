---
ms.openlocfilehash: 201db57d243c9d0e22553366bc653d02e183aa4b
ms.sourcegitcommit: 09e0ddec3bb6aa99b7340158bbac86a5a8243b43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/24/2019
ms.locfileid: "66193867"
---
# <a name="introduction"></a><span data-ttu-id="99b6e-101">Introduzione</span><span class="sxs-lookup"><span data-stu-id="99b6e-101">Introduction</span></span>

<span data-ttu-id="99b6e-102">C#, pronunciato "See Sharp", è un linguaggio di programmazione semplice, moderno, orientato a oggetti e indipendente dai tipi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-102">C# (pronounced "See Sharp") is a simple, modern, object-oriented, and type-safe programming language.</span></span> <span data-ttu-id="99b6e-103">C# ha le sue radici nella famiglia di linguaggi C e risulterà immediatamente familiare ai programmatori di C, C++ e Java.</span><span class="sxs-lookup"><span data-stu-id="99b6e-103">C# has its roots in the C family of languages and will be immediately familiar to C, C++, and Java programmers.</span></span> <span data-ttu-id="99b6e-104">C# è standardizzato di ECMA International come le ***ECMA-334*** standard e da ISO/IEC come le ***ISO/IEC 23270*** standard.</span><span class="sxs-lookup"><span data-stu-id="99b6e-104">C# is standardized by ECMA International as the ***ECMA-334*** standard and by ISO/IEC as the ***ISO/IEC 23270*** standard.</span></span> <span data-ttu-id="99b6e-105">Compilatore c# di Microsoft per .NET Framework è un'implementazione conforme della entrambi questi standard.</span><span class="sxs-lookup"><span data-stu-id="99b6e-105">Microsoft's C# compiler for the .NET Framework is a conforming implementation of both of these standards.</span></span>

<span data-ttu-id="99b6e-106">C# è un linguaggio orientato a oggetti, ma include anche il supporto per la programmazione ***orientata ai componenti***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-106">C# is an object-oriented language, but C# further includes support for ***component-oriented*** programming.</span></span> <span data-ttu-id="99b6e-107">La progettazione software contemporanea è basata in misura sempre maggiore su componenti software costituiti da pacchetti di funzionalità autonomi e autodescrittivi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-107">Contemporary software design increasingly relies on software components in the form of self-contained and self-describing packages of functionality.</span></span> <span data-ttu-id="99b6e-108">L'aspetto chiave di tali componenti è che presentano un modello di programmazione con proprietà, metodi ed eventi. Presentano inoltre attributi che forniscono informazioni dichiarative sul componente. Questi componenti, infine, includono la propria documentazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-108">Key to such components is that they present a programming model with properties, methods, and events; they have attributes that provide declarative information about the component; and they incorporate their own documentation.</span></span> <span data-ttu-id="99b6e-109">C# offre costrutti di linguaggio per supportare direttamente questi concetti, rendendo c# un linguaggio estremamente naturale in cui si desidera creare e usare i componenti software.</span><span class="sxs-lookup"><span data-stu-id="99b6e-109">C# provides language constructs to directly support these concepts, making C# a very natural language in which to create and use software components.</span></span>

<span data-ttu-id="99b6e-110">Diverse funzionalità C# offrono un valido aiuto per la creazione di applicazioni affidabili e durevoli: ***Operazione di Garbage collection*** recupera automaticamente la memoria occupata dagli oggetti inutilizzati. ***la gestione delle eccezioni*** offre un approccio strutturato ed estendibile al rilevamento degli errori e il ripristino; e il ***indipendente dai tipi*** progettazione del linguaggio rende impossibili la lettura da variabili non inizializzate, per indicizzare matrici oltre i limiti o per eseguire unchecked cast di tipo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-110">Several C# features aid in the construction of robust and durable applications: ***Garbage collection*** automatically reclaims memory occupied by unused objects; ***exception handling*** provides a structured and extensible approach to error detection and recovery; and the ***type-safe*** design of the language makes it impossible to read from uninitialized variables, to index arrays beyond their bounds, or to perform unchecked type casts.</span></span>

<span data-ttu-id="99b6e-111">C# presenta un ***sistema di tipi unificato***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-111">C# has a ***unified type system***.</span></span> <span data-ttu-id="99b6e-112">Tutti i tipi C#, inclusi i tipi di primitiva quali `int` e `double`, ereditano da un unico tipo `object` radice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-112">All C# types, including primitive types such as `int` and `double`, inherit from a single root `object` type.</span></span> <span data-ttu-id="99b6e-113">Di conseguenza, tutti i tipi condividono un set di operazioni comuni e i valori dei diversi tipi possono essere archiviati, trasportati e gestiti in modo coerente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-113">Thus, all types share a set of common operations, and values of any type can be stored, transported, and operated upon in a consistent manner.</span></span> <span data-ttu-id="99b6e-114">C#, inoltre, supporta sia i tipi riferimento sia i tipi valore definiti dall'utente, consentendo l'allocazione dinamica di oggetti e l'archiviazione inline di strutture leggere.</span><span class="sxs-lookup"><span data-stu-id="99b6e-114">Furthermore, C# supports both user-defined reference types and value types, allowing dynamic allocation of objects as well as in-line storage of lightweight structures.</span></span>

<span data-ttu-id="99b6e-115">Per garantire che i programmi c# e le librerie possono evolversi nel tempo, in modo compatibile, particolare attenzione è stato inserito nella ***controllo delle versioni*** nella progettazione di #.</span><span class="sxs-lookup"><span data-stu-id="99b6e-115">To ensure that C# programs and libraries can evolve over time in a compatible manner, much emphasis has been placed on ***versioning*** in C#'s design.</span></span> <span data-ttu-id="99b6e-116">Molti linguaggi di programmazione prestano scarsa attenzione a questo aspetto e, di conseguenza, i programmi scritti in tali linguaggi si interrompono molto più spesso del necessario quando vengono introdotte nuove versioni delle librerie dipendenti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-116">Many programming languages pay little attention to this issue, and, as a result, programs written in those languages break more often than necessary when newer versions of dependent libraries are introduced.</span></span> <span data-ttu-id="99b6e-117">Gli aspetti di C#di progettazione che direttamente interessati dalle considerazioni sul controllo delle versioni includono distinte `virtual` e `override` modificatori, le regole per la risoluzione dell'overload di metodo e il supporto per le dichiarazioni di membro di interfaccia esplicita.</span><span class="sxs-lookup"><span data-stu-id="99b6e-117">Aspects of C#'s design that were directly influenced by versioning considerations include the separate `virtual` and `override` modifiers, the rules for method overload resolution, and support for explicit interface member declarations.</span></span>

<span data-ttu-id="99b6e-118">Il resto di questo capitolo vengono descritte le funzionalità essenziali del linguaggio c#.</span><span class="sxs-lookup"><span data-stu-id="99b6e-118">The rest of this chapter describes the essential features of the C# language.</span></span> <span data-ttu-id="99b6e-119">Anche se negli ultimi capitoli descrivono regole ed eccezioni in modo orientato ai dettagli e talvolta matematico, in questo capitolo è impegnata per maggiore chiarezza e brevità a scapito della completezza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-119">Although later chapters describe rules and exceptions in a detail-oriented and sometimes mathematical manner, this chapter strives for clarity and brevity at the expense of completeness.</span></span> <span data-ttu-id="99b6e-120">Lo scopo consiste nel fornire un'introduzione al linguaggio di grado di agevolerà la scrittura dei primi programmi e la lettura di negli ultimi capitoli il lettore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-120">The intent is to provide the reader with an introduction to the language that will facilitate the writing of early programs and the reading of later chapters.</span></span>

## <a name="hello-world"></a><span data-ttu-id="99b6e-121">Hello world</span><span class="sxs-lookup"><span data-stu-id="99b6e-121">Hello world</span></span>

<span data-ttu-id="99b6e-122">Il programma "Hello World" viene tradizionalmente usato per presentare un linguaggio di programmazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-122">The "Hello, World" program is traditionally used to introduce a programming language.</span></span> <span data-ttu-id="99b6e-123">Di seguito è riportato il programma Hello, World in C#:</span><span class="sxs-lookup"><span data-stu-id="99b6e-123">Here it is in C#:</span></span>

```csharp
using System;

class Hello
{
    static void Main() {
        Console.WriteLine("Hello, World");
    }
}
```

<span data-ttu-id="99b6e-124">I file di origine C# hanno in genere l'estensione `.cs`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-124">C# source files typically have the file extension `.cs`.</span></span> <span data-ttu-id="99b6e-125">Supponendo che il programma "Hello, World" sia archiviato nel file `hello.cs`, il programma può essere compilato con il compilatore Microsoft c# dalla riga di comando</span><span class="sxs-lookup"><span data-stu-id="99b6e-125">Assuming that the "Hello, World" program is stored in the file `hello.cs`, the program can be compiled with the Microsoft C# compiler using the command line</span></span>
```
csc hello.cs
```
<span data-ttu-id="99b6e-126">che genera un assembly eseguibile denominato `hello.exe`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-126">which produces an executable assembly named `hello.exe`.</span></span> <span data-ttu-id="99b6e-127">L'output prodotto dall'applicazione quando viene eseguito è</span><span class="sxs-lookup"><span data-stu-id="99b6e-127">The output produced by this application when it is run is</span></span>
```
Hello, World
```

<span data-ttu-id="99b6e-128">Il programma "Hello World" inizia con una direttiva `using` che fa riferimento allo spazio dei nomi `System`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-128">The "Hello, World" program starts with a `using` directive that references the `System` namespace.</span></span> <span data-ttu-id="99b6e-129">Gli spazi dei nomi consentono di organizzare i programmi e le librerie C# in modo gerarchico.</span><span class="sxs-lookup"><span data-stu-id="99b6e-129">Namespaces provide a hierarchical means of organizing C# programs and libraries.</span></span> <span data-ttu-id="99b6e-130">Gli spazi dei nomi contengono tipi e altri spazi dei nomi. Lo stazio dei nomi `System`, ad esempio, contiene diversi tipi, come la classe `Console` a cui viene fatto riferimento nel programma, e altri spazi dei nomi, come `IO` e `Collections`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-130">Namespaces contain types and other namespaces—for example, the `System` namespace contains a number of types, such as the `Console` class referenced in the program, and a number of other namespaces, such as `IO` and `Collections`.</span></span> <span data-ttu-id="99b6e-131">Una direttiva `using` che fa riferimento a un determinato spazio dei nomi consente l'uso non qualificato dei tipi che sono membri di tale spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-131">A `using` directive that references a given namespace enables unqualified use of the types that are members of that namespace.</span></span> <span data-ttu-id="99b6e-132">Grazie alla direttiva `using`, il programma può usare `Console.WriteLine` come sintassi abbreviata per `System.Console.WriteLine`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-132">Because of the `using` directive, the program can use `Console.WriteLine` as shorthand for `System.Console.WriteLine`.</span></span>

<span data-ttu-id="99b6e-133">La classe `Hello` dichiarata dal programma "Hello World" ha un solo membro, ovvero il metodo denominato `Main`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-133">The `Hello` class declared by the "Hello, World" program has a single member, the method named `Main`.</span></span> <span data-ttu-id="99b6e-134">Il `Main` metodo viene dichiarato con la `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-134">The `Main` method is declared with the `static` modifier.</span></span> <span data-ttu-id="99b6e-135">Mentre i metodi di istanza possono fare riferimento a una particolare istanza dell'oggetto contenitore usando la parola chiave `this`, i metodi statici operano senza riferimento a un determinato oggetto.</span><span class="sxs-lookup"><span data-stu-id="99b6e-135">While instance methods can reference a particular enclosing object instance using the keyword `this`, static methods operate without reference to a particular object.</span></span> <span data-ttu-id="99b6e-136">Per convenzione, un metodo statico denominato `Main` funge da punto di ingresso di un programma.</span><span class="sxs-lookup"><span data-stu-id="99b6e-136">By convention, a static method named `Main` serves as the entry point of a program.</span></span>

<span data-ttu-id="99b6e-137">L'output del programma viene prodotto dal metodo `WriteLine` della classe `Console` nello spazio dei nomi `System`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-137">The output of the program is produced by the `WriteLine` method of the `Console` class in the `System` namespace.</span></span> <span data-ttu-id="99b6e-138">Questa classe viene fornita da librerie di classi .NET Framework, che, per impostazione predefinita, vengono fatto automaticamente riferimento il compilatore Microsoft c#.</span><span class="sxs-lookup"><span data-stu-id="99b6e-138">This class is provided by the .NET Framework class libraries, which, by default, are automatically referenced by the Microsoft C# compiler.</span></span> <span data-ttu-id="99b6e-139">Tenere presente che c# stesso non dispone di una libreria di runtime separato.</span><span class="sxs-lookup"><span data-stu-id="99b6e-139">Note that C# itself does not have a separate runtime library.</span></span> <span data-ttu-id="99b6e-140">Al contrario, .NET Framework è la libreria di runtime del linguaggio c#.</span><span class="sxs-lookup"><span data-stu-id="99b6e-140">Instead, the .NET Framework is the runtime library of C#.</span></span>

## <a name="program-structure"></a><span data-ttu-id="99b6e-141">Struttura del programma</span><span class="sxs-lookup"><span data-stu-id="99b6e-141">Program structure</span></span>

<span data-ttu-id="99b6e-142">I concetti organizzativi chiave di C# sono i ***programmi***, gli ***spazi dei nomi***, i ***tipi***, i ***membri*** e gli ***assembly***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-142">The key organizational concepts in C# are ***programs***, ***namespaces***, ***types***, ***members***, and ***assemblies***.</span></span> <span data-ttu-id="99b6e-143">I programmi C# sono costituiti da uno o più file di origine.</span><span class="sxs-lookup"><span data-stu-id="99b6e-143">C# programs consist of one or more source files.</span></span> <span data-ttu-id="99b6e-144">I programmi dichiarano i tipi, che contengono i membri e possono essere organizzati in spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-144">Programs declare types, which contain members and can be organized into namespaces.</span></span> <span data-ttu-id="99b6e-145">Le classi e le interfacce sono esempi di tipi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-145">Classes and interfaces are examples of types.</span></span> <span data-ttu-id="99b6e-146">I campi, i metodi, le proprietà e gli eventi sono esempi di membri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-146">Fields, methods, properties, and events are examples of members.</span></span> <span data-ttu-id="99b6e-147">Quando vengono compilati, i programmi C# vengono inseriti fisicamente in assembly.</span><span class="sxs-lookup"><span data-stu-id="99b6e-147">When C# programs are compiled, they are physically packaged into assemblies.</span></span> <span data-ttu-id="99b6e-148">Gli assembly hanno in genere l'estensione del file `.exe` oppure `.dll`, a seconda che implementino ***applicazioni*** oppure ***librerie***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-148">Assemblies typically have the file extension `.exe` or `.dll`, depending on whether they implement ***applications*** or ***libraries***.</span></span>

<span data-ttu-id="99b6e-149">L'esempio</span><span class="sxs-lookup"><span data-stu-id="99b6e-149">The example</span></span>

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
<span data-ttu-id="99b6e-150">dichiara una classe denominata `Stack` in uno spazio dei nomi denominato `Acme.Collections`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-150">declares a class named `Stack` in a namespace called `Acme.Collections`.</span></span> <span data-ttu-id="99b6e-151">Il nome completo di questa classe è `Acme.Collections.Stack`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-151">The fully qualified name of this class is `Acme.Collections.Stack`.</span></span> <span data-ttu-id="99b6e-152">La classe contiene vari membri: un campo `top`, due metodi `Push` e `Pop` e una classe annidata `Entry`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-152">The class contains several members: a field named `top`, two methods named `Push` and `Pop`, and a nested class named `Entry`.</span></span> <span data-ttu-id="99b6e-153">La classe `Entry` contiene altri tre membri: un campo `next`, un campo `data` e un costruttore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-153">The `Entry` class further contains three members: a field named `next`, a field named `data`, and a constructor.</span></span> <span data-ttu-id="99b6e-154">Supponendo che il codice sorgente dell'esempio sia archiviato nel file `acme.cs`, la riga di comando</span><span class="sxs-lookup"><span data-stu-id="99b6e-154">Assuming that the source code of the example is stored in the file `acme.cs`, the command line</span></span>

```
csc /t:library acme.cs
```
<span data-ttu-id="99b6e-155">compila l'esempio come libreria (codice senza un punto di ingresso `Main`) e genera un assembly denominato `acme.dll`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-155">compiles the example as a library (code without a `Main` entry point) and produces an assembly named `acme.dll`.</span></span>

<span data-ttu-id="99b6e-156">Gli assembly contengono codice eseguibile sotto forma di ***Intermediate Language*** istruzioni (IL) e informazioni simboliche sotto forma di ***metadati***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-156">Assemblies contain executable code in the form of ***Intermediate Language*** (IL) instructions, and symbolic information in the form of ***metadata***.</span></span> <span data-ttu-id="99b6e-157">Prima di essere eseguito, il codice IL presente in un assembly viene convertito automaticamente nel codice specifico del processore dal compilatore JIT (Just-In-Time) di .NET Common Language Runtime.</span><span class="sxs-lookup"><span data-stu-id="99b6e-157">Before it is executed, the IL code in an assembly is automatically converted to processor-specific code by the Just-In-Time (JIT) compiler of .NET Common Language Runtime.</span></span>

<span data-ttu-id="99b6e-158">Poiché un assembly è un'unità autodescrittiva di funzionalità contenente codice e metadati, in C# non sono necessari file di intestazione e direttive `#include`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-158">Because an assembly is a self-describing unit of functionality containing both code and metadata, there is no need for `#include` directives and header files in C#.</span></span> <span data-ttu-id="99b6e-159">I membri e i tipi pubblici contenuti in un determinato assembly vengono resi disponibili in un programma C# semplicemente facendo riferimento a tale assembly durante la compilazione del programma.</span><span class="sxs-lookup"><span data-stu-id="99b6e-159">The public types and members contained in a particular assembly are made available in a C# program simply by referencing that assembly when compiling the program.</span></span> <span data-ttu-id="99b6e-160">Questo programma usa ad esempio la classe `Acme.Collections.Stack` dell'assembly `acme.dll`:</span><span class="sxs-lookup"><span data-stu-id="99b6e-160">For example, this program uses the `Acme.Collections.Stack` class from the `acme.dll` assembly:</span></span>

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
<span data-ttu-id="99b6e-161">Se il programma viene archiviato nel file `test.cs`, quando `test.cs` viene compilata, il `acme.dll` assembly possono farvi riferimento tramite il compilatore `/r` opzione:</span><span class="sxs-lookup"><span data-stu-id="99b6e-161">If the program is stored in the file `test.cs`, when `test.cs` is compiled, the `acme.dll` assembly can be referenced using the compiler's `/r` option:</span></span>

```
csc /r:acme.dll test.cs
```
<span data-ttu-id="99b6e-162">In questo modo verrà creato un assembly eseguibile denominato `test.exe` che, quando viene eseguito, genera l'output:</span><span class="sxs-lookup"><span data-stu-id="99b6e-162">This creates an executable assembly named `test.exe`, which, when run, produces the output:</span></span>

```
100
10
1
```
<span data-ttu-id="99b6e-163">C# consente di archiviare il testo di origine di un programma in vari file di origine.</span><span class="sxs-lookup"><span data-stu-id="99b6e-163">C# permits the source text of a program to be stored in several source files.</span></span> <span data-ttu-id="99b6e-164">Quando viene compilato un programma C# costituito da più file, tutti i file di origine vengono elaborati insieme e possono fare riferimento l'uno all'altro. A livello concettuale è come se tutti i file di origine fossero concatenati in un unico grande file prima di essere elaborati.</span><span class="sxs-lookup"><span data-stu-id="99b6e-164">When a multi-file C# program is compiled, all of the source files are processed together, and the source files can freely reference each other—conceptually, it is as if all the source files were concatenated into one large file before being processed.</span></span> <span data-ttu-id="99b6e-165">Le dichiarazioni con prototipo non sono mai necessarie in C# perché, tranne che in rare eccezioni, l'ordine di dichiarazione non è significativo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-165">Forward declarations are never needed in C# because, with very few exceptions, declaration order is insignificant.</span></span> <span data-ttu-id="99b6e-166">C# non limita un file di origine alla dichiarazione di un solo tipo pubblico e non richiede che il nome del file di origine corrisponda a un tipo dichiarato nel file di origine.</span><span class="sxs-lookup"><span data-stu-id="99b6e-166">C# does not limit a source file to declaring only one public type nor does it require the name of the source file to match a type declared in the source file.</span></span>

## <a name="types-and-variables"></a><span data-ttu-id="99b6e-167">Tipi e variabili</span><span class="sxs-lookup"><span data-stu-id="99b6e-167">Types and variables</span></span>

<span data-ttu-id="99b6e-168">In C# esistono due generi di tipi: ***tipi valore*** e ***tipi riferimento***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-168">There are two kinds of types in C#: ***value types*** and ***reference types***.</span></span> <span data-ttu-id="99b6e-169">Le variabili dei tipi valore contengono direttamente i propri dati, mentre le variabili dei tipi riferimento archiviano i riferimenti ai propri dati, noti come oggetti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-169">Variables of value types directly contain their data whereas variables of reference types store references to their data, the latter being known as objects.</span></span> <span data-ttu-id="99b6e-170">Con i tipi riferimento, due variabili possono fare riferimento allo stesso oggetto e di conseguenza le operazioni su una delle due variabili possono influire sull'oggetto a cui fa riferimento l'altra.</span><span class="sxs-lookup"><span data-stu-id="99b6e-170">With reference types, it is possible for two variables to reference the same object and thus possible for operations on one variable to affect the object referenced by the other variable.</span></span> <span data-ttu-id="99b6e-171">Con i tipi valore, ogni variabile ha una propria copia dei dati e non è possibile che le operazioni su una variabile influiscano sull'altra (tranne nel caso delle variabili di parametro `ref` e `out`).</span><span class="sxs-lookup"><span data-stu-id="99b6e-171">With value types, the variables each have their own copy of the data, and it is not possible for operations on one to affect the other (except in the case of `ref` and `out` parameter variables).</span></span>

<span data-ttu-id="99b6e-172">C#di tipi di valore sono ulteriormente suddivisi in ***tipi semplici***, ***tipi enum***, ***i tipi struct***, e ***tipi nullable***, e C#del i tipi di riferimento sono ulteriormente suddivise ***tipi di classe***, ***i tipi di interfaccia***, ***tipi matrice***, e ***tipi delegati***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-172">C#'s value types are further divided into ***simple types***, ***enum types***, ***struct types***, and ***nullable types***, and C#'s reference types are further divided into ***class types***, ***interface types***, ***array types***, and ***delegate types***.</span></span>

<span data-ttu-id="99b6e-173">Nella tabella seguente offre una panoramica di C#del sistema di tipi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-173">The following table provides an overview of C#'s type system.</span></span>

| <span data-ttu-id="99b6e-174">__Categoria__</span><span class="sxs-lookup"><span data-stu-id="99b6e-174">__Category__</span></span>    |                 | <span data-ttu-id="99b6e-175">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-175">__Description__</span></span> |
|-----------------|-----------------|-----------------|
| <span data-ttu-id="99b6e-176">Tipi valore</span><span class="sxs-lookup"><span data-stu-id="99b6e-176">Value types</span></span>     | <span data-ttu-id="99b6e-177">Tipi semplici</span><span class="sxs-lookup"><span data-stu-id="99b6e-177">Simple types</span></span>    | <span data-ttu-id="99b6e-178">Signed Integer: `sbyte`, `short`, `int`,`long`</span><span class="sxs-lookup"><span data-stu-id="99b6e-178">Signed integral: `sbyte`, `short`, `int`, `long`</span></span> |
|                 |                 | <span data-ttu-id="99b6e-179">Unsigned Integer: `byte`, `ushort`, `uint`,`ulong`</span><span class="sxs-lookup"><span data-stu-id="99b6e-179">Unsigned integral: `byte`, `ushort`, `uint`, `ulong`</span></span> |
|                 |                 | <span data-ttu-id="99b6e-180">Caratteri Unicode: `char`</span><span class="sxs-lookup"><span data-stu-id="99b6e-180">Unicode characters: `char`</span></span> |
|                 |                 | <span data-ttu-id="99b6e-181">Virgola mobile IEEE: `float`, `double`</span><span class="sxs-lookup"><span data-stu-id="99b6e-181">IEEE floating point: `float`, `double`</span></span> |
|                 |                 | <span data-ttu-id="99b6e-182">Decimale ad alta precisione: `decimal`</span><span class="sxs-lookup"><span data-stu-id="99b6e-182">High-precision decimal: `decimal`</span></span> |
|                 |                 | <span data-ttu-id="99b6e-183">Booleano: `bool`</span><span class="sxs-lookup"><span data-stu-id="99b6e-183">Boolean: `bool`</span></span> |
|                 | <span data-ttu-id="99b6e-184">Tipi enum</span><span class="sxs-lookup"><span data-stu-id="99b6e-184">Enum types</span></span>      | <span data-ttu-id="99b6e-185">Tipi definiti dall'utente nel formato `enum E {...}`</span><span class="sxs-lookup"><span data-stu-id="99b6e-185">User-defined types of the form `enum E {...}`</span></span> |
|                 | <span data-ttu-id="99b6e-186">Tipi struct</span><span class="sxs-lookup"><span data-stu-id="99b6e-186">Struct types</span></span>    | <span data-ttu-id="99b6e-187">Tipi definiti dall'utente nel formato `struct S {...}`</span><span class="sxs-lookup"><span data-stu-id="99b6e-187">User-defined types of the form `struct S {...}`</span></span> |
|                 | <span data-ttu-id="99b6e-188">Tipi nullable</span><span class="sxs-lookup"><span data-stu-id="99b6e-188">Nullable types</span></span>  | <span data-ttu-id="99b6e-189">Estensioni di tutti gli altri tipi valore con un valore `null`</span><span class="sxs-lookup"><span data-stu-id="99b6e-189">Extensions of all other value types with a `null` value</span></span> |
| <span data-ttu-id="99b6e-190">Tipi riferimento</span><span class="sxs-lookup"><span data-stu-id="99b6e-190">Reference types</span></span> | <span data-ttu-id="99b6e-191">Tipi classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-191">Class types</span></span>     | <span data-ttu-id="99b6e-192">Classe di base principale di tutti gli altri tipi: `object`</span><span class="sxs-lookup"><span data-stu-id="99b6e-192">Ultimate base class of all other types: `object`</span></span> |
|                 |                 | <span data-ttu-id="99b6e-193">Stringhe Unicode: `string`</span><span class="sxs-lookup"><span data-stu-id="99b6e-193">Unicode strings: `string`</span></span> |
|                 |                 | <span data-ttu-id="99b6e-194">Tipi definiti dall'utente nel formato `class C {...}`</span><span class="sxs-lookup"><span data-stu-id="99b6e-194">User-defined types of the form `class C {...}`</span></span> |
|                 | <span data-ttu-id="99b6e-195">Tipi interfaccia</span><span class="sxs-lookup"><span data-stu-id="99b6e-195">Interface types</span></span> | <span data-ttu-id="99b6e-196">Tipi definiti dall'utente nel formato `interface I {...}`</span><span class="sxs-lookup"><span data-stu-id="99b6e-196">User-defined types of the form `interface I {...}`</span></span> |
|                 | <span data-ttu-id="99b6e-197">Tipi matrice</span><span class="sxs-lookup"><span data-stu-id="99b6e-197">Array types</span></span>     | <span data-ttu-id="99b6e-198">Unidimensionale e multidimensionale, ad esempio `int[]` e `int[,]`</span><span class="sxs-lookup"><span data-stu-id="99b6e-198">Single- and multi-dimensional, for example, `int[]` and `int[,]`</span></span> |
|                 | <span data-ttu-id="99b6e-199">Tipi delegato</span><span class="sxs-lookup"><span data-stu-id="99b6e-199">Delegate types</span></span>  | <span data-ttu-id="99b6e-200">Tipi definiti dall'utente del form, ad esempio `delegate int  D(...)`</span><span class="sxs-lookup"><span data-stu-id="99b6e-200">User-defined types of the form e.g. `delegate int  D(...)`</span></span> |

<span data-ttu-id="99b6e-201">Gli otto tipi integrali offrono supporto per i valori a 8, 16, 32 e 64 bit in formato con segno o senza segno.</span><span class="sxs-lookup"><span data-stu-id="99b6e-201">The eight integral types provide support for 8-bit, 16-bit, 32-bit, and 64-bit values in signed or unsigned form.</span></span>

<span data-ttu-id="99b6e-202">Tipi, i due mobile punto `float` e `double`, vengono rappresentati usando i formati a 32 bit e precisione singola e a 64 bit a precisione doppia IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="99b6e-202">The two floating point types, `float` and `double`, are represented using the 32-bit single-precision and 64-bit double-precision IEEE 754 formats.</span></span>

<span data-ttu-id="99b6e-203">Il tipo `decimal` è un tipo dati a 128 bit adatto per i calcoli finanziari e monetari.</span><span class="sxs-lookup"><span data-stu-id="99b6e-203">The `decimal` type is a 128-bit data type suitable for financial and monetary calculations.</span></span>

<span data-ttu-id="99b6e-204">C#del `bool` tipo viene usato per rappresentare valori booleani, valori che possono essere `true` o `false`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-204">C#'s `bool` type is used to represent boolean values—values that are either `true` or `false`.</span></span>

<span data-ttu-id="99b6e-205">Per l'elaborazione di caratteri e stringhe, in C# viene usata la codifica Unicode.</span><span class="sxs-lookup"><span data-stu-id="99b6e-205">Character and string processing in C# uses Unicode encoding.</span></span> <span data-ttu-id="99b6e-206">Il tipo `char` rappresenta un'unità di codice UTF-16, mentre il tipo `string` rappresenta una sequenza di unità di codice UTF-16.</span><span class="sxs-lookup"><span data-stu-id="99b6e-206">The `char` type represents a UTF-16 code unit, and the `string` type represents a sequence of UTF-16 code units.</span></span>

<span data-ttu-id="99b6e-207">La tabella seguente riepiloga C#di tipi numerici.</span><span class="sxs-lookup"><span data-stu-id="99b6e-207">The following table summarizes C#'s numeric types.</span></span>


| <span data-ttu-id="99b6e-208">__Categoria__</span><span class="sxs-lookup"><span data-stu-id="99b6e-208">__Category__</span></span>      | <span data-ttu-id="99b6e-209">__BITS__</span><span class="sxs-lookup"><span data-stu-id="99b6e-209">__Bits__</span></span> | <span data-ttu-id="99b6e-210">__Type__</span><span class="sxs-lookup"><span data-stu-id="99b6e-210">__Type__</span></span>  | <span data-ttu-id="99b6e-211">__Intervallo di precisione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-211">__Range/Precision__</span></span> |
|-------------------|----------|-----------|---------------------|
| <span data-ttu-id="99b6e-212">Signed Integer</span><span class="sxs-lookup"><span data-stu-id="99b6e-212">Signed integral</span></span>   | <span data-ttu-id="99b6e-213">8</span><span class="sxs-lookup"><span data-stu-id="99b6e-213">8</span></span>        | `sbyte`   | <span data-ttu-id="99b6e-214">-128...127</span><span class="sxs-lookup"><span data-stu-id="99b6e-214">-128...127</span></span> |
|                   | <span data-ttu-id="99b6e-215">16</span><span class="sxs-lookup"><span data-stu-id="99b6e-215">16</span></span>       | `short`   | <span data-ttu-id="99b6e-216">-32,768...32,767</span><span class="sxs-lookup"><span data-stu-id="99b6e-216">-32,768...32,767</span></span> |
|                   | <span data-ttu-id="99b6e-217">32</span><span class="sxs-lookup"><span data-stu-id="99b6e-217">32</span></span>       | `int`     | <span data-ttu-id="99b6e-218">-2,147,483,648...2,147,483,647</span><span class="sxs-lookup"><span data-stu-id="99b6e-218">-2,147,483,648...2,147,483,647</span></span> |
|                   | <span data-ttu-id="99b6e-219">64</span><span class="sxs-lookup"><span data-stu-id="99b6e-219">64</span></span>       | `long`    | <span data-ttu-id="99b6e-220">-9,223,372,036,854,775,808...9,223,372,036,854,775,807</span><span class="sxs-lookup"><span data-stu-id="99b6e-220">-9,223,372,036,854,775,808...9,223,372,036,854,775,807</span></span> |
| <span data-ttu-id="99b6e-221">Unsigned Integer</span><span class="sxs-lookup"><span data-stu-id="99b6e-221">Unsigned integral</span></span> | <span data-ttu-id="99b6e-222">8</span><span class="sxs-lookup"><span data-stu-id="99b6e-222">8</span></span>        | `byte`    | <span data-ttu-id="99b6e-223">0...255</span><span class="sxs-lookup"><span data-stu-id="99b6e-223">0...255</span></span> |
|                   | <span data-ttu-id="99b6e-224">16</span><span class="sxs-lookup"><span data-stu-id="99b6e-224">16</span></span>       | `ushort`  | <span data-ttu-id="99b6e-225">0...65,535</span><span class="sxs-lookup"><span data-stu-id="99b6e-225">0...65,535</span></span> |
|                   | <span data-ttu-id="99b6e-226">32</span><span class="sxs-lookup"><span data-stu-id="99b6e-226">32</span></span>       | `uint`    | <span data-ttu-id="99b6e-227">0...4,294,967,295</span><span class="sxs-lookup"><span data-stu-id="99b6e-227">0...4,294,967,295</span></span> |
|                   | <span data-ttu-id="99b6e-228">64</span><span class="sxs-lookup"><span data-stu-id="99b6e-228">64</span></span>       | `ulong`   | <span data-ttu-id="99b6e-229">0...18,446,744,073,709,551,615</span><span class="sxs-lookup"><span data-stu-id="99b6e-229">0...18,446,744,073,709,551,615</span></span> |
| <span data-ttu-id="99b6e-230">Virgola mobile</span><span class="sxs-lookup"><span data-stu-id="99b6e-230">Floating point</span></span>    | <span data-ttu-id="99b6e-231">32</span><span class="sxs-lookup"><span data-stu-id="99b6e-231">32</span></span>       | `float`   | <span data-ttu-id="99b6e-232">1,5 × 10 ^ −45 e 3,4 × 10 ^ 38, precisione di 7 cifre</span><span class="sxs-lookup"><span data-stu-id="99b6e-232">1.5 × 10^−45 to 3.4 × 10^38, 7-digit precision</span></span> |
|                   | <span data-ttu-id="99b6e-233">64</span><span class="sxs-lookup"><span data-stu-id="99b6e-233">64</span></span>       | `double`  | <span data-ttu-id="99b6e-234">5,0 × 10 ^ − 324 e 1,7 × 10 ^ 308 precisione a 15 cifre</span><span class="sxs-lookup"><span data-stu-id="99b6e-234">5.0 × 10^−324 to 1.7 × 10^308, 15-digit precision</span></span> |
| <span data-ttu-id="99b6e-235">Decimale</span><span class="sxs-lookup"><span data-stu-id="99b6e-235">Decimal</span></span>           | <span data-ttu-id="99b6e-236">128</span><span class="sxs-lookup"><span data-stu-id="99b6e-236">128</span></span>      | `decimal` | <span data-ttu-id="99b6e-237">1.0 × 10 ^ 28 e 7,9 × 10 ^, 28 e precisione di 28 cifre</span><span class="sxs-lookup"><span data-stu-id="99b6e-237">1.0 × 10^−28 to 7.9 × 10^28, 28-digit precision</span></span> |

<span data-ttu-id="99b6e-238">I programmi C# usano le ***dichiarazioni di tipo*** per creare nuovi tipi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-238">C# programs use ***type declarations*** to create new types.</span></span> <span data-ttu-id="99b6e-239">Una dichiarazione di tipo consente di specificare il nome e i membri del nuovo tipo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-239">A type declaration specifies the name and the members of the new type.</span></span> <span data-ttu-id="99b6e-240">Cinque delle C#di categorie di tipi sono definibili dall'utente: tipi, i tipi struct, tipi interfaccia, tipi enum di classi e tipi di delegati.</span><span class="sxs-lookup"><span data-stu-id="99b6e-240">Five of C#'s categories of types are user-definable: class types, struct types, interface types, enum types, and delegate types.</span></span>

<span data-ttu-id="99b6e-241">Un tipo di classe definisce una struttura di dati che contiene i membri dati (campi) e i membri di funzione (metodi, proprietà e altri).</span><span class="sxs-lookup"><span data-stu-id="99b6e-241">A class type defines a data structure that contains data members (fields) and function members (methods, properties, and others).</span></span> <span data-ttu-id="99b6e-242">I tipi classe supportano l'ereditarietà singola e il polimorfismo, meccanismi in base ai quali le classi derivate possono estendere e specializzare le classi di base.</span><span class="sxs-lookup"><span data-stu-id="99b6e-242">Class types support single inheritance and polymorphism, mechanisms whereby derived classes can extend and specialize base classes.</span></span>

<span data-ttu-id="99b6e-243">Un tipo struct è simile a un tipo di classe in quanto rappresenta una struttura con membri dati e i membri di funzione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-243">A struct type is similar to a class type in that it represents a structure with data members and function members.</span></span> <span data-ttu-id="99b6e-244">Tuttavia, a differenza delle classi, struct sono tipi valore e non richiedono l'allocazione di heap.</span><span class="sxs-lookup"><span data-stu-id="99b6e-244">However, unlike classes, structs are value types and do not require heap allocation.</span></span> <span data-ttu-id="99b6e-245">I tipi struct non supportano l'ereditarietà specificata dall'utente. Tutti i tipi struct ereditano implicitamente dal tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-245">Struct types do not support user-specified inheritance, and all struct types implicitly inherit from type `object`.</span></span>

<span data-ttu-id="99b6e-246">Un tipo di interfaccia definisce un contratto come un set denominato di membri funzione pubblici.</span><span class="sxs-lookup"><span data-stu-id="99b6e-246">An interface type defines a contract as a named set of public function members.</span></span> <span data-ttu-id="99b6e-247">Una classe o struct che implementa un'interfaccia deve fornire le implementazioni dei membri di funzione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="99b6e-247">A class or struct that implements an interface must provide implementations of the interface's function members.</span></span> <span data-ttu-id="99b6e-248">Un'interfaccia può ereditare da più interfacce di base e una classe o struct può implementare più interfacce.</span><span class="sxs-lookup"><span data-stu-id="99b6e-248">An interface may inherit from multiple base interfaces, and a class or struct may implement multiple interfaces.</span></span>

<span data-ttu-id="99b6e-249">Un tipo delegato rappresenta riferimenti ai metodi con un elenco di parametri specifico e il tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-249">A delegate type represents references to methods with a particular parameter list and return type.</span></span> <span data-ttu-id="99b6e-250">I delegati consentono di trattare i metodi come entità che è possibile assegnare a variabili e passare come parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-250">Delegates make it possible to treat methods as entities that can be assigned to variables and passed as parameters.</span></span> <span data-ttu-id="99b6e-251">I delegati sono simili al concetto di puntatori a funzione disponibili in altri linguaggi. A differenza dei puntatori a funzione, tuttavia, i delegati sono orientati agli oggetti e indipendenti dai tipi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-251">Delegates are similar to the concept of function pointers found in some other languages, but unlike function pointers, delegates are object-oriented and type-safe.</span></span>

<span data-ttu-id="99b6e-252">I tipi supportano tutti generics, in base al quale possono essere parametrizzati con altri tipi di classe, struct, interfaccia e delegato.</span><span class="sxs-lookup"><span data-stu-id="99b6e-252">Class, struct, interface and delegate types all support generics, whereby they can be parameterized with other types.</span></span>

<span data-ttu-id="99b6e-253">Un tipo di enumerazione è un tipo distinto con costanti denominate.</span><span class="sxs-lookup"><span data-stu-id="99b6e-253">An enum type is a distinct type with named constants.</span></span> <span data-ttu-id="99b6e-254">Ogni tipo di enumerazione ha un tipo sottostante, che deve essere uno degli otto tipi integrali.</span><span class="sxs-lookup"><span data-stu-id="99b6e-254">Every enum type has an underlying type, which must be one of the eight integral types.</span></span> <span data-ttu-id="99b6e-255">Il set di valori di un tipo di enumerazione è quello utilizzato per il set di valori del tipo sottostante.</span><span class="sxs-lookup"><span data-stu-id="99b6e-255">The set of values of an enum type is the same as the set of values of the underlying type.</span></span>

<span data-ttu-id="99b6e-256">C# supporta matrici unidimensionali e multidimensionali di qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-256">C# supports single- and multi-dimensional arrays of any type.</span></span> <span data-ttu-id="99b6e-257">A differenza dei tipi elencati in precedenza, i tipi matrice non devono essere dichiarati prima dell'uso.</span><span class="sxs-lookup"><span data-stu-id="99b6e-257">Unlike the types listed above, array types do not have to be declared before they can be used.</span></span> <span data-ttu-id="99b6e-258">Al contrario, i tipi matrice vengono costruiti facendo seguire a un nome di tipo delle parentesi quadre.</span><span class="sxs-lookup"><span data-stu-id="99b6e-258">Instead, array types are constructed by following a type name with square brackets.</span></span> <span data-ttu-id="99b6e-259">Ad esempio, `int[]` è una matrice unidimensionale di `int`, `int[,]` è una matrice bidimensionale di `int`, e `int[][]` è una matrice unidimensionale di matrici unidimensionali di `int`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-259">For example, `int[]` is a single-dimensional array of `int`, `int[,]` is a two-dimensional array of `int`, and `int[][]` is a single-dimensional array of single-dimensional arrays of `int`.</span></span>

<span data-ttu-id="99b6e-260">Tipi nullable inoltre non devono essere dichiarati prima di poter essere usati.</span><span class="sxs-lookup"><span data-stu-id="99b6e-260">Nullable types also do not have to be declared before they can be used.</span></span> <span data-ttu-id="99b6e-261">Per ogni tipo di valore non nullable `T` è presente un tipo nullable corrispondente `T?`, che può contenere un valore aggiuntivo `null`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-261">For each non-nullable value type `T` there is a corresponding nullable type `T?`, which can hold an additional value `null`.</span></span> <span data-ttu-id="99b6e-262">Ad esempio, `int?` è un tipo che può contenere qualsiasi numero intero a 32 bit o il valore `null`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-262">For instance, `int?` is a type that can hold any 32 bit integer or the value `null`.</span></span>

<span data-ttu-id="99b6e-263">C#del sistema di tipi è unificato in modo che un valore di qualsiasi tipo può essere considerato come un oggetto.</span><span class="sxs-lookup"><span data-stu-id="99b6e-263">C#'s type system is unified such that a value of any type can be treated as an object.</span></span> <span data-ttu-id="99b6e-264">In C# ogni tipo deriva direttamente o indirettamente dal tipo classe `object` e `object` è la classe di base principale di tutti i tipi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-264">Every type in C# directly or indirectly derives from the `object` class type, and `object` is the ultimate base class of all types.</span></span> <span data-ttu-id="99b6e-265">I valori dei tipi riferimento vengono trattati come oggetti semplicemente visualizzando tali valori come tipi `object`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-265">Values of reference types are treated as objects simply by viewing the values as type `object`.</span></span> <span data-ttu-id="99b6e-266">I valori dei tipi di valore vengono trattati come oggetti eseguendo ***boxing*** e ***unboxing*** operazioni.</span><span class="sxs-lookup"><span data-stu-id="99b6e-266">Values of value types are treated as objects by performing ***boxing*** and ***unboxing*** operations.</span></span> <span data-ttu-id="99b6e-267">Nell'esempio seguente un valore `int` viene convertito in `object` e quindi convertito nuovamente in `int`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-267">In the following example, an `int` value is converted to `object` and back again to `int`.</span></span>

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
<span data-ttu-id="99b6e-268">Quando un valore di un tipo valore viene convertito nel tipo `object`, un'istanza dell'oggetto, denominata anche "box", viene allocata per contenere il valore e il valore viene copiato nel box.</span><span class="sxs-lookup"><span data-stu-id="99b6e-268">When a value of a value type is converted to type `object`, an object instance, also called a "box," is allocated to hold the value, and the value is copied into that box.</span></span> <span data-ttu-id="99b6e-269">Al contrario, quando un `object` viene eseguito il cast di riferimento a un tipo valore, viene eseguito un controllo che l'oggetto di riferimento sia un box del tipo valore corretto e, se la verifica ha esito positivo, viene copiato il valore nella finestra di.</span><span class="sxs-lookup"><span data-stu-id="99b6e-269">Conversely, when an `object` reference is cast to a value type, a check is made that the referenced object is a box of the correct value type, and, if the check succeeds, the value in the box is copied out.</span></span>

<span data-ttu-id="99b6e-270">C#unificato di tipo sistema ciò significa che i tipi di valore possono diventare oggetti "su"richiesta.</span><span class="sxs-lookup"><span data-stu-id="99b6e-270">C#'s unified type system effectively means that value types can become objects "on demand."</span></span> <span data-ttu-id="99b6e-271">Grazie all'unificazione, le librerie generiche che usano il tipo `object` possono essere usate con entrambi i tipi riferimento e valore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-271">Because of the unification, general-purpose libraries that use type `object` can be used with both reference types and value types.</span></span>

<span data-ttu-id="99b6e-272">In C# sono disponibili diversi tipi di ***variabili***, inclusi campi, elementi matrice, variabili locali e parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-272">There are several kinds of ***variables*** in C#, including fields, array elements, local variables, and parameters.</span></span> <span data-ttu-id="99b6e-273">Le variabili rappresentano posizioni di archiviazione e ogni variabile dispone di un tipo che determina quali valori possono essere archiviati nella variabile, come illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-273">Variables represent storage locations, and every variable has a type that determines what values can be stored in the variable, as shown by the following table.</span></span>


| <span data-ttu-id="99b6e-274">__Tipo di variabile__</span><span class="sxs-lookup"><span data-stu-id="99b6e-274">__Type of Variable__</span></span>    | <span data-ttu-id="99b6e-275">__Possibili contenuti__</span><span class="sxs-lookup"><span data-stu-id="99b6e-275">__Possible Contents__</span></span> |
|-------------------------|-----------------------|
| <span data-ttu-id="99b6e-276">Tipo valore non-nullable</span><span class="sxs-lookup"><span data-stu-id="99b6e-276">Non-nullable value type</span></span> | <span data-ttu-id="99b6e-277">Valore esattamente del tipo indicato</span><span class="sxs-lookup"><span data-stu-id="99b6e-277">A value of that exact type</span></span> |
| <span data-ttu-id="99b6e-278">Tipo valore nullable</span><span class="sxs-lookup"><span data-stu-id="99b6e-278">Nullable value type</span></span>     | <span data-ttu-id="99b6e-279">Un valore null o un valore esattamente del tipo</span><span class="sxs-lookup"><span data-stu-id="99b6e-279">A null value or a value of that exact type</span></span> |
| `object`                | <span data-ttu-id="99b6e-280">Un riferimento null, un riferimento a un oggetto di qualsiasi tipo riferimento o un riferimento a un valore boxed di qualsiasi tipo di valore</span><span class="sxs-lookup"><span data-stu-id="99b6e-280">A null reference, a reference to an object of any reference type, or a reference to a boxed value of any value type</span></span> |
| <span data-ttu-id="99b6e-281">Tipo classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-281">Class type</span></span>              | <span data-ttu-id="99b6e-282">Un riferimento null, un riferimento a un'istanza di quel tipo di classe o un riferimento a un'istanza di una classe derivata da tale tipo di classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-282">A null reference, a reference to an instance of that class type, or a reference to an instance of a class derived from that class type</span></span> |
| <span data-ttu-id="99b6e-283">Tipo interfaccia</span><span class="sxs-lookup"><span data-stu-id="99b6e-283">Interface type</span></span>          | <span data-ttu-id="99b6e-284">Un riferimento null, un riferimento a un'istanza di un tipo di classe che implementa il tipo interfaccia o un riferimento a un valore boxed di un tipo di valore che implementa il tipo interfaccia</span><span class="sxs-lookup"><span data-stu-id="99b6e-284">A null reference, a reference to an instance of a class type that implements that interface type, or a reference to a boxed value of a value type that implements that interface type</span></span> |
| <span data-ttu-id="99b6e-285">Tipo matrice</span><span class="sxs-lookup"><span data-stu-id="99b6e-285">Array type</span></span>              | <span data-ttu-id="99b6e-286">Un riferimento null, un riferimento a un'istanza di quel tipo di matrice o un riferimento a un'istanza di un tipo matrice compatibile</span><span class="sxs-lookup"><span data-stu-id="99b6e-286">A null reference, a reference to an instance of that array type, or a reference to an instance of a compatible array type</span></span> |
| <span data-ttu-id="99b6e-287">Tipo delegato</span><span class="sxs-lookup"><span data-stu-id="99b6e-287">Delegate type</span></span>           | <span data-ttu-id="99b6e-288">Un riferimento null o un riferimento a un'istanza di quel tipo di delegato</span><span class="sxs-lookup"><span data-stu-id="99b6e-288">A null reference or a reference to an instance of that delegate type</span></span> |

## <a name="expressions"></a><span data-ttu-id="99b6e-289">Espressioni</span><span class="sxs-lookup"><span data-stu-id="99b6e-289">Expressions</span></span>

<span data-ttu-id="99b6e-290">Le ***espressioni*** sono costituite da ***operandi*** e ***operatori***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-290">***Expressions*** are constructed from ***operands*** and ***operators***.</span></span> <span data-ttu-id="99b6e-291">Gli operatori di un'espressione indicano le operazioni che devono essere eseguite sugli operandi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-291">The operators of an expression indicate which operations to apply to the operands.</span></span> <span data-ttu-id="99b6e-292">Alcuni esempi di operatori sono `+`, `-`, `*`, `/` e `new`,</span><span class="sxs-lookup"><span data-stu-id="99b6e-292">Examples of operators include `+`, `-`, `*`, `/`, and `new`.</span></span> <span data-ttu-id="99b6e-293">mentre i valori effettivi, i campi, le variabili locali e le espressioni sono esempi di operandi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-293">Examples of operands include literals, fields, local variables, and expressions.</span></span>

<span data-ttu-id="99b6e-294">Se un'espressione contiene più operatori, la ***precedenza*** degli operatori determina l'ordine in cui vengono valutati i singoli operatori.</span><span class="sxs-lookup"><span data-stu-id="99b6e-294">When an expression contains multiple operators, the ***precedence*** of the operators controls the order in which the individual operators are evaluated.</span></span> <span data-ttu-id="99b6e-295">L'espressione `x + y * z`, ad esempio, viene valutata come `x + (y * z)` poiché l'operatore `*` ha la precedenza sull'operatore `+`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-295">For example, the expression `x + y * z` is evaluated as `x + (y * z)` because the `*` operator has higher precedence than the `+` operator.</span></span>

<span data-ttu-id="99b6e-296">La maggior parte degli operatori può essere***in overload***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-296">Most operators can be ***overloaded***.</span></span> <span data-ttu-id="99b6e-297">L'overload degli operatori consente di specificare implementazioni di operatori definite dall'utente per le operazioni in cui uno o entrambi gli operandi appartengono a un tipo struct o a una classe definita dall'utente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-297">Operator overloading permits user-defined operator implementations to be specified for operations where one or both of the operands are of a user-defined class or struct type.</span></span>

<span data-ttu-id="99b6e-298">La tabella seguente riepiloga C#di operatori, elenca le categorie di operatori in ordine di precedenza dalla più alta alla più bassa.</span><span class="sxs-lookup"><span data-stu-id="99b6e-298">The following table summarizes C#'s operators, listing the operator categories in order of precedence from highest to lowest.</span></span> <span data-ttu-id="99b6e-299">Gli operatori della stessa categoria hanno uguale precedenza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-299">Operators in the same category have equal precedence.</span></span>


| <span data-ttu-id="99b6e-300">__Categoria__</span><span class="sxs-lookup"><span data-stu-id="99b6e-300">__Category__</span></span>                     | <span data-ttu-id="99b6e-301">__Espressione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-301">__Expression__</span></span>    | <span data-ttu-id="99b6e-302">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-302">__Description__</span></span> |
|----------------------------------|-------------------|-----------------|
| <span data-ttu-id="99b6e-303">Primario</span><span class="sxs-lookup"><span data-stu-id="99b6e-303">Primary</span></span>                          | `x.m`             | <span data-ttu-id="99b6e-304">Accesso ai membri</span><span class="sxs-lookup"><span data-stu-id="99b6e-304">Member access</span></span> |
|                                  | `x(...)`          | <span data-ttu-id="99b6e-305">Chiamata a metodi e delegati</span><span class="sxs-lookup"><span data-stu-id="99b6e-305">Method and delegate invocation</span></span> |
|                                  | `x[...]`          | <span data-ttu-id="99b6e-306">Accesso a matrici e indicizzatori</span><span class="sxs-lookup"><span data-stu-id="99b6e-306">Array and indexer access</span></span> |
|                                  | `x++`             | <span data-ttu-id="99b6e-307">Post-incremento</span><span class="sxs-lookup"><span data-stu-id="99b6e-307">Post-increment</span></span> |
|                                  | `x--`             | <span data-ttu-id="99b6e-308">Post-decremento</span><span class="sxs-lookup"><span data-stu-id="99b6e-308">Post-decrement</span></span> |
|                                  | `new T(...)`      | <span data-ttu-id="99b6e-309">Creazione di oggetti e delegati</span><span class="sxs-lookup"><span data-stu-id="99b6e-309">Object and delegate creation</span></span> |
|                                  | `new T(...){...}` | <span data-ttu-id="99b6e-310">creazione di oggetti con inizializzatore</span><span class="sxs-lookup"><span data-stu-id="99b6e-310">Object creation with initializer</span></span> |
|                                  | `new {...}`       | <span data-ttu-id="99b6e-311">inizializzatore di oggetti anonimo</span><span class="sxs-lookup"><span data-stu-id="99b6e-311">Anonymous object initializer</span></span> |
|                                  | `new T[...]`      | <span data-ttu-id="99b6e-312">creazione di matrici</span><span class="sxs-lookup"><span data-stu-id="99b6e-312">Array creation</span></span> |
|                                  | `typeof(T)`       | <span data-ttu-id="99b6e-313">ottiene l'oggetto `System.Type` per `T`</span><span class="sxs-lookup"><span data-stu-id="99b6e-313">Obtain `System.Type` object for `T`</span></span> |
|                                  | `checked(x)`      | <span data-ttu-id="99b6e-314">Valutare l'espressione in un contesto controllato (checked)</span><span class="sxs-lookup"><span data-stu-id="99b6e-314">Evaluate expression in checked context</span></span> |
|                                  | `unchecked(x)`    | <span data-ttu-id="99b6e-315">Valutare l'espressione in un contesto non controllato (unchecked)</span><span class="sxs-lookup"><span data-stu-id="99b6e-315">Evaluate expression in unchecked context</span></span> |
|                                  | `default(T)`      | <span data-ttu-id="99b6e-316">ottiene un valore predefinito di tipo `T`</span><span class="sxs-lookup"><span data-stu-id="99b6e-316">Obtain default value of type `T`</span></span> |
|                                  | `delegate {...}`  | <span data-ttu-id="99b6e-317">Funzione anonima (metodo anonimo)</span><span class="sxs-lookup"><span data-stu-id="99b6e-317">Anonymous function (anonymous method)</span></span> |
| <span data-ttu-id="99b6e-318">Unario</span><span class="sxs-lookup"><span data-stu-id="99b6e-318">Unary</span></span>                            | `+x`              | <span data-ttu-id="99b6e-319">identità</span><span class="sxs-lookup"><span data-stu-id="99b6e-319">Identity</span></span> |
|                                  | `-x`              | <span data-ttu-id="99b6e-320">Negazione</span><span class="sxs-lookup"><span data-stu-id="99b6e-320">Negation</span></span> |
|                                  | `!x`              | <span data-ttu-id="99b6e-321">Negazione logica</span><span class="sxs-lookup"><span data-stu-id="99b6e-321">Logical negation</span></span> |
|                                  | `~x`              | <span data-ttu-id="99b6e-322">Negazione bit per bit</span><span class="sxs-lookup"><span data-stu-id="99b6e-322">Bitwise negation</span></span> |
|                                  | `++x`             | <span data-ttu-id="99b6e-323">Pre-incremento</span><span class="sxs-lookup"><span data-stu-id="99b6e-323">Pre-increment</span></span> |
|                                  | `--x`             | <span data-ttu-id="99b6e-324">Pre-decremento</span><span class="sxs-lookup"><span data-stu-id="99b6e-324">Pre-decrement</span></span> |
|                                  | `(T)x`            | <span data-ttu-id="99b6e-325">converte in modo esplicito `x` al tipo `T`</span><span class="sxs-lookup"><span data-stu-id="99b6e-325">Explicitly convert `x` to type `T`</span></span> |
|                                  | `await x`         | <span data-ttu-id="99b6e-326">attende in modo asincrono il completamento di `x`</span><span class="sxs-lookup"><span data-stu-id="99b6e-326">Asynchronously wait for `x` to complete</span></span> |
| <span data-ttu-id="99b6e-327">Moltiplicazione</span><span class="sxs-lookup"><span data-stu-id="99b6e-327">Multiplicative</span></span>                   | `x * y`           | <span data-ttu-id="99b6e-328">Moltiplicazione</span><span class="sxs-lookup"><span data-stu-id="99b6e-328">Multiplication</span></span> |
|                                  | `x / y`           | <span data-ttu-id="99b6e-329">Divisione</span><span class="sxs-lookup"><span data-stu-id="99b6e-329">Division</span></span> |
|                                  | `x % y`           | <span data-ttu-id="99b6e-330">Resto</span><span class="sxs-lookup"><span data-stu-id="99b6e-330">Remainder</span></span> |
| <span data-ttu-id="99b6e-331">Addizione</span><span class="sxs-lookup"><span data-stu-id="99b6e-331">Additive</span></span>                         | `x + y`           | <span data-ttu-id="99b6e-332">Addizione, concatenazione di stringhe, combinazione di delegati</span><span class="sxs-lookup"><span data-stu-id="99b6e-332">Addition, string concatenation, delegate combination</span></span> |
|                                  | `x - y`           | <span data-ttu-id="99b6e-333">Sottrazione, rimozione di delegati</span><span class="sxs-lookup"><span data-stu-id="99b6e-333">Subtraction, delegate removal</span></span> |
| <span data-ttu-id="99b6e-334">Shift</span><span class="sxs-lookup"><span data-stu-id="99b6e-334">Shift</span></span>                            | `x << y`          | <span data-ttu-id="99b6e-335">Spostamento a sinistra</span><span class="sxs-lookup"><span data-stu-id="99b6e-335">Shift left</span></span> |
|                                  | `x >> y`          | <span data-ttu-id="99b6e-336">Spostamento a destra</span><span class="sxs-lookup"><span data-stu-id="99b6e-336">Shift right</span></span> |
| <span data-ttu-id="99b6e-337">Operatori relazionali e operatori di test del tipo</span><span class="sxs-lookup"><span data-stu-id="99b6e-337">Relational and type testing</span></span>      | `x < y`           | <span data-ttu-id="99b6e-338">Minore di</span><span class="sxs-lookup"><span data-stu-id="99b6e-338">Less than</span></span> |
|                                  | `x > y`           | <span data-ttu-id="99b6e-339">Maggiore di</span><span class="sxs-lookup"><span data-stu-id="99b6e-339">Greater than</span></span> |
|                                  | `x <= y`          | <span data-ttu-id="99b6e-340">Minore o uguale a</span><span class="sxs-lookup"><span data-stu-id="99b6e-340">Less than or equal</span></span> |
|                                  | `x >= y`          | <span data-ttu-id="99b6e-341">Maggiore o uguale a</span><span class="sxs-lookup"><span data-stu-id="99b6e-341">Greater than or equal</span></span> |
|                                  | `x is T`          | <span data-ttu-id="99b6e-342">restituisce `true` se `x` è un oggetto `T`, altrimenti `false`</span><span class="sxs-lookup"><span data-stu-id="99b6e-342">Return `true` if `x` is a `T`, `false` otherwise</span></span> |
|                                  | `x as T`          | <span data-ttu-id="99b6e-343">restituisce `x` tipizzato come `T` oppure `null` se `x` non è un oggetto `T`</span><span class="sxs-lookup"><span data-stu-id="99b6e-343">Return `x` typed as `T`, or `null` if `x` is not a `T`</span></span> |
| <span data-ttu-id="99b6e-344">Uguaglianza</span><span class="sxs-lookup"><span data-stu-id="99b6e-344">Equality</span></span>                         | `x == y`          | <span data-ttu-id="99b6e-345">Uguale</span><span class="sxs-lookup"><span data-stu-id="99b6e-345">Equal</span></span>      |
|                                  | `x != y`          | <span data-ttu-id="99b6e-346">Non uguaglianza</span><span class="sxs-lookup"><span data-stu-id="99b6e-346">Not equal</span></span> |
| <span data-ttu-id="99b6e-347">AND logico</span><span class="sxs-lookup"><span data-stu-id="99b6e-347">Logical AND</span></span>                      | `x & y`           | <span data-ttu-id="99b6e-348">AND Integer bit per bit, AND logico booleano</span><span class="sxs-lookup"><span data-stu-id="99b6e-348">Integer bitwise AND, boolean logical AND</span></span> |
| <span data-ttu-id="99b6e-349">XOR logico</span><span class="sxs-lookup"><span data-stu-id="99b6e-349">Logical XOR</span></span>                      | `x ^ y`           | <span data-ttu-id="99b6e-350">XOR Integer bit per bit, XOR logico booleano</span><span class="sxs-lookup"><span data-stu-id="99b6e-350">Integer bitwise XOR, boolean logical XOR</span></span> |
| <span data-ttu-id="99b6e-351">OR logico</span><span class="sxs-lookup"><span data-stu-id="99b6e-351">Logical OR</span></span>                       | <code>x &#124; y</code> | <span data-ttu-id="99b6e-352">OR Integer bit per bit, OR logico booleano</span><span class="sxs-lookup"><span data-stu-id="99b6e-352">Integer bitwise OR, boolean logical OR</span></span> |
| <span data-ttu-id="99b6e-353">AND condizionale</span><span class="sxs-lookup"><span data-stu-id="99b6e-353">Conditional AND</span></span>                  | `x && y`          | <span data-ttu-id="99b6e-354">Viene valutata `y` solo se `x` è `true`</span><span class="sxs-lookup"><span data-stu-id="99b6e-354">Evaluates `y` only if `x` is `true`</span></span> |
| <span data-ttu-id="99b6e-355">OR condizionale</span><span class="sxs-lookup"><span data-stu-id="99b6e-355">Conditional OR</span></span>                   | <code>x &#124;&#124; y</code> | <span data-ttu-id="99b6e-356">Viene valutata `y` solo se `x` è `false`</span><span class="sxs-lookup"><span data-stu-id="99b6e-356">Evaluates `y` only if `x` is `false`</span></span> |
| <span data-ttu-id="99b6e-357">Null-coalescing</span><span class="sxs-lookup"><span data-stu-id="99b6e-357">Null coalescing</span></span>                  | `x ?? y`          | <span data-ttu-id="99b6e-358">Viene valutata `y` se `x` viene `null`, a `x` in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="99b6e-358">Evaluates to `y` if `x` is `null`, to `x` otherwise</span></span> |
| <span data-ttu-id="99b6e-359">Condizionale</span><span class="sxs-lookup"><span data-stu-id="99b6e-359">Conditional</span></span>                      | `x ? y : z`       | <span data-ttu-id="99b6e-360">restituisce `y` se `x` è `true`, `z` se `x` è `false`</span><span class="sxs-lookup"><span data-stu-id="99b6e-360">Evaluates `y` if `x` is `true`, `z` if `x` is `false`</span></span> |
| <span data-ttu-id="99b6e-361">Assegnazione o funzione anonima</span><span class="sxs-lookup"><span data-stu-id="99b6e-361">Assignment or anonymous function</span></span> | `x = y`           | <span data-ttu-id="99b6e-362">Assegnazione</span><span class="sxs-lookup"><span data-stu-id="99b6e-362">Assignment</span></span> |
|                                  | `x op= y`         | <span data-ttu-id="99b6e-363">Assegnazione composta; gli operatori supportati sono `*=` `/=` `%=` `+=` `-=` `<<=` `>>=` `&=` `^=` <code>&#124;=</code></span><span class="sxs-lookup"><span data-stu-id="99b6e-363">Compound assignment; supported operators are `*=` `/=` `%=` `+=` `-=` `<<=` `>>=` `&=` `^=` <code>&#124;=</code></span></span> |
|                                  | `(T x) => y`      | <span data-ttu-id="99b6e-364">Funzione anonima (espressione lambda)</span><span class="sxs-lookup"><span data-stu-id="99b6e-364">Anonymous function (lambda expression)</span></span> |

## <a name="statements"></a><span data-ttu-id="99b6e-365">Istruzioni</span><span class="sxs-lookup"><span data-stu-id="99b6e-365">Statements</span></span>

<span data-ttu-id="99b6e-366">Le azioni di un programma vengono espresse mediante ***istruzioni***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-366">The actions of a program are expressed using ***statements***.</span></span> <span data-ttu-id="99b6e-367">C# supporta numerosi tipi di istruzioni, alcune delle quali sono definite in termini di istruzioni nidificate.</span><span class="sxs-lookup"><span data-stu-id="99b6e-367">C# supports several different kinds of statements, a number of which are defined in terms of embedded statements.</span></span>

<span data-ttu-id="99b6e-368">Un ***blocco*** consente di scrivere più istruzioni nei contesti in cui ne è consentita una sola.</span><span class="sxs-lookup"><span data-stu-id="99b6e-368">A ***block*** permits multiple statements to be written in contexts where a single statement is allowed.</span></span> <span data-ttu-id="99b6e-369">Un blocco è costituito da un elenco di istruzioni scritte tra i delimitatori `{` e `}`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-369">A block consists of a list of statements written between the delimiters `{` and `}`.</span></span>

<span data-ttu-id="99b6e-370">Le ***istruzioni di dichiarazione*** vengono usate per dichiarare le costanti e le variabili locali.</span><span class="sxs-lookup"><span data-stu-id="99b6e-370">***Declaration statements*** are used to declare local variables and constants.</span></span>

<span data-ttu-id="99b6e-371">Le ***istruzioni di espressione*** vengono usate per valutare le espressioni.</span><span class="sxs-lookup"><span data-stu-id="99b6e-371">***Expression statements*** are used to evaluate expressions.</span></span> <span data-ttu-id="99b6e-372">Le espressioni che possono essere usate come istruzioni includono chiamate del metodo, dell'oggetto allocazioni usando il `new` operatore, le assegnazioni mediante `=` e operatori di assegnamento composti, operazioni di incremento e decremento in forma tramite il `++`e `--` operatori ed espressioni await.</span><span class="sxs-lookup"><span data-stu-id="99b6e-372">Expressions that can be used as statements include method invocations, object allocations using the `new` operator, assignments using `=` and the compound assignment operators, increment and decrement operations using the `++` and `--` operators and await expressions.</span></span>

<span data-ttu-id="99b6e-373">Le ***istruzioni di selezione*** vengono usate per selezionare una tra più istruzioni che è possibile eseguire sulla base del valore di alcune espressioni.</span><span class="sxs-lookup"><span data-stu-id="99b6e-373">***Selection statements*** are used to select one of a number of possible statements for execution based on the value of some expression.</span></span> <span data-ttu-id="99b6e-374">In questo gruppo sono incluse le istruzioni `if` e `switch`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-374">In this group are the `if` and `switch` statements.</span></span>

<span data-ttu-id="99b6e-375">***Istruzioni di iterazione*** vengono utilizzati per eseguire ripetutamente un'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-375">***Iteration statements*** are used to repeatedly execute an embedded statement.</span></span> <span data-ttu-id="99b6e-376">In questo gruppo sono incluse le istruzioni `while`, `do`, `for` e `foreach`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-376">In this group are the `while`, `do`, `for`, and `foreach` statements.</span></span>

<span data-ttu-id="99b6e-377">Le ***istruzioni di spostamento*** vengono usate per trasferire il controllo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-377">***Jump statements*** are used to transfer control.</span></span> <span data-ttu-id="99b6e-378">In questo gruppo sono incluse le istruzioni `break`, `continue`, `goto`, `throw`, `return` e `yield`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-378">In this group are the `break`, `continue`, `goto`, `throw`, `return`, and `yield` statements.</span></span>

<span data-ttu-id="99b6e-379">L'istruzione `try`...`catch` viene usata per rilevare le eccezioni che si verificano durante l'esecuzione di un blocco, mentre l'istruzione `try`...`finally` viene usata per specificare il codice di finalizzazione che viene eseguito sempre, indipendentemente dal fatto che si sia verificata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-379">The `try`...`catch` statement is used to catch exceptions that occur during execution of a block, and the `try`...`finally` statement is used to specify finalization code that is always executed, whether an exception occurred or not.</span></span>

<span data-ttu-id="99b6e-380">Il `checked` e `unchecked` istruzioni consentono di controllare il contesto per le conversioni e operazioni aritmetiche di tipo integrale di controllo dell'overflow.</span><span class="sxs-lookup"><span data-stu-id="99b6e-380">The `checked` and `unchecked` statements are used to control the overflow checking context for integral-type arithmetic operations and conversions.</span></span>

<span data-ttu-id="99b6e-381">L'istruzione `lock` viene usata per ottenere il blocco a esclusione reciproca per un oggetto specificato, eseguire un'istruzione e quindi rilasciare il blocco.</span><span class="sxs-lookup"><span data-stu-id="99b6e-381">The `lock` statement is used to obtain the mutual-exclusion lock for a given object, execute a statement, and then release the lock.</span></span>

<span data-ttu-id="99b6e-382">L'istruzione `using` viene usata per ottenere una risorsa, eseguire un'istruzione e quindi eliminare la risorsa.</span><span class="sxs-lookup"><span data-stu-id="99b6e-382">The `using` statement is used to obtain a resource, execute a statement, and then dispose of that resource.</span></span>

<span data-ttu-id="99b6e-383">Di seguito sono riportati esempi di ogni tipo di istruzione</span><span class="sxs-lookup"><span data-stu-id="99b6e-383">Below are examples of each kind of statement</span></span>

<span data-ttu-id="99b6e-384">__Dichiarazioni di variabili locali__</span><span class="sxs-lookup"><span data-stu-id="99b6e-384">__Local variable declarations__</span></span>

```csharp
static void Main() {
   int a;
   int b = 2, c = 3;
   a = 1;
   Console.WriteLine(a + b + c);
}
```


<span data-ttu-id="99b6e-385">__Dichiarazione di costante locale__</span><span class="sxs-lookup"><span data-stu-id="99b6e-385">__Local constant declaration__</span></span>

```csharp
static void Main() {
    const float pi = 3.1415927f;
    const int r = 25;
    Console.WriteLine(pi * r * r);
}
```


<span data-ttu-id="99b6e-386">__Istruzione di espressione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-386">__Expression statement__</span></span>

```csharp
static void Main() {
    int i;
    i = 123;                // Expression statement
    Console.WriteLine(i);   // Expression statement
    i++;                    // Expression statement
    Console.WriteLine(i);   // Expression statement
}
```

<span data-ttu-id="99b6e-387">__`if` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-387">__`if` statement__</span></span>

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


<span data-ttu-id="99b6e-388">__`switch` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-388">__`switch` statement__</span></span>

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

<span data-ttu-id="99b6e-389">__`while` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-389">__`while` statement__</span></span>

```csharp
static void Main(string[] args) {
    int i = 0;
    while (i < args.Length) {
        Console.WriteLine(args[i]);
        i++;
    }
}
```


<span data-ttu-id="99b6e-390">__`do` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-390">__`do` statement__</span></span>

```csharp
static void Main() {
    string s;
    do {
        s = Console.ReadLine();
        if (s != null) Console.WriteLine(s);
    } while (s != null);
}
```

<span data-ttu-id="99b6e-391">__`for` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-391">__`for` statement__</span></span>

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        Console.WriteLine(args[i]);
    }
}
```

<span data-ttu-id="99b6e-392">__`foreach` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-392">__`foreach` statement__</span></span>

```csharp
static void Main(string[] args) {
    foreach (string s in args) {
        Console.WriteLine(s);
    }
}
```

<span data-ttu-id="99b6e-393">__`break` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-393">__`break` statement__</span></span>

```csharp
static void Main() {
    while (true) {
        string s = Console.ReadLine();
        if (s == null) break;
        Console.WriteLine(s);
    }
}
```

<span data-ttu-id="99b6e-394">__`continue` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-394">__`continue` statement__</span></span>

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        if (args[i].StartsWith("/")) continue;
        Console.WriteLine(args[i]);
    }
}
```

<span data-ttu-id="99b6e-395">__`goto` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-395">__`goto` statement__</span></span>

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

<span data-ttu-id="99b6e-396">__`return` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-396">__`return` statement__</span></span>

```csharp
static int Add(int a, int b) {
    return a + b;
}

static void Main() {
    Console.WriteLine(Add(1, 2));
    return;
}
```

<span data-ttu-id="99b6e-397">__`yield` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-397">__`yield` statement__</span></span>

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

<span data-ttu-id="99b6e-398">__`throw` e `try` istruzioni__</span><span class="sxs-lookup"><span data-stu-id="99b6e-398">__`throw` and `try` statements__</span></span>

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

<span data-ttu-id="99b6e-399">__`checked` e `unchecked` istruzioni__</span><span class="sxs-lookup"><span data-stu-id="99b6e-399">__`checked` and `unchecked` statements__</span></span>

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

<span data-ttu-id="99b6e-400">__`lock` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-400">__`lock` statement__</span></span>

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

<span data-ttu-id="99b6e-401">__`using` Istruzione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-401">__`using` statement__</span></span>

```csharp
static void Main() {
    using (TextWriter w = File.CreateText("test.txt")) {
        w.WriteLine("Line one");
        w.WriteLine("Line two");
        w.WriteLine("Line three");
    }
}
```

## <a name="classes-and-objects"></a><span data-ttu-id="99b6e-402">Classi e oggetti</span><span class="sxs-lookup"><span data-stu-id="99b6e-402">Classes and objects</span></span>

<span data-ttu-id="99b6e-403">Le ***classi*** sono i tipi C# più importanti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-403">***Classes*** are the most fundamental of C#'s types.</span></span> <span data-ttu-id="99b6e-404">Una classe è una struttura di dati che combina in una singola unità lo stato (campi) e le azioni (metodi e altri membri di funzione).</span><span class="sxs-lookup"><span data-stu-id="99b6e-404">A class is a data structure that combines state (fields) and actions (methods and other function members) in a single unit.</span></span> <span data-ttu-id="99b6e-405">Una classe fornisce una definizione per ***istanze*** della classe create dinamicamente, note anche come ***oggetti***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-405">A class provides a definition for dynamically created ***instances*** of the class, also known as ***objects***.</span></span> <span data-ttu-id="99b6e-406">Le classi supportano l'***ereditarietà*** e il ***polimorfismo***, meccanismi in base ai quali le ***classi derivate*** possono estendere e specializzare le ***classi di base***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-406">Classes support ***inheritance*** and ***polymorphism***, mechanisms whereby ***derived classes*** can extend and specialize ***base classes***.</span></span>

<span data-ttu-id="99b6e-407">Le nuove classi vengono create tramite dichiarazioni di classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-407">New classes are created using class declarations.</span></span> <span data-ttu-id="99b6e-408">Una dichiarazione di classe inizia con un'intestazione che specifica gli attributi e i modificatori della classe, il nome della classe, la classe di base (se disponibile) e le interfacce implementate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-408">A class declaration starts with a header that specifies the attributes and modifiers of the class, the name of the class, the base class (if given), and the interfaces implemented by the class.</span></span> <span data-ttu-id="99b6e-409">L'intestazione è seguita dal corpo della classe, costituito da un elenco di dichiarazioni di membro scritte tra i delimitatori `{` e `}`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-409">The header is followed by the class body, which consists of a list of member declarations written between the delimiters `{` and `}`.</span></span>

<span data-ttu-id="99b6e-410">Di seguito è riportata una dichiarazione di una classe semplice denominata `Point`:</span><span class="sxs-lookup"><span data-stu-id="99b6e-410">The following is a declaration of a simple class named `Point`:</span></span>

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
<span data-ttu-id="99b6e-411">Le istanze delle classi vengono create usando l'operatore `new`, che alloca memoria per una nuova istanza, richiama un costruttore per inizializzare l'istanza e restituisce un riferimento all'istanza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-411">Instances of classes are created using the `new` operator, which allocates memory for a new instance, invokes a constructor to initialize the instance, and returns a reference to the instance.</span></span> <span data-ttu-id="99b6e-412">Le istruzioni seguenti creano due `Point` degli oggetti e archiviare i riferimenti agli oggetti in due variabili:</span><span class="sxs-lookup"><span data-stu-id="99b6e-412">The following statements create two `Point` objects and store references to those objects in two variables:</span></span>

```
Point p1 = new Point(0, 0);
Point p2 = new Point(10, 20);
```
<span data-ttu-id="99b6e-413">La memoria occupata da un oggetto viene recuperata automaticamente quando l'oggetto non è più in uso.</span><span class="sxs-lookup"><span data-stu-id="99b6e-413">The memory occupied by an object is automatically reclaimed when the object is no longer in use.</span></span> <span data-ttu-id="99b6e-414">In C# non è possibile, né necessario, deallocare oggetti in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-414">It is neither necessary nor possible to explicitly deallocate objects in C#.</span></span>

### <a name="members"></a><span data-ttu-id="99b6e-415">Membri</span><span class="sxs-lookup"><span data-stu-id="99b6e-415">Members</span></span>

<span data-ttu-id="99b6e-416">I membri di una classe sono entrambi ***i membri statici*** oppure ***membri di istanza***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-416">The members of a class are either ***static members*** or ***instance members***.</span></span> <span data-ttu-id="99b6e-417">I primi appartengono a classi, mentre i secondi appartengono a oggetti, ovvero a istanze di classi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-417">Static members belong to classes, and instance members belong to objects (instances of classes).</span></span>

<span data-ttu-id="99b6e-418">Nella tabella seguente fornisce una panoramica dei tipi di membri di che una classe può contenere.</span><span class="sxs-lookup"><span data-stu-id="99b6e-418">The following table provides an overview of the kinds of members a class can contain.</span></span>


| <span data-ttu-id="99b6e-419">__Membro__</span><span class="sxs-lookup"><span data-stu-id="99b6e-419">__Member__</span></span>   | <span data-ttu-id="99b6e-420">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="99b6e-420">__Description__</span></span> |
|------------  |-----------------|
| <span data-ttu-id="99b6e-421">Costanti</span><span class="sxs-lookup"><span data-stu-id="99b6e-421">Constants</span></span>    | <span data-ttu-id="99b6e-422">Valori costanti associati alla classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-422">Constant values associated with the class</span></span> |
| <span data-ttu-id="99b6e-423">Campi</span><span class="sxs-lookup"><span data-stu-id="99b6e-423">Fields</span></span>       | <span data-ttu-id="99b6e-424">Variabili della classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-424">Variables of the class</span></span> |
| <span data-ttu-id="99b6e-425">Metodi</span><span class="sxs-lookup"><span data-stu-id="99b6e-425">Methods</span></span>      | <span data-ttu-id="99b6e-426">Calcoli e azioni che possono essere eseguiti dalla classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-426">Computations and actions that can be performed by the class</span></span> |
| <span data-ttu-id="99b6e-427">Proprietà</span><span class="sxs-lookup"><span data-stu-id="99b6e-427">Properties</span></span>   | <span data-ttu-id="99b6e-428">Azioni associate alla lettura e alla scrittura di proprietà denominate della classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-428">Actions associated with reading and writing named properties of the class</span></span> |
| <span data-ttu-id="99b6e-429">Indicizzatori</span><span class="sxs-lookup"><span data-stu-id="99b6e-429">Indexers</span></span>     | <span data-ttu-id="99b6e-430">Azioni associate all'indicizzazione di istanze della classe, come una matrice</span><span class="sxs-lookup"><span data-stu-id="99b6e-430">Actions associated with indexing instances of the class like an array</span></span> |
| <span data-ttu-id="99b6e-431">Eventi</span><span class="sxs-lookup"><span data-stu-id="99b6e-431">Events</span></span>       | <span data-ttu-id="99b6e-432">Notifiche che possono essere generate dalla classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-432">Notifications that can be generated by the class</span></span> |
| <span data-ttu-id="99b6e-433">Operatori</span><span class="sxs-lookup"><span data-stu-id="99b6e-433">Operators</span></span>    | <span data-ttu-id="99b6e-434">Conversioni e operatori di espressione supportati dalla classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-434">Conversions and expression operators supported by the class</span></span> |
| <span data-ttu-id="99b6e-435">Costruttori</span><span class="sxs-lookup"><span data-stu-id="99b6e-435">Constructors</span></span> | <span data-ttu-id="99b6e-436">Azioni necessarie per inizializzare istanze della classe o la classe stessa</span><span class="sxs-lookup"><span data-stu-id="99b6e-436">Actions required to initialize instances of the class or the class itself</span></span> |
| <span data-ttu-id="99b6e-437">Distruttori</span><span class="sxs-lookup"><span data-stu-id="99b6e-437">Destructors</span></span>  | <span data-ttu-id="99b6e-438">Azioni da eseguire prima che istanze della classe vengano eliminate in modo permanente</span><span class="sxs-lookup"><span data-stu-id="99b6e-438">Actions to perform before instances of the class are permanently discarded</span></span> |
| <span data-ttu-id="99b6e-439">Tipi</span><span class="sxs-lookup"><span data-stu-id="99b6e-439">Types</span></span>        | <span data-ttu-id="99b6e-440">Tipi annidati dichiarati dalla classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-440">Nested types declared by the class</span></span> |

### <a name="accessibility"></a><span data-ttu-id="99b6e-441">Accessibilità</span><span class="sxs-lookup"><span data-stu-id="99b6e-441">Accessibility</span></span>

<span data-ttu-id="99b6e-442">A ogni membro di una classe è associata una caratteristica di accessibilità, che controlla le aree di testo del programma in grado di accedere al membro.</span><span class="sxs-lookup"><span data-stu-id="99b6e-442">Each member of a class has an associated accessibility, which controls the regions of program text that are able to access the member.</span></span> <span data-ttu-id="99b6e-443">Esistono cinque diverse forme di accessibilità,</span><span class="sxs-lookup"><span data-stu-id="99b6e-443">There are five possible forms of accessibility.</span></span> <span data-ttu-id="99b6e-444">Questi report sono riepilogati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-444">These are summarized in the following table.</span></span>


| <span data-ttu-id="99b6e-445">__Accessibilità__</span><span class="sxs-lookup"><span data-stu-id="99b6e-445">__Accessibility__</span></span>    | <span data-ttu-id="99b6e-446">__Significato__</span><span class="sxs-lookup"><span data-stu-id="99b6e-446">__Meaning__</span></span> |
|----------------------|-----------------|
| `public`             | <span data-ttu-id="99b6e-447">Accesso non limitato</span><span class="sxs-lookup"><span data-stu-id="99b6e-447">Access not limited</span></span> |
| `protected`          | <span data-ttu-id="99b6e-448">Accesso limitato a questa classe o alle classi derivate da questa classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-448">Access limited to this class or classes derived from this class</span></span> |
| `internal`           | <span data-ttu-id="99b6e-449">Accesso limitato a questo programma</span><span class="sxs-lookup"><span data-stu-id="99b6e-449">Access limited to this program</span></span> |
| `protected internal` | <span data-ttu-id="99b6e-450">Accesso limitato a questo programma o alle classi derivate da questa classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-450">Access limited to this program or classes derived from this class</span></span> |
| `private`            | <span data-ttu-id="99b6e-451">Accesso limitato a questa classe</span><span class="sxs-lookup"><span data-stu-id="99b6e-451">Access limited to this class</span></span> |

### <a name="type-parameters"></a><span data-ttu-id="99b6e-452">Parametri di tipo</span><span class="sxs-lookup"><span data-stu-id="99b6e-452">Type parameters</span></span>

<span data-ttu-id="99b6e-453">Una definizione di classe può specificare un set di parametri di tipo se si fa seguire il nome della classe da un elenco di nomi di parametri di tipo, racchiuso tra parentesi uncinate.</span><span class="sxs-lookup"><span data-stu-id="99b6e-453">A class definition may specify a set of type parameters by following the class name with angle brackets enclosing a list of type parameter names.</span></span> <span data-ttu-id="99b6e-454">I parametri di tipo possono di essere utilizzati all'interno delle dichiarazioni di classe per definire i membri della classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-454">The type parameters can the be used in the body of the class declarations to define the members of the class.</span></span> <span data-ttu-id="99b6e-455">Nell'esempio seguente i parametri di tipo di `Pair` sono `TFirst` e `TSecond`:</span><span class="sxs-lookup"><span data-stu-id="99b6e-455">In the following example, the type parameters of `Pair` are `TFirst` and `TSecond`:</span></span>

```csharp
public class Pair<TFirst,TSecond>
{
    public TFirst First;
    public TSecond Second;
}
```
<span data-ttu-id="99b6e-456">Un tipo di classe viene dichiarato per accettare parametri di tipo viene chiamato un tipo di classe generica.</span><span class="sxs-lookup"><span data-stu-id="99b6e-456">A class type that is declared to take type parameters is called a generic class type.</span></span> <span data-ttu-id="99b6e-457">Possono essere generici anche i tipi struct, interfaccia e delegato.</span><span class="sxs-lookup"><span data-stu-id="99b6e-457">Struct, interface and delegate types can also be generic.</span></span>

<span data-ttu-id="99b6e-458">Quando si usa la classe generica, è necessario specificare argomenti di tipo per ogni parametro di tipo:</span><span class="sxs-lookup"><span data-stu-id="99b6e-458">When the generic class is used, type arguments must be provided for each of the type parameters:</span></span>

```csharp
Pair<int,string> pair = new Pair<int,string> { First = 1, Second = "two" };
int i = pair.First;     // TFirst is int
string s = pair.Second; // TSecond is string
```
<span data-ttu-id="99b6e-459">Un tipo generico con argomenti di tipo specificato, ad esempio `Pair<int,string>` sopra, viene chiamato un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-459">A generic type with type arguments provided, like `Pair<int,string>` above, is called a constructed type.</span></span>

### <a name="base-classes"></a><span data-ttu-id="99b6e-460">Classi di base</span><span class="sxs-lookup"><span data-stu-id="99b6e-460">Base classes</span></span>

<span data-ttu-id="99b6e-461">Una dichiarazione di classe può specificare una classe di base se si fa seguire il nome della classe e i parametri di tipo dai due punti e dal nome della classe di base.</span><span class="sxs-lookup"><span data-stu-id="99b6e-461">A class declaration may specify a base class by following the class name and type parameters with a colon and the name of the base class.</span></span> <span data-ttu-id="99b6e-462">L'omissione della specifica della classe di base equivale alla derivazione dal tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-462">Omitting a base class specification is the same as deriving from type `object`.</span></span> <span data-ttu-id="99b6e-463">Nell'esempio seguente la classe di base di `Point3D` è `Point` e quella di `Point` è `object`:</span><span class="sxs-lookup"><span data-stu-id="99b6e-463">In the following example, the base class of `Point3D` is `Point`, and the base class of `Point` is `object`:</span></span>

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
<span data-ttu-id="99b6e-464">Una classe eredita i membri della relativa classe di base.</span><span class="sxs-lookup"><span data-stu-id="99b6e-464">A class inherits the members of its base class.</span></span> <span data-ttu-id="99b6e-465">L'ereditarietà prevede che una classe contenga in modo implicito tutti i membri della relativa classe base, fatta eccezione per l'istanza e i costruttori statici e i distruttori della classe di base.</span><span class="sxs-lookup"><span data-stu-id="99b6e-465">Inheritance means that a class implicitly contains all members of its base class, except for the instance and static constructors, and the destructors of the base class.</span></span> <span data-ttu-id="99b6e-466">Una classe derivata può aggiungere nuovi membri a quelli ereditati, ma non può rimuovere la definizione di un membro ereditato.</span><span class="sxs-lookup"><span data-stu-id="99b6e-466">A derived class can add new members to those it inherits, but it cannot remove the definition of an inherited member.</span></span> <span data-ttu-id="99b6e-467">Nell'esempio precedente `Point3D` eredita i campi `x` e `y` da `Point` e ogni istanza di `Point3D` contiene tre campi: `x`, `y` e `z`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-467">In the previous example, `Point3D` inherits the `x` and `y` fields from `Point`, and every `Point3D` instance contains three fields, `x`, `y`, and `z`.</span></span>

<span data-ttu-id="99b6e-468">Un tipo di classe viene implicitamente convertito in uno dei relativi tipi di classe di base.</span><span class="sxs-lookup"><span data-stu-id="99b6e-468">An implicit conversion exists from a class type to any of its base class types.</span></span> <span data-ttu-id="99b6e-469">Una variabile di un tipo di classe, quindi, può fare riferimento a un'istanza della classe o a un'istanza di una classe derivata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-469">Therefore, a variable of a class type can reference an instance of that class or an instance of any derived class.</span></span> <span data-ttu-id="99b6e-470">Nel caso delle dichiarazioni di classe precedenti, ad esempio, una variabile di tipo `Point` può fare riferimento a `Point` o `Point3D`:</span><span class="sxs-lookup"><span data-stu-id="99b6e-470">For example, given the previous class declarations, a variable of type `Point` can reference either a `Point` or a `Point3D`:</span></span>

```csharp
Point a = new Point(10, 20);
Point b = new Point3D(10, 20, 30);
```

### <a name="fields"></a><span data-ttu-id="99b6e-471">Campi</span><span class="sxs-lookup"><span data-stu-id="99b6e-471">Fields</span></span>

<span data-ttu-id="99b6e-472">Un campo è una variabile che è associato a una classe o a un'istanza di una classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-472">A field is a variable that is associated with a class or with an instance of a class.</span></span>

<span data-ttu-id="99b6e-473">Un campo dichiarato con la `static` modificatore definisce un ***campo statico***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-473">A field declared with the `static` modifier defines a ***static field***.</span></span> <span data-ttu-id="99b6e-474">che identifica esattamente una posizione di memoria.</span><span class="sxs-lookup"><span data-stu-id="99b6e-474">A static field identifies exactly one storage location.</span></span> <span data-ttu-id="99b6e-475">Indipendentemente dal numero di istanze di una classe create, esiste una sola copia di un campo statico.</span><span class="sxs-lookup"><span data-stu-id="99b6e-475">No matter how many instances of a class are created, there is only ever one copy of a static field.</span></span>

<span data-ttu-id="99b6e-476">Un campo dichiarato senza il `static` modificatore definisce un ***campo di istanza***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-476">A field declared without the `static` modifier defines an ***instance field***.</span></span> <span data-ttu-id="99b6e-477">Ogni istanza di una classe contiene una copia separata di tutti i campi di istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-477">Every instance of a class contains a separate copy of all the instance fields of that class.</span></span>

<span data-ttu-id="99b6e-478">Nell'esempio seguente ogni istanza della classe `Color` include una copia distinta dei campi di istanza `r`, `g` e `b`, mentre esiste una sola copia dei campi statici `Black`, `White`, `Red`, `Green` e `Blue`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-478">In the following example, each instance of the `Color` class has a separate copy of the `r`, `g`, and `b` instance fields, but there is only one copy of the `Black`, `White`, `Red`, `Green`, and `Blue` static fields:</span></span>

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
<span data-ttu-id="99b6e-479">Come illustrato nell'esempio precedente, i ***campi di sola lettura*** possono essere dichiarati con un modificatore `readonly`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-479">As shown in the previous example, ***read-only fields*** may be declared with a `readonly` modifier.</span></span> <span data-ttu-id="99b6e-480">Assegnazione a un `readonly` campo può verificarsi solo come parte della dichiarazione del campo o in un costruttore della stessa classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-480">Assignment to a `readonly` field can only occur as part of the field's declaration or in a constructor in the same class.</span></span>

### <a name="methods"></a><span data-ttu-id="99b6e-481">Metodi</span><span class="sxs-lookup"><span data-stu-id="99b6e-481">Methods</span></span>

<span data-ttu-id="99b6e-482">Un ***metodo*** è un membro che implementa un calcolo o un'azione che può essere eseguita da un oggetto o una classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-482">A ***method*** is a member that implements a computation or action that can be performed by an object or class.</span></span> <span data-ttu-id="99b6e-483">I ***metodi statici*** sono accessibili tramite la classe,</span><span class="sxs-lookup"><span data-stu-id="99b6e-483">***Static methods*** are accessed through the class.</span></span> <span data-ttu-id="99b6e-484">mentre i ***metodi di istanza*** sono accessibili tramite istanze della classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-484">***Instance methods*** are accessed through instances of the class.</span></span>

<span data-ttu-id="99b6e-485">I metodi hanno un elenco (eventualmente vuoto) di ***parametri***, che rappresentano valori o riferimenti a variabili passati al metodo e una ***tipo restituito***, che specifica il tipo di valore calcolato e restituito da il metodo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-485">Methods have a (possibly empty) list of ***parameters***, which represent values or variable references passed to the method, and a ***return type***, which specifies the type of the value computed and returned by the method.</span></span> <span data-ttu-id="99b6e-486">Il tipo restituito del metodo `void` se non viene restituito un valore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-486">A method's return type is `void` if it does not return a value.</span></span>

<span data-ttu-id="99b6e-487">Come i tipi, anche i metodi possono avere un set di parametri di tipo per i quali è necessario specificare argomenti di tipo quando vengono chiamati.</span><span class="sxs-lookup"><span data-stu-id="99b6e-487">Like types, methods may also have a set of type parameters, for which type arguments must be specified when the method is called.</span></span> <span data-ttu-id="99b6e-488">A differenza dei tipi, gli argomenti di tipo possono essere spesso dedotti dagli argomenti di una chiamata al metodo e non devono essere assegnati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-488">Unlike types, the type arguments can often be inferred from the arguments of a method call and need not be explicitly given.</span></span>

<span data-ttu-id="99b6e-489">La ***firma*** di un metodo deve essere univoca nell'ambito della classe in cui viene dichiarato il metodo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-489">The ***signature*** of a method must be unique in the class in which the method is declared.</span></span> <span data-ttu-id="99b6e-490">La firma di un metodo è costituita dal nome del metodo, dal numero di parametri di tipo e dal numero, dai modificatori e dai tipi dei rispettivi parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-490">The signature of a method consists of the name of the method, the number of type parameters and the number, modifiers, and types of its parameters.</span></span> <span data-ttu-id="99b6e-491">Nella firma di un metodo non è incluso il tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-491">The signature of a method does not include the return type.</span></span>

#### <a name="parameters"></a><span data-ttu-id="99b6e-492">Parametri</span><span class="sxs-lookup"><span data-stu-id="99b6e-492">Parameters</span></span>

<span data-ttu-id="99b6e-493">I parametri consentono di passare ai metodi valori o riferimenti a variabili.</span><span class="sxs-lookup"><span data-stu-id="99b6e-493">Parameters are used to pass values or variable references to methods.</span></span> <span data-ttu-id="99b6e-494">I parametri di un metodo ottengono i valori effettivi dagli ***argomenti*** specificati quando viene richiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-494">The parameters of a method get their actual values from the ***arguments*** that are specified when the method is invoked.</span></span> <span data-ttu-id="99b6e-495">Esistono quattro tipi di parametri: parametri di valore, parametri di riferimento, i parametri di output e matrici di parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-495">There are four kinds of parameters: value parameters, reference parameters, output parameters, and parameter arrays.</span></span>

<span data-ttu-id="99b6e-496">Un ***parametro di valore*** consente di passare parametri di input.</span><span class="sxs-lookup"><span data-stu-id="99b6e-496">A ***value parameter*** is used for input parameter passing.</span></span> <span data-ttu-id="99b6e-497">Corrisponde a una variabile locale che ottiene il valore iniziale dall'argomento passato per il parametro.</span><span class="sxs-lookup"><span data-stu-id="99b6e-497">A value parameter corresponds to a local variable that gets its initial value from the argument that was passed for the parameter.</span></span> <span data-ttu-id="99b6e-498">Eventuali modifiche a un parametro di valore non interessano l'argomento passato per il parametro.</span><span class="sxs-lookup"><span data-stu-id="99b6e-498">Modifications to a value parameter do not affect the argument that was passed for the parameter.</span></span>

<span data-ttu-id="99b6e-499">I parametri di valore possono essere facoltativi specificando un valore predefinito. In questo caso gli argomenti corrispondenti possono essere omessi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-499">Value parameters can be optional, by specifying a default value so that corresponding arguments can be omitted.</span></span>

<span data-ttu-id="99b6e-500">Un ***parametro di riferimento*** consente di passare parametri di input e di output.</span><span class="sxs-lookup"><span data-stu-id="99b6e-500">A ***reference parameter*** is used for both input and output parameter passing.</span></span> <span data-ttu-id="99b6e-501">L'argomento passato per un parametro di riferimento deve essere una variabile e, durante l'esecuzione del metodo, il parametro di riferimento rappresenta la stessa posizione di memoria della variabile di argomento.</span><span class="sxs-lookup"><span data-stu-id="99b6e-501">The argument passed for a reference parameter must be a variable, and during execution of the method, the reference parameter represents the same storage location as the argument variable.</span></span> <span data-ttu-id="99b6e-502">Un parametro di riferimento viene dichiarato con il modificatore `ref`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-502">A reference parameter is declared with the `ref` modifier.</span></span> <span data-ttu-id="99b6e-503">Nell'esempio seguente viene illustrato l'uso di parametri `ref`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-503">The following example shows the use of `ref` parameters.</span></span>

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
<span data-ttu-id="99b6e-504">Un ***parametro di output*** consente di passare parametri di input.</span><span class="sxs-lookup"><span data-stu-id="99b6e-504">An ***output parameter*** is used for output parameter passing.</span></span> <span data-ttu-id="99b6e-505">Un parametro di output è simile a un parametro di riferimento, da cui differisce perché il valore iniziale dell'argomento fornito dal chiamante non è importante.</span><span class="sxs-lookup"><span data-stu-id="99b6e-505">An output parameter is similar to a reference parameter except that the initial value of the caller-provided argument is unimportant.</span></span> <span data-ttu-id="99b6e-506">Un parametro di output viene dichiarato con il modificatore `out`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-506">An output parameter is declared with the `out` modifier.</span></span> <span data-ttu-id="99b6e-507">Nell'esempio seguente viene illustrato l'uso di parametri `out`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-507">The following example shows the use of `out` parameters.</span></span>

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
<span data-ttu-id="99b6e-508">Una ***matrice di parametri*** consente di passare un numero variabile di argomenti a un metodo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-508">A ***parameter array*** permits a variable number of arguments to be passed to a method.</span></span> <span data-ttu-id="99b6e-509">Una matrice di parametri viene dichiarata con il modificatore `params`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-509">A parameter array is declared with the `params` modifier.</span></span> <span data-ttu-id="99b6e-510">Solo l'ultimo parametro di un metodo può essere costituito da una matrice di parametri, che deve essere sempre di tipo unidimensionale.</span><span class="sxs-lookup"><span data-stu-id="99b6e-510">Only the last parameter of a method can be a parameter array, and the type of a parameter array must be a single-dimensional array type.</span></span> <span data-ttu-id="99b6e-511">Il `Write` e `WriteLine` metodi del `System.Console` classe sono ottimi esempi di uso delle matrici di parametro.</span><span class="sxs-lookup"><span data-stu-id="99b6e-511">The `Write` and `WriteLine` methods of the `System.Console` class are good examples of parameter array usage.</span></span> <span data-ttu-id="99b6e-512">Vengono dichiarati come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-512">They are declared as follows.</span></span>

```csharp
public class Console
{
    public static void Write(string fmt, params object[] args) {...}
    public static void WriteLine(string fmt, params object[] args) {...}
    ...
}
```
<span data-ttu-id="99b6e-513">All'interno di un metodo, una matrice di parametri si comporta esattamente come un normale parametro di tipo matrice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-513">Within a method that uses a parameter array, the parameter array behaves exactly like a regular parameter of an array type.</span></span> <span data-ttu-id="99b6e-514">In una chiamata di un metodo con una matrice di parametri, tuttavia, è possibile passare un singolo argomento di tipo matrice di parametri oppure un qualsiasi numero di argomenti di tipo elemento della matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-514">However, in an invocation of a method with a parameter array, it is possible to pass either a single argument of the parameter array type or any number of arguments of the element type of the parameter array.</span></span> <span data-ttu-id="99b6e-515">Nel secondo caso, un'istanza di matrice viene automaticamente creata e inizializzata con gli argomenti specificati.</span><span class="sxs-lookup"><span data-stu-id="99b6e-515">In the latter case, an array instance is automatically created and initialized with the given arguments.</span></span> <span data-ttu-id="99b6e-516">Questo esempio</span><span class="sxs-lookup"><span data-stu-id="99b6e-516">This example</span></span>

```csharp
Console.WriteLine("x={0} y={1} z={2}", x, y, z);
```
<span data-ttu-id="99b6e-517">è equivalente alla sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-517">is equivalent to writing the following.</span></span>

```csharp
string s = "x={0} y={1} z={2}";
object[] args = new object[3];
args[0] = x;
args[1] = y;
args[2] = z;
Console.WriteLine(s, args);
```

#### <a name="method-body-and-local-variables"></a><span data-ttu-id="99b6e-518">Corpo del metodo e variabili locali</span><span class="sxs-lookup"><span data-stu-id="99b6e-518">Method body and local variables</span></span>

<span data-ttu-id="99b6e-519">Corpo del metodo specifica le istruzioni da eseguire quando viene richiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-519">A method's body specifies the statements to execute when the method is invoked.</span></span>

<span data-ttu-id="99b6e-520">Il corpo di un metodo può dichiarare variabili specifiche per la chiamata del metodo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-520">A method body can declare variables that are specific to the invocation of the method.</span></span> <span data-ttu-id="99b6e-521">Queste variabili prendono il nome di ***variabili locali***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-521">Such variables are called ***local variables***.</span></span> <span data-ttu-id="99b6e-522">Una dichiarazione di variabile locale specifica un nome di tipo, un nome di variabile e possibilmente un valore iniziale.</span><span class="sxs-lookup"><span data-stu-id="99b6e-522">A local variable declaration specifies a type name, a variable name, and possibly an initial value.</span></span> <span data-ttu-id="99b6e-523">Nell'esempio seguente viene dichiarata una variabile locale `i` con un valore iniziale pari a zero e una variabile locale `j` senza valore iniziale.</span><span class="sxs-lookup"><span data-stu-id="99b6e-523">The following example declares a local variable `i` with an initial value of zero and a local variable `j` with no initial value.</span></span>

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
<span data-ttu-id="99b6e-524">In C# è necessario ***assegnare esplicitamente*** una variabile locale prima di poterne ottenere il valore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-524">C# requires a local variable to be ***definitely assigned*** before its value can be obtained.</span></span> <span data-ttu-id="99b6e-525">Se ad esempio nella dichiarazione della variabile locale `i` precedente non fosse stato incluso un valore iniziale, il compilatore avrebbe segnalato un errore ogni volta che la variabile `i` veniva usata, perché `i` non era assegnata esplicitamente in quei punti del programma.</span><span class="sxs-lookup"><span data-stu-id="99b6e-525">For example, if the declaration of the previous `i` did not include an initial value, the compiler would report an error for the subsequent usages of `i` because `i` would not be definitely assigned at those points in the program.</span></span>

<span data-ttu-id="99b6e-526">Un metodo può usare istruzioni `return` per restituire il controllo al chiamante.</span><span class="sxs-lookup"><span data-stu-id="99b6e-526">A method can use `return` statements to return control to its caller.</span></span> <span data-ttu-id="99b6e-527">In un metodo che restituisce `void`, le istruzioni `return` non possono specificare un'espressione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-527">In a method returning `void`, `return` statements cannot specify an expression.</span></span> <span data-ttu-id="99b6e-528">In un metodo di restituzione non -`void`, `return` l'istruzione deve includere un'espressione che calcola il valore restituito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-528">In a method returning non-`void`, `return` statements must include an expression that computes the return value.</span></span>

#### <a name="static-and-instance-methods"></a><span data-ttu-id="99b6e-529">Metodi statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="99b6e-529">Static and instance methods</span></span>

<span data-ttu-id="99b6e-530">Un metodo dichiarato con un `static` modificatore è una ***metodo statico***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-530">A method declared with a `static` modifier is a ***static method***.</span></span> <span data-ttu-id="99b6e-531">Questo metodo non agisce su un'istanza specifica e può accedere direttamente solo a membri statici.</span><span class="sxs-lookup"><span data-stu-id="99b6e-531">A static method does not operate on a specific instance and can only directly access static members.</span></span>

<span data-ttu-id="99b6e-532">Un metodo dichiarato senza un `static` modificatore è un ***metodo di istanza***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-532">A method declared without a `static` modifier is an ***instance method***.</span></span> <span data-ttu-id="99b6e-533">Questo metodo agisce su un'istanza specifica e può accedere a membri statici e di istanza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-533">An instance method operates on a specific instance and can access both static and instance members.</span></span> <span data-ttu-id="99b6e-534">L'istanza in cui è stato richiamato un metodo di istanza è accessibile in modo esplicito come `this`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-534">The instance on which an instance method was invoked can be explicitly accessed as `this`.</span></span> <span data-ttu-id="99b6e-535">È un errore fare riferimento a `this` in un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="99b6e-535">It is an error to refer to `this` in a static method.</span></span>

<span data-ttu-id="99b6e-536">La classe `Entity` seguente contiene sia membri statici sia membri di istanza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-536">The following `Entity` class has both static and instance members.</span></span>

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
<span data-ttu-id="99b6e-537">Ogni istanza `Entity` contiene un numero di serie (e probabilmente anche altre informazioni non illustrate qui).</span><span class="sxs-lookup"><span data-stu-id="99b6e-537">Each `Entity` instance contains a serial number (and presumably some other information that is not shown here).</span></span> <span data-ttu-id="99b6e-538">Il costruttore `Entity` (simile a un metodo di istanza) inizializza la nuova istanza con il successivo numero di serie disponibile.</span><span class="sxs-lookup"><span data-stu-id="99b6e-538">The `Entity` constructor (which is like an instance method) initializes the new instance with the next available serial number.</span></span> <span data-ttu-id="99b6e-539">Poiché il costruttore è un membro di istanza, è consentito accedere sia al campo di istanza `serialNo` sia al campo statico `nextSerialNo`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-539">Because the constructor is an instance member, it is permitted to access both the `serialNo` instance field and the `nextSerialNo` static field.</span></span>

<span data-ttu-id="99b6e-540">I metodi statici `GetNextSerialNo` e `SetNextSerialNo` possono accedere al campo statico `nextSerialNo`, ma si verificherebbe un errore se accedessero direttamente al campo di istanza `serialNo`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-540">The `GetNextSerialNo` and `SetNextSerialNo` static methods can access the `nextSerialNo` static field, but it would be an error for them to directly access the `serialNo` instance field.</span></span>

<span data-ttu-id="99b6e-541">L'esempio seguente illustra l'uso del `Entity` classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-541">The following example shows the use of the `Entity` class.</span></span>

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
<span data-ttu-id="99b6e-542">Osservare come, mentre i metodi statici `SetNextSerialNo` e `GetNextSerialNo` vengono richiamati sulla classe, il metodo di istanza `GetSerialNo` viene richiamato su istanze della classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-542">Note that the `SetNextSerialNo` and `GetNextSerialNo` static methods are invoked on the class whereas the `GetSerialNo` instance method is invoked on instances of the class.</span></span>

#### <a name="virtual-override-and-abstract-methods"></a><span data-ttu-id="99b6e-543">Metodi virtuali, di override e astratti</span><span class="sxs-lookup"><span data-stu-id="99b6e-543">Virtual, override, and abstract methods</span></span>

<span data-ttu-id="99b6e-544">Se una dichiarazione di metodo di istanza include un modificatore `virtual`, il metodo viene definito ***metodo virtuale***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-544">When an instance method declaration includes a `virtual` modifier, the method is said to be a ***virtual method***.</span></span> <span data-ttu-id="99b6e-545">Se non si specifica `virtual` modificatore è presente, il metodo viene definito un ***metodo non virtuale***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-545">When no `virtual` modifier is present, the method is said to be a ***non-virtual method***.</span></span>

<span data-ttu-id="99b6e-546">Quando viene richiamato un metodo virtuale, il ***tipo in fase di esecuzione*** dell'istanza per cui viene eseguita la chiamata determina l'implementazione effettiva del metodo da richiamare.</span><span class="sxs-lookup"><span data-stu-id="99b6e-546">When a virtual method is invoked, the ***run-time type*** of the instance for which that invocation takes place determines the actual method implementation to invoke.</span></span> <span data-ttu-id="99b6e-547">In una chiamata a un metodo non virtuale, il fattore determinante è il ***tipo in fase di compilazione*** dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-547">In a nonvirtual method invocation, the ***compile-time type*** of the instance is the determining factor.</span></span>

<span data-ttu-id="99b6e-548">Un metodo virtuale può essere ***sottoposto a override*** in una classe derivata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-548">A virtual method can be ***overridden*** in a derived class.</span></span> <span data-ttu-id="99b6e-549">Quando una dichiarazione di metodo di istanza include un `override` modificatore, il metodo esegue l'override di un metodo virtuale ereditato con la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="99b6e-549">When an instance method declaration includes an `override` modifier, the method overrides an inherited virtual method with the same signature.</span></span> <span data-ttu-id="99b6e-550">Mentre una dichiarazione di metodo virtuale introduce un nuovo metodo, una dichiarazione di metodo di override specializza un metodo virtuale ereditato esistente specificando una nuova implementazione del metodo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-550">Whereas a virtual method declaration introduces a new method, an override method declaration specializes an existing inherited virtual method by providing a new implementation of that method.</span></span>

<span data-ttu-id="99b6e-551">Un' ***astratta*** è un metodo virtuale senza implementazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-551">An ***abstract*** method is a virtual method with no implementation.</span></span> <span data-ttu-id="99b6e-552">Un metodo astratto viene dichiarato con la `abstract` modificatore ed è consentito solo in una classe che viene dichiarata `abstract`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-552">An abstract method is declared with the `abstract` modifier and is permitted only in a class that is also declared `abstract`.</span></span> <span data-ttu-id="99b6e-553">Un metodo astratto deve essere sottoposto a override in ogni classe derivata non astratta.</span><span class="sxs-lookup"><span data-stu-id="99b6e-553">An abstract method must be overridden in every non-abstract derived class.</span></span>

<span data-ttu-id="99b6e-554">Nell'esempio seguente viene dichiarata una classe astratta, `Expression`, che rappresenta un nodo dell'albero delle espressioni, e tre classi derivate, `Constant`, `VariableReference` e `Operation`, che implementano i nodi dell'albero delle espressioni relativi a costanti, riferimenti a variabili e operazioni aritmetiche.</span><span class="sxs-lookup"><span data-stu-id="99b6e-554">The following example declares an abstract class, `Expression`, which represents an expression tree node, and three derived classes, `Constant`, `VariableReference`, and `Operation`, which implement expression tree nodes for constants, variable references, and arithmetic operations.</span></span> <span data-ttu-id="99b6e-555">(È simile a, ma non deve per essere confusa con i tipi di albero delle espressioni introdotta in [tipi di albero delle espressioni](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="99b6e-555">(This is similar to, but not to be confused with the expression tree types introduced in [Expression tree types](types.md#expression-tree-types)).</span></span>

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
<span data-ttu-id="99b6e-556">Le quattro classi precedenti possono essere usate per modellare espressioni aritmetiche.</span><span class="sxs-lookup"><span data-stu-id="99b6e-556">The previous four classes can be used to model arithmetic expressions.</span></span> <span data-ttu-id="99b6e-557">Usando istanze di queste classi, l'espressione `x + 3`, ad esempio, può essere rappresentata come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-557">For example, using instances of these classes, the expression `x + 3` can be represented as follows.</span></span>

```csharp
Expression e = new Operation(
    new VariableReference("x"),
    '+',
    new Constant(3));
```
<span data-ttu-id="99b6e-558">Il metodo `Evaluate` di un'istanza `Expression` viene richiamato per valutare l'espressione specificata e generare un valore `double`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-558">The `Evaluate` method of an `Expression` instance is invoked to evaluate the given expression and produce a `double` value.</span></span> <span data-ttu-id="99b6e-559">Il metodo accetta come argomento un `Hashtable` che contiene i nomi delle variabili (come chiavi delle voci) e i valori (come valori delle voci).</span><span class="sxs-lookup"><span data-stu-id="99b6e-559">The method takes as an argument a `Hashtable` that contains variable names (as keys of the entries) and values (as values of the entries).</span></span> <span data-ttu-id="99b6e-560">Il `Evaluate` è un metodo astratto virtuale, vale a dire che le classi derivate non astratte necessario eseguirne l'override per fornire un'implementazione effettiva.</span><span class="sxs-lookup"><span data-stu-id="99b6e-560">The `Evaluate` method is a virtual abstract method, meaning that non-abstract derived classes must override it to provide an actual implementation.</span></span>

<span data-ttu-id="99b6e-561">L'implementazione di un valore `Constant` del metodo `Evaluate` restituisce semplicemente la costante memorizzata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-561">A `Constant`'s implementation of `Evaluate` simply returns the stored constant.</span></span> <span data-ttu-id="99b6e-562">Oggetto `VariableReference`dell'implementazione cerca il nome della variabile nella tabella hash e restituisce il valore risultante.</span><span class="sxs-lookup"><span data-stu-id="99b6e-562">A `VariableReference`'s implementation looks up the variable name in the hashtable and returns the resulting value.</span></span> <span data-ttu-id="99b6e-563">L'implementazione di un valore `Operation` valuta prima gli operandi sinistro e destro (richiamando in modo ricorsivo i metodi `Evaluate`) e quindi esegue l'operazione aritmetica specificata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-563">An `Operation`'s implementation first evaluates the left and right operands (by recursively invoking their `Evaluate` methods) and then performs the given arithmetic operation.</span></span>

<span data-ttu-id="99b6e-564">Il programma seguente usa le classi `Expression` per valutare l'espressione `x * (y + 2)` per valori diversi di `x` e `y`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-564">The following program uses the `Expression` classes to evaluate the expression `x * (y + 2)` for different values of `x` and `y`.</span></span>

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

#### <a name="method-overloading"></a><span data-ttu-id="99b6e-565">Overload di un metodo</span><span class="sxs-lookup"><span data-stu-id="99b6e-565">Method overloading</span></span>

<span data-ttu-id="99b6e-566">L'***overload*** di un metodo consente a più metodi della stessa classe di avere lo stesso nome, purché abbiano firme univoche.</span><span class="sxs-lookup"><span data-stu-id="99b6e-566">Method ***overloading*** permits multiple methods in the same class to have the same name as long as they have unique signatures.</span></span> <span data-ttu-id="99b6e-567">Quando si compila una chiamata di un metodo di overload, il compilatore usa la ***risoluzione dell'overload*** per determinare il metodo specifico da richiamare.</span><span class="sxs-lookup"><span data-stu-id="99b6e-567">When compiling an invocation of an overloaded method, the compiler uses ***overload resolution*** to determine the specific method to invoke.</span></span> <span data-ttu-id="99b6e-568">La risoluzione dell'overload trova il metodo che meglio corrisponde agli argomenti o segnala un errore se non riesce a trovare alcuna corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-568">Overload resolution finds the one method that best matches the arguments or reports an error if no single best match can be found.</span></span> <span data-ttu-id="99b6e-569">Nell'esempio seguente viene illustrato il funzionamento effettivo della risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="99b6e-569">The following example shows overload resolution in effect.</span></span> <span data-ttu-id="99b6e-570">Il commento relativo a ogni chiamata del metodo `Main` mostra il metodo effettivamente richiamato.</span><span class="sxs-lookup"><span data-stu-id="99b6e-570">The comment for each invocation in the `Main` method shows which method is actually invoked.</span></span>

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
<span data-ttu-id="99b6e-571">Come illustrato nell'esempio, è sempre possibile selezionare un metodo specifico eseguendo in modo esplicito il cast degli argomenti ai tipi di parametro corretti e/o specificando in modo esplicito gli argomenti di tipo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-571">As shown by the example, a particular method can always be selected by explicitly casting the arguments to the exact parameter types and/or explicitly supplying type arguments.</span></span>

### <a name="other-function-members"></a><span data-ttu-id="99b6e-572">Altri membri di funzione</span><span class="sxs-lookup"><span data-stu-id="99b6e-572">Other function members</span></span>

<span data-ttu-id="99b6e-573">I membri che contengono codice eseguibile sono noti come ***membri funzione*** di una classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-573">Members that contain executable code are collectively known as the ***function members*** of a class.</span></span> <span data-ttu-id="99b6e-574">Nella sezione precedente sono stati descritti i metodi, che costituiscono i membri di funzione principali.</span><span class="sxs-lookup"><span data-stu-id="99b6e-574">The preceding section describes methods, which are the primary kind of function members.</span></span> <span data-ttu-id="99b6e-575">In questa sezione vengono descritti altri membri di funzione supportati da c#: costruttori, proprietà, indicizzatori, eventi, operatori e i distruttori.</span><span class="sxs-lookup"><span data-stu-id="99b6e-575">This section describes the other kinds of function members supported by C#: constructors, properties, indexers, events, operators, and destructors.</span></span>

<span data-ttu-id="99b6e-576">Il codice seguente illustra una classe generica denominata `List<T>`, che implementa un elenco espandibile di oggetti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-576">The following code shows a generic class called `List<T>`, which implements a growable list of objects.</span></span> <span data-ttu-id="99b6e-577">Nella classe sono contenuti alcuni esempi di membri di funzione più comuni.</span><span class="sxs-lookup"><span data-stu-id="99b6e-577">The class contains several examples of the most common kinds of function members.</span></span>


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

#### <a name="constructors"></a><span data-ttu-id="99b6e-578">Costruttori</span><span class="sxs-lookup"><span data-stu-id="99b6e-578">Constructors</span></span>

<span data-ttu-id="99b6e-579">C# supporta sia costruttori di istanza sia costruttori statici.</span><span class="sxs-lookup"><span data-stu-id="99b6e-579">C# supports both instance and static constructors.</span></span> <span data-ttu-id="99b6e-580">Un ***costruttore di istanza*** è un membro che implementa le azioni necessarie per inizializzare un'istanza di una classe,</span><span class="sxs-lookup"><span data-stu-id="99b6e-580">An ***instance constructor*** is a member that implements the actions required to initialize an instance of a class.</span></span> <span data-ttu-id="99b6e-581">mentre un ***costruttore statico*** è un membro che implementa le azioni necessarie per inizializzare una classe nel momento in cui viene caricata per la prima volta.</span><span class="sxs-lookup"><span data-stu-id="99b6e-581">A ***static constructor*** is a member that implements the actions required to initialize a class itself when it is first loaded.</span></span>

<span data-ttu-id="99b6e-582">Un costruttore viene dichiarato come un metodo, senza tipo restituito e con lo stesso nome della classe in cui è contenuto.</span><span class="sxs-lookup"><span data-stu-id="99b6e-582">A constructor is declared like a method with no return type and the same name as the containing class.</span></span> <span data-ttu-id="99b6e-583">Se una dichiarazione di costruttore include un `static` modificatore, dichiara un costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="99b6e-583">If a constructor declaration includes a `static` modifier, it declares a static constructor.</span></span> <span data-ttu-id="99b6e-584">In caso contrario, dichiara un costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-584">Otherwise, it declares an instance constructor.</span></span>

<span data-ttu-id="99b6e-585">Costruttori di istanza possono essere sottoposti a overload.</span><span class="sxs-lookup"><span data-stu-id="99b6e-585">Instance constructors can be overloaded.</span></span> <span data-ttu-id="99b6e-586">La classe `List<T>`, ad esempio, dichiara due costruttori di istanza, uno senza parametri e uno che accetta un parametro `int`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-586">For example, the `List<T>` class declares two instance constructors, one with no parameters and one that takes an `int` parameter.</span></span> <span data-ttu-id="99b6e-587">I costruttori di istanza vengono richiamati con l'operatore `new`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-587">Instance constructors are invoked using the `new` operator.</span></span> <span data-ttu-id="99b6e-588">Le istruzioni seguenti allocano due `List<string>` istanze usando tutti i costruttori dell'ambito di `List` classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-588">The following statements allocate two `List<string>` instances using each of the constructors of the `List` class.</span></span>

```csharp
List<string> list1 = new List<string>();
List<string> list2 = new List<string>(10);
```
<span data-ttu-id="99b6e-589">A differenza di altri membri, i costruttori di istanza non vengono ereditati e una classe non può contenere costruttori di istanza diversi da quelli effettivamente dichiarati nella classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-589">Unlike other members, instance constructors are not inherited, and a class has no instance constructors other than those actually declared in the class.</span></span> <span data-ttu-id="99b6e-590">Se per una classe non è specificato alcun costruttore di istanza, ne viene automaticamente fornito uno vuoto senza parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-590">If no instance constructor is supplied for a class, then an empty one with no parameters is automatically provided.</span></span>

#### <a name="properties"></a><span data-ttu-id="99b6e-591">Proprietà</span><span class="sxs-lookup"><span data-stu-id="99b6e-591">Properties</span></span>

<span data-ttu-id="99b6e-592">Le ***proprietà*** sono una naturale estensione dei campi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-592">***Properties*** are a natural extension of fields.</span></span> <span data-ttu-id="99b6e-593">Entrambi sono membri denominati con tipi associati e la sintassi per accedere ai campi e alle proprietà è identica.</span><span class="sxs-lookup"><span data-stu-id="99b6e-593">Both are named members with associated types, and the syntax for accessing fields and properties is the same.</span></span> <span data-ttu-id="99b6e-594">A differenza dei campi, tuttavia, le proprietà non denotano posizioni di memoria,</span><span class="sxs-lookup"><span data-stu-id="99b6e-594">However, unlike fields, properties do not denote storage locations.</span></span> <span data-ttu-id="99b6e-595">ma hanno ***funzioni di accesso*** che specificano le istruzioni da eseguire nel momento in cui ne vengono letti o scritti i valori.</span><span class="sxs-lookup"><span data-stu-id="99b6e-595">Instead, properties have ***accessors*** that specify the statements to be executed when their values are read or written.</span></span>

<span data-ttu-id="99b6e-596">Una proprietà viene dichiarata come un campo, ad eccezione del fatto che la dichiarazione termina con un `get` funzione di accesso e/o un `set` funzione di accesso scritte tra i delimitatori `{` e `}` invece che terminano con un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="99b6e-596">A property is declared like a field, except that the declaration ends with a `get` accessor and/or a `set` accessor written between the delimiters `{` and `}` instead of ending in a semicolon.</span></span> <span data-ttu-id="99b6e-597">Una proprietà che dispone di entrambe un `get` funzione di accesso e un `set` funzione di accesso è un ***proprietà di lettura / scrittura***, una proprietà è disponibile solo un `get` funzione di accesso è un ***proprietà di sola lettura***e un proprietà che è presente solo una `set` funzione di accesso è un ***proprietà di sola scrittura***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-597">A property that has both a `get` accessor and a `set` accessor is a ***read-write property***, a property that has only a `get` accessor is a ***read-only property***, and a property that has only a `set` accessor is a ***write-only property***.</span></span>

<span data-ttu-id="99b6e-598">Oggetto `get` della funzione di accesso corrispondente a un metodo senza parametri con un valore restituito del tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="99b6e-598">A `get` accessor corresponds to a parameterless method with a return value of the property type.</span></span> <span data-ttu-id="99b6e-599">Fatta eccezione per quanto la destinazione di un'assegnazione, quando viene fatto riferimento a una proprietà in un'espressione, il `get` della funzione di accesso della proprietà viene richiamata per calcolare il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="99b6e-599">Except as the target of an assignment, when a property is referenced in an expression, the `get` accessor of the property is invoked to compute the value of the property.</span></span>

<span data-ttu-id="99b6e-600">Oggetto `set` funzione di accesso corrisponde a un metodo con un singolo parametro denominato `value` e senza tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-600">A `set` accessor corresponds to a method with a single parameter named `value` and no return type.</span></span> <span data-ttu-id="99b6e-601">Se si fa riferimento a una proprietà come destinazione di un'assegnazione o come operando del `++` oppure `--`, il `set` della funzione di accesso viene richiamato con un argomento che fornisce il nuovo valore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-601">When a property is referenced as the target of an assignment or as the operand of `++` or `--`, the `set` accessor is invoked with an argument that provides the new value.</span></span>

<span data-ttu-id="99b6e-602">La classe `List<T>` dichiara due proprietà, `Count` e `Capacity`, che sono rispettivamente di sola lettura e di lettura/scrittura.</span><span class="sxs-lookup"><span data-stu-id="99b6e-602">The `List<T>` class declares two properties, `Count` and `Capacity`, which are read-only and read-write, respectively.</span></span> <span data-ttu-id="99b6e-603">Di seguito è riportato un esempio d'uso di queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="99b6e-603">The following is an example of use of these properties.</span></span>

```csharp
List<string> names = new List<string>();
names.Capacity = 100;            // Invokes set accessor
int i = names.Count;             // Invokes get accessor
int j = names.Capacity;          // Invokes get accessor
```
<span data-ttu-id="99b6e-604">Come per i campi e i metodi, C# supporta sia proprietà di istanza sia proprietà statiche.</span><span class="sxs-lookup"><span data-stu-id="99b6e-604">Similar to fields and methods, C# supports both instance properties and static properties.</span></span> <span data-ttu-id="99b6e-605">Proprietà statiche vengono dichiarate con la `static` modificatore e le proprietà dell'istanza vengono dichiarate senza di essa.</span><span class="sxs-lookup"><span data-stu-id="99b6e-605">Static properties are declared with the `static` modifier, and instance properties are declared without it.</span></span>

<span data-ttu-id="99b6e-606">Le funzioni di accesso di una proprietà possono essere virtuali.</span><span class="sxs-lookup"><span data-stu-id="99b6e-606">The accessor(s) of a property can be virtual.</span></span> <span data-ttu-id="99b6e-607">Se una dichiarazione di proprietà contiene un modificatore `virtual`, `abstract` o `override`, questo viene applicato anche alle funzioni di accesso della proprietà.</span><span class="sxs-lookup"><span data-stu-id="99b6e-607">When a property declaration includes a `virtual`, `abstract`, or `override` modifier, it applies to the accessor(s) of the property.</span></span>

#### <a name="indexers"></a><span data-ttu-id="99b6e-608">Indicizzatori</span><span class="sxs-lookup"><span data-stu-id="99b6e-608">Indexers</span></span>

<span data-ttu-id="99b6e-609">Un ***indicizzatore*** è un membro che consente di indicizzare gli oggetti esattamente come una matrice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-609">An ***indexer*** is a member that enables objects to be indexed in the same way as an array.</span></span> <span data-ttu-id="99b6e-610">Un indicizzatore viene dichiarato come una proprietà, ad eccezione del fatto che il nome del membro `this` seguita da un elenco di parametri scritto tra i delimitatori `[` e `]`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-610">An indexer is declared like a property except that the name of the member is `this` followed by a parameter list written between the delimiters `[` and `]`.</span></span> <span data-ttu-id="99b6e-611">I parametri sono disponibili nelle funzioni di accesso dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-611">The parameters are available in the accessor(s) of the indexer.</span></span> <span data-ttu-id="99b6e-612">Analogamente alle proprietà, gli indicizzatori possono essere di lettura/scrittura, di sola lettura o di sola scrittura e le funzioni di accesso di un indicizzatore possono essere virtuali.</span><span class="sxs-lookup"><span data-stu-id="99b6e-612">Similar to properties, indexers can be read-write, read-only, and write-only, and the accessor(s) of an indexer can be virtual.</span></span>

<span data-ttu-id="99b6e-613">La classe `List` dichiara un indicizzatore di lettura/scrittura che accetta un parametro `int`</span><span class="sxs-lookup"><span data-stu-id="99b6e-613">The `List` class declares a single read-write indexer that takes an `int` parameter.</span></span> <span data-ttu-id="99b6e-614">e consente di indicizzare istanze `List` con valori `int`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-614">The indexer makes it possible to index `List` instances with `int` values.</span></span> <span data-ttu-id="99b6e-615">Esempio:</span><span class="sxs-lookup"><span data-stu-id="99b6e-615">For example</span></span>

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
<span data-ttu-id="99b6e-616">Gli indicizzatori possono essere sottoposti a overload, ovvero una classe può dichiarare più indicizzatori purché includano un numero o tipi diversi di parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-616">Indexers can be overloaded, meaning that a class can declare multiple indexers as long as the number or types of their parameters differ.</span></span>

#### <a name="events"></a><span data-ttu-id="99b6e-617">Eventi</span><span class="sxs-lookup"><span data-stu-id="99b6e-617">Events</span></span>

<span data-ttu-id="99b6e-618">Un ***evento*** è un membro che consente a una classe o a un oggetto di inviare notifiche.</span><span class="sxs-lookup"><span data-stu-id="99b6e-618">An ***event*** is a member that enables a class or object to provide notifications.</span></span> <span data-ttu-id="99b6e-619">Un evento viene dichiarato come un campo, ad eccezione del fatto che la dichiarazione include un `event` (parola chiave) e il tipo deve essere un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="99b6e-619">An event is declared like a field except that the declaration includes an `event` keyword and the type must be a delegate type.</span></span>

<span data-ttu-id="99b6e-620">In una classe che dichiara un membro di evento, l'evento si comporta esattamente come un campo di un tipo delegato, purché l'evento non sia astratto e non dichiari funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="99b6e-620">Within a class that declares an event member, the event behaves just like a field of a delegate type (provided the event is not abstract and does not declare accessors).</span></span> <span data-ttu-id="99b6e-621">Il campo archivia un riferimento a un delegato che rappresenta i gestori eventi aggiunti all'evento.</span><span class="sxs-lookup"><span data-stu-id="99b6e-621">The field stores a reference to a delegate that represents the event handlers that have been added to the event.</span></span> <span data-ttu-id="99b6e-622">Se non sono presente alcun gestore eventi, il campo è `null`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-622">If no event handles are present, the field is `null`.</span></span>

<span data-ttu-id="99b6e-623">La classe `List<T>` dichiara un singolo membro di evento denominato `Changed`, con cui si indica che un nuovo elemento è stato aggiunto all'elenco.</span><span class="sxs-lookup"><span data-stu-id="99b6e-623">The `List<T>` class declares a single event member called `Changed`, which indicates that a new item has been added to the list.</span></span> <span data-ttu-id="99b6e-624">Il `Changed` evento viene generato mediante il `OnChanged` metodo virtuale, che prima controlla se l'evento è `null` (vale a dire che è presente alcun gestore).</span><span class="sxs-lookup"><span data-stu-id="99b6e-624">The `Changed` event is raised by the `OnChanged` virtual method, which first checks whether the event is `null` (meaning that no handlers are present).</span></span> <span data-ttu-id="99b6e-625">Generare un evento equivale a richiamare il delegato rappresentato dall'evento. Non sono quindi necessari speciali costrutti di linguaggio per generare eventi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-625">The notion of raising an event is precisely equivalent to invoking the delegate represented by the event—thus, there are no special language constructs for raising events.</span></span>

<span data-ttu-id="99b6e-626">I client rispondono agli eventi tramite ***gestori eventi***,</span><span class="sxs-lookup"><span data-stu-id="99b6e-626">Clients react to events through ***event handlers***.</span></span> <span data-ttu-id="99b6e-627">che possono essere aggiunti con l'operatore `+=` e rimossi con l'operatore `-=`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-627">Event handlers are attached using the `+=` operator and removed using the `-=` operator.</span></span> <span data-ttu-id="99b6e-628">Nell'esempio seguente un gestore eventi viene aggiunto all'evento `Changed` di `List<string>`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-628">The following example attaches an event handler to the `Changed` event of a `List<string>`.</span></span>

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
<span data-ttu-id="99b6e-629">Negli scenari avanzati in cui è auspicabile controllare l'archiviazione sottostante di un evento, una dichiarazione di evento può fornire in modo esplicito le funzioni di accesso `add` e `remove`, simili alla funzione di accesso `set` di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="99b6e-629">For advanced scenarios where control of the underlying storage of an event is desired, an event declaration can explicitly provide `add` and `remove` accessors, which are somewhat similar to the `set` accessor of a property.</span></span>

#### <a name="operators"></a><span data-ttu-id="99b6e-630">Operatori</span><span class="sxs-lookup"><span data-stu-id="99b6e-630">Operators</span></span>

<span data-ttu-id="99b6e-631">Un ***operatore*** è un membro che definisce il significato dell'applicazione di un particolare operatore di espressione alle istanze di una classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-631">An ***operator*** is a member that defines the meaning of applying a particular expression operator to instances of a class.</span></span> <span data-ttu-id="99b6e-632">È possibile definire tre tipi di operatori: unari, binari e di conversione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-632">Three kinds of operators can be defined: unary operators, binary operators, and conversion operators.</span></span> <span data-ttu-id="99b6e-633">Tutti gli operatori devono essere dichiarati come `public` e `static`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-633">All operators must be declared as `public` and `static`.</span></span>

<span data-ttu-id="99b6e-634">La classe `List<T>` dichiara due operatori, `operator==` e `operator!=`, attribuendo così un nuovo significato alle espressioni che applicano questi operatori alle istanze di `List`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-634">The `List<T>` class declares two operators, `operator==` and `operator!=`, and thus gives new meaning to expressions that apply those operators to `List` instances.</span></span> <span data-ttu-id="99b6e-635">In particolare, gli operatori definiscono l'uguaglianza di due `List<T>` istanze, confrontando ognuno degli oggetti contenuti con loro `Equals` metodi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-635">Specifically, the operators define equality of two `List<T>` instances as comparing each of the contained objects using their `Equals` methods.</span></span> <span data-ttu-id="99b6e-636">Nell'esempio seguente viene usato l'operatore `==` per confrontare due istanze di `List<int>`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-636">The following example uses the `==` operator to compare two `List<int>` instances.</span></span>

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

<span data-ttu-id="99b6e-637">Il primo `Console.WriteLine` restituisce `True` perché i due elenchi contengono lo stesso numero di oggetti con gli stessi valori e nello stesso ordine.</span><span class="sxs-lookup"><span data-stu-id="99b6e-637">The first `Console.WriteLine` outputs `True` because the two lists contain the same number of objects with the same values in the same order.</span></span> <span data-ttu-id="99b6e-638">Se in `List<T>` non fosse stato definito l'operatore `operator==`, il primo `Console.WriteLine` avrebbe restituito `False` perché `a` e `b` fanno riferimento a istanze di `List<int>` diverse.</span><span class="sxs-lookup"><span data-stu-id="99b6e-638">Had `List<T>` not defined `operator==`, the first `Console.WriteLine` would have output `False` because `a` and `b` reference different `List<int>` instances.</span></span>

#### <a name="destructors"></a><span data-ttu-id="99b6e-639">Distruttori</span><span class="sxs-lookup"><span data-stu-id="99b6e-639">Destructors</span></span>

<span data-ttu-id="99b6e-640">Oggetto ***distruttore*** è un membro che implementa le azioni necessarie per distruggere un'istanza di una classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-640">A ***destructor*** is a member that implements the actions required to destruct an instance of a class.</span></span> <span data-ttu-id="99b6e-641">I distruttori non possono avere parametri e non possono avere modificatori di accessibilità non possono essere richiamati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-641">Destructors cannot have parameters, they cannot have accessibility modifiers, and they cannot be invoked explicitly.</span></span> <span data-ttu-id="99b6e-642">Il distruttore per un'istanza viene richiamato automaticamente durante l'operazione di garbage collection.</span><span class="sxs-lookup"><span data-stu-id="99b6e-642">The destructor for an instance is invoked automatically during garbage collection.</span></span>

<span data-ttu-id="99b6e-643">Il garbage collector viene lasciato ampia scelta per decidere quando raccogliere oggetti ed eseguire i distruttori.</span><span class="sxs-lookup"><span data-stu-id="99b6e-643">The garbage collector is allowed wide latitude in deciding when to collect objects and run destructors.</span></span> <span data-ttu-id="99b6e-644">In particolare, i tempi delle chiamate di distruttore non sono deterministico e i distruttori possono essere eseguiti in qualsiasi thread.</span><span class="sxs-lookup"><span data-stu-id="99b6e-644">Specifically, the timing of destructor invocations is not deterministic, and destructors may be executed on any thread.</span></span> <span data-ttu-id="99b6e-645">Per questi e altri motivi, le classi devono implementare i distruttori solo quando nessun altro soluzioni sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="99b6e-645">For these and other reasons, classes should implement destructors only when no other solutions are feasible.</span></span>

<span data-ttu-id="99b6e-646">L'istruzione `using` offre una soluzione più efficace per l'eliminazione di oggetti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-646">The `using` statement provides a better approach to object destruction.</span></span>

## <a name="structs"></a><span data-ttu-id="99b6e-647">Struct</span><span class="sxs-lookup"><span data-stu-id="99b6e-647">Structs</span></span>

<span data-ttu-id="99b6e-648">Analogamente alle classi, i tipi ***struct*** sono strutture dati che possono contenere membri dati e membri funzione. A differenza delle classi, tuttavia, i tipi struct sono tipi valore e non richiedono l'allocazione dell'heap.</span><span class="sxs-lookup"><span data-stu-id="99b6e-648">Like classes, ***structs*** are data structures that can contain data members and function members, but unlike classes, structs are value types and do not require heap allocation.</span></span> <span data-ttu-id="99b6e-649">Una variabile di un tipo struct archivia direttamente i relativi dati, mentre una variabile di un tipo classe archivia un riferimento a un oggetto allocato in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="99b6e-649">A variable of a struct type directly stores the data of the struct, whereas a variable of a class type stores a reference to a dynamically allocated object.</span></span> <span data-ttu-id="99b6e-650">I tipi struct non supportano l'ereditarietà specificata dall'utente. Tutti i tipi struct ereditano implicitamente dal tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-650">Struct types do not support user-specified inheritance, and all struct types implicitly inherit from type `object`.</span></span>

<span data-ttu-id="99b6e-651">I tipi struct sono particolarmente utili per strutture dati di piccole dimensioni che hanno una semantica di valori.</span><span class="sxs-lookup"><span data-stu-id="99b6e-651">Structs are particularly useful for small data structures that have value semantics.</span></span> <span data-ttu-id="99b6e-652">I numeri complessi, i punti di un sistema di coordinate o le coppie chiave-valore di un dizionario sono buoni esempi di struct.</span><span class="sxs-lookup"><span data-stu-id="99b6e-652">Complex numbers, points in a coordinate system, or key-value pairs in a dictionary are all good examples of structs.</span></span> <span data-ttu-id="99b6e-653">L'uso dei tipi struct al posto delle classi può determinare una notevole differenza riguardo al numero di allocazioni di memoria eseguite da un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-653">The use of structs rather than classes for small data structures can make a large difference in the number of memory allocations an application performs.</span></span> <span data-ttu-id="99b6e-654">Il programma seguente, ad esempio, crea e inizializza una matrice di 100 punti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-654">For example, the following program creates and initializes an array of 100 points.</span></span> <span data-ttu-id="99b6e-655">Con `Point` implementato come classe, vengono create istanze di 101 oggetti separati, uno per la matrice e uno per ciascuno dei 100 elementi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-655">With `Point` implemented as a class, 101 separate objects are instantiated—one for the array and one each for the 100 elements.</span></span>

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
<span data-ttu-id="99b6e-656">In alternativa è possibile rendere `Point` uno struct.</span><span class="sxs-lookup"><span data-stu-id="99b6e-656">An alternative is to make `Point` a struct.</span></span>

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
<span data-ttu-id="99b6e-657">Viene ora creata un'istanza di un solo oggetto, quello relativo alla matrice, e le istanze di `Point` vengono memorizzate inline nella matrice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-657">Now, only one object is instantiated—the one for the array—and the `Point` instances are stored in-line in the array.</span></span>

<span data-ttu-id="99b6e-658">Con l'operatore `new` vengono chiamati i costruttori di struct, ma questo non implica l'allocazione di memoria.</span><span class="sxs-lookup"><span data-stu-id="99b6e-658">Struct constructors are invoked with the `new` operator, but that does not imply that memory is being allocated.</span></span> <span data-ttu-id="99b6e-659">Anziché allocare dinamicamente un oggetto e restituire un riferimento a quest'ultimo, un costruttore di struct restituisce semplicemente il valore del tipo struct (in genere una posizione temporanea nello stack), che viene quindi copiato in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="99b6e-659">Instead of dynamically allocating an object and returning a reference to it, a struct constructor simply returns the struct value itself (typically in a temporary location on the stack), and this value is then copied as necessary.</span></span>

<span data-ttu-id="99b6e-660">Con le classi, due variabili possono fare riferimento allo stesso oggetto e pertanto le operazioni su una variabile possono influire sull'oggetto a cui fa riferimento l'altra.</span><span class="sxs-lookup"><span data-stu-id="99b6e-660">With classes, it is possible for two variables to reference the same object and thus possible for operations on one variable to affect the object referenced by the other variable.</span></span> <span data-ttu-id="99b6e-661">Con i tipi struct, ogni variabile ha una propria copia dei dati e le operazioni su una variabile non possono influire sull'altra.</span><span class="sxs-lookup"><span data-stu-id="99b6e-661">With structs, the variables each have their own copy of the data, and it is not possible for operations on one to affect the other.</span></span> <span data-ttu-id="99b6e-662">Ad esempio, l'output prodotto dal frammento di codice seguente dipende dal fatto che `Point` è una classe o uno struct.</span><span class="sxs-lookup"><span data-stu-id="99b6e-662">For example, the output produced by the following code fragment depends on whether `Point` is a class or a struct.</span></span>

```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 20;
Console.WriteLine(b.x);
```
<span data-ttu-id="99b6e-663">Se `Point` è una classe, l'output viene `20` poiché `a` e `b` fanno riferimento allo stesso oggetto.</span><span class="sxs-lookup"><span data-stu-id="99b6e-663">If `Point` is a class, the output is `20` because `a` and `b` reference the same object.</span></span> <span data-ttu-id="99b6e-664">Se `Point` è uno struct, l'output viene `10` perché l'assegnazione dei `a` al `b` crea una copia del valore, e questa copia non è interessata dalla successiva assegnazione a `a.x`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-664">If `Point` is a struct, the output is `10` because the assignment of `a` to `b` creates a copy of the value, and this copy is unaffected by the subsequent assignment to `a.x`.</span></span>

<span data-ttu-id="99b6e-665">L'esempio precedente evidenzia due delle limitazioni dei tipi struct.</span><span class="sxs-lookup"><span data-stu-id="99b6e-665">The previous example highlights two of the limitations of structs.</span></span> <span data-ttu-id="99b6e-666">In primo luogo, la copia di un intero tipo struct è in genere meno efficiente della copia di un riferimento all'oggetto. Di conseguenza, il passaggio dei parametri di assegnazione e valore può risultare molto più costoso con i tipi struct che con i tipi riferimento.</span><span class="sxs-lookup"><span data-stu-id="99b6e-666">First, copying an entire struct is typically less efficient than copying an object reference, so assignment and value parameter passing can be more expensive with structs than with reference types.</span></span> <span data-ttu-id="99b6e-667">In secondo luogo, ad eccezione dei parametri `ref` e `out`, non è possibile creare riferimenti ai tipi struct e questa condizione che ne limita l'uso in varie situazioni.</span><span class="sxs-lookup"><span data-stu-id="99b6e-667">Second, except for `ref` and `out` parameters, it is not possible to create references to structs, which rules out their usage in a number of situations.</span></span>

## <a name="arrays"></a><span data-ttu-id="99b6e-668">Matrici</span><span class="sxs-lookup"><span data-stu-id="99b6e-668">Arrays</span></span>

<span data-ttu-id="99b6e-669">Una ***matrice*** è una struttura di dati contenente una serie di variabili accessibili tramite indici calcolati.</span><span class="sxs-lookup"><span data-stu-id="99b6e-669">An ***array*** is a data structure that contains a number of variables that are accessed through computed indices.</span></span> <span data-ttu-id="99b6e-670">Le variabili contenute in una matrice, chiamate anche ***elementi*** della matrice, sono tutte dello stesso tipo, definito ***tipo di elemento*** della matrice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-670">The variables contained in an array, also called the ***elements*** of the array, are all of the same type, and this type is called the ***element type*** of the array.</span></span>

<span data-ttu-id="99b6e-671">Poiché i tipi di matrice sono tipi di riferimento, la dichiarazione di una variabile di matrice si limita a riservare spazio per un riferimento a un'istanza di matrice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-671">Array types are reference types, and the declaration of an array variable simply sets aside space for a reference to an array instance.</span></span> <span data-ttu-id="99b6e-672">Istanze di matrice effettive vengono create dinamicamente in fase di esecuzione utilizzando il `new` operatore.</span><span class="sxs-lookup"><span data-stu-id="99b6e-672">Actual array instances are created dynamically at run-time using the `new` operator.</span></span> <span data-ttu-id="99b6e-673">Il `new` operazione consente di specificare il ***lunghezza*** della nuova istanza di matrice, che viene fissata per la durata dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="99b6e-673">The `new` operation specifies the ***length*** of the new array instance, which is then fixed for the lifetime of the instance.</span></span> <span data-ttu-id="99b6e-674">Gli indici degli elementi di una matrice sono compresi tra `0` e `Length - 1`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-674">The indices of the elements of an array range from `0` to `Length - 1`.</span></span> <span data-ttu-id="99b6e-675">L'operatore `new` inizializza automaticamente gli elementi di una matrice sul rispettivo valore predefinito che, ad esempio, equivale a zero per tutti i tipi numerici e a `null` per tutti i tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="99b6e-675">The `new` operator automatically initializes the elements of an array to their default value, which, for example, is zero for all numeric types and `null` for all reference types.</span></span>

<span data-ttu-id="99b6e-676">Nell'esempio seguente viene creata una matrice di elementi `int`, viene inizializzata la matrice e ne viene stampato il contenuto.</span><span class="sxs-lookup"><span data-stu-id="99b6e-676">The following example creates an array of `int` elements, initializes the array, and prints out the contents of the array.</span></span>

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
<span data-ttu-id="99b6e-677">In questo esempio viene creata e usata una ***matrice unidimensionale***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-677">This example creates and operates on a ***single-dimensional array***.</span></span> <span data-ttu-id="99b6e-678">C# supporta anche ***matrici multidimensionali***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-678">C# also supports ***multi-dimensional arrays***.</span></span> <span data-ttu-id="99b6e-679">Il numero di dimensioni di un tipo di matrice, chiamato anche ***rango*** del tipo di matrice, è pari a uno più il numero di virgole tra parentesi quadre del tipo di matrice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-679">The number of dimensions of an array type, also known as the ***rank*** of the array type, is one plus the number of commas written between the square brackets of the array type.</span></span> <span data-ttu-id="99b6e-680">Nell'esempio seguente consente di allocare una matrice unidimensionale, una bidimensionale e una matrice tridimensionale.</span><span class="sxs-lookup"><span data-stu-id="99b6e-680">The following example allocates a one-dimensional, a two-dimensional, and a three-dimensional array.</span></span>

```csharp
int[] a1 = new int[10];
int[,] a2 = new int[10, 5];
int[,,] a3 = new int[10, 5, 2];
```
<span data-ttu-id="99b6e-681">La matrice `a1` contiene 10 elementi, la matrice `a2` contiene 50 (10 × 5) elementi e la `a3` matrice contiene 100 (10 × 5 × 2) elementi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-681">The `a1` array contains 10 elements, the `a2` array contains 50 (10 × 5) elements, and the `a3` array contains 100 (10 × 5 × 2) elements.</span></span>

<span data-ttu-id="99b6e-682">L'elemento di una matrice può essere di qualsiasi tipo, anche di tipo matrice.</span><span class="sxs-lookup"><span data-stu-id="99b6e-682">The element type of an array can be any type, including an array type.</span></span> <span data-ttu-id="99b6e-683">Una matrice con elementi di tipo matrice viene chiamata talvolta ***matrice di matrici***, poiché le lunghezze delle matrici elemento non devono essere tutte uguali.</span><span class="sxs-lookup"><span data-stu-id="99b6e-683">An array with elements of an array type is sometimes called a ***jagged array*** because the lengths of the element arrays do not all have to be the same.</span></span> <span data-ttu-id="99b6e-684">Nell'esempio seguente viene allocata una matrice di matrici di `int`:</span><span class="sxs-lookup"><span data-stu-id="99b6e-684">The following example allocates an array of arrays of `int`:</span></span>

```csharp
int[][] a = new int[3][];
a[0] = new int[10];
a[1] = new int[5];
a[2] = new int[20];
```
<span data-ttu-id="99b6e-685">La prima riga crea una matrice con tre elementi, ognuno di tipo `int[]` e con un valore iniziale di `null`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-685">The first line creates an array with three elements, each of type `int[]` and each with an initial value of `null`.</span></span> <span data-ttu-id="99b6e-686">Le righe successive inizializzano quindi i tre elementi con riferimenti a singole istanze di matrice di lunghezza variabile.</span><span class="sxs-lookup"><span data-stu-id="99b6e-686">The subsequent lines then initialize the three elements with references to individual array instances of varying lengths.</span></span>

<span data-ttu-id="99b6e-687">Il `new` operatore consente di specificare i valori iniziali degli elementi della matrice essere specificata tramite un ***inizializzatore di matrice***, ovvero un elenco di espressioni scritte tra i delimitatori `{` e `}`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-687">The `new` operator permits the initial values of the array elements to be specified using an ***array initializer***, which is a list of expressions written between the delimiters `{` and `}`.</span></span> <span data-ttu-id="99b6e-688">Nell'esempio seguente viene allocata e inizializzata una matrice `int[]` con tre elementi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-688">The following example allocates and initializes an `int[]` with three elements.</span></span>

```csharp
int[] a = new int[] {1, 2, 3};
```
<span data-ttu-id="99b6e-689">Si noti che la lunghezza della matrice viene dedotta dal numero di espressioni tra `{` e `}`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-689">Note that the length of the array is inferred from the number of expressions between `{` and `}`.</span></span> <span data-ttu-id="99b6e-690">Per evitare di ridefinire il tipo di matrice, è possibile abbreviare le dichiarazioni di campo e variabile locale.</span><span class="sxs-lookup"><span data-stu-id="99b6e-690">Local variable and field declarations can be shortened further such that the array type does not have to be restated.</span></span>

```csharp
int[] a = {1, 2, 3};
```
<span data-ttu-id="99b6e-691">Entrambi gli esempi precedenti equivalgono a quello seguente:</span><span class="sxs-lookup"><span data-stu-id="99b6e-691">Both of the previous examples are equivalent to the following:</span></span>

```csharp
int[] t = new int[3];
t[0] = 1;
t[1] = 2;
t[2] = 3;
int[] a = t;
```
## <a name="interfaces"></a><span data-ttu-id="99b6e-692">Interfacce</span><span class="sxs-lookup"><span data-stu-id="99b6e-692">Interfaces</span></span>

<span data-ttu-id="99b6e-693">Un'***interfaccia*** definisce un contratto che può essere implementato da classi e struct.</span><span class="sxs-lookup"><span data-stu-id="99b6e-693">An ***interface*** defines a contract that can be implemented by classes and structs.</span></span> <span data-ttu-id="99b6e-694">Può contenere metodi, proprietà, eventi e indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="99b6e-694">An interface can contain methods, properties, events, and indexers.</span></span> <span data-ttu-id="99b6e-695">Un'interfaccia non fornisce le implementazioni dei membri che definisce, ma specifica semplicemente i membri che devono essere forniti dalle classi o dai tipi struct che la implementano.</span><span class="sxs-lookup"><span data-stu-id="99b6e-695">An interface does not provide implementations of the members it defines—it merely specifies the members that must be supplied by classes or structs that implement the interface.</span></span>

<span data-ttu-id="99b6e-696">Le interfacce possono usare l'***ereditarietà multipla***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-696">Interfaces may employ ***multiple inheritance***.</span></span> <span data-ttu-id="99b6e-697">Nell'esempio seguente l'interfaccia `IComboBox` eredita da `ITextBox` e `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-697">In the following example, the interface `IComboBox` inherits from both `ITextBox` and `IListBox`.</span></span>

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
<span data-ttu-id="99b6e-698">Classi e struct possono implementare più interfacce.</span><span class="sxs-lookup"><span data-stu-id="99b6e-698">Classes and structs can implement multiple interfaces.</span></span> <span data-ttu-id="99b6e-699">Nell'esempio seguente la classe `EditBox` implementa `IControl` e `IDataBound`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-699">In the following example, the class `EditBox` implements both `IControl` and `IDataBound`.</span></span>

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
<span data-ttu-id="99b6e-700">Quando una classe o un tipo struct implementa un'interfaccia specifica, le istanze di tale classe o struct possono essere convertite in modo implicito in quel tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="99b6e-700">When a class or struct implements a particular interface, instances of that class or struct can be implicitly converted to that interface type.</span></span> <span data-ttu-id="99b6e-701">Esempio:</span><span class="sxs-lookup"><span data-stu-id="99b6e-701">For example</span></span>

```csharp
EditBox editBox = new EditBox();
IControl control = editBox;
IDataBound dataBound = editBox;
```
<span data-ttu-id="99b6e-702">Nei casi in cui un'istanza non implementa staticamente un'interfaccia specifica è possibile usare cast di tipo dinamico.</span><span class="sxs-lookup"><span data-stu-id="99b6e-702">In cases where an instance is not statically known to implement a particular interface, dynamic type casts can be used.</span></span> <span data-ttu-id="99b6e-703">Ad esempio, le istruzioni seguenti usano cast di tipo dinamico per ottenere un oggetto `IControl` e `IDataBound` implementazioni di interfacce.</span><span class="sxs-lookup"><span data-stu-id="99b6e-703">For example, the following statements use dynamic type casts to obtain an object's `IControl` and `IDataBound` interface implementations.</span></span> <span data-ttu-id="99b6e-704">Poiché il tipo effettivo dell'oggetto è `EditBox`, i cast vengono eseguiti correttamente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-704">Because the actual type of the object is `EditBox`, the casts succeed.</span></span>

```csharp
object obj = new EditBox();
IControl control = (IControl)obj;
IDataBound dataBound = (IDataBound)obj;
```
<span data-ttu-id="99b6e-705">Nella precedente `EditBox` (classe), il `Paint` metodo dal `IControl` interfaccia e il `Bind` metodo dal `IDataBound` interfaccia vengono implementate usando `public` membri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-705">In the previous `EditBox` class, the `Paint` method from the `IControl` interface and the `Bind` method from the `IDataBound` interface are implemented using `public` members.</span></span> <span data-ttu-id="99b6e-706">C# supporta anche ***tali implementazioni***, con cui la classe o struct può evitare di specificare i membri `public`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-706">C# also supports ***explicit interface member implementations***, using which the class or struct can avoid making the members `public`.</span></span> <span data-ttu-id="99b6e-707">Un'implementazione esplicita di un membro dell'interfaccia viene scritta usando il nome completo del membro dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="99b6e-707">An explicit interface member implementation is written using the fully qualified interface member name.</span></span> <span data-ttu-id="99b6e-708">La classe `EditBox` può ad esempio implementare i metodi `IControl.Paint` e `IDataBound.Bind` usando implementazioni esplicite di un membro dell'interfaccia, come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-708">For example, the `EditBox` class could implement the `IControl.Paint` and `IDataBound.Bind` methods using explicit interface member implementations as follows.</span></span>

```csharp
public class EditBox: IControl, IDataBound
{
    void IControl.Paint() {...}
    void IDataBound.Bind(Binder b) {...}
}
```
<span data-ttu-id="99b6e-709">È possibile accedere ai membri dell'interfaccia esplicita solo tramite il tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="99b6e-709">Explicit interface members can only be accessed via the interface type.</span></span> <span data-ttu-id="99b6e-710">Ad esempio, l'implementazione di `IControl.Paint` fornite dalla precedente `EditBox` classe può essere richiamata solo convertendo per prima cosa il `EditBox` fanno riferimento al `IControl` tipo interfaccia.</span><span class="sxs-lookup"><span data-stu-id="99b6e-710">For example, the implementation of `IControl.Paint` provided by the previous `EditBox` class can only be invoked by first converting the `EditBox` reference to the `IControl` interface type.</span></span>

```csharp
EditBox editBox = new EditBox();
editBox.Paint();                        // Error, no such method
IControl control = editBox;
control.Paint();                        // Ok
```

## <a name="enums"></a><span data-ttu-id="99b6e-711">Enumerazioni</span><span class="sxs-lookup"><span data-stu-id="99b6e-711">Enums</span></span>

<span data-ttu-id="99b6e-712">Un ***tipo enum*** è un tipo valore distinto con un set di costanti denominate.</span><span class="sxs-lookup"><span data-stu-id="99b6e-712">An ***enum type*** is a distinct value type with a set of named constants.</span></span> <span data-ttu-id="99b6e-713">Nell'esempio seguente viene dichiarato e usato un tipo di enumerazione denominato `Color` con tre valori di costante `Red`, `Green`, e `Blue`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-713">The following example declares and uses an enum type named `Color` with three constant values, `Red`, `Green`, and `Blue`.</span></span>

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
<span data-ttu-id="99b6e-714">Ogni tipo di enumerazione ha un tipo integrale corrispondente denominato il ***tipo sottostante*** del tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-714">Each enum type has a corresponding integral type called the ***underlying type*** of the enum type.</span></span> <span data-ttu-id="99b6e-715">Un tipo di enumerazione che non dichiara in modo esplicito un tipo sottostante è un tipo sottostante di `int`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-715">An enum type that does not explicitly declare an underlying type has an underlying type of `int`.</span></span> <span data-ttu-id="99b6e-716">Il formato di archiviazione e l'intervallo di valori possibili sono determinati dal tipo sottostante.</span><span class="sxs-lookup"><span data-stu-id="99b6e-716">An enum type's storage format and range of possible values are determined by its underlying type.</span></span> <span data-ttu-id="99b6e-717">Il set di valori che può assumere un tipo enum non è limitato dai relativi membri enum.</span><span class="sxs-lookup"><span data-stu-id="99b6e-717">The set of values that an enum type can take on is not limited by its enum members.</span></span> <span data-ttu-id="99b6e-718">In particolare, qualsiasi valore del tipo sottostante di un'enumerazione può essere convertito nel tipo di enumerazione e rappresenta un valore valido distinto di quel tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-718">In particular, any value of the underlying type of an enum can be cast to the enum type and is a distinct valid value of that enum type.</span></span>

<span data-ttu-id="99b6e-719">L'esempio seguente dichiara un tipo di enumerazione denominato `Alignment` con un tipo sottostante di `sbyte`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-719">The following example declares an enum type named `Alignment` with an underlying type of `sbyte`.</span></span>

```csharp
enum Alignment: sbyte
{
    Left = -1,
    Center = 0,
    Right = 1
}
```
<span data-ttu-id="99b6e-720">Come illustrato nell'esempio precedente, una dichiarazione di membro di enumerazione può includere un'espressione costante che specifica il valore del membro.</span><span class="sxs-lookup"><span data-stu-id="99b6e-720">As shown by the previous example, an enum member declaration can include a constant expression that specifies the value of the member.</span></span> <span data-ttu-id="99b6e-721">Il valore costante per ogni membro di enumerazione deve essere compreso nell'intervallo del tipo sottostante dell'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-721">The constant value for each enum member must be in the range of the underlying type of the enum.</span></span> <span data-ttu-id="99b6e-722">Quando una dichiarazione di membro di enumerazione non specifica in modo esplicito un valore, il membro viene assegnato il valore zero (se è il primo membro nel tipo di enumerazione) o il valore del membro enum testualmente precedente più uno.</span><span class="sxs-lookup"><span data-stu-id="99b6e-722">When an enum member declaration does not explicitly specify a value, the member is given the value zero (if it is the first member in the enum type) or the value of the textually preceding enum member plus one.</span></span>

<span data-ttu-id="99b6e-723">I valori di enumerazione possono essere convertiti in valori integrali e viceversa usando i cast di tipo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-723">Enum values can be converted to integral values and vice versa using type casts.</span></span> <span data-ttu-id="99b6e-724">Esempio:</span><span class="sxs-lookup"><span data-stu-id="99b6e-724">For example</span></span>

```csharp
int i = (int)Color.Blue;        // int i = 2;
Color c = (Color)2;             // Color c = Color.Blue;
```
<span data-ttu-id="99b6e-725">Il valore predefinito di qualsiasi tipo di enumerazione è il valore integrale zero convertito nel tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-725">The default value of any enum type is the integral value zero converted to the enum type.</span></span> <span data-ttu-id="99b6e-726">Nei casi in cui le variabili vengono automaticamente inizializzate su un valore predefinito, questo è il valore assegnato alle variabili dei tipi enum.</span><span class="sxs-lookup"><span data-stu-id="99b6e-726">In cases where variables are automatically initialized to a default value, this is the value given to variables of enum types.</span></span> <span data-ttu-id="99b6e-727">Affinché il valore predefinito di un tipo di enumerazione per rendere facilmente disponibile, il valore letterale `0` converte in modo implicito a qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-727">In order for the default value of an enum type to be easily available, the literal `0` implicitly converts to any enum type.</span></span> <span data-ttu-id="99b6e-728">Di conseguenza, quanto riportato di seguito è consentito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-728">Thus, the following is permitted.</span></span>

```csharp
Color c = 0;
```

## <a name="delegates"></a><span data-ttu-id="99b6e-729">Delegati</span><span class="sxs-lookup"><span data-stu-id="99b6e-729">Delegates</span></span>

<span data-ttu-id="99b6e-730">Un ***tipo delegato*** rappresenta riferimenti ai metodi con un elenco di parametri e un tipo restituito particolari.</span><span class="sxs-lookup"><span data-stu-id="99b6e-730">A ***delegate type*** represents references to methods with a particular parameter list and return type.</span></span> <span data-ttu-id="99b6e-731">I delegati consentono di trattare i metodi come entità che è possibile assegnare a variabili e passare come parametri.</span><span class="sxs-lookup"><span data-stu-id="99b6e-731">Delegates make it possible to treat methods as entities that can be assigned to variables and passed as parameters.</span></span> <span data-ttu-id="99b6e-732">I delegati sono simili al concetto di puntatori a funzione disponibili in altri linguaggi. A differenza dei puntatori a funzione, tuttavia, i delegati sono orientati agli oggetti e indipendenti dai tipi.</span><span class="sxs-lookup"><span data-stu-id="99b6e-732">Delegates are similar to the concept of function pointers found in some other languages, but unlike function pointers, delegates are object-oriented and type-safe.</span></span>

<span data-ttu-id="99b6e-733">Nell'esempio seguente viene dichiarato e usato un tipo delegato denominato `Function`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-733">The following example declares and uses a delegate type named `Function`.</span></span>

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
<span data-ttu-id="99b6e-734">Un'istanza del tipo delegato `Function` può fare riferimento a qualsiasi metodo che accetta un argomento `double` e restituisce un valore `double`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-734">An instance of the `Function` delegate type can reference any method that takes a `double` argument and returns a `double` value.</span></span> <span data-ttu-id="99b6e-735">Il `Apply` metodo si applica un determinato `Function` agli elementi di un `double[]`, restituendo un `double[]` con i risultati.</span><span class="sxs-lookup"><span data-stu-id="99b6e-735">The `Apply` method applies a given `Function` to the elements of a `double[]`, returning a `double[]` with the results.</span></span> <span data-ttu-id="99b6e-736">Nel metodo `Main`, `Apply` viene usato per applicare a `double[]` tre funzioni differenti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-736">In the `Main` method, `Apply` is used to apply three different functions to a `double[]`.</span></span>

<span data-ttu-id="99b6e-737">Un delegato può fare riferimento a un metodo statico, come `Square` o `Math.Sin` nell'esempio precedente, o a un metodo di istanza, come `m.Multiply` nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-737">A delegate can reference either a static method (such as `Square` or `Math.Sin` in the previous example) or an instance method (such as `m.Multiply` in the previous example).</span></span> <span data-ttu-id="99b6e-738">Un delegato che fa riferimento a un metodo di istanza fa riferimento anche a un oggetto particolare. Quando il metodo di istanza viene richiamato tramite il delegato, l'oggetto diventa `this` nella chiamata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-738">A delegate that references an instance method also references a particular object, and when the instance method is invoked through the delegate, that object becomes `this` in the invocation.</span></span>

<span data-ttu-id="99b6e-739">I delegati possono essere creati anche mediante funzioni anonime, ovvero "metodi inline" creati in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="99b6e-739">Delegates can also be created using anonymous functions, which are "inline methods" that are created on the fly.</span></span> <span data-ttu-id="99b6e-740">Le funzioni anonime possono vedere le variabili locali dei metodi circostanti.</span><span class="sxs-lookup"><span data-stu-id="99b6e-740">Anonymous functions can see the local variables of the surrounding methods.</span></span> <span data-ttu-id="99b6e-741">Di conseguenza, il precedente esempio di moltiplicatore può essere scritto più facilmente senza usare un `Multiplier` classe:</span><span class="sxs-lookup"><span data-stu-id="99b6e-741">Thus, the multiplier example above can be written more easily without using a `Multiplier` class:</span></span>

```csharp
double[] doubles =  Apply(a, (double x) => x * 2.0);
```
<span data-ttu-id="99b6e-742">Una proprietà interessante e utile di un delegato consiste nel fatto che non conosce o non prende in considerazione la classe del metodo a cui fa riferimento. Ciò che conta è che il metodo a cui un delegato fa riferimento abbia gli stessi parametri e restituisca lo stesso tipo del delegato in questione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-742">An interesting and useful property of a delegate is that it does not know or care about the class of the method it references; all that matters is that the referenced method has the same parameters and return type as the delegate.</span></span>

## <a name="attributes"></a><span data-ttu-id="99b6e-743">Attributi</span><span class="sxs-lookup"><span data-stu-id="99b6e-743">Attributes</span></span>

<span data-ttu-id="99b6e-744">Tipi, membri e altre entità di un programma C# supportano modificatori che controllano alcuni aspetti del loro comportamento.</span><span class="sxs-lookup"><span data-stu-id="99b6e-744">Types, members, and other entities in a C# program support modifiers that control certain aspects of their behavior.</span></span> <span data-ttu-id="99b6e-745">L'accessibilità di un metodo, ad esempio, è controllata con i modificatori `public`, `protected`, `internal` e `private`.</span><span class="sxs-lookup"><span data-stu-id="99b6e-745">For example, the accessibility of a method is controlled using the `public`, `protected`, `internal`, and `private` modifiers.</span></span> <span data-ttu-id="99b6e-746">Il linguaggio C# generalizza questa funzionalità in modo che i tipi di informazioni dichiarative definiti dall'utente possano essere associati a entità di programma e recuperati in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="99b6e-746">C# generalizes this capability such that user-defined types of declarative information can be attached to program entities and retrieved at run-time.</span></span> <span data-ttu-id="99b6e-747">I programmi specificano queste informazioni dichiarative aggiuntive definendo e usando ***attributi***.</span><span class="sxs-lookup"><span data-stu-id="99b6e-747">Programs specify this additional declarative information by defining and using ***attributes***.</span></span>

<span data-ttu-id="99b6e-748">Nell'esempio seguente viene dichiarato un attributo `HelpAttribute` che può essere inserito in entità di programma per fornire collegamenti alla documentazione correlata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-748">The following example declares a `HelpAttribute` attribute that can be placed on program entities to provide links to their associated documentation.</span></span>

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
<span data-ttu-id="99b6e-749">Tutte le classi di attributo derivano dal `System.Attribute` classe forniti da .NET Framework di base.</span><span class="sxs-lookup"><span data-stu-id="99b6e-749">All attribute classes derive from the `System.Attribute` base class provided by the .NET Framework.</span></span> <span data-ttu-id="99b6e-750">Gli attributi possono essere applicati specificandone il nome (con eventuali argomenti) tra parentesi quadre appena prima della dichiarazione associata.</span><span class="sxs-lookup"><span data-stu-id="99b6e-750">Attributes can be applied by giving their name, along with any arguments, inside square brackets just before the associated declaration.</span></span> <span data-ttu-id="99b6e-751">Se il nome dell'attributo termina `Attribute`, tale parte del nome può essere omessa quando si fa riferimento l'attributo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-751">If an attribute's name ends in `Attribute`, that part of the name can be omitted when the attribute is referenced.</span></span> <span data-ttu-id="99b6e-752">L'attributo `HelpAttribute`, ad esempio, può essere usato come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="99b6e-752">For example, the `HelpAttribute` attribute can be used as follows.</span></span>

```csharp
[Help("http://msdn.microsoft.com/.../MyClass.htm")]
public class Widget
{
    [Help("http://msdn.microsoft.com/.../MyClass.htm", Topic = "Display")]
    public void Display(string text) {}
}
```
<span data-ttu-id="99b6e-753">Questo esempio associa un `HelpAttribute` per il `Widget` classe e un'altra `HelpAttribute` per il `Display` metodo nella classe.</span><span class="sxs-lookup"><span data-stu-id="99b6e-753">This example attaches a `HelpAttribute` to the `Widget` class and another `HelpAttribute` to the `Display` method in the class.</span></span> <span data-ttu-id="99b6e-754">I costruttori pubblici di una classe di attributo controllano le informazioni che devono essere specificate quando l'attributo è associato a un'entità di programma.</span><span class="sxs-lookup"><span data-stu-id="99b6e-754">The public constructors of an attribute class control the information that must be provided when the attribute is attached to a program entity.</span></span> <span data-ttu-id="99b6e-755">È possibile specificare informazioni aggiuntive facendo riferimento a proprietà di lettura/scrittura pubbliche della classe di attributo, ad esempio il riferimento alla proprietà `Topic` precedente.</span><span class="sxs-lookup"><span data-stu-id="99b6e-755">Additional information can be provided by referencing public read-write properties of the attribute class (such as the reference to the `Topic` property previously).</span></span>

<span data-ttu-id="99b6e-756">L'esempio seguente illustra come le informazioni sugli attributi per un'entità di programma specificato può essere recuperati in fase di esecuzione usando reflection.</span><span class="sxs-lookup"><span data-stu-id="99b6e-756">The following example shows how attribute information for a given program entity can be retrieved at run-time using reflection.</span></span>

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
<span data-ttu-id="99b6e-757">Se un attributo viene richiesto tramite reflection, il costruttore della classe di attributo viene richiamato con le informazioni specificate nell'origine del programma e viene restituita l'istanza dell'attributo risultante.</span><span class="sxs-lookup"><span data-stu-id="99b6e-757">When a particular attribute is requested through reflection, the constructor for the attribute class is invoked with the information provided in the program source, and the resulting attribute instance is returned.</span></span> <span data-ttu-id="99b6e-758">Se sono state fornite informazioni aggiuntive tramite proprietà, queste vengono impostate sui valori specificati prima che venga restituita l'istanza dell'attributo.</span><span class="sxs-lookup"><span data-stu-id="99b6e-758">If additional information was provided through properties, those properties are set to the given values before the attribute instance is returned.</span></span>
