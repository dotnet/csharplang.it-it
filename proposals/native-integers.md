---
ms.openlocfilehash: 42839a8c233468dd0b5ec6dad436dc71f056a6d9
ms.sourcegitcommit: d414836632ba2730545e0b058373a94696bba5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81447818"
---
# <a name="native-sized-integers"></a>Numeri interi di dimensioni native

## <a name="summary"></a>Summary
[summary]: #summary

Supporto del linguaggio per tipi integer con e senza segno di dimensioni native.

La motivazione è per gli scenari di interoperabilità e per le librerie di basso livello.

## <a name="design"></a>Progettazione
[design]: #design

Gli `nint` identificatori `nuint` e sono nuove parole chiave contestuali che rappresentano tipi integer con segno e senza segno nativi.
Gli identificatori vengono trattati come parole chiave solo quando la ricerca del nome non trova un risultato fattibile in tale percorso del programma.
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

I `nint` tipi `nuint` e sono rappresentati dai tipi `System.IntPtr` sottostanti e `System.UIntPtr` con il compilatore che visualizza conversioni e operazioni aggiuntive per tali tipi come int nativi.

### <a name="constants"></a>Costanti

Le espressioni costanti `nint` possono `nuint`essere di tipo o .
Non esiste una sintassi diretta per i valori letterali int nativi. È invece possibile utilizzare cast impliciti o `const nint i = (nint)42;`espliciti di altri valori costanti integrali: .

`nint`le costanti sono nell'intervallo [ `int.MinValue`, `int.MaxValue` ].

`nuint`le costanti sono nell'intervallo [ `uint.MinValue`, `uint.MaxValue` ].

Non sono `MinValue` `MaxValue` presenti `nint` campi `nuint` o perché, ad esempio, `nuint.MinValue`tali valori non possono essere emessi come costanti.

La riduzione costante è supportata `+` `-`per `~` tutti gli `+`operatori `-` `*`unari , , , e gli operatori binari , , , , `/`, `%`, `==`, `!=`, `<` `<=`, `>`, `>=`, , , `&`, , `|`, , `^`, , `<<`, , `>>` , .
Le operazioni di piegatura costante vengono valutate con `Int32` e `UInt32` operandi anziché int nativi per un comportamento coerente indipendentemente dalla piattaforma del compilatore.
Se l'operazione restituisce un valore costante in 32 bit, la riduzione costante viene eseguita in fase di compilazione.
In caso contrario, l'operazione viene eseguita in fase di esecuzione e non viene considerata una costante.

### <a name="conversions"></a>Conversioni
Esiste una conversione `nint` `IntPtr`di identità tra e , e tra `nuint` e `UIntPtr`.
Esiste una conversione di identità tra tipi composti che differiscono solo `Nullable<>`in formato nativo e tipi sottostanti: matrici, tipi costruiti e tuple.

Le tabelle seguenti riguardano le conversioni tra tipi speciali.
(Il linguaggio il per ogni `unchecked` `checked` conversione include le varianti per e i contesti se diverso.)

