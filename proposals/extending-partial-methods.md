---
ms.openlocfilehash: 04dca01bad04d5c53aa1c7c876343fb7ef33d2fa
ms.sourcegitcommit: 5c7cc619214ade6a8f3a0caddfb4862635f5241d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82021930"
---
<a name="extending-partial-methods"></a>Estensione di metodi parzialiExtending Partial Methods
=====

## <a name="summary"></a>Summary
Questa proposta ha lo scopo di `partial` rimuovere tutte le restrizioni relative alle firme dei metodi in C. L'obiettivo è quello di espandere il set di scenari in cui questi metodi possono utilizzare i generatori di origine, nonché essere un formato di dichiarazione più generale per i metodi di C.

## <a name="motivation"></a>Motivazione
Il supporto limitato per gli sviluppatori che suddivide i metodi in dichiarazioni e definizioni/implementazioni. 

```cs 
partial class C
{
    // The declaration of C.M
    partial void M(string message);
}

partial class C
{
    // The definition of C.M
    partial void M(string message) => Console.WriteLine(message);
}
```

Un comportamento `partial` dei metodi è che quando la definizione è assente, il linguaggio cancellerà semplicemente tutte le chiamate al `partial` metodo. Essenzialmente si comporta come una `[Conditional]` chiamata a un metodo in cui la condizione è stata valutata su false. 

```cs
partial class D
{
    partial void M(string message);

    void Example()
    {
        M(GetIt()); // Call to M and GetIt erased at compile time
    }

    string GetIt() => "Hello World";
}
```

La motivazione originale per questa funzionalità è stata la generazione di origine sotto forma di codice generato dalla finestra di progettazione. Gli utenti modificavano costantemente il codice generato perché desideravano associare alcuni aspetti del codice generato. In particolare parti del processo di avvio di Windows Form, dopo l'inizializzazione dei componenti.

La modifica del codice generato era soggetta a errori perché qualsiasi azione che ha causato la rigenerazione del codice da parte della finestra di progettazione causerebbe la cancellazione della modifica da parte dell'utente. La `partial` funzionalità del metodo ha allentato questa tensione perché ha `partial` permesso ai progettisti di emettere hook sotto forma di metodi. 

I progettisti potevano emettere hook come `partial void OnComponentInit()` e gli sviluppatori potevano definire le dichiarazioni per loro o non definirli. In entrambi i casi, anche se il codice generato verrebbe compilato e gli sviluppatori che erano interessati al processo potrebbero eseguire l'hook in base alle esigenze. 

Ciò significa che i metodi parziali hanno diverse restrizioni:This does that partial methods have several restrictions:

1. Deve avere `void` un tipo restituito.
1. Non `ref` può `out` avere o parametri. 
1. Non può avere accessibilità `private`(implicitamente).

Queste restrizioni esistono perché la lingua deve essere in grado di generare codice quando il sito di chiamata viene cancellato. Dato che possono essere `private` cancellati è l'unica accessibilità possibile perché il membro non può essere esposto nei metadati dell'assembly. Queste restrizioni servono anche a limitare `partial` il set di scenari in cui i metodi possono essere applicati.

La proposta è quella di eliminare tutte `partial` le restrizioni esistenti sui metodi. Essenzialmente lasciare `out`che abbiano , tipi restituiti non void o qualsiasi tipo di accessibilità. Tali `partial` dichiarazioni avrebbero quindi il requisito aggiuntivo che una definizione deve esistere. Ciò significa che la lingua non deve considerare l'impatto della cancellazione dei siti di chiamata. 

Questo espanderebbe l'insieme `partial` di scenari di generatori a cui i metodi potrebbero partecipare e quindi collegarsi bene con la nostra funzionalità dei generatori di origine. Ad esempio, un'espressione regolare può essere definita usando il modello seguente:For example a regex could be defined using the following pattern:

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

Questo dà sia lo sviluppatore un semplice modo dichiarativo di opting in generatori, nonché dando generatori un set molto semplice di dichiarazioni per guardare attraverso nel codice sorgente per guidare il loro output generato. 

Confrontare che con la difficoltà che un generatore avrebbe agganciando il seguente frammento di codice. 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

