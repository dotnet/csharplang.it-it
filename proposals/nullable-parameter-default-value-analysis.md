---
ms.openlocfilehash: bb8c3bd0647940240310e4825d902bdd0f3c9097
ms.sourcegitcommit: 29b0f41953ad7b0d4052715e5b53f933cc42e5ee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338432"
---
# <a name="nullable-parameter-default-value-analysis"></a><span data-ttu-id="6a947-101">Analisi del valore predefinito del parametro Nullable</span><span class="sxs-lookup"><span data-stu-id="6a947-101">Nullable Parameter Default Value Analysis</span></span>

## <a name="analysis-of-declarations"></a><span data-ttu-id="6a947-102">Analisi delle dichiarazioni</span><span class="sxs-lookup"><span data-stu-id="6a947-102">Analysis of declarations</span></span>

<span data-ttu-id="6a947-103">In una dichiarazione di metodo è opportuno che il compilatore fornisca avvisi per i valori predefiniti dei parametri che non sono compatibili con il tipo del parametro.</span><span class="sxs-lookup"><span data-stu-id="6a947-103">In a method declaration it's desirable for the compiler to give warnings for parameter default values which are incompatible with the parameter's type.</span></span>

```cs
void M(string s = null) // warning CS8600: Converting null literal or possible null value to non-nullable type.
{
}
```

<span data-ttu-id="6a947-104">Tuttavia, i generics non vincolati presentano un problema per cui un valore errato può entrare, ma non viene visualizzato un avviso per motivi di compatibilità.</span><span class="sxs-lookup"><span data-stu-id="6a947-104">However, unconstrained generics present a problem where a bad value can go in but we don't warn about it for compat reasons.</span></span> <span data-ttu-id="6a947-105">È stata quindi adottata una strategia per simulare un'assegnazione del valore predefinito al parametro nel corpo del metodo, quindi eseguire il join nello stato risultante, fornendo gli avvisi desiderati nella firma del metodo, nonché lo stato iniziale Nullable desiderato per il parametro.</span><span class="sxs-lookup"><span data-stu-id="6a947-105">Therefore we adopted a strategy of simulating a assignment of the default value to the parameter in the method body, then joining in the resulting state, giving us the desired warnings in the method signature as well as the desired initial nullable state for the parameter.</span></span>

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

<span data-ttu-id="6a947-106">È difficile aggiornare correttamente lo stato iniziale del parametro in tutti gli scenari.</span><span class="sxs-lookup"><span data-stu-id="6a947-106">It's difficult to update the parameter initial state appropriately in all scenarios.</span></span> <span data-ttu-id="6a947-107">Di seguito sono riportati alcuni scenari in cui viene affrontato il metodo:</span><span class="sxs-lookup"><span data-stu-id="6a947-107">Here are some scenarios where the approach falls over:</span></span>

### <a name="overriding-methods-with-optional-parameters"></a>[<span data-ttu-id="6a947-108">Override di metodi con parametri facoltativi</span><span class="sxs-lookup"><span data-stu-id="6a947-108">Overriding methods with optional parameters</span></span>](https://github.com/dotnet/roslyn/issues/48848)
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
<span data-ttu-id="6a947-109">Nell'esempio precedente è possibile chiamare il metodo `Base<string>.M()` e inviare a `Override.M()` .</span><span class="sxs-lookup"><span data-stu-id="6a947-109">In the above sample we may call the method `Base<string>.M()` and dispatch to `Override.M()`.</span></span> <span data-ttu-id="6a947-110">È necessario tenere conto della possibilità che il chiamante venga fornito in modo implicito `null` come argomento per `s` tramite la base, ma attualmente non lo è.</span><span class="sxs-lookup"><span data-stu-id="6a947-110">We need to account for the possibility that the caller implicitly provided `null` as an argument for `s` via the base, but currently we do not do so.</span></span>

---

