---
ms.openlocfilehash: f16a182cb205c889c15eae2d33bfa342e9579b10
ms.sourcegitcommit: c3df20406f43fcd460cfedd1cd61b6cc47d27250
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554630"
---
# <a name="native-sized-integers"></a>Integer con dimensione nativa

## <a name="summary"></a>Riepilogo
[summary]: #summary

Supporto delle lingue per tipi firmati e Unsigned Integer di dimensioni native.

La motivazione è per gli scenari di interoperabilità e per le librerie di basso livello.

## <a name="design"></a>Progettazione
[design]: #design

Gli identificatori `nint` e `nuint` sono nuove parole chiave contestuali che rappresentano tipi nativi firmati e unsigned integer.
Gli identificatori vengono trattati come parole chiave solo quando la ricerca del nome non trova un risultato valido in tale posizione del programma.
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

I tipi `nint` e `nuint` sono rappresentati dai tipi sottostanti `System.IntPtr` e `System.UIntPtr` con il compilatore che presenta le conversioni e le operazioni aggiuntive per tali tipi come int native.

### <a name="constants"></a>Costanti

Le espressioni costanti possono essere di tipo `nint` o `nuint` .
Non esiste alcuna sintassi diretta per i valori letterali int nativi. È invece possibile usare cast impliciti o espliciti di altri valori di costanti integrali: `const nint i = (nint)42;` .

`nint` le costanti sono comprese nell'intervallo [ `int.MinValue` , `int.MaxValue` ].

`nuint` le costanti sono comprese nell'intervallo [ `uint.MinValue` , `uint.MaxValue` ].

Non sono presenti `MinValue` `MaxValue` campi o in `nint` o `nuint` perché, oltre a `nuint.MinValue` , tali valori non possono essere emessi come costanti.

La riduzione delle costanti è supportata per tutti gli operatori unari { `+` , `-` , `~` } e gli operatori binari { `+` , `-` , `*` , `/` , `%` , `==` , `!=` , `<` , `<=` , `>` , `>=` , `&` , `|` , `^` ,, `<<` `>>` }.
Le operazioni di riduzione costanti vengono valutate con `Int32` `UInt32` operandi e anziché come int nativi per un comportamento coerente indipendentemente dalla piattaforma del compilatore.
Se l'operazione restituisce un valore costante in 32 bit, la riduzione continua viene eseguita in fase di compilazione.
In caso contrario, l'operazione viene eseguita in fase di esecuzione e non è considerata una costante.

### <a name="conversions"></a>Conversioni
Esiste una conversione di identità tra `nint` e e `IntPtr` tra `nuint` e `UIntPtr` .
Esiste una conversione di identità tra tipi composti che differiscono solo per i tipi int e i tipi sottostanti nativi: matrici, `Nullable<>` , tipi costruiti e Tuple.

Le tabelle seguenti coprono le conversioni tra tipi speciali.
Il linguaggio il per ogni conversione include le varianti per `unchecked` e i `checked` contesti se diversi.

