# <a name="namespaces"></a><span data-ttu-id="0c20a-101">Spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="0c20a-101">Namespaces</span></span>

<span data-ttu-id="0c20a-102">Organizzazione dei programmi c# usando spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-102">C# programs are organized using namespaces.</span></span> <span data-ttu-id="0c20a-103">Gli spazi dei nomi vengono usati sia come un sistema di organizzazione "interno" per un programma che come un sistema di organizzazione "external", ovvero un modo per presentare gli elementi di programma che vengono esposti ad altri programmi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-103">Namespaces are used both as an "internal" organization system for a program, and as an "external" organization system—a way of presenting program elements that are exposed to other programs.</span></span>

<span data-ttu-id="0c20a-104">Direttive using ([direttive Using](namespaces.md#using-directives)) sono fornite per facilitare l'uso degli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-104">Using directives ([Using directives](namespaces.md#using-directives)) are provided to facilitate the use of namespaces.</span></span>

## <a name="compilation-units"></a><span data-ttu-id="0c20a-105">Unità di compilazione</span><span class="sxs-lookup"><span data-stu-id="0c20a-105">Compilation units</span></span>

<span data-ttu-id="0c20a-106">Oggetto *compilation_unit* definisce la struttura generale di un file di origine.</span><span class="sxs-lookup"><span data-stu-id="0c20a-106">A *compilation_unit* defines the overall structure of a source file.</span></span> <span data-ttu-id="0c20a-107">Un'unità di compilazione è costituito da zero o più *using_directive*s seguito da zero o più *global_attributes* seguito da zero o più *namespace_member_declaration*s .</span><span class="sxs-lookup"><span data-stu-id="0c20a-107">A compilation unit consists of zero or more *using_directive*s followed by zero or more *global_attributes* followed by zero or more *namespace_member_declaration*s.</span></span>

```antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? namespace_member_declaration*
    ;
```

<span data-ttu-id="0c20a-108">Un programma c# è costituito da uno o più unità di compilazione, contenuti ognuno in un file di origine separato.</span><span class="sxs-lookup"><span data-stu-id="0c20a-108">A C# program consists of one or more compilation units, each contained in a separate source file.</span></span> <span data-ttu-id="0c20a-109">Quando viene compilato un programma c#, tutte le unità di compilazione vengono elaborate insieme.</span><span class="sxs-lookup"><span data-stu-id="0c20a-109">When a C# program is compiled, all of the compilation units are processed together.</span></span> <span data-ttu-id="0c20a-110">Di conseguenza, unità di compilazione può dipendere tra loro, possibilmente in modo circolare.</span><span class="sxs-lookup"><span data-stu-id="0c20a-110">Thus, compilation units can depend on each other, possibly in a circular fashion.</span></span>

<span data-ttu-id="0c20a-111">Il *using_directive*s di un effetto di unità di compilazione le *global_attributes* e *namespace_member_declaration*s di tale unità di compilazione, ma non hanno alcun effetto altre unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-111">The *using_directive*s of a compilation unit affect the *global_attributes* and *namespace_member_declaration*s of that compilation unit, but have no effect on other compilation units.</span></span>

<span data-ttu-id="0c20a-112">Il *global_attributes* ([attributi](attributes.md)) consentono la specifica di attributi per il modulo e assembly di destinazione di un'unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-112">The *global_attributes* ([Attributes](attributes.md)) of a compilation unit permit the specification of attributes for the target assembly and module.</span></span> <span data-ttu-id="0c20a-113">Moduli e assembly fungono da contenitori per i tipi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-113">Assemblies and modules act as physical containers for types.</span></span> <span data-ttu-id="0c20a-114">Un assembly può essere costituita da più moduli separati.</span><span class="sxs-lookup"><span data-stu-id="0c20a-114">An assembly may consist of several physically separate modules.</span></span>

<span data-ttu-id="0c20a-115">Il *namespace_member_declaration*s di ogni unità di compilazione di un programma contribuiscono ai membri di un unico spazio di dichiarazione chiamato spazio dei nomi globale.</span><span class="sxs-lookup"><span data-stu-id="0c20a-115">The *namespace_member_declaration*s of each compilation unit of a program contribute members to a single declaration space called the global namespace.</span></span> <span data-ttu-id="0c20a-116">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-116">For example:</span></span>

<span data-ttu-id="0c20a-117">File `A.cs`:</span><span class="sxs-lookup"><span data-stu-id="0c20a-117">File `A.cs`:</span></span>
```csharp
class A {}
```

<span data-ttu-id="0c20a-118">File `B.cs`:</span><span class="sxs-lookup"><span data-stu-id="0c20a-118">File `B.cs`:</span></span>
```csharp
class B {}
```

<span data-ttu-id="0c20a-119">Le due unità di compilazione contribuiscono per il singolo spazio dei nomi globale, in questo caso dichiarare due classi con nomi completi `A` e `B`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-119">The two compilation units contribute to the single global namespace, in this case declaring two classes with the fully qualified names `A` and `B`.</span></span> <span data-ttu-id="0c20a-120">Poiché le due unità di compilazione contribuiscono allo stesso spazio di dichiarazione, sarebbe stato un errore se ognuno contiene una dichiarazione di un membro con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="0c20a-120">Because the two compilation units contribute to the same declaration space, it would have been an error if each contained a declaration of a member with the same name.</span></span>

## <a name="namespace-declarations"></a><span data-ttu-id="0c20a-121">Dichiarazioni dello spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="0c20a-121">Namespace declarations</span></span>

<span data-ttu-id="0c20a-122">Oggetto *namespace_declaration* costituita dalla parola chiave `namespace`, seguito da uno spazio dei nomi e corpo, seguito facoltativamente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="0c20a-122">A *namespace_declaration* consists of the keyword `namespace`, followed by a namespace name and body, optionally followed by a semicolon.</span></span>

```antlr
namespace_declaration
    : 'namespace' qualified_identifier namespace_body ';'?
    ;

qualified_identifier
    : identifier ('.' identifier)*
    ;

namespace_body
    : '{' extern_alias_directive* using_directive* namespace_member_declaration* '}'
    ;
```

<span data-ttu-id="0c20a-123">Oggetto *namespace_declaration* può verificarsi una dichiarazione di livello superiore in un *compilation_unit* o come una dichiarazione di membro all'interno di altra *namespace_declaration*.</span><span class="sxs-lookup"><span data-stu-id="0c20a-123">A *namespace_declaration* may occur as a top-level declaration in a *compilation_unit* or as a member declaration within another *namespace_declaration*.</span></span> <span data-ttu-id="0c20a-124">Quando un *namespace_declaration* si verifica una dichiarazione di livello superiore in un *compilation_unit*, lo spazio dei nomi diventa un membro dello spazio dei nomi globali.</span><span class="sxs-lookup"><span data-stu-id="0c20a-124">When a *namespace_declaration* occurs as a top-level declaration in a *compilation_unit*, the namespace becomes a member of the global namespace.</span></span> <span data-ttu-id="0c20a-125">Quando un *namespace_declaration* si verifica all'interno di altra *namespace_declaration*, lo spazio dei nomi interna diventa un membro dello spazio dei nomi esterno.</span><span class="sxs-lookup"><span data-stu-id="0c20a-125">When a *namespace_declaration* occurs within another *namespace_declaration*, the inner namespace becomes a member of the outer namespace.</span></span> <span data-ttu-id="0c20a-126">In entrambi i casi, il nome di uno spazio dei nomi deve essere univoco all'interno dello spazio dei nomi che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="0c20a-126">In either case, the name of a namespace must be unique within the containing namespace.</span></span>

<span data-ttu-id="0c20a-127">Gli spazi dei nomi sono implicitamente `public` e la dichiarazione di uno spazio dei nomi non può includere modificatori di accesso.</span><span class="sxs-lookup"><span data-stu-id="0c20a-127">Namespaces are implicitly `public` and the declaration of a namespace cannot include any access modifiers.</span></span>

<span data-ttu-id="0c20a-128">All'interno di un *namespace_body*, l'opzione facoltativa *using_directive*s Importa i nomi di altri spazi dei nomi, tipi e membri, consentendo loro di fare riferimento direttamente anziché tramite i nomi completi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-128">Within a *namespace_body*, the optional *using_directive*s import the names of other namespaces, types and members, allowing them to be referenced directly instead of through qualified names.</span></span> <span data-ttu-id="0c20a-129">L'opzione facoltativa *namespace_member_declaration*s aggiungono membri allo spazio di dichiarazione dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-129">The optional *namespace_member_declaration*s contribute members to the declaration space of the namespace.</span></span> <span data-ttu-id="0c20a-130">Si noti che tutti i *using_directive*s deve precedere le dichiarazioni di membri.</span><span class="sxs-lookup"><span data-stu-id="0c20a-130">Note that all *using_directive*s must appear before any member declarations.</span></span>

<span data-ttu-id="0c20a-131">Il *qualified_identifier* di un *namespace_declaration* può essere un identificatore singolo o una sequenza di identificatori delimitati da "`.`" token.</span><span class="sxs-lookup"><span data-stu-id="0c20a-131">The *qualified_identifier* of a *namespace_declaration* may be a single identifier or a sequence of identifiers separated by "`.`" tokens.</span></span> <span data-ttu-id="0c20a-132">La seconda forma consente al programma per definire uno spazio dei nomi annidato senza annidamento lessicalmente diverse dichiarazioni dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-132">The latter form permits a program to define a nested namespace without lexically nesting several namespace declarations.</span></span> <span data-ttu-id="0c20a-133">Ad esempio,</span><span class="sxs-lookup"><span data-stu-id="0c20a-133">For example,</span></span>

```csharp
namespace N1.N2
{
    class A {}

    class B {}
}
```
<span data-ttu-id="0c20a-134">è semanticamente equivalente a</span><span class="sxs-lookup"><span data-stu-id="0c20a-134">is semantically equivalent to</span></span>
```csharp
namespace N1
{
    namespace N2
    {
        class A {}

        class B {}
    }
}
```

<span data-ttu-id="0c20a-135">Gli spazi dei nomi sono aperti e due dichiarazioni dello spazio dei nomi con lo stesso nome completo contribuiscono allo stesso spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)).</span><span class="sxs-lookup"><span data-stu-id="0c20a-135">Namespaces are open-ended, and two namespace declarations with the same fully qualified name contribute to the same declaration space ([Declarations](basic-concepts.md#declarations)).</span></span> <span data-ttu-id="0c20a-136">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-136">In the example</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N1.N2
{
    class B {}
}
```
<span data-ttu-id="0c20a-137">le due dichiarazioni dello spazio dei nomi sopra contribuiscono allo stesso spazio di dichiarazione, in questo caso dichiarare due classi con nomi completi `N1.N2.A` e `N1.N2.B`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-137">the two namespace declarations above contribute to the same declaration space, in this case declaring two classes with the fully qualified names `N1.N2.A` and `N1.N2.B`.</span></span> <span data-ttu-id="0c20a-138">Poiché le due dichiarazioni contribuiscono allo stesso spazio di dichiarazione, sarebbe stato un errore se ogni contenuta una dichiarazione di un membro con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="0c20a-138">Because the two declarations contribute to the same declaration space, it would have been an error if each contained a declaration of a member with the same name.</span></span>

## <a name="extern-aliases"></a><span data-ttu-id="0c20a-139">Alias extern</span><span class="sxs-lookup"><span data-stu-id="0c20a-139">Extern aliases</span></span>

<span data-ttu-id="0c20a-140">Un' *extern_alias_directive* introduce un identificatore che funge da un alias per uno spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-140">An *extern_alias_directive* introduces an identifier that serves as an alias for a namespace.</span></span> <span data-ttu-id="0c20a-141">La specifica dello spazio dei nomi con alias è esterna al codice sorgente del programma e si applica anche a spazi dei nomi annidati dello spazio dei nomi con alias.</span><span class="sxs-lookup"><span data-stu-id="0c20a-141">The specification of the aliased namespace is external to the source code of the program and applies also to nested namespaces of the aliased namespace.</span></span>

```antlr
extern_alias_directive
    : 'extern' 'alias' identifier ';'
    ;
```

<span data-ttu-id="0c20a-142">L'ambito di un' *extern_alias_directive* consente di estendere tramite il *using_directive*s, *global_attributes* e *namespace_member_declaration*s della relativa compilazione unità oppure lo spazio dei nomi nel corpo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-142">The scope of an *extern_alias_directive* extends over the *using_directive*s, *global_attributes* and *namespace_member_declaration*s of its immediately containing compilation unit or namespace body.</span></span>

<span data-ttu-id="0c20a-143">All'interno di un corpo di spazio dei nomi o unità di compilazione che contiene un' *extern_alias_directive*, l'identificatore introdotto dalle *extern_alias_directive* può essere utilizzato per lo spazio dei nomi con alias di riferimento.</span><span class="sxs-lookup"><span data-stu-id="0c20a-143">Within a compilation unit or namespace body that contains an *extern_alias_directive*, the identifier introduced by the *extern_alias_directive* can be used to reference the aliased namespace.</span></span> <span data-ttu-id="0c20a-144">Tratta di un errore in fase di compilazione per il *identifier* è la parola `global`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-144">It is a compile-time error for the *identifier* to be the word `global`.</span></span>

<span data-ttu-id="0c20a-145">Un' *extern_alias_directive* rende disponibile un alias all'interno di un particolare compilazione unità o dello spazio dei nomi nel corpo, ma non aggiunge nuovi membri allo spazio di dichiarazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="0c20a-145">An *extern_alias_directive* makes an alias available within a particular compilation unit or namespace body, but it does not contribute any new members to the underlying declaration space.</span></span> <span data-ttu-id="0c20a-146">In altre parole, un' *extern_alias_directive* non è transitiva, ma, invece, interessa solo la compilazione unità oppure lo spazio dei nomi del corpo in cui si verifica.</span><span class="sxs-lookup"><span data-stu-id="0c20a-146">In other words, an *extern_alias_directive* is not transitive, but, rather, affects only the compilation unit or namespace body in which it occurs.</span></span>

<span data-ttu-id="0c20a-147">Il programma seguente viene dichiarata due alias extern, `X` e `Y`, ognuno dei quali rappresentano la radice di una gerarchia dello spazio dei nomi distinti:</span><span class="sxs-lookup"><span data-stu-id="0c20a-147">The following program declares and uses two extern aliases, `X` and `Y`, each of which represent the root of a distinct namespace hierarchy:</span></span>
```csharp
extern alias X;
extern alias Y;

class Test
{
    X::N.A a;
    X::N.B b1;
    Y::N.B b2;
    Y::N.C c;
}
```

<span data-ttu-id="0c20a-148">Il programma dichiara l'esistenza di extern alias `X` e `Y`, ma le definizioni degli alias effettive sono esterne al programma.</span><span class="sxs-lookup"><span data-stu-id="0c20a-148">The program declares the existence of the extern aliases `X` and `Y`, but the actual definitions of the aliases are external to the program.</span></span> <span data-ttu-id="0c20a-149">Con nome identico `N.B` classi sono ora possibile fare riferimento come `X.N.B` e `Y.N.B`, o, utilizzando il qualificatore di alias dello spazio dei nomi, `X::N.B` e `Y::N.B`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-149">The identically named `N.B` classes can now be referenced as `X.N.B` and `Y.N.B`, or, using the namespace alias qualifier, `X::N.B` and `Y::N.B`.</span></span> <span data-ttu-id="0c20a-150">Si verifica un errore se un programma dichiara un alias extern per il quale viene specificata alcuna definizione esterna.</span><span class="sxs-lookup"><span data-stu-id="0c20a-150">An error occurs if a program declares an extern alias for which no external definition is provided.</span></span>

## <a name="using-directives"></a><span data-ttu-id="0c20a-151">direttive using</span><span class="sxs-lookup"><span data-stu-id="0c20a-151">Using directives</span></span>

<span data-ttu-id="0c20a-152">***Direttive using*** facilitare l'uso di spazi dei nomi e tipi definiti in altri spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-152">***Using directives*** facilitate the use of namespaces and types defined in other namespaces.</span></span> <span data-ttu-id="0c20a-153">Tramite il processo di risoluzione dei nomi di impatto direttive *namespace_or_type_name*s ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) e *simple_name*s ([nomi semplici ](expressions.md#simple-names)), ma a differenza delle dichiarazioni, le direttive using non vengono conteggiati i nuovi membri a spazi di dichiarazione sottostante dell'unità di compilazione o gli spazi dei nomi in cui vengono usati.</span><span class="sxs-lookup"><span data-stu-id="0c20a-153">Using directives impact the name resolution process of *namespace_or_type_name*s ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) and *simple_name*s ([Simple names](expressions.md#simple-names)), but unlike declarations, using directives do not contribute new members to the underlying declaration spaces of the compilation units or namespaces within which they are used.</span></span>

```antlr
using_directive
    : using_alias_directive
    | using_namespace_directive
    | using_static_directive
    ;
```

<span data-ttu-id="0c20a-154">Oggetto *using_alias_directive* ([usando direttive alias](namespaces.md#using-alias-directives)) introduce un alias per un tipo o dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-154">A *using_alias_directive* ([Using alias directives](namespaces.md#using-alias-directives)) introduces an alias for a namespace or type.</span></span>

<span data-ttu-id="0c20a-155">Oggetto *using_namespace_directive* ([usando le direttive dello spazio dei nomi](namespaces.md#using-namespace-directives)) Importa i membri di tipo di uno spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-155">A *using_namespace_directive* ([Using namespace directives](namespaces.md#using-namespace-directives)) imports the type members of a namespace.</span></span>

<span data-ttu-id="0c20a-156">Oggetto *using_static_directive* ([direttive Using static](namespaces.md#using-static-directives)) Importa i tipi annidati e membri statici di un tipo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-156">A *using_static_directive* ([Using static directives](namespaces.md#using-static-directives)) imports the nested types and static members of a type.</span></span>

<span data-ttu-id="0c20a-157">L'ambito di una *using_directive* consente di estendere tramite il *namespace_member_declaration*s della relativa compilazione unità oppure lo spazio dei nomi nel corpo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-157">The scope of a *using_directive* extends over the *namespace_member_declaration*s of its immediately containing compilation unit or namespace body.</span></span> <span data-ttu-id="0c20a-158">L'ambito di una *using_directive* in modo specifico, non include il relativo peer *using_directive*s.</span><span class="sxs-lookup"><span data-stu-id="0c20a-158">The scope of a *using_directive* specifically does not include its peer *using_directive*s.</span></span> <span data-ttu-id="0c20a-159">Di conseguenza, il peering *using_directive*s non interferiscano tra loro e l'ordine in cui vengono scritti non è significativo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-159">Thus, peer *using_directive*s do not affect each other, and the order in which they are written is insignificant.</span></span>

### <a name="using-alias-directives"></a><span data-ttu-id="0c20a-160">Usando direttive alias</span><span class="sxs-lookup"><span data-stu-id="0c20a-160">Using alias directives</span></span>

<span data-ttu-id="0c20a-161">Oggetto *using_alias_directive* introduce un identificatore che funge da un alias per un tipo all'interno del corpo di spazio dei nomi o unità di compilazione che la contiene o dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-161">A *using_alias_directive* introduces an identifier that serves as an alias for a namespace or type within the immediately enclosing compilation unit or namespace body.</span></span>

```antlr
using_alias_directive
    : 'using' identifier '=' namespace_or_type_name ';'
    ;
```

<span data-ttu-id="0c20a-162">All'interno delle dichiarazioni di membro in un corpo di spazio dei nomi o unità di compilazione che contiene un *using_alias_directive*, l'identificatore introdotto dalle *using_alias_directive* può essere usato per fare riferimento al determinato spazio dei nomi o tipo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-162">Within member declarations in a compilation unit or namespace body that contains a *using_alias_directive*, the identifier introduced by the *using_alias_directive* can be used to reference the given namespace or type.</span></span> <span data-ttu-id="0c20a-163">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-163">For example:</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;

    class B: A {}
}
```

<span data-ttu-id="0c20a-164">Precedente, all'interno delle dichiarazioni di membro nel `N3` spazio dei nomi `A` è un alias per `N1.N2.A`e la classe `N3.B` deriva dalla classe `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-164">Above, within member declarations in the `N3` namespace, `A` is an alias for `N1.N2.A`, and thus class `N3.B` derives from class `N1.N2.A`.</span></span> <span data-ttu-id="0c20a-165">Lo stesso effetto può essere ottenuto tramite la creazione di un alias `R` per `N1.N2` e fare riferimento a `R.A`:</span><span class="sxs-lookup"><span data-stu-id="0c20a-165">The same effect can be obtained by creating an alias `R` for `N1.N2` and then referencing `R.A`:</span></span>
```csharp
namespace N3
{
    using R = N1.N2;

    class B: R.A {}
}
```

<span data-ttu-id="0c20a-166">Il *identifier* di un *using_alias_directive* devono essere univoci nello spazio di dichiarazione di unità di compilazione o dello spazio dei nomi che contiene direttamente il *using_alias_directive* .</span><span class="sxs-lookup"><span data-stu-id="0c20a-166">The *identifier* of a *using_alias_directive* must be unique within the declaration space of the compilation unit or namespace that immediately contains the *using_alias_directive*.</span></span> <span data-ttu-id="0c20a-167">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-167">For example:</span></span>
```csharp
namespace N3
{
    class A {}
}

namespace N3
{
    using A = N1.N2.A;        // Error, A already exists
}
```

<span data-ttu-id="0c20a-168">Precedente `N3` contiene già un membro `A`, in modo che tratta di un errore in fase di compilazione per una *using_alias_directive* utilizzare tale identificatore.</span><span class="sxs-lookup"><span data-stu-id="0c20a-168">Above, `N3` already contains a member `A`, so it is a compile-time error for a *using_alias_directive* to use that identifier.</span></span> <span data-ttu-id="0c20a-169">Analogamente, è un errore in fase di compilazione per due o più *using_alias_directive*s presenti nella stessa corpo spazio dei nomi o unità di compilazione di dichiarare gli alias con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="0c20a-169">Likewise, it is a compile-time error for two or more *using_alias_directive*s in the same compilation unit or namespace body to declare aliases by the same name.</span></span>

<span data-ttu-id="0c20a-170">Oggetto *using_alias_directive* rende disponibile un alias all'interno di un particolare compilazione unità o dello spazio dei nomi nel corpo, ma non aggiunge nuovi membri allo spazio di dichiarazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="0c20a-170">A *using_alias_directive* makes an alias available within a particular compilation unit or namespace body, but it does not contribute any new members to the underlying declaration space.</span></span> <span data-ttu-id="0c20a-171">In altre parole, una *using_alias_directive* non è transitiva, ma solo la compilazione unità oppure lo spazio dei nomi al corpo in cui si verifica.</span><span class="sxs-lookup"><span data-stu-id="0c20a-171">In other words, a *using_alias_directive* is not transitive but rather affects only the compilation unit or namespace body in which it occurs.</span></span> <span data-ttu-id="0c20a-172">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-172">In the example</span></span>
```csharp
namespace N3
{
    using R = N1.N2;
}

namespace N3
{
    class B: R.A {}            // Error, R unknown
}
```
<span data-ttu-id="0c20a-173">l'ambito del *using_alias_directive* che introduce `R` estende solo alle dichiarazioni dei membri all'interno dello spazio dei nomi in cui è contenuto, in modo `R` sconosciuto nella seconda dichiarazione dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-173">the scope of the *using_alias_directive* that introduces `R` only extends to member declarations in the namespace body in which it is contained, so `R` is unknown in the second namespace declaration.</span></span> <span data-ttu-id="0c20a-174">Tuttavia, posizionare il *using_alias_directive* nella compilazione contenente unit fa sì che l'alias da diventano disponibili in entrambe le dichiarazioni dello spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="0c20a-174">However, placing the *using_alias_directive* in the containing compilation unit causes the alias to become available within both namespace declarations:</span></span>
```csharp
using R = N1.N2;

namespace N3
{
    class B: R.A {}
}

namespace N3
{
    class C: R.A {}
}
```

<span data-ttu-id="0c20a-175">Proprio come i membri normali, i nomi introdotti dalla *using_alias_directive*s sono nascosti da altri membri allo stesso modo nome negli ambiti annidati.</span><span class="sxs-lookup"><span data-stu-id="0c20a-175">Just like regular members, names introduced by *using_alias_directive*s are hidden by similarly named members in nested scopes.</span></span> <span data-ttu-id="0c20a-176">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-176">In the example</span></span>
```csharp
using R = N1.N2;

namespace N3
{
    class R {}

    class B: R.A {}        // Error, R has no member A
}
```
<span data-ttu-id="0c20a-177">il riferimento al `R.A` nella dichiarazione di `B` provoca un errore in fase di compilazione perché `R` si intende `N3.R`, non `N1.N2`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-177">the reference to `R.A` in the declaration of `B` causes a compile-time error because `R` refers to `N3.R`, not `N1.N2`.</span></span>

<span data-ttu-id="0c20a-178">L'ordine in cui *using_alias_directive*sono scritte non ha alcun significato e la risoluzione delle *namespace_or_type_name* fa un *using_alias_directive*non è influenzato il *using_alias_directive* stesso o da altri *using_directive*s nel corpo di spazio dei nomi o unità di compilazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="0c20a-178">The order in which *using_alias_directive*s are written has no significance, and resolution of the *namespace_or_type_name* referenced by a *using_alias_directive* is not affected by the *using_alias_directive* itself or by other *using_directive*s in the immediately containing compilation unit or namespace body.</span></span> <span data-ttu-id="0c20a-179">In altre parole, il *namespace_or_type_name* di un *using_alias_directive* viene risolta come se la compilazione unità oppure lo spazio dei nomi nel corpo non fosse no *using_directive*s.</span><span class="sxs-lookup"><span data-stu-id="0c20a-179">In other words, the *namespace_or_type_name* of a *using_alias_directive* is resolved as if the immediately containing compilation unit or namespace body had no *using_directive*s.</span></span> <span data-ttu-id="0c20a-180">Oggetto *using_alias_directive* può tuttavia essere influenzato *extern_alias_directive*s nel corpo di spazio dei nomi o unità di compilazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="0c20a-180">A *using_alias_directive* may however be affected by *extern_alias_directive*s in the immediately containing compilation unit or namespace body.</span></span> <span data-ttu-id="0c20a-181">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-181">In the example</span></span>
```csharp
namespace N1.N2 {}

namespace N3
{
    extern alias E;

    using R1 = E.N;        // OK

    using R2 = N1;         // OK

    using R3 = N1.N2;      // OK

    using R4 = R2.N2;      // Error, R2 unknown
}
```
<span data-ttu-id="0c20a-182">l'ultima *using_alias_directive* genera un errore in fase di compilazione in quanto non dipende dal primo *using_alias_directive*.</span><span class="sxs-lookup"><span data-stu-id="0c20a-182">the last *using_alias_directive* results in a compile-time error because it is not affected by the first *using_alias_directive*.</span></span> <span data-ttu-id="0c20a-183">Il primo *using_alias_directive* non produce un errore perché l'ambito dell'alias extern `E` include il *using_alias_directive*.</span><span class="sxs-lookup"><span data-stu-id="0c20a-183">The first *using_alias_directive* does not result in an error since the scope of the extern alias `E` includes the *using_alias_directive*.</span></span>

<span data-ttu-id="0c20a-184">Oggetto *using_alias_directive* può creare un alias per qualsiasi tipo, incluso lo spazio dei nomi all'interno del quale viene visualizzato o dello spazio dei nomi e qualsiasi tipo o dello spazio dei nomi annidato all'interno di tale spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-184">A *using_alias_directive* can create an alias for any namespace or type, including the namespace within which it appears and any namespace or type nested within that namespace.</span></span>

<span data-ttu-id="0c20a-185">L'accesso a un tipo o dello spazio dei nomi tramite un alias restituisce esattamente lo stesso risultato come l'accesso a tale spazio dei nomi o tipo tramite il relativo nome dichiarato.</span><span class="sxs-lookup"><span data-stu-id="0c20a-185">Accessing a namespace or type through an alias yields exactly the same result as accessing that namespace or type through its declared name.</span></span> <span data-ttu-id="0c20a-186">Si consideri, ad esempio,</span><span class="sxs-lookup"><span data-stu-id="0c20a-186">For example, given</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using R1 = N1;
    using R2 = N1.N2;

    class B
    {
        N1.N2.A a;            // refers to N1.N2.A
        R1.N2.A b;            // refers to N1.N2.A
        R2.A c;               // refers to N1.N2.A
    }
}
```
<span data-ttu-id="0c20a-187">i nomi `N1.N2.A`, `R1.N2.A`, e `R2.A` sono equivalenti e tutte fanno riferimento alla classe cui nome completo è `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-187">the names `N1.N2.A`, `R1.N2.A`, and `R2.A` are equivalent and all refer to the class whose fully qualified name is `N1.N2.A`.</span></span>

<span data-ttu-id="0c20a-188">Utilizzo degli alias possibile denominare un tipo costruito chiuso, ma non una dichiarazione di tipo generico non associate nome senza fornire argomenti di tipo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-188">Using aliases can name a closed constructed type, but cannot name an unbound generic type declaration without supplying type arguments.</span></span> <span data-ttu-id="0c20a-189">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-189">For example:</span></span>
```csharp
namespace N1
{
    class A<T>
    {
        class B {}
    }
}

namespace N2
{
    using W = N1.A;          // Error, cannot name unbound generic type

    using X = N1.A.B;        // Error, cannot name unbound generic type

    using Y = N1.A<int>;     // Ok, can name closed constructed type

    using Z<T> = N1.A<T>;    // Error, using alias cannot have type parameters
}
```

### <a name="using-namespace-directives"></a><span data-ttu-id="0c20a-190">Le direttive dello spazio dei nomi using</span><span class="sxs-lookup"><span data-stu-id="0c20a-190">Using namespace directives</span></span>

<span data-ttu-id="0c20a-191">Oggetto *using_namespace_directive* Importa i tipi contenuti in uno spazio dei nomi nel contenitore immediatamente la compilazione unità oppure lo spazio dei nomi corpo, consentendo l'identificatore di ogni tipo di essere utilizzati senza qualifica.</span><span class="sxs-lookup"><span data-stu-id="0c20a-191">A *using_namespace_directive* imports the types contained in a namespace into the immediately enclosing compilation unit or namespace body, enabling the identifier of each type to be used without qualification.</span></span>

```antlr
using_namespace_directive
    : 'using' namespace_name ';'
    ;
```

<span data-ttu-id="0c20a-192">All'interno delle dichiarazioni di membro in un corpo di spazio dei nomi o unità di compilazione che contiene un *using_namespace_directive*, possibile fare direttamente riferimento ai tipi contenuti nello spazio dei nomi specificato.</span><span class="sxs-lookup"><span data-stu-id="0c20a-192">Within member declarations in a compilation unit or namespace body that contains a *using_namespace_directive*, the types contained in the given namespace can be referenced directly.</span></span> <span data-ttu-id="0c20a-193">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-193">For example:</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1.N2;

    class B: A {}
}
```

<span data-ttu-id="0c20a-194">Precedente, all'interno delle dichiarazioni di membro nel `N3` dello spazio dei nomi, i membri del tipo `N1.N2` sono direttamente disponibili e la classe `N3.B` deriva dalla classe `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-194">Above, within member declarations in the `N3` namespace, the type members of `N1.N2` are directly available, and thus class `N3.B` derives from class `N1.N2.A`.</span></span>

