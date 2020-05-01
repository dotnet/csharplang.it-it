---
ms.openlocfilehash: 7522bee6ac14d205aaf2a8491c13a321d2c18d62
ms.sourcegitcommit: c30039481ee8a75c3b3e4ddd369fdf8f84f8945b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598581"
---
# <a name="module-initializers"></a>Inizializzatori di modulo

* [x] proposto
* [] Prototipo: [in corso](https://github.com/jnm2/roslyn/tree/module_initializer)
* [] Implementazione: [in corso](https://github.com/dotnet/roslyn/tree/features/module-initializers)
* [] Specifica: [non avviata]()

## <a name="summary"></a>Summary
[summary]: #summary

Sebbene la piattaforma .NET disponga di una funzionalità che supporta direttamente la scrittura del codice di inizializzazione per l'assembly (tecnicamente, il modulo), non viene esposta in C#.  Si tratta di uno scenario di nicchia piuttosto semplice, ma dopo l'esecuzione le soluzioni sembrano essere piuttosto dolorose.  Ci sono rapporti di [un numero di clienti](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (all'interno e all'esterno di Microsoft) che faticano a riscontrare il problema e non ci sono dubbi più casi non documentati.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

- Consentire alle librerie di eseguire l'inizializzazione unica, una volta caricata, con un sovraccarico minimo e senza che l'utente debba chiamare esplicitamente niente
- Un particolare punto problematico degli `static` approcci dei costruttori correnti è che il runtime deve eseguire verifiche aggiuntive sull'utilizzo di un tipo con un costruttore statico, per decidere se il costruttore statico deve essere eseguito o meno. Questo aggiunge un sovraccarico misurabile.
- Consentire ai generatori di origine di eseguire una logica di inizializzazione globale senza che l'utente debba chiamare esplicitamente un elemento

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Il compilatore C# riconoscerà l'attributo seguente:

``` c#
namespace System.Runtime.CompilerServices
{
    // Note: an Obsolete attribute is not needed here,
    // because only C# 9 compilers will have access to attributes added in .NET 5.
    // LangVersion checks will still be necessary.
    [AttributeUsage(AttributeTargets.Module, AllowMultiple = false)]
    public class ModuleInitializerAttribute : Attribute
    {
        public ModuleInitializerAttribute(Type type) { }
    }
}
```

Che verrà usata come questa

``` c#
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyModuleInitializer))]

internal static class MyModuleInitializer
{
    static MyModuleInitializer()
    {
        // put your module initializer here
    }
}
```

e il compilatore C# creerebbe quindi un costruttore di modulo che causa l'attivazione del costruttore statico del tipo identificato:

``` c#
void .cctor()
{
    // synthesize and call a dummy method with an unspeakable name,
    // which will cause the runtime to call the static constructor
    MyModuleInitializer.<TriggerClassConstructor>();
}
```

Si noti che il sovraccarico di verifica se il costruttore statico è stato eseguito in un tipo di`MyModuleInitializer` inizializzatore di modulo (in questo esempio) può essere mantenuto come minimo facendo riferimento al `[module: ModuleInitializer(typeof(MyModuleInitializer))]` tipo solo nell'attributo.

Questa proposta USA `AttributeUsage(AllowMultiple = false)` per impedire all'utente di dichiarare più di una classe dell'inizializzatore senza aggiungere alcuna regola di linguaggio speciale per eseguire tale operazione. È molto leggero perché usa la sintassi e le regole semantiche esistenti per gli attributi. D'altra parte, può risultare più difficile per gli utenti tenere presente che il costruttore statico che sta esaminando è l'inizializzatore del modulo, perché l'attributo può essere distante dalla classe che contiene effettivamente l'inizializzatore del modulo e richiede anche di decidere cosa accade se l'attributo viene usato per fare riferimento a un tipo da un altro assembly.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

#### <a name="should-we-permit-multiple-types-to-be-decorated-with-moduleinitializerattribute-in-a-compilation-if-so-in-what-order-should-the-static-constructors-be-invoked"></a>È possibile consentire la decorazione di più tipi con ModuleInitializerAttribute in una compilazione? In tal caso, nell'ordine in cui devono essere richiamati i costruttori statici?

Un'opzione: eseguire la stessa operazione per gli inizializzatori statici nelle classi parziali. Ordinarle in base all'ordine del file e alla posizione di origine.

#### <a name="should-we-permit-using-a-type-from-another-assembly-as-the-module-initializer"></a>È consigliabile consentire l'uso di un tipo da un altro assembly come inizializzatore di modulo?

e.g.`[module: ModuleInitializerAttribute(typeof(InitializerFromOtherAssembly))]`

Questo potrebbe rendere difficile richiamare il costruttore della classe semplicemente chiamando un metodo fittizio, perché non sarà possibile sintetizzare tale metodo sul tipo. È possibile considerare la possibilità di `System.Runtime.CompilerServices.RuntimeHelpers.RunClassConstructor()` eseguire il fallback in questo caso, ma introduce una dipendenza dallo stack di Reflection.

Questo caso d'uso implica che la classe dell'inizializzatore non sia l'inizializzatore di modulo per l'assembly in cui è dichiarato. Si tratta di un caso di utilizzo che deve invece essere gestito esponendo un metodo che il consumer esterno deve chiamare dall'inizializzatore del modulo.

```cs
// Assembly 1
public class MyLibInit
{
    public static void Init() { }
}

// Assembly 2
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyInit))]

class MyInit
{
    static
    {
        MyLibInit.Init();
    }
}
```

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Perché *non* eseguire questa operazione?

- Forse gli strumenti di terze parti esistenti per gli inizializzatori di modulo "inserimento" sono sufficienti per gli utenti che hanno richiesto questa funzionalità.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Quali altre progettazioni sono state considerate? Qual è l'effetto di questa operazione?

Esistono diversi modi possibili per esporre questa funzionalità nel linguaggio:

### <a name="1-special-global-method-declaration"></a>1. dichiarazione speciale di metodo globale

Viene fornito un inizializzatore di modulo scrivendo un tipo speciale di metodo nell'ambito globale:

```csharp
internal void operator init() ...
```

In questo modo il nuovo linguaggio crea la propria sintassi. Tuttavia, date le rare e le nicchie dello scenario, questo approccio è probabilmente troppo pesante.

### <a name="2-attribute-on-the-type-to-be-initialized"></a>2. attributo sul tipo da inizializzare

Anziché un attributo a livello di modulo, probabilmente l'attributo verrebbe inserito sul tipo da inizializzare

```csharp
[ModuleInitializer]
class ToInitialize
{
    static ToInitialize() ...
}
```

Con questo approccio, è necessario rifiutare un programma che contiene più di un'applicazione di questo attributo o fornire un criterio per definire l'ordine nel caso in cui venga usato più volte. In entrambi i casi, è più complesso rispetto alla proposta originale precedente.

#### <a name="3-attribute-on-the-static-constructor-to-be-initialized"></a>3. attributo sul costruttore statico da inizializzare

Invece di un attributo a livello di modulo, probabilmente l'attributo viene inserito nel costruttore statico da inizializzare.

```csharp
class ToInitialize
{
    [ModuleInitializer]
    static ToInitialize() ...
}
```

Con questo approccio, è necessario rifiutare un programma che contiene più di un'applicazione di questo attributo o fornire un criterio per definire l'ordine nel caso in cui venga usato più volte. In entrambi i casi, è più complesso rispetto alla proposta originale precedente.

#### <a name="4-attribute-on-a-static-method-to-be-called"></a>4. attributo su un metodo statico da chiamare

Invece di un attributo a livello di modulo, probabilmente l'attributo viene inserito nel metodo da chiamare per eseguire l'inizializzazione.

```csharp
class Any
{
    [ModuleInitializer]
    static void Initializer() ...
}
```

Come nell'approccio precedente, è necessario rifiutare un programma che contiene più di un'applicazione di questo attributo o fornire alcuni criteri per definire l'ordine nel caso in cui venga usato più volte. In entrambi i casi, è più complesso della proposta originale. Potrebbe anche essere necessario rifiutare qualsiasi metodo con questo attributo se presenta parametri o un tipo non`void` restituito.

#### <a name="5-do-nothing"></a>5. non eseguire alcuna operazione
Se questa funzionalità non viene implementata, effettuare le operazioni seguenti:
- Gli utenti che necessitano degli inizializzatori di moduli continuano a basarsi sugli strumenti di terze parti per inserirli negli assembly.
- I generatori di origine dovranno basarsi su costruttori statici che aggiungono sovraccarico o sui metodi init () che gli utenti devono chiamare in modo esplicito.

## <a name="design-meetings"></a>Riunioni di progettazione

### <a name="april-8th-2020"></a>[8 aprile 2020](/meetings/2020/LDM-2020-04-08.md#module-initializers)
È possibile lasciare che qualsiasi metodo statico sia un inizializzatore di modulo (opzione 4 nella proposta precedente) e contrassegnare il metodo usando un attributo noto. Verranno inoltre consentiti più metodi di inizializzazione del modulo e ognuno verrà chiamato in un ordine riservato, ma deterministico.