| Operando | Destinazione | Conversione | IL |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | Unboxing | `unbox` |
| `void*` | `nint` | PointerToVoid | `conv.i` |
| `sbyte` | `nint` | ImplicitNumeric | `conv.i` |
| `byte` | `nint` | ImplicitNumeric | `conv.u` |
| `short` | `nint` | ImplicitNumeric | `conv.i` |
| `ushort` | `nint` | ImplicitNumeric | `conv.u` |
| `int` | `nint` | ImplicitNumeric | `conv.i` |
| `uint` | `nint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | ImplicitNumeric | `conv.i` |
| `float` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | ExplicitNumeric | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | Identità | |
| `UIntPtr` | `nint` | Nessuno | |
| `object` | `nuint` | Unboxing | `unbox` |
| `void*` | `nuint` | PointerToVoid | `conv.u` |
| `sbyte` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | ImplicitNumeric | `conv.u` |
| `short` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | ImplicitNumeric | `conv.u` |
| `int` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | ImplicitNumeric | `conv.u` |
| `long` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | ImplicitNumeric | `conv.u` |
| `float` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | ExplicitNumeric | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | Nessuno | |
| `UIntPtr` | `nuint` | Identità | |

| Operando | Destinazione | Conversione | IL |
|:---:|:---:|:---:|:---:|
| `nint` | `object` | Boxing | `box` |
| `nint` | `void*` | PointerToVoid | `conv.i` |
| `nint` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `nint` | `sbyte` | ExplicitNumeric | `conv.i1` / `conv.ovf.i1` |
| `nint` | `byte` | ExplicitNumeric | `conv.u1` / `conv.ovf.u1` |
| `nint` | `short` | ExplicitNumeric | `conv.i2` / `conv.ovf.i2` |
| `nint` | `ushort` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nint` | `int` | ExplicitNumeric | `conv.i4` / `conv.ovf.i4` |
| `nint` | `uint` | ExplicitNumeric | `conv.u4` / `conv.ovf.u4` |
| `nint` | `long` | ImplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | ExplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | ImplicitNumeric | `conv.r4` |
| `nint` | `double` | ImplicitNumeric | `conv.r8` |
| `nint` | `decimal` | ImplicitNumeric | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | Identità | |
| `nint` | `UIntPtr` | Nessuno | |
| `nuint` | `object` | Boxing | `box` |
| `nuint` | `void*` | PointerToVoid | `conv.u` |
| `nuint` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `nuint` | `sbyte` | ExplicitNumeric | `conv.i1` / `conv.ovf.i1` |
| `nuint` | `byte` | ExplicitNumeric | `conv.u1` / `conv.ovf.u1` |
| `nuint` | `short` | ExplicitNumeric | `conv.i2` / `conv.ovf.i2` |
| `nuint` | `ushort` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nuint` | `int` | ExplicitNumeric | `conv.i4` / `conv.ovf.i4` |
| `nuint` | `uint` | ExplicitNumeric | `conv.u4` / `conv.ovf.u4` |
| `nuint` | `long` | ExplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nuint` | `ulong` | ImplicitNumeric | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | ImplicitNumeric | `conv.r.un conv.r4` |
| `nuint` | `double` | ImplicitNumeric | `conv.r.un conv.r8` |
| `nuint` | `decimal` | ImplicitNumeric | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | Nessuno | |
| `nuint` | `UIntPtr` | Identità | |

La conversione da `A` a `Nullable<B>` è:
- conversione implicita nullable in presenza di una conversione di identità o di conversione implicita da `A` a `B` ;
- conversione esplicita di valori null in caso di conversione esplicita da `A` a `B` ;
- in caso contrario, non valido.

La conversione da `Nullable<A>` a `B` è:
- conversione esplicita di valori null in caso di conversione di identità o conversione numerica implicita o esplicita da `A` a `B` ;
- in caso contrario, non valido.

La conversione da `Nullable<A>` a `Nullable<B>` è:
- una conversione di identità se è presente una conversione di identità da `A` a `B` ;
- conversione esplicita nullable se è presente una conversione numerica implicita o esplicita da `A` a `B` ;
- in caso contrario, non valido.

### <a name="operators"></a>Operatori

Gli operatori predefiniti sono i seguenti.
Questi operatori vengono considerati durante la risoluzione dell'overload in base alle regole normali per le conversioni implicite _se almeno uno degli operandi è di `nint` tipo `nuint` o _.

Il linguaggio il per ogni operatore include le varianti per `unchecked` i `checked` contesti e se diverso.