<span data-ttu-id="0c20a-195">Oggetto *using_namespace_directive* Importa i tipi contenuti nello spazio dei nomi specificato, ma non spazi dei nomi annidati.</span><span class="sxs-lookup"><span data-stu-id="0c20a-195">A *using_namespace_directive* imports the types contained in the given namespace, but specifically does not import nested namespaces.</span></span> <span data-ttu-id="0c20a-196">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-196">In the example</span></span>
```csharp
namespace N1.N2
{
    class A {}
}

namespace N3
{
    using N1;

    class B: N2.A {}        // Error, N2 unknown
}
```
<span data-ttu-id="0c20a-197">il *using_namespace_directive* Importa i tipi contenuti nel `N1`, ma non gli spazi dei nomi annidati `N1`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-197">the *using_namespace_directive* imports the types contained in `N1`, but not the namespaces nested in `N1`.</span></span> <span data-ttu-id="0c20a-198">Di conseguenza, il riferimento al `N2.A` nella dichiarazione di `B` comporta un errore in fase di compilazione perché nessun membro denominato `N2` rientrano nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="0c20a-198">Thus, the reference to `N2.A` in the declaration of `B` results in a compile-time error because no members named `N2` are in scope.</span></span>

<span data-ttu-id="0c20a-199">A differenza di una *using_alias_directive*, un *using_namespace_directive* può importare i tipi con gli identificatori sono già definiti all'interno del corpo di spazio dei nomi o unità di compilazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="0c20a-199">Unlike a *using_alias_directive*, a *using_namespace_directive* may import types whose identifiers are already defined within the enclosing compilation unit or namespace body.</span></span> <span data-ttu-id="0c20a-200">In effetti, i nomi importato da un *using_namespace_directive* nascosti dai membri denominati in modo analogo nel corpo di spazio dei nomi o unità di compilazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="0c20a-200">In effect, names imported by a *using_namespace_directive* are hidden by similarly named members in the enclosing compilation unit or namespace body.</span></span> <span data-ttu-id="0c20a-201">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-201">For example:</span></span>
```csharp
namespace N1.N2
{
    class A {}

    class B {}
}

namespace N3
{
    using N1.N2;

    class A {}
}
```

