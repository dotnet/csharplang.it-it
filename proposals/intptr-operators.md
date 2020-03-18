---
ms.openlocfilehash: 340a1dc5a2eac653458d7d29f74551e5fe28b6d5
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484559"
---
# <a name="operators-should-be-exposed-for-systemintptr-and-systemuintptr"></a>Gli operatori devono essere esposti per `System.IntPtr` e `System.UIntPtr`

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Summary
[summary]: #summary

CLR supporta un set di operatori per i tipi di `System.IntPtr` e di `System.UIntPtr` (`native int`). Questi operatori possono essere visualizzati in `III.1.5` della specifica Common Language Infrastructure (`ECMA-335`). Tuttavia, questi operatori non sono supportati da C#.

È necessario fornire supporto per la lingua per il set completo di operatori supportati da `System.IntPtr` e `System.UIntPtr`. Questi operatori sono: `Add`, `Divide`, `Multiply`, `Remainder`, `Subtract`, `Negate`, `Equals`, `Compare`, `And`, `Not`, `Or`, `XOr`, `ShiftLeft``ShiftRight`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Attualmente, gli utenti possono scrivere C# facilmente applicazioni destinate a più piattaforme usando diversi strumenti e Framework, ad esempio: `Xamarin`, `.NET Core`, `Mono`e così via.

Quando si scrive codice multipiattaforma, spesso è necessario scrivere codice di interoperabilità che interagisce con una determinata piattaforma di destinazione in modo specifico. Questo potrebbe includere la scrittura di codice grafico, la chiamata di alcune API di sistema o l'interazione con una libreria nativa esistente.

Questo codice di interoperabilità spesso deve gestire handle, memoria non gestita o anche solo Integer di dimensioni specifiche della piattaforma.

Il runtime fornisce supporto a questo scopo definendo un set di operatori che possono essere usati nei tipi primitivi `native int` (`System.IntPtr`) e `native unsigned int` (`System.UIntPtr`).

C#non ha mai supportato questi operatori, quindi gli utenti devono aggirare il problema. Questo aumenta spesso la complessità del codice e riduce la gestibilità del codice.

Di conseguenza, il linguaggio deve iniziare a supportare questi operatori per contribuire al miglioramento del linguaggio in modo da supportare meglio tali requisiti.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Il set completo di operatori supportati è definito in `III.1.5` della specifica Common Language Infrastructure (`ECMA-335`). La specifica è disponibile qui: [https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf).

* Di seguito è riportato un riepilogo degli operatori per praticità.
* Gli operatori non verificabili definiti dalla specifica dell'interfaccia della riga di comando non sono elencati e non fanno attualmente parte di questa proposta (anche se è opportuno considerare anche questi).
* La specifica di una parola chiave, ad esempio `nint` e `nuint`, né la possibilità di dichiarare i valori letterali per `System.IntPtr` e `System.UIntPtr` (ad esempio 0n) non fa parte di questa proposta (anche se può essere utile considerare anche questi).

### <a name="unary-plus-operator"></a>Operatore più unario

```csharp
System.IntPtr operator +(System.IntPtr)
```

```csharp
System.UIntPtr operator +(System.UIntPtr)
```

### <a name="unary-minus-operator"></a>Operatore meno unario

```csharp
System.IntPtr operator -(System.IntPtr)
```

### <a name="bitwise-complement-operator"></a>Operatore di complemento bit per bit

```csharp
System.IntPtr operator ~(System.IntPtr)
```

```csharp
System.UIntPtr operator ~(System.UIntPtr)
```

### <a name="cast-operators"></a>Operatori di cast