| Unaria | Firma operatore | IL |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint value)` | `nop` |
| `+` | `nuint operator +(nuint value)` | `nop` |
| `-` | `nint operator -(nint value)` | `neg` |
| `~` | `nint operator ~(nint value)` | `not` |
| `~` | `nuint operator ~(nuint value)` | `not` |

| Binary | Firma operatore | IL |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint left, nint right)` | `add` / `add.ovf` |
| `+` | `nuint operator +(nuint left, nuint right)` | `add` / `add.ovf.un` |
| `-` | `nint operator -(nint left, nint right)` | `sub` / `sub.ovf` |
| `-` | `nuint operator -(nuint left, nuint right)` | `sub` / `sub.ovf.un` |
| `*` | `nint operator *(nint left, nint right)` | `mul` / `mul.ovf` |
| `*` | `nuint operator *(nuint left, nuint right)` | `mul` / `mul.ovf.un` |
| `/` | `nint operator /(nint left, nint right)` | `div` |
| `/` | `nuint operator /(nuint left, nuint right)` | `div.un` |
| `%` | `nint operator %(nint left, nint right)` | `rem` |
| `%` | `nuint operator %(nuint left, nuint right)` | `rem.un` |
| `==` | `bool operator ==(nint left, nint right)` | `beq` / `ceq` |
| `==` | `bool operator ==(nuint left, nuint right)` | `beq` / `ceq` |
| `!=` | `bool operator !=(nint left, nint right)` | `bne` |
| `!=` | `bool operator !=(nuint left, nuint right)` | `bne` |
| `<` | `bool operator <(nint left, nint right)` | `blt` / `clt` |
| `<` | `bool operator <(nuint left, nuint right)` | `blt.un` / `clt.un` |
| `<=` | `bool operator <=(nint left, nint right)` | `ble` |
| `<=` | `bool operator <=(nuint left, nuint right)` | `ble.un` |
| `>` | `bool operator >(nint left, nint right)` | `bgt` / `cgt` |
| `>` | `bool operator >(nuint left, nuint right)` | `bgt.un` / `cgt.un` |
| `>=` | `bool operator >=(nint left, nint right)` | `bge` |
| `>=` | `bool operator >=(nuint left, nuint right)` | `bge.un` |
| `&` | `nint operator &(nint left, nint right)` | `and` |
| `&` | `nuint operator &(nuint left, nuint right)` | `and` |
| <code>&#124;</code> | <code>nint operator &#124;(nint left, nint right)</code> | `or` |
| <code>&#124;</code> | <code>nuint operator &#124;(nuint left, nuint right)</code> | `or` |
| `^` | `nint operator ^(nint left, nint right)` | `xor` |
| `^` | `nuint operator ^(nuint left, nuint right)` | `xor` |
| `<<` | `nint operator <<(nint left, int right)` | `shl` |
| `<<` | `nuint operator <<(nuint left, int right)` | `shl` |
| `>>` | `nint operator >>(nint left, int right)` | `shr` |
| `>>` | `nuint operator >>(nuint left, int right)` | `shr.un` |