### <a name="lambda-conversion-to-delegates-which-have-optional-parameters"></a>[<span data-ttu-id="6a947-111">Conversione lambda in delegati che dispongono di parametri facoltativi</span><span class="sxs-lookup"><span data-stu-id="6a947-111">Lambda conversion to delegates which have optional parameters</span></span>](https://github.com/dotnet/roslyn/issues/48844)
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
<span data-ttu-id="6a947-112">Nell'esempio precedente si prevede che un'espressione lambda convertita nel tipo `Del<string>` avrà uno `MaybeNull` stato iniziale per il parametro a causa del valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="6a947-112">In the above sample we expect that a lambda converted to the type `Del<string>` will have a `MaybeNull` initial state for its parameter because of the default value.</span></span> <span data-ttu-id="6a947-113">Attualmente questo caso non viene gestito correttamente.</span><span class="sxs-lookup"><span data-stu-id="6a947-113">Currently we don't handle this case properly.</span></span>

---

### <a name="abstract-methods-and-delegate-declarations-which-have-optional-parameters"></a>[<span data-ttu-id="6a947-114">Metodi astratti e dichiarazioni di delegati che dispongono di parametri facoltativi</span><span class="sxs-lookup"><span data-stu-id="6a947-114">Abstract methods and delegate declarations which have optional parameters</span></span>](https://github.com/dotnet/roslyn/issues/48847)
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
<span data-ttu-id="6a947-115">Nell'esempio precedente, si desiderano avvisi su questi parametri che non sono direttamente associati ad alcuna implementazione del metodo.</span><span class="sxs-lookup"><span data-stu-id="6a947-115">In the above sample, we want warnings on these parameters which aren't directly associated with any method implementation.</span></span> <span data-ttu-id="6a947-116">Tuttavia, dal momento che gli elenchi di parametri non hanno metodi con i corpi da analizzare, non viene mai raggiunto il metodo EnterParameters in NullableWalker che simula le assegnazioni e genera gli avvisi.</span><span class="sxs-lookup"><span data-stu-id="6a947-116">However, since these parameter lists don't have any methods with bodies that we want to flow analyze, we never hit the EnterParameters method in NullableWalker which simulates the assignments and produces the warnings.</span></span>

---

### <a name="indexers-with-get-and-set-accessors"></a><span data-ttu-id="6a947-117">Indicizzatori con funzioni di accesso get e set</span><span class="sxs-lookup"><span data-stu-id="6a947-117">Indexers with get and set accessors</span></span>
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
<span data-ttu-id="6a947-118">Questo ultimo esempio è semplicemente un problema.</span><span class="sxs-lookup"><span data-stu-id="6a947-118">This last sample is just an annoyance.</span></span> <span data-ttu-id="6a947-119">Qui viene sintetizzato un simbolo di parametro distinto per ogni funzione di accesso, il cui percorso è l'intera sintassi della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="6a947-119">Here we synthesize a distinct parameter symbol for each accessor, whose location is the entire accessor syntax.</span></span> <span data-ttu-id="6a947-120">Viene simulata l'assegnazione del valore predefinito in ogni funzione di accesso e viene visualizzato un avviso sul parametro, che consente di ottenere avvisi duplicati che non mostrano effettivamente dove si trova il problema.</span><span class="sxs-lookup"><span data-stu-id="6a947-120">We simulate the default value assignment in each accessor and give a warning on the parameter, which ends up giving duplicate warnings that don't really show where the problem is.</span></span>

## <a name="suggested-change-to-declaration-analysis"></a><span data-ttu-id="6a947-121">Modifica consigliata per l'analisi delle dichiarazioni</span><span class="sxs-lookup"><span data-stu-id="6a947-121">Suggested change to declaration analysis</span></span>

