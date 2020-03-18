---
ms.openlocfilehash: 36f3e6204d12c2569b3a55f3a47f58337e8a08e4
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484629"
---
# <a name="indexing-fixed-fields-should-not-require-pinning-regardless-of-the-movableunmovable-context"></a>L'indicizzazione di campi `fixed` non deve richiedere il blocco indipendentemente dal contesto mobile/unmovibile. #

La modifica ha la dimensione di una correzione di bug. Può trovarsi in 7,3 e non è in conflitto con qualsiasi direzione.
Questa modifica consente solo di consentire il funzionamento dello scenario seguente anche se `s` è mobile. È già valido quando `s` non è mobile. 

Nota: in entrambi i casi, è ancora necessario `unsafe` contesto. È possibile leggere dati non inizializzati o persino fuori intervallo. Che non è in corso di modifica.

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int p = s.myFixedField[5]; // indexing fixed-size array fields would be ok
    }
}
```

La principale "sfida" che ho visto qui è come spiegare il relax nella specifica. In particolare, dal momento che le operazioni seguenti dovrebbero comunque avere un blocco. Poiché `s` è mobile e il campo viene usato in modo esplicito come puntatore)

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int* ptr = s.myFixedField; // taking a pointer explicitly still requires pinning.
        int p = ptr[5];
    }
}
```

Uno dei motivi per cui è necessario aggiungere la destinazione quando è mobile è l'artefatto della strategia di generazione del codice, ovvero si esegue sempre la conversione in un puntatore non gestito e quindi si impone l'aggiunta dell'utente tramite `fixed` istruzione. Tuttavia, la conversione in non gestita non è necessaria quando si esegue l'indicizzazione. La stessa matematica del puntatore non sicuro è ugualmente applicabile quando il ricevitore è sotto forma di puntatore gestito. In tal caso, il riferimento intermedio è gestito (GC-tracked) e il blocco non è necessario.

Il https://github.com/dotnet/roslyn/pull/24966 delle modifiche è un prototipo di richiesta pull che attenua questo requisito.