Per alcuni operatori binari, gli operatori IL supportano i tipi di operandi aggiuntivi (vedere la tabella del tipo di operando [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III. 1.5).
Tuttavia, il set di tipi di operando supportati da C# è limitato per semplicità e per coerenza con gli operatori esistenti nel linguaggio.

Sono supportate le versioni sollevate degli operatori, in cui gli argomenti e i tipi restituiti sono `nint?` e `nuint?` .

Le operazioni di assegnazione composta in `x op= y` cui `x` o `y` sono int nativi seguono le stesse regole di altri tipi primitivi con operatori predefiniti.
In particolare, l'espressione viene associata come `x = (T)(x op y)` dove `T` è il tipo di `x` e dove `x` viene valutato una sola volta.

Gli operatori shift devono mascherare il numero di bit per passare a 5 bit se `sizeof(nint)` è 4 e a 6 bit se `sizeof(nint)` è 8.
(vedere [operatori shift](../../spec/expressions.md#shift-operators) in C# spec).

Il compilatore C# 9 segnalerà gli errori di associazione agli operatori Integer nativi predefiniti durante la compilazione con una versione del linguaggio precedente, ma consentirà l'utilizzo di conversioni predefinite da e verso numeri interi nativi.

`csc -langversion:9 -t:library A.cs`
```C#
public class A
{
    public static nint F;
}
```

`csc -langversion:8 -r:A.dll B.cs`
```C#
class B : A
{
    static void Main()
    {
        F = F + 1; // error: nint operator+ not available with -langversion:8
        F = (System.IntPtr)F + 1; // ok
    }
}
```

### <a name="dynamic"></a>Dinamico

Le conversioni e gli operatori vengono sintetizzati dal compilatore e non fanno parte dei `IntPtr` tipi e sottostanti `UIntPtr` .
Di conseguenza, le conversioni e gli operatori _non sono disponibili_ dal gestore di associazione di runtime per `dynamic` . 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a>Membri dei tipi

L'unico costruttore per `nint` o `nuint` è il costruttore senza parametri.

I membri seguenti di `System.IntPtr` e `System.UIntPtr` _sono esclusi in modo esplicito_ da `nint` o `nuint` :
```C#
// constructors
// arithmetic operators
// implicit and explicit conversions
public static readonly IntPtr Zero; // use 0 instead
public static int Size { get; }     // use sizeof() instead
public static IntPtr Add(IntPtr pointer, int offset);
public static IntPtr Subtract(IntPtr pointer, int offset);
public int ToInt32();
public long ToInt64();
public void* ToPointer();
```

I membri rimanenti di `System.IntPtr` e `System.UIntPtr` _sono inclusi in modo implicito_ in `nint` e `nuint` . Per .NET Framework 4.7.2:
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

Le interfacce implementate da `System.IntPtr` e `System.UIntPtr` _sono incluse in modo implicito_ in `nint` e `nuint` , con le occorrenze dei tipi sottostanti sostituiti dai tipi Integer nativi corrispondenti.
Ad esempio `IntPtr` , se implementa `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>` , `nint` implementa `ISerializable, IEquatable<nint>, IComparable<nint>` .

### <a name="overriding-hiding-and-implementing"></a>Override, nascondere e implementare

`nint` e `System.IntPtr` , e `nuint` e `System.UIntPtr` sono considerati equivalenti per l'override, l'occultamento e l'implementazione di.

Gli overload non possono differire da `nint` and `System.IntPtr` , and `nuint` e `System.UIntPtr` , alone.
Le sostituzioni e le implementazioni possono variare `nint` solo per and `System.IntPtr` , or `nuint` e `System.UIntPtr` .
I metodi nascondono altri metodi che differiscono `nint` solo per and `System.IntPtr` , or `nuint` e `System.UIntPtr` .

### <a name="miscellaneous"></a>Varie

`nint` le `nuint` espressioni e utilizzate come indici di matrice vengono emesse senza conversione.
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

`nint` e `nuint` possono essere usati come `enum` tipo di base.
```C#
enum E : nint // ok
{
}
```

Le letture e le Scritture sono atomiche per i tipi `nint` , `nuint` e `enum` con il tipo di base `nint` o `nuint` .

I campi possono essere contrassegnati `volatile` per i tipi `nint` e `nuint` .
[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 non include `enum` tuttavia il tipo di `System.IntPtr` base `System.UIntPtr` .

`default(nint)` e `new nint()` sono equivalenti a `(nint)0` .

`typeof(nint)` è `typeof(IntPtr)`.

`sizeof(nint)` è supportato ma richiede la compilazione in un contesto non sicuro (così com'è `sizeof(IntPtr)` ).
Il valore non è una costante in fase di compilazione.
`sizeof(nint)` viene implementato come `sizeof(IntPtr)` anziché `IntPtr.Size` .

Diagnostica del compilatore per i riferimenti ai tipi che coinvolgono o `nint` `nuint` segnalano o `nint` `nuint` invece di `IntPtr` o `UIntPtr` .

### <a name="metadata"></a>Metadati

`nint` e `nuint` sono rappresentati nei metadati come `System.IntPtr` e `System.UIntPtr` .

I riferimenti ai tipi che includono `nint` o `nuint` vengono emessi con un oggetto `System.Runtime.CompilerServices.NativeIntegerAttribute` per indicare quali parti del riferimento al tipo sono native int.

```C#
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(
        AttributeTargets.Class |
        AttributeTargets.Event |
        AttributeTargets.Field |
        AttributeTargets.GenericParameter |
        AttributeTargets.Parameter |
        AttributeTargets.Property |
        AttributeTargets.ReturnValue,
        AllowMultiple = false,
        Inherited = false)]
    public sealed class NativeIntegerAttribute : Attribute
    {
        public NativeIntegerAttribute()
        {
            TransformFlags = new[] { true };
        }
        public NativeIntegerAttribute(bool[] flags)
        {
            TransformFlags = flags;
        }
        public readonly bool[] TransformFlags;
    }
}
```

La codifica dei riferimenti ai tipi con `NativeIntegerAttribute` è analizzata in [NativeIntegerAttribute.MD](https://github.com/dotnet/roslyn/blob/master/docs/features/NativeIntegerAttribute.md).

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Un'alternativa all'approccio precedente alla cancellazione dei tipi è l'introduzione di nuovi tipi: `System.NativeInt` e `System.NativeUInt` .
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
I tipi distinti consentono l'overload di DISTINCT da `IntPtr` e consentiranno l'analisi DISTINCT e `ToString()` .
Tuttavia, il CLR potrà gestire questi tipi in modo efficiente, evitando allo scopo principale l'efficienza della funzionalità.
E l'interoperabilità con il codice int nativo esistente che usa `IntPtr` sarebbe più difficile. 

Un'altra alternativa consiste nell'aggiungere un supporto nativo int per `IntPtr` nel Framework ma senza alcun supporto del compilatore specifico.
Tutte le nuove conversioni e le operazioni aritmetiche sono supportate automaticamente dal compilatore.
Tuttavia, il linguaggio non fornirebbe parole chiave, costanti o `checked` operazioni.

## <a name="design-meetings"></a>Riunioni di progettazione

https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md