<span data-ttu-id="0c20a-202">A questo punto, all'interno delle dichiarazioni di membro nel `N3` spazio dei nomi `A` fa riferimento a `N3.A` anziché `N1.N2.A`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-202">Here, within member declarations in the `N3` namespace, `A` refers to `N3.A` rather than `N1.N2.A`.</span></span>

<span data-ttu-id="0c20a-203">Quando più di uno spazio dei nomi o il tipo importato dal *using_namespace_directive*s oppure *using_static_directive*s nel corpo di spazio dei nomi o unità di compilazione stessa contengono i tipi con lo stesso nome, i riferimenti a tale nome come una *type_name* sono considerati ambigui.</span><span class="sxs-lookup"><span data-stu-id="0c20a-203">When more than one namespace or type imported by *using_namespace_directive*s or *using_static_directive*s in the same compilation unit or namespace body contain types by the same name, references to that name as a *type_name* are considered ambiguous.</span></span> <span data-ttu-id="0c20a-204">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-204">In the example</span></span>
```csharp
namespace N1
{
    class A {}
}

namespace N2
{
    class A {}
}

namespace N3
{
    using N1;

    using N2;

    class B: A {}                // Error, A is ambiguous
}
```
<span data-ttu-id="0c20a-205">entrambe `N1` e `N2` può contenere un membro `A`e poiché `N3` Importa entrambe, che fa riferimento `A` in `N3` è un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-205">both `N1` and `N2` contain a member `A`, and because `N3` imports both, referencing `A` in `N3` is a compile-time error.</span></span> <span data-ttu-id="0c20a-206">In questo caso, il conflitto può essere risolto sia attraverso qualifica dei riferimenti a `A`, o con l'introduzione di un *using_alias_directive* che seleziona un determinato `A`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-206">In this situation, the conflict can be resolved either through qualification of references to `A`, or by introducing a *using_alias_directive* that picks a particular `A`.</span></span> <span data-ttu-id="0c20a-207">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-207">For example:</span></span>
```csharp
namespace N3
{
    using N1;

    using N2;

    using A = N1.A;

    class B: A {}                // A means N1.A
}
```