| Operando | Destinazione | Conversione | IL |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | Conversione unboxing | `unbox` |
| `void*` | `nint` | PointerToVoid | `conv.i` |
| `sbyte` | `nint` | Numerico implicito | `conv.i` |
| `byte` | `nint` | Numerico implicito | `conv.u` |
| `short` | `nint` | Numerico implicito | `conv.i` |
| `ushort` | `nint` | Numerico implicito | `conv.u` |
| `int` | `nint` | Numerico implicito | `conv.i` |
| `uint` | `nint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | Numerico implicito | `conv.i` |
| `float` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | ExplicitNumeric | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | Identità | |
| `UIntPtr` | `nint` | nessuno | |
| `object` | `nuint` | Conversione unboxing | `unbox` |
| `void*` | `nuint` | PointerToVoid | `conv.u` |
| `sbyte` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | Numerico implicito | `conv.u` |
| `short` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | Numerico implicito | `conv.u` |
| `int` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | Numerico implicito | `conv.u` |
| `long` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | Numerico implicito | `conv.u` |
| `float` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | ExplicitNumeric | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | nessuno | |
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
| `nint` | `long` | Numerico implicito | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | ExplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | Numerico implicito | `conv.r4` |
| `nint` | `double` | Numerico implicito | `conv.r8` |
| `nint` | `decimal` | Numerico implicito | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | Identità | |
| `nint` | `UIntPtr` | nessuno | |
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
| `nuint` | `ulong` | Numerico implicito | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | Numerico implicito | `conv.r.un conv.r4` |
| `nuint` | `double` | Numerico implicito | `conv.r.un conv.r8` |
| `nuint` | `decimal` | Numerico implicito | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | nessuno | |
| `nuint` | `UIntPtr` | Identità | |

Conversione `A` `Nullable<B>` da a è:
- una conversione implicita nullable se è `A` presente `B`una conversione di identità o una conversione implicita da a ;
- una conversione esplicita nullable se `A` `B`è presente una conversione esplicita da a ;
- in caso contrario, non è valido.

Conversione `Nullable<A>` `B` da a è:
- una conversione esplicita a nullable se è presente `A` `B`una conversione di identità o una conversione numerica implicita o esplicita da a ;
- in caso contrario, non è valido.

Conversione `Nullable<A>` `Nullable<B>` da a è:
- una conversione di identità `A` se `B`c'è una conversione di identità da a ;
- una conversione esplicita a valore null se `A` `B`è presente una conversione numerica implicita o esplicita da a ;
- in caso contrario, non è valido.

### <a name="operators"></a>Operatori

Gli operatori predefiniti sono i seguenti.
Questi operatori vengono considerati durante la risoluzione dell'overload in base alle regole normali per le conversioni implicite _se almeno uno degli operandi è di tipo `nint` o `nuint` _.

(Il linguaggio degli altri per `unchecked` `checked` ogni operatore include le varianti per e i contesti se diverso.)

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

Per alcuni operatori binari, gli operatori IL supportano tipi di operando aggiuntivi (vedere la tabella di tipo [Operando ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5).
Tuttavia, il set di tipi di operando supportati da C, è limitato per semplicità e coerenza con gli operatori esistenti nel linguaggio.

Le versioni eliminate degli operatori, in `nint?` cui `nuint?`gli argomenti e i tipi restituiti sono e , sono supportati.

Le operazioni `x op= y` `x` di `y` assegnazione composta in cui o sono int nativi seguono le stesse regole di altri tipi primitivi con operatori predefiniti.
In particolare l'espressione è associata `x = (T)(x op y)` come where `T` è il tipo di `x` e dove `x` viene valutato una sola volta.

Gli operatori di spostamento devono mascherare il numero di `sizeof(nint)` bit da spostare - `sizeof(nint)` a 5 bit se è 4 e a 6 bit se è 8.
(vedere [gli operatori di spostamento](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators) nelle specifiche di C.

### <a name="dynamic"></a>Dinamico

Le conversioni e gli operatori vengono sintetizzati dal `IntPtr` `UIntPtr` compilatore e non fanno parte dei tipi e sottostanti.
Di conseguenza, tali conversioni e operatori non `dynamic`sono _disponibili_ dal gestore di associazione di runtime per . 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a>Membri dei tipi

L'unico `nint` costruttore per o `nuint` è il costruttore senza parametri.

I seguenti `System.IntPtr` membri `System.UIntPtr` di e `nint` `nuint` _sono esplicitamente esclusi_ da o :
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

I restanti `System.IntPtr` `System.UIntPtr` membri di e `nint` `nuint` _sono implicitamente inclusi_ in e . Per .NET Framework 4.7.2:
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

Le interfacce `System.IntPtr` `System.UIntPtr` implementate da `nint` e `nuint`sono incluse in _modo implicito_ in e , con occorrenze dei tipi sottostanti sostituite dai tipi integer nativi corrispondenti.
Ad `IntPtr` esempio, `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`se `nint` `ISerializable, IEquatable<nint>, IComparable<nint>`implementa , implementa .

### <a name="overriding-hiding-and-implementing"></a>Sostituzione, occultamento e implementazione

`nint`e `System.IntPtr`, `nuint` `System.UIntPtr`e , sono considerati equivalenti per l'override, l'occultamento e l'implementazione.

Gli overload non `nint` `System.IntPtr`possono `nuint` essere `System.UIntPtr`diversi solo da e , e e , .
Gli override e le `nint` implementazioni possono differire solo per e `System.IntPtr`, o `nuint` e `System.UIntPtr`, .
I metodi nascondono `nint` `System.IntPtr`altri `nuint` metodi `System.UIntPtr`che differiscono da e , o e , da soli.

### <a name="miscellaneous"></a>Varie

`nint`e `nuint` le espressioni utilizzate come indici di matrice vengono generate senza conversione.
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

`nint`e `nuint` può essere `enum` utilizzato come tipo di base.
```C#
enum E : nint // ok
{
}
```

Le letture e le `nint` `nuint`scritture `enum` sono `nint` atomiche per i tipi , e con il tipo di base o `nuint`.

I campi `volatile` possono `nint` essere `nuint`contrassegnati per i tipi e .
[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 non `enum` include `System.IntPtr` con `System.UIntPtr` il tipo di base o comunque.

`default(nint)`e `new nint()` sono `(nint)0`equivalenti a .

`typeof(nint)` è `typeof(IntPtr)`.

`sizeof(nint)`è supportato ma richiede la compilazione in `sizeof(IntPtr)`un contesto unsafe (come fa ).
Il valore non è una costante in fase di compilazione.
`sizeof(nint)`viene implementato `sizeof(IntPtr)` come `IntPtr.Size`anziché .

Diagnostica del compilatore `nint` `nuint` per `nint` `nuint` i riferimenti ai tipi che coinvolgono o report o anziché `IntPtr` o `UIntPtr`.

### <a name="metadata"></a>Metadati

`nint`e `nuint` sono rappresentati `System.IntPtr` `System.UIntPtr`nei metadati come e .

Riferimenti ai `nint` `nuint` tipi che includono `System.Runtime.CompilerServices.NativeIntegerAttribute` o vengono generati con a per indicare quali parti del riferimento al tipo sono native ints.

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
        public IList<bool> TransformFlags { get; }
    }
}
```

