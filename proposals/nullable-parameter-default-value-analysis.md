---
ms.openlocfilehash: bb8c3bd0647940240310e4825d902bdd0f3c9097
ms.sourcegitcommit: 29b0f41953ad7b0d4052715e5b53f933cc42e5ee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338432"
---
# <a name="nullable-parameter-default-value-analysis"></a>Analisi del valore predefinito del parametro Nullable

## <a name="analysis-of-declarations"></a>Analisi delle dichiarazioni

In una dichiarazione di metodo è opportuno che il compilatore fornisca avvisi per i valori predefiniti dei parametri che non sono compatibili con il tipo del parametro.

```cs
void M(string s = null) // warning CS8600: Converting null literal or possible null value to non-nullable type.
{
}
```

Tuttavia, i generics non vincolati presentano un problema per cui un valore errato può entrare, ma non viene visualizzato un avviso per motivi di compatibilità. È stata quindi adottata una strategia per simulare un'assegnazione del valore predefinito al parametro nel corpo del metodo, quindi eseguire il join nello stato risultante, fornendo gli avvisi desiderati nella firma del metodo, nonché lo stato iniziale Nullable desiderato per il parametro.

```cs
class C<T>
{
    void M0(T t) { }

    void M1(T t = default) // no warning here
    {
        M0(t); // warning CS8604: Possible null reference argument for parameter 't' in 'void C<T>.M0(T t)'.
    }
}
```

È difficile aggiornare correttamente lo stato iniziale del parametro in tutti gli scenari. Di seguito sono riportati alcuni scenari in cui viene affrontato il metodo:

### <a name="overriding-methods-with-optional-parameters"></a>[Override di metodi con parametri facoltativi](https://github.com/dotnet/roslyn/issues/48848)
```cs
Base<string> obj = new Override();
obj.M(); // throws NRE at runtime

public class Base<T>
{
    public virtual void M(T t = default) { } // no warning
}

public class Override : Base<string>
{
    public override void M(string s)
    {
        s.ToString(); // no warning today, but something in this sample ought to warn. :)
    }
}
```
Nell'esempio precedente è possibile chiamare il metodo `Base<string>.M()` e inviare a `Override.M()` . È necessario tenere conto della possibilità che il chiamante venga fornito in modo implicito `null` come argomento per `s` tramite la base, ma attualmente non lo è.

---

### <a name="lambda-conversion-to-delegates-which-have-optional-parameters"></a>[Conversione lambda in delegati che dispongono di parametri facoltativi](https://github.com/dotnet/roslyn/issues/48844)
```cs
public delegate void Del<T>(T t = default);

public class C
{
    public static void Main()
    {
        Del<string> del = str => str.ToString(); // expected warning, but didn't get one
        del(); // throws NRE at runtime
    }
}
```
Nell'esempio precedente si prevede che un'espressione lambda convertita nel tipo `Del<string>` avrà uno `MaybeNull` stato iniziale per il parametro a causa del valore predefinito. Attualmente questo caso non viene gestito correttamente.

---

### <a name="abstract-methods-and-delegate-declarations-which-have-optional-parameters"></a>[Metodi astratti e dichiarazioni di delegati che dispongono di parametri facoltativi](https://github.com/dotnet/roslyn/issues/48847)
```cs
public abstract class C
{
    public abstract void M1(string s = null); // expected warning, but didn't get one
}

interface I
{
    void M1(string s = null); // expected warning, but didn't get one
}

public delegate void Del1(string s = null); // expected warning, but didn't get one
```
Nell'esempio precedente, si desiderano avvisi su questi parametri che non sono direttamente associati ad alcuna implementazione del metodo. Tuttavia, dal momento che gli elenchi di parametri non hanno metodi con i corpi da analizzare, non viene mai raggiunto il metodo EnterParameters in NullableWalker che simula le assegnazioni e genera gli avvisi.

---

### <a name="indexers-with-get-and-set-accessors"></a>Indicizzatori con funzioni di accesso get e set
```cs
public class C
{
    public int this[int i, string s = null] // no warning here
    {
        get // entire accessor syntax has warning CS8600: Converting null literal or possible null value to non-nullable type.
        {
            return i;
        }

        set // entire accessor syntax has warning CS8600: Converting null literal or possible null value to non-nullable type.
        {
        }
    }
}
```
Questo ultimo esempio è semplicemente un problema. Qui viene sintetizzato un simbolo di parametro distinto per ogni funzione di accesso, il cui percorso è l'intera sintassi della funzione di accesso. Viene simulata l'assegnazione del valore predefinito in ogni funzione di accesso e viene visualizzato un avviso sul parametro, che consente di ottenere avvisi duplicati che non mostrano effettivamente dove si trova il problema.

## <a name="suggested-change-to-declaration-analysis"></a>Modifica consigliata per l'analisi delle dichiarazioni

**Non è consigliabile aggiornare lo stato iniziale del parametro nell'analisi di flusso in base al valore predefinito.** Introduce complessità anomale e avvisi mancanti per l'override, le conversioni dei delegati e così via, che non vale la pena tenere in considerazione e che potrebbero causare confusione nell'utente se sono stati configurati. Rivisitando l'esempio di override precedente:

```cs
public class Base<T>
{
    public virtual void M(T t = default) { } // let's start warning here
}

public class Override : Base<string>
{
    public override void M(string s)
    {
        s.ToString(); // let's not warn here
    }
}
```
Come un utente, probabilmente si noterà un avviso su `s.ToString()` confuso e inutile, ovvero l'elemento che si interrompe qui è l'incompatibilità del tipo e del valore predefinito in `T t = default` e questo è il punto in cui deve essere eseguita la correzione dell'utente.

**Al contrario, è necessario applicare che il valore predefinito è compatibile con il parametro in tutti gli scenari, inclusi i generics non vincolati.** Sono certo che questo è il modo in cui deve essere eseguita in `/langversion:9` Visual studio 16,9. Credo anche che questa operazione venga eseguita in `/langversion:8` base all'ombrello "correzione di bug". `[AllowNull]` può essere applicato a parametri generici non vincolati per consentire `default` come valore predefinito, quindi gli utenti di C# 8 non vengono bloccati. Si può essere certi che non sia in grado di eseguire questa operazione in `/langversion:8` a seconda dell'effetto.

Per quanto riguarda la strategia di implementazione, è sufficiente eseguire questa operazione in SourceComplexParameterSymbol allo stesso tempo in cui viene associato il valore predefinito del parametro. È possibile garantire una quantità sufficiente di coerenza, nonché una gestione ragionevole dell'eliminazione, probabilmente creando un NullableWalker ed eseguendo una "mini-analisi" dell'assegnazione del valore predefinito il cui stato finale viene ignorato.