<span data-ttu-id="0c20a-208">Inoltre, quando più di uno spazio dei nomi o tipo importati dal *using_namespace_directive*s o *using_static_directive*s nel corpo di spazio dei nomi o unità di compilazione stessa contengono i tipi o membri per il stesso nome, i riferimenti a tale nome come una *simple_name* sono considerati ambigui.</span><span class="sxs-lookup"><span data-stu-id="0c20a-208">Furthermore, when more than one namespace or type imported by *using_namespace_directive*s or *using_static_directive*s in the same compilation unit or namespace body contain types or members by the same name, references to that name as a *simple_name* are considered ambiguous.</span></span> <span data-ttu-id="0c20a-209">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-209">In the example</span></span>
```csharp
namespace N1
{
    class A {}
}

class C
{
    public static int A;
}

namespace N2
{
    using N1;
    using static C;

    class B
    {
        void M() 
        { 
            A a = new A();   // Ok, A is unambiguous as a type-name
            A.Equals(2);     // Error, A is ambiguous as a simple-name
        }
    }
}
```
<span data-ttu-id="0c20a-210">`N1` contiene un membro di tipo `A`, e `C` contiene un campo statico `A`e poiché `N2` Importa entrambe, che fa riferimento `A` come un *simple_name* è ambiguo e una fase di compilazione errore.</span><span class="sxs-lookup"><span data-stu-id="0c20a-210">`N1` contains a type member `A`, and `C` contains a static field `A`, and because `N2` imports both, referencing `A` as a *simple_name* is ambiguous and a compile-time error.</span></span> 