La codifica utilizza l'approccio `DynamicAttribute`utilizzato `DynamicAttribute` per codificare , sebbene `dynamic` ovviamente stia codificando i tipi all'interno del riferimento al tipo piuttosto che i tipi nativi int.
Se la codifica restituisce `false` una `NativeIntegerAttribute` matrice di valori, non è necessario alcun valore.
Il costruttore senza parametri `NativeIntegerAttribute` genera `true` una codifica con un singolo valore.

```C#
nuint A;                    // [NativeInteger] UIntPtr A
(Stream, nint) B;           // [NativeInteger(new[] { false, false, true })] ValueType<Stream, IntPtr> B
```

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Un'alternativa all'approccio "type erasure" di `System.NativeInt` `System.NativeUInt`cui sopra è quello di introdurre nuovi tipi: e .
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
Tipi distinti consentono l'overload distinto da `IntPtr` e `ToString()`consentirebbe l'analisi distinta e .
Ma ci sarebbe più lavoro per CLR per gestire questi tipi in modo efficiente che vanifica lo scopo principale della funzionalità - efficienza.
E l'interoperabilità con il `IntPtr` codice int nativo esistente che utilizza sarebbe più difficile. 

Un'altra alternativa consiste nell'aggiungere `IntPtr` più nativo int supporto per nel framework, ma senza alcun supporto del compilatore specifico.
Eventuali nuove conversioni e operazioni aritmetiche verrebbero supportate automaticamente dal compilatore.
Ma il linguaggio non fornirebbe `checked` parole chiave, costanti o operazioni.

## <a name="design-meetings"></a>Riunioni di progettazione

https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md