<span data-ttu-id="6a947-122">**Non è consigliabile aggiornare lo stato iniziale del parametro nell'analisi di flusso in base al valore predefinito.**</span><span class="sxs-lookup"><span data-stu-id="6a947-122">**We shouldn't update the parameter's initial state in flow analysis based on the default value.**</span></span> <span data-ttu-id="6a947-123">Introduce complessità anomale e avvisi mancanti per l'override, le conversioni dei delegati e così via, che non vale la pena tenere in considerazione e che potrebbero causare confusione nell'utente se sono stati configurati.</span><span class="sxs-lookup"><span data-stu-id="6a947-123">It introduces strange complexity and missing warnings around overriding, delegate conversions, etc. that is not worth accounting for, and would cause user confusion if we did account for them.</span></span> <span data-ttu-id="6a947-124">Rivisitando l'esempio di override precedente:</span><span class="sxs-lookup"><span data-stu-id="6a947-124">Revisiting the overriding sample from above:</span></span>

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
<span data-ttu-id="6a947-125">Come un utente, probabilmente si noterà un avviso su `s.ToString()` confuso e inutile, ovvero l'elemento che si interrompe qui è l'incompatibilità del tipo e del valore predefinito in `T t = default` e questo è il punto in cui deve essere eseguita la correzione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6a947-125">As a user you'd probably find a warning on `s.ToString()` confusing and useless--the thing that's broken here is the incompatibility of the type and default value in `T t = default`, and that's where user's fix needs to go.</span></span>

<span data-ttu-id="6a947-126">**Al contrario, è necessario applicare che il valore predefinito è compatibile con il parametro in tutti gli scenari, inclusi i generics non vincolati.**</span><span class="sxs-lookup"><span data-stu-id="6a947-126">**Instead, we should enforce that the default value is compatible with the parameter in all scenarios, including unconstrained generics.**</span></span> <span data-ttu-id="6a947-127">Sono certo che questo è il modo in cui deve essere eseguita in `/langversion:9` Visual studio 16,9.</span><span class="sxs-lookup"><span data-stu-id="6a947-127">I am certain that this is how we should do it in `/langversion:9` in VS 16.9.</span></span> <span data-ttu-id="6a947-128">Credo anche che questa operazione venga eseguita in `/langversion:8` base all'ombrello "correzione di bug".</span><span class="sxs-lookup"><span data-stu-id="6a947-128">I also believe that we should do this in `/langversion:8` under the "bug fix" umbrella.</span></span> <span data-ttu-id="6a947-129">`[AllowNull]` può essere applicato a parametri generici non vincolati per consentire `default` come valore predefinito, quindi gli utenti di C# 8 non vengono bloccati.</span><span class="sxs-lookup"><span data-stu-id="6a947-129">`[AllowNull]` can be applied to unconstrained generic parameters to allow `default` as a default value, so C# 8 users are not blocked.</span></span> <span data-ttu-id="6a947-130">Si può essere certi che non sia in grado di eseguire questa operazione in `/langversion:8` a seconda dell'effetto.</span><span class="sxs-lookup"><span data-stu-id="6a947-130">I could be convinced otherwise about doing it in `/langversion:8` depending on the impact.</span></span>

<span data-ttu-id="6a947-131">Per quanto riguarda la strategia di implementazione, è sufficiente eseguire questa operazione in SourceComplexParameterSymbol allo stesso tempo in cui viene associato il valore predefinito del parametro.</span><span class="sxs-lookup"><span data-stu-id="6a947-131">As far as implementation strategy: we should just do this in SourceComplexParameterSymbol at the same time we bind the parameter's default value.</span></span> <span data-ttu-id="6a947-132">È possibile garantire una quantità sufficiente di coerenza, nonché una gestione ragionevole dell'eliminazione, probabilmente creando un NullableWalker ed eseguendo una "mini-analisi" dell'assegnazione del valore predefinito il cui stato finale viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="6a947-132">We can ensure sufficient amount of consistency, as well as reasonable handling of suppression, perhaps by creating a NullableWalker and doing a "mini-analysis" of the assignment of the default value whose final state is discarded.</span></span>