Dato che il compilatore non consente ai generatori di modificare il codice che collega questo modello sarebbe praticamente impossibile per i generatori. Avrebbero bisogno di ricorrere `IsMatch` alla reflection nell'implementazione o chiedere agli utenti di modificare i loro siti di chiamata a un nuovo metodo - effettuare il refactoring dell'espressione regolare per passare il valore letterale stringa come argomento. È piuttosto disordinato.

## <a name="detailed-design"></a>Progettazione dettagliata
Il linguaggio cambierà per consentire `partial` agli metodi di essere annotati con un modificatore di accessibilità esplicito. Ciò significa che possono `private` `public`essere etichettati come , , ecc ... 

Quando `partial` un metodo dispone di un modificatore di accessibilità esplicito, anche `private`se il linguaggio richiede che la dichiarazione abbia una definizione corrispondente anche quando l'accessibilità è :

```cs
partial class C
{
    // Okay because no definition is required here
    partial void M1();

    // Okay because M2 has a definition
    private partial void M2();

    // Error: partial method M3 must have a definition
    private partial void M3();
}

partial class C
{
    private partial void M2() { }
}
```

Inoltre il linguaggio rimuoverà tutte le `partial` restrizioni su ciò che può apparire su un metodo che ha un'accessibilità esplicita. Tali dichiarazioni possono contenere tipi `ref` `out` restituiti `extern` non void, o parametri, modificatori e così via. Queste firme avranno l'espressività completa del linguaggio C.

```cs
partial class D
{
    // Okay
    internal partial bool TryParse(string s, out int i); 
}

partial class D
{
    internal partial bool TryParse(string s, out int i) { }
}
```

Ciò consente `partial` in modo `overrides` `interface` esplicito ai metodi di partecipare e alle implementazioni:This explicitly allows for methods to participate to e implementations:

```cs
interface IStudent
{
    string GetName();
}

partial class C : IStudent
{
    public virtual partial string GetName(); 
}

partial class C
{
    public virtual partial string GetName() => "Jarde";
}
```

Il compilatore modificherà l'errore `partial` generato quando un metodo contiene un elemento non valido per dire essenzialmente:

> Impossibile `ref` utilizzare `partial` un metodo privo di accessibilità esplicita 

Questo aiuterà gli sviluppatori a indirizzare gli sviluppatori nella giusta direzione quando si utilizza questa funzionalità.

Restrizioni:
- `partial`le dichiarazioni con accessibilità esplicita devono avere una definizione
- `partial`Le dichiarazioni e le firme di definizione devono corrispondere a tutti i modificatori di metodo e parametro. Gli unici aspetti che possono differire sono i nomi dei parametri `partial` e gli elenchi di attributi (questo non è nuovo, ma piuttosto un requisito esistente dei metodi).

## <a name="questions"></a>Domande

### <a name="partial-on-all-members"></a>parziale su tutti i membri
Dato che ci stiamo `partial` espandendo per essere più amichevoli ai generatori di origine dovremmo anche espanderlo per lavorare su tutti i membri della classe? Per esempio dovremmo essere `partial` in grado di dichiarare costruttori, operatori, ecc ...

**Risoluzione** L'idea è valida, ma a questo punto della pianificazione di C è 9 stiamo cercando di evitare inutili funzionalità creep. Vuoi risolvere il problema immediato di espandere la funzione per lavorare con i generatori di origine moderni. 

L'estensione `partial` per supportare altri membri verrà considerata per la versione di C . Sembra probabile che prenderemo in considerazione questa estensione.

### <a name="use-abstract-instead-of-partial"></a>Utilizzare astratto anziché parziale
Il nocciolo di questa proposta garantisce essenzialmente che una dichiarazione abbia una definizione/attuazione corrispondente. Dato che dovremmo `abstract` usare dal momento che è già una parola chiave del linguaggio che costringe lo sviluppatore a pensare di avere un'implementazione?

**Risoluzione** C'è stata una sana discussione su questo, ma alla fine è stato deciso contro.
Sì, i requisiti sono familiari, ma i concetti sono significativamente diversi.
Potrebbe facilmente portare lo sviluppatore a credere che stavano creando slot virtuali quando non lo stavano facendo.