```csharp
explicit operator sbyte(System.IntPtr)               // Truncate
explicit operator short(System.IntPtr)               // Truncate
explicit operator int(System.IntPtr)                 // Truncate
explicit operator long(System.IntPtr)                // Sign Extend

explicit operator byte(System.IntPtr)                // Truncate
explicit operator ushort(System.IntPtr)              // Truncate
explicit operator uint(System.IntPtr)                // Truncate
explicit operator ulong(System.IntPtr)               // Zero Extend

explicit operator System.IntPtr(int)                 // Sign Extend
explicit operator System.IntPtr(long)                // Truncate

explicit operator System.IntPtr(uint)                // Sign Extend
explicit operator System.IntPtr(ulong)               // Truncate

explicit operator System.IntPtr(System.IntPtr)
explicit operator System.IntPtr(System.UIntPtr)
```

```csharp
explicit operator sbyte(System.UIntPtr)               // Truncate
explicit operator short(System.UIntPtr)               // Truncate
explicit operator int(System.UIntPtr)                 // Truncate
explicit operator long(System.UIntPtr)                // Sign Extend

explicit operator byte(System.UIntPtr)                // Truncate
explicit operator ushort(System.UIntPtr)              // Truncate
explicit operator uint(System.UIntPtr)                // Truncate
explicit operator ulong(System.UIntPtr)               // Zero Extend

explicit operator System.UIntPtr(int)                 // Zero Extend
explicit operator System.UIntPtr(long)                // Truncate

explicit operator System.UIntPtr(uint)                // Zero Extend
explicit operator System.UIntPtr(ulong)               // Truncate

explicit operator System.UIntPtr(System.IntPtr)
explicit operator System.UIntPtr(System.UIntPtr)
```

### <a name="multiplication-operator"></a>Operatore di moltiplicazione

```csharp
System.IntPtr operator *(int, System.IntPtr)
System.IntPtr operator *(System.IntPtr, int)
System.IntPtr operator *(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator *(uint, System.UIntPtr)
System.UIntPtr operator *(System.UIntPtr, uint)
System.UIntPtr operator *(System.UIntPtr, System.UIntPtr)
```

### <a name="division-operator"></a>Operatore di divisione

```csharp
System.IntPtr operator /(int, System.IntPtr)
System.IntPtr operator /(System.IntPtr, int)
System.IntPtr operator /(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator /(uint, System.UIntPtr)
System.UIntPtr operator /(System.UIntPtr, uint)
System.UIntPtr operator /(System.UIntPtr, System.UIntPtr)
```

### <a name="remainder-operator"></a>Operatore resto

```csharp
System.IntPtr operator %(int, System.IntPtr)
System.IntPtr operator %(System.IntPtr, int)
System.IntPtr operator %(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator %(uint, System.UIntPtr)
System.UIntPtr operator %(System.UIntPtr, uint)
System.UIntPtr operator %(System.UIntPtr, System.UIntPtr)
```

### <a name="addition-operator"></a>Operatore di addizione

```csharp
System.IntPtr operator +(int, System.IntPtr)
System.IntPtr operator +(System.IntPtr, int)
System.IntPtr operator +(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator +(uint, System.UIntPtr)
System.UIntPtr operator +(System.UIntPtr, uint)
System.UIntPtr operator +(System.UIntPtr, System.UIntPtr)
```

### <a name="subtraction-operator"></a>Operatore di sottrazione

```csharp
System.IntPtr operator -(int, System.IntPtr)
System.IntPtr operator -(System.IntPtr, int)
System.IntPtr operator -(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator -(uint, System.UIntPtr)
System.UIntPtr operator -(System.UIntPtr, uint)
System.UIntPtr operator -(System.UIntPtr, System.UIntPtr)
```

### <a name="shift-operators"></a>Operatori shift

```csharp
System.IntPtr operator <<(System.IntPtr, int)
System.IntPtr operator >>(System.IntPtr, int)
```

```csharp
System.UIntPtr operator <<(System.UIntPtr, int)
System.UIntPtr operator >>(System.UIntPtr, int)
```

### <a name="integer-comparison-operators"></a>Operatori di confronto integer