<span data-ttu-id="0c20a-211">Ad esempio un *using_alias_directive*, un *using_namespace_directive* non aggiunge nuovi membri allo spazio di dichiarazione sottostante dell'unità di compilazione o dello spazio dei nomi, ma solo il compilazione unità o dello spazio dei nomi nel corpo in cui è presente.</span><span class="sxs-lookup"><span data-stu-id="0c20a-211">Like a *using_alias_directive*, a *using_namespace_directive* does not contribute any new members to the underlying declaration space of the compilation unit or namespace, but rather affects only the compilation unit or namespace body in which it appears.</span></span>

<span data-ttu-id="0c20a-212">Il *namespace_name* fa riferimento un *using_namespace_directive* viene risolto nello stesso modo il *namespace_or_type_name* fa un  *using_alias_directive*.</span><span class="sxs-lookup"><span data-stu-id="0c20a-212">The *namespace_name* referenced by a *using_namespace_directive* is resolved in the same way as the *namespace_or_type_name* referenced by a *using_alias_directive*.</span></span> <span data-ttu-id="0c20a-213">Pertanto *using_namespace_directive*s nel corpo di spazio dei nomi o unità di compilazione stesso non interferiscano tra loro e possono essere scritti in qualsiasi ordine.</span><span class="sxs-lookup"><span data-stu-id="0c20a-213">Thus, *using_namespace_directive*s in the same compilation unit or namespace body do not affect each other and can be written in any order.</span></span>

