---
ms.openlocfilehash: 6fbc866af9971d86a287b026013e235e5b25fc21
ms.sourcegitcommit: ab0873759f86d44adfc5daefb18cb922df8adb8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82162074"
---
<a name="extending-partial-methods"></a>Estensione di metodi parziali
=====

## <a name="summary"></a>Riepilogo
Questa proposta mira a rimuovere tutte le restrizioni relative alle firme `partial` dei metodi in C#. L'obiettivo è quello di espandere il set di scenari in cui questi metodi possono funzionare con i generatori di origine, nonché un modulo di dichiarazione più generale per i metodi C#.

Vedere anche la [specifica dei metodi parziali originali](/spec/classes.md#partial-methods).

## <a name="motivation"></a>Motivazione
C# offre un supporto limitato per gli sviluppatori che dividono metodi in dichiarazioni e definizioni/implementazioni. 

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

Un comportamento dei `partial` metodi è che, quando la definizione è assente, il linguaggio cancellerà semplicemente tutte le `partial` chiamate al metodo. Sostanzialmente si comporta come una chiamata a un `[Conditional]` metodo in cui la condizione è stata valutata come false. 

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

La motivazione originale per questa caratteristica è la generazione di origini sotto forma di codice generato dalla finestra di progettazione. Gli utenti modificavano costantemente il codice generato perché volevano associare un aspetto del codice generato. In particolare parti del processo di avvio Windows Forms, dopo l'inizializzazione dei componenti.

La modifica del codice generato è soggetta a errori poiché qualsiasi azione che ha causato la rigenerazione del codice da parte della finestra di progettazione comporterebbe la cancellazione della modifica dell'utente. La `partial` funzionalità del metodo ha semplificato questa tensione perché ha consentito ai progettisti di creare hook `partial` sotto forma di metodi. 

I progettisti possono creare hook `partial void OnComponentInit()` come e gli sviluppatori possono definire dichiarazioni per loro o non definirli. In entrambi i casi, anche se il codice generato viene compilato e gli sviluppatori interessati al processo potrebbero collegarsi in base alle esigenze. 

Ciò significa che i metodi parziali presentano alcune restrizioni:

1. Deve avere un `void` tipo restituito.
1. Non possono `out` avere parametri. 
1. Non può avere alcuna accessibilità ( `private`in modo implicito).

Queste restrizioni sono dovute al fatto che la lingua deve essere in grado di emettere codice quando viene cancellato il sito di chiamata. Dato che possono essere cancellati `private` , l'unica accessibilità possibile è che il membro non può essere esposto nei metadati dell'assembly. Queste restrizioni servono anche per limitare il set di scenari in cui `partial` è possibile applicare i metodi.

La proposta consiste nel rimuovere tutte le restrizioni esistenti intorno `partial` ai metodi. In sostanza, è `out`possibile consentire l'accesso ai tipi restituiti non void o a qualsiasi tipo di accessibilità. Tali `partial` dichiarazioni avrebbero quindi il requisito aggiuntivo che deve esistere una definizione. Questo significa che la lingua non deve considerare l'effetto della cancellazione dei siti di chiamata. 

In questo modo si espande il set di scenari `partial` di generazione che i metodi possono partecipare e quindi si collegano correttamente con la funzionalità generatori di origine. Ad esempio, è possibile definire un'espressione regolare usando il modello seguente:

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

In questo modo, allo sviluppatore viene assegnato un semplice metodo dichiarativo per optare per i generatori e fornire ai generatori un set molto semplice di dichiarazioni da esaminare nel codice sorgente per guidare l'output generato. 

Si confronti con la difficoltà che un generatore avrebbe collegato al frammento di codice seguente. 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

Dato che il compilatore non consente ai generatori di modificare il codice, l'associazione di questo modello potrebbe essere molto impossible per i generatori. Dovranno ricorrere alla reflection nell' `IsMatch` implementazione o chiedere agli utenti di modificare i siti di chiamata in un nuovo metodo e di eseguire il refactoring dell'espressione regolare per passare il valore letterale stringa come argomento. È piuttosto complicato.

## <a name="detailed-design"></a>Progettazione dettagliata
Il linguaggio verrà modificato in modo `partial` da consentire l'annotazione dei metodi con un modificatore di accessibilità esplicito. Ciò significa che possono essere etichettati come `private`, `public`e così via... 

Quando un `partial` metodo dispone di un modificatore di accessibilità esplicita, anche se per il linguaggio è necessario che la dichiarazione includa una definizione corrispondente anche quando l'accessibilità è `private`:

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

Oltre al linguaggio verranno rimosse tutte le restrizioni su ciò che può `partial` essere visualizzato in un metodo con un'accessibilità esplicita. Tali dichiarazioni possono contenere tipi restituiti non void, `out` parametri, `extern` modificatori e così via. Queste firme avranno la massima espressività del linguaggio C#.

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

Questo consente in `partial` `overrides` modo esplicito ai metodi di partecipare `interface` alle implementazioni di e:

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

Il compilatore modificherà l'errore emesso quando un `partial` metodo contiene un elemento non valido per indicare essenzialmente:

> Impossibile utilizzare `ref` su un `partial` metodo privo di accessibilità esplicita 

Ciò consentirà agli sviluppatori di puntare alla direzione corretta quando si usa questa funzionalità.

Restrizioni:
- `partial`le dichiarazioni con accessibilità esplicita devono avere una definizione
- `partial`le dichiarazioni e le firme delle definizioni devono corrispondere a tutti i modificatori di parametro e metodo. Gli unici aspetti che possono variare sono i nomi di parametro e gli elenchi di attributi (questo non è un nuovo requisito `partial` , ma piuttosto un requisito esistente di metodi).

## <a name="questions"></a>Domande

### <a name="partial-on-all-members"></a>parziale su tutti i membri
Dato che la nostra espansione `partial` è più intuitiva per i generatori di origine, dobbiamo espanderla anche per lavorare su tutti i membri della classe? Ad esempio, dovrebbe essere possibile dichiarare `partial` costruttori, operatori e così via...

**Risoluzione** dei problemi L'idea è valida, ma a questo punto nella pianificazione di C# 9 si sta tentando di evitare il creep della funzionalità non necessaria. Si vuole risolvere il problema immediato dell'espansione della funzionalità per lavorare con i generatori di origine moderni. 

L' `partial` estensione per il supporto di altri membri verrà considerata per la versione di C# 10. Sembra probabile che questa estensione verrà considerata.

### <a name="use-abstract-instead-of-partial"></a>USA abstract anziché partial
Il punto cruciale di questa proposta è essenzialmente garantire che una dichiarazione disponga di una definizione/implementazione corrispondente. Dato che è necessario usare `abstract` perché è già una parola chiave del linguaggio che impone allo sviluppatore di pensare a un'implementazione?

**Risoluzione** dei problemi Si è verificata una discussione di questo aspetto, ma alla fine è stata decisa.
Sì, i requisiti sono noti ma i concetti sono significativamente diversi.
Potrebbe facilmente condurre lo sviluppatore a credere che stesse creando slot virtuali quando non lo facevano.