```csharp
bool operator ==(int, System.IntPtr)
bool operator ==(System.IntPtr, int)
bool operator ==(System.IntPtr, System.IntPtr)

bool operator !=(int, System.IntPtr)
bool operator !=(System.IntPtr, int)
bool operator !=(System.IntPtr, System.IntPtr)

bool operator  <(int, System.IntPtr)
bool operator  <(System.IntPtr, int)
bool operator  <(System.IntPtr, System.IntPtr)

bool operator  >(int, System.IntPtr)
bool operator  >(System.IntPtr, int)
bool operator  >(System.IntPtr, System.IntPtr)

bool operator <=(int, System.IntPtr)
bool operator <=(System.IntPtr, int)
bool operator <=(System.IntPtr, System.IntPtr)

bool operator >=(int, System.IntPtr)
bool operator >=(System.IntPtr, int)
bool operator >=(System.IntPtr, System.IntPtr)
```

```csharp
bool operator ==(uint, System.UIntPtr)
bool operator ==(System.UIntPtr, uint)
bool operator ==(System.UIntPtr, System.UIntPtr)

bool operator !=(uint, System.UIntPtr)
bool operator !=(System.UIntPtr, uint)
bool operator !=(System.UIntPtr, System.UIntPtr)

bool operator  <(uint, System.UIntPtr)
bool operator  <(System.UIntPtr, uint)
bool operator  <(System.UIntPtr, System.UIntPtr)

bool operator  >(uint, System.UIntPtr)
bool operator  >(System.UIntPtr, uint)
bool operator  >(System.UIntPtr, System.UIntPtr)

bool operator <=(uint, System.UIntPtr)
bool operator <=(System.UIntPtr, uint)
bool operator <=(System.UIntPtr, System.UIntPtr)

bool operator >=(uint, System.UIntPtr)
bool operator >=(System.UIntPtr, uint)
bool operator >=(System.UIntPtr, System.UIntPtr)
```

### <a name="integer-logical-operators"></a>Operatori logici integer

```csharp
System.IntPtr operator &(int, System.IntPtr)
System.IntPtr operator &(System.IntPtr, int)
System.IntPtr operator &(System.IntPtr, System.IntPtr)

System.IntPtr operator |(int, System.IntPtr)
System.IntPtr operator |(System.IntPtr, int)
System.IntPtr operator |(System.IntPtr, System.IntPtr)

System.IntPtr operator ^(int, System.IntPtr)
System.IntPtr operator ^(System.IntPtr, int)
System.IntPtr operator ^(System.IntPtr, System.IntPtr)
```

```csharp
System.UIntPtr operator &(uint, System.UIntPtr)
System.UIntPtr operator &(System.UIntPtr, uint)
System.UIntPtr operator &(System.UIntPtr, System.UIntPtr)

System.UIntPtr operator |(uint, System.UIntPtr)
System.UIntPtr operator |(System.UIntPtr, uint)
System.UIntPtr operator |(System.UIntPtr, System.UIntPtr)

System.UIntPtr operator ^(uint, System.UIntPtr)
System.UIntPtr operator ^(System.UIntPtr, uint)
System.UIntPtr operator ^(System.UIntPtr, System.UIntPtr)
```

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

L'utilizzo effettivo di questi operatori può essere ridotto e limitato agli utenti finali che scrivono librerie di livello inferiore o codice di interoperabilità. La maggior parte degli utenti finali utilizzerebbe probabilmente queste librerie di basso livello, che avrebbero ricavato i numeri interi, gli handle e il codice di interoperabilità nativi. Di conseguenza, non avrebbero bisogno degli operatori stessi.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Fare in modo che il Framework implementi gli operatori necessari scrivendoli direttamente in IL. Inoltre, il runtime può fornire il supporto intrinseco per gli operatori definiti dal Framework, in modo da ottimizzare al meglio le prestazioni finali.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

Quali parti della progettazione sono ancora da definire?

## <a name="design-meetings"></a>Riunioni di progettazione

Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.