### <a name="using-static-directives"></a><span data-ttu-id="0c20a-214">Direttive using static</span><span class="sxs-lookup"><span data-stu-id="0c20a-214">Using static directives</span></span>

<span data-ttu-id="0c20a-215">Oggetto *using_static_directive* Importa i tipi annidati e membri statici contenuti direttamente in una dichiarazione di tipo nel contenitore immediatamente la compilazione unità oppure lo spazio dei nomi corpo, consentendo l'identificatore di ogni membro e il tipo sia utilizzati senza qualifica.</span><span class="sxs-lookup"><span data-stu-id="0c20a-215">A *using_static_directive* imports the nested types and static members contained directly in a type declaration into the immediately enclosing compilation unit or namespace body, enabling the identifier of each member and type to be used without qualification.</span></span>

```antlr
using_static_directive
    : 'using' 'static' type_name ';'
    ;
```

<span data-ttu-id="0c20a-216">All'interno delle dichiarazioni di membro in un corpo di spazio dei nomi o unità di compilazione che contiene un *using_static_directive*, accessibile l'annidati di tipi e membri statici (tranne i metodi di estensione) contenuti direttamente nella dichiarazione del tipo specificato può essere fatto riferimento direttamente.</span><span class="sxs-lookup"><span data-stu-id="0c20a-216">Within member declarations in a compilation unit or namespace body that contains a *using_static_directive*, the accessible nested types and static members (except extension methods) contained directly in the declaration of the given type can be referenced directly.</span></span> <span data-ttu-id="0c20a-217">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0c20a-217">For example:</span></span>

```csharp
namespace N1
{
    class A 
    {
        public class B{}
        public static B M(){ return new B(); }
    }
}

namespace N2
{
    using static N1.A;
    class C
    {
        void N() { B b = M(); }
    }
}
```

<span data-ttu-id="0c20a-218">Precedente, all'interno delle dichiarazioni di membro nel `N2` dello spazio dei nomi, i membri statici e i tipi annidati di `N1.A` sono direttamente disponibili e pertanto il metodo `N` è in grado di fare riferimento a entrambi il `B` e `M` i membri del `N1.A`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-218">Above, within member declarations in the `N2` namespace, the static members and nested types of `N1.A` are directly available, and thus the method `N` is able to reference both the `B` and `M` members of `N1.A`.</span></span>

<span data-ttu-id="0c20a-219">Oggetto *using_static_directive* in modo specifico, non importa i metodi di estensione direttamente come metodi statici, ma li rende disponibili per la chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="0c20a-219">A *using_static_directive* specifically does not import extension methods directly as static methods, but makes them available for extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="0c20a-220">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-220">In the example</span></span>

```csharp
namespace N1 
{
    static class A 
    {
        public static void M(this string s){}
    }
}

namespace N2
{
    using static N1.A;

    class B
    {
        void N() 
        {
            M("A");      // Error, M unknown
            "B".M();     // Ok, M known as extension method
            N1.A.M("C"); // Ok, fully qualified
        }
    }
}
```
<span data-ttu-id="0c20a-221">il *using_static_directive* Importa il metodo di estensione `M` contenuti in `N1.A`, ma solo come un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-221">the *using_static_directive* imports the extension method `M` contained in `N1.A`, but only as an extension method.</span></span> <span data-ttu-id="0c20a-222">Di conseguenza, il primo riferimento alla `M` all'interno delle `B.N` comporta un errore in fase di compilazione perché nessun membro denominato `M` rientrano nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="0c20a-222">Thus, the first reference to `M` in the body of `B.N` results in a compile-time error because no members named `M` are in scope.</span></span>

<span data-ttu-id="0c20a-223">Oggetto *using_static_directive* Importa solo i membri e i tipi dichiarati direttamente nel tipo specificato, non i tipi e membri dichiarati nelle classi di base.</span><span class="sxs-lookup"><span data-stu-id="0c20a-223">A *using_static_directive* only imports members and types declared directly in the given type, not members and types declared in base classes.</span></span>

<span data-ttu-id="0c20a-224">TODO: Esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-224">TODO: Example</span></span>

<span data-ttu-id="0c20a-225">Ambiguità tra più *using_namespace_directives* e *using_static_directives* vengono discussi in [usando le direttive dello spazio dei nomi](namespaces.md#using-namespace-directives).</span><span class="sxs-lookup"><span data-stu-id="0c20a-225">Ambiguities between multiple *using_namespace_directives* and *using_static_directives* are discussed in [Using namespace directives](namespaces.md#using-namespace-directives).</span></span>

## <a name="namespace-members"></a><span data-ttu-id="0c20a-226">Membri di Namespace</span><span class="sxs-lookup"><span data-stu-id="0c20a-226">Namespace members</span></span>

<span data-ttu-id="0c20a-227">Oggetto *namespace_member_declaration* può essere una *namespace_declaration* ([Namespace dichiarazioni](namespaces.md#namespace-declarations)) o un *type_declaration* ( [Dichiarazioni di tipo](namespaces.md#type-declarations)).</span><span class="sxs-lookup"><span data-stu-id="0c20a-227">A *namespace_member_declaration* is either a *namespace_declaration* ([Namespace declarations](namespaces.md#namespace-declarations)) or a *type_declaration* ([Type declarations](namespaces.md#type-declarations)).</span></span>

```antlr
namespace_member_declaration
    : namespace_declaration
    | type_declaration
    ;
```

<span data-ttu-id="0c20a-228">Un'unità di compilazione o un corpo dello spazio dei nomi può contenere *namespace_member_declaration*s e tali dichiarazioni aggiungono nuovi membri allo spazio di dichiarazione sottostante del corpo di spazio dei nomi o unità di compilazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="0c20a-228">A compilation unit or a namespace body can contain *namespace_member_declaration*s, and such declarations contribute new members to the underlying declaration space of the containing compilation unit or namespace body.</span></span>

## <a name="type-declarations"></a><span data-ttu-id="0c20a-229">Dichiarazioni di tipo</span><span class="sxs-lookup"><span data-stu-id="0c20a-229">Type declarations</span></span>

<span data-ttu-id="0c20a-230">Oggetto *type_declaration* è un *class_declaration* ([le dichiarazioni di classe](classes.md#class-declarations)), un *struct_declaration* ([Struct le dichiarazioni](structs.md#struct-declarations)), un' *interface_declaration* ([dichiarazioni di interfaccia](interfaces.md#interface-declarations)), un *enum_declaration* ([Enum le dichiarazioni](enums.md#enum-declarations)), o un *delegate_declaration* ([dichiarazioni Delegate](delegates.md#delegate-declarations)).</span><span class="sxs-lookup"><span data-stu-id="0c20a-230">A *type_declaration* is a *class_declaration* ([Class declarations](classes.md#class-declarations)), a *struct_declaration* ([Struct declarations](structs.md#struct-declarations)), an *interface_declaration* ([Interface declarations](interfaces.md#interface-declarations)), an *enum_declaration* ([Enum declarations](enums.md#enum-declarations)), or a *delegate_declaration* ([Delegate declarations](delegates.md#delegate-declarations)).</span></span>

```antlr
type_declaration
    : class_declaration
    | struct_declaration
    | interface_declaration
    | enum_declaration
    | delegate_declaration
    ;
```

<span data-ttu-id="0c20a-231">Oggetto *type_declaration* può verificarsi come una dichiarazione di primo livello in un'unità di compilazione o come una dichiarazione di membro all'interno dello spazio dei nomi, classe o struct.</span><span class="sxs-lookup"><span data-stu-id="0c20a-231">A *type_declaration* can occur as a top-level declaration in a compilation unit or as a member declaration within a namespace, class, or struct.</span></span>

<span data-ttu-id="0c20a-232">Quando una dichiarazione del tipo per un tipo `T` viene visualizzata come una dichiarazione di primo livello in un'unità di compilazione, il nome completo del tipo appena dichiarato è semplicemente `T`.</span><span class="sxs-lookup"><span data-stu-id="0c20a-232">When a type declaration for a type `T` occurs as a top-level declaration in a compilation unit, the fully qualified name of the newly declared type is simply `T`.</span></span> <span data-ttu-id="0c20a-233">Quando una dichiarazione del tipo per un tipo `T` si verifica all'interno di uno spazio dei nomi, classe o struct, specificare il nome completo del tipo dichiarato appena `N.T`, dove `N` è il nome completo dello spazio dei nomi, della classe o struct che contiene.</span><span class="sxs-lookup"><span data-stu-id="0c20a-233">When a type declaration for a type `T` occurs within a namespace, class, or struct, the fully qualified name of the newly declared type is `N.T`, where `N` is the fully qualified name of the containing namespace, class, or struct.</span></span>

<span data-ttu-id="0c20a-234">Un tipo dichiarato all'interno di una classe o struct viene chiamato un tipo annidato ([i tipi annidati](classes.md#nested-types)).</span><span class="sxs-lookup"><span data-stu-id="0c20a-234">A type declared within a class or struct is called a nested type ([Nested types](classes.md#nested-types)).</span></span>

<span data-ttu-id="0c20a-235">I modificatori di accesso autorizzato e l'accesso predefinito per una dichiarazione del tipo dipende dal contesto in cui viene eseguita la dichiarazione ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)):</span><span class="sxs-lookup"><span data-stu-id="0c20a-235">The permitted access modifiers and the default access for a type declaration depend on the context in which the declaration takes place ([Declared accessibility](basic-concepts.md#declared-accessibility)):</span></span>

*  <span data-ttu-id="0c20a-236">I tipi dichiarati in unità di compilazione o gli spazi dei nomi possono avere `public` o `internal` accesso.</span><span class="sxs-lookup"><span data-stu-id="0c20a-236">Types declared in compilation units or namespaces can have `public` or `internal` access.</span></span> <span data-ttu-id="0c20a-237">Il valore predefinito è `internal` accesso.</span><span class="sxs-lookup"><span data-stu-id="0c20a-237">The default is `internal` access.</span></span>
*  <span data-ttu-id="0c20a-238">Tipi dichiarati nelle classi possono avere `public`, `protected internal`, `protected`, `internal`, o `private` accesso.</span><span class="sxs-lookup"><span data-stu-id="0c20a-238">Types declared in classes can have `public`, `protected internal`, `protected`, `internal`, or `private` access.</span></span> <span data-ttu-id="0c20a-239">Il valore predefinito è `private` accesso.</span><span class="sxs-lookup"><span data-stu-id="0c20a-239">The default is `private` access.</span></span>
*  <span data-ttu-id="0c20a-240">I tipi dichiarati negli struct possono avere `public`, `internal`, o `private` accesso.</span><span class="sxs-lookup"><span data-stu-id="0c20a-240">Types declared in structs can have `public`, `internal`, or `private` access.</span></span> <span data-ttu-id="0c20a-241">Il valore predefinito è `private` accesso.</span><span class="sxs-lookup"><span data-stu-id="0c20a-241">The default is `private` access.</span></span>

## <a name="namespace-alias-qualifiers"></a><span data-ttu-id="0c20a-242">Qualificatori di alias Namespace</span><span class="sxs-lookup"><span data-stu-id="0c20a-242">Namespace alias qualifiers</span></span>

<span data-ttu-id="0c20a-243">Il ***qualificatore di alias dello spazio dei nomi*** `::` rende possibile garantire che le ricerche di nomi di tipo non sono interessate dall'introduzione di nuovi tipi e membri.</span><span class="sxs-lookup"><span data-stu-id="0c20a-243">The ***namespace alias qualifier*** `::` makes it possible to guarantee that type name lookups are unaffected by the introduction of new types and members.</span></span> <span data-ttu-id="0c20a-244">Il qualificatore di alias dello spazio dei nomi viene sempre visualizzata tra due identificatori detti gli identificatori sinistro e destro.</span><span class="sxs-lookup"><span data-stu-id="0c20a-244">The namespace alias qualifier always appears between two identifiers referred to as the left-hand and right-hand identifiers.</span></span> <span data-ttu-id="0c20a-245">A differenza dei normali `.` qualificatore, l'identificatore a sinistra della `::` qualificatore viene eseguita una ricerca solo come extern o tramite l'alias.</span><span class="sxs-lookup"><span data-stu-id="0c20a-245">Unlike the regular `.` qualifier, the left-hand identifier of the `::` qualifier is looked up only as an extern or using alias.</span></span>

<span data-ttu-id="0c20a-246">Oggetto *qualified_alias_member* viene definito come segue:</span><span class="sxs-lookup"><span data-stu-id="0c20a-246">A *qualified_alias_member* is defined as follows:</span></span>

```antlr
qualified_alias_member
    : identifier '::' identifier type_argument_list?
    ;
```

<span data-ttu-id="0c20a-247">Oggetto *qualified_alias_member* può essere usato come una *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) o come operando di sinistra in un *member_access* ([L'accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="0c20a-247">A *qualified_alias_member* can be used as a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) or as the left operand in a *member_access* ([Member access](expressions.md#member-access)).</span></span>

<span data-ttu-id="0c20a-248">Oggetto *qualified_alias_member* presenta uno dei due formati:</span><span class="sxs-lookup"><span data-stu-id="0c20a-248">A *qualified_alias_member* has one of two forms:</span></span>

*  <span data-ttu-id="0c20a-249">`N::I<A1, ..., Ak>`, dove `N` e `I` rappresentano gli identificatori, e `<A1, ..., Ak>` è un elenco di argomenti.</span><span class="sxs-lookup"><span data-stu-id="0c20a-249">`N::I<A1, ..., Ak>`, where `N` and `I` represent identifiers, and `<A1, ..., Ak>` is a type argument list.</span></span> <span data-ttu-id="0c20a-250">(`K` è sempre almeno uno.)</span><span class="sxs-lookup"><span data-stu-id="0c20a-250">(`K` is always at least one.)</span></span>
*  <span data-ttu-id="0c20a-251">`N::I`, dove `N` e `I` rappresentano gli identificatori.</span><span class="sxs-lookup"><span data-stu-id="0c20a-251">`N::I`, where `N` and `I` represent identifiers.</span></span> <span data-ttu-id="0c20a-252">(In questo caso, `K` viene considerato uguale a zero.)</span><span class="sxs-lookup"><span data-stu-id="0c20a-252">(In this case, `K` is considered to be zero.)</span></span>

<span data-ttu-id="0c20a-253">Mediante questa notazione, il significato di un *qualified_alias_member* viene determinata come segue:</span><span class="sxs-lookup"><span data-stu-id="0c20a-253">Using this notation, the meaning of a *qualified_alias_member* is determined as follows:</span></span>

*  <span data-ttu-id="0c20a-254">Se `N` è l'identificatore `global`, quindi lo spazio dei nomi globale viene cercato `I`:</span><span class="sxs-lookup"><span data-stu-id="0c20a-254">If `N` is the identifier `global`, then the global namespace is searched for `I`:</span></span>
   * <span data-ttu-id="0c20a-255">Se lo spazio dei nomi globale contiene uno spazio dei nomi denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento allo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-255">If the global namespace contains a namespace named `I` and `K` is zero, then the *qualified_alias_member* refers to that namespace.</span></span>
   * <span data-ttu-id="0c20a-256">In caso contrario, se lo spazio dei nomi globale contiene un tipo non generico denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento a quel tipo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-256">Otherwise, if the global namespace contains a non-generic type named `I` and `K` is zero, then the *qualified_alias_member* refers to that type.</span></span>
   * <span data-ttu-id="0c20a-257">In caso contrario, se lo spazio dei nomi globale contiene un tipo denominato `I` dotato `K`  parametri di tipo, il *qualified_alias_member* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="0c20a-257">Otherwise, if the global namespace contains a type named `I` that has `K` type parameters, then the *qualified_alias_member* refers to that type constructed with the given type arguments.</span></span>
   * <span data-ttu-id="0c20a-258">In caso contrario, il *qualified_alias_member* è non definito e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-258">Otherwise, the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>

*  <span data-ttu-id="0c20a-259">In caso contrario, inizia con la dichiarazione dello spazio dei nomi ([Namespace dichiarazioni](namespaces.md#namespace-declarations)) che contiene il *qualified_alias_member* (se presente), proseguendo con ogni dichiarazione dello spazio dei nomi contenitore (se presente) e che termina con l'unità di compilazione contenente le *qualified_alias_member*, la procedura seguente viene valutata fino a quando non viene individuata un'entità:</span><span class="sxs-lookup"><span data-stu-id="0c20a-259">Otherwise, starting with the namespace declaration ([Namespace declarations](namespaces.md#namespace-declarations)) immediately containing the *qualified_alias_member* (if any), continuing with each enclosing namespace declaration (if any), and ending with the compilation unit containing the *qualified_alias_member*, the following steps are evaluated until an entity is located:</span></span>

   * <span data-ttu-id="0c20a-260">Se l'unità di compilazione o di dichiarazione dello spazio dei nomi contiene un *using_alias_directive* che associa `N` con un tipo, il *qualified_alias_member* è definito e una fase di compilazione si verifica l'errore.</span><span class="sxs-lookup"><span data-stu-id="0c20a-260">If the namespace declaration or compilation unit contains a *using_alias_directive* that associates `N` with a type, then the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>
   * <span data-ttu-id="0c20a-261">In caso contrario, se l'unità di compilazione o di dichiarazione dello spazio dei nomi contiene un' *extern_alias_directive* oppure *using_alias_directive* che associa `N` con uno spazio dei nomi, quindi:</span><span class="sxs-lookup"><span data-stu-id="0c20a-261">Otherwise, if the namespace declaration or compilation unit contains an *extern_alias_directive* or *using_alias_directive* that associates `N` with a namespace, then:</span></span>
     * <span data-ttu-id="0c20a-262">Se lo spazio dei nomi associati `N` contiene uno spazio dei nomi denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento allo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-262">If the namespace associated with `N` contains a namespace named `I` and `K` is zero, then the *qualified_alias_member* refers to that namespace.</span></span>
     * <span data-ttu-id="0c20a-263">In caso contrario, se lo spazio dei nomi associati `N` contiene un tipo non generico denominato `I` e `K` è uguale a zero, il *qualified_alias_member* fa riferimento a quel tipo.</span><span class="sxs-lookup"><span data-stu-id="0c20a-263">Otherwise, if the namespace associated with `N` contains a non-generic type named `I` and `K` is zero, then the *qualified_alias_member* refers to that type.</span></span>
     * <span data-ttu-id="0c20a-264">In caso contrario, se lo spazio dei nomi associati `N` contiene un tipo denominato `I` dotato `K`  parametri di tipo, il *qualified_alias_member* fa riferimento a tale tipo costruito con gli argomenti di tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="0c20a-264">Otherwise, if the namespace associated with `N` contains a type named `I` that has `K` type parameters, then the *qualified_alias_member* refers to that type constructed with the given type arguments.</span></span>
     * <span data-ttu-id="0c20a-265">In caso contrario, il *qualified_alias_member* è non definito e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-265">Otherwise, the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>
*  <span data-ttu-id="0c20a-266">In caso contrario, il *qualified_alias_member* è non definito e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-266">Otherwise, the *qualified_alias_member* is undefined and a compile-time error occurs.</span></span>

<span data-ttu-id="0c20a-267">Si noti che tramite il qualificatore di alias dello spazio dei nomi con un alias che fa riferimento a un tipo causa un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0c20a-267">Note that using the namespace alias qualifier with an alias that references a type causes a compile-time error.</span></span> <span data-ttu-id="0c20a-268">Si noti anche che se l'identificatore `N` viene `global`, quindi la ricerca viene eseguita nello spazio dei nomi globale, anche se è presente un'associazione di alias usando `global` con un tipo o dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-268">Also note that if the identifier `N` is `global`, then lookup is performed in the global namespace, even if there is a using alias associating `global` with a type or namespace.</span></span>

### <a name="uniqueness-of-aliases"></a><span data-ttu-id="0c20a-269">Univocità degli alias</span><span class="sxs-lookup"><span data-stu-id="0c20a-269">Uniqueness of aliases</span></span>

<span data-ttu-id="0c20a-270">Ogni unità di compilazione o corpo dello spazio dei nomi ha uno spazio di dichiarazione separata per gli alias extern e utilizzo degli alias.</span><span class="sxs-lookup"><span data-stu-id="0c20a-270">Each compilation unit and namespace body has a separate declaration space for extern aliases and using aliases.</span></span> <span data-ttu-id="0c20a-271">Di conseguenza, anche se il nome di alias extern o tramite l'alias deve essere univoco all'interno del set di alias extern e utilizzo degli alias dichiarate nel corpo di spazio dei nomi o unità di compilazione che li contiene, un alias può avere lo stesso nome di un tipo o spazio dei nomi, purché si t viene usato solo con la `::` qualificatore.</span><span class="sxs-lookup"><span data-stu-id="0c20a-271">Thus, while the name of an extern alias or using alias must be unique within the set of extern aliases and using aliases declared in the immediately containing compilation unit or namespace body, an alias is permitted to have the same name as a type or namespace as long as it is used only with the `::` qualifier.</span></span>

<span data-ttu-id="0c20a-272">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="0c20a-272">In the example</span></span>
```csharp
namespace N
{
    public class A {}

    public class B {}
}

namespace N
{
    using A = System.IO;

    class X
    {
        A.Stream s1;            // Error, A is ambiguous

        A::Stream s2;           // Ok
    }
}
```
<span data-ttu-id="0c20a-273">il nome `A` ha due significati possibili all'interno dello spazio dei nomi secondo perché la classe `A` e l'utilizzo alias `A` rientrano nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="0c20a-273">the name `A` has two possible meanings in the second namespace body because both the class `A` and the using alias `A` are in scope.</span></span> <span data-ttu-id="0c20a-274">Per questo motivo, l'uso di `A` nel nome qualificato `A.Stream` è ambiguo e genera un errore in fase di compilazione si verifichi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-274">For this reason, use of `A` in the qualified name `A.Stream` is ambiguous and causes a compile-time error to occur.</span></span> <span data-ttu-id="0c20a-275">Tuttavia, usare `A` con il `::` qualificatore non è un errore perché `A` viene cercato solo come un alias dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0c20a-275">However, use of `A` with the `::` qualifier is not an error because `A` is looked up only as a namespace alias.</span></span>
