---
ms.openlocfilehash: 340a1dc5a2eac653458d7d29f74551e5fe28b6d5
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484559"
---
# <a name="operators-should-be-exposed-for-systemintptr-and-systemuintptr"></a><span data-ttu-id="385e3-101">Gli operatori devono essere esposti per `System.IntPtr` e `System.UIntPtr`</span><span class="sxs-lookup"><span data-stu-id="385e3-101">Operators should be exposed for `System.IntPtr` and `System.UIntPtr`</span></span>

* <span data-ttu-id="385e3-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="385e3-102">[x] Proposed</span></span>
* <span data-ttu-id="385e3-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="385e3-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="385e3-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="385e3-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="385e3-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="385e3-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="385e3-106">Summary</span><span class="sxs-lookup"><span data-stu-id="385e3-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="385e3-107">CLR supporta un set di operatori per i tipi di `System.IntPtr` e di `System.UIntPtr` (`native int`).</span><span class="sxs-lookup"><span data-stu-id="385e3-107">The CLR supports a set of operators for the `System.IntPtr` and `System.UIntPtr` types (`native int`).</span></span> <span data-ttu-id="385e3-108">Questi operatori possono essere visualizzati in `III.1.5` della specifica Common Language Infrastructure (`ECMA-335`).</span><span class="sxs-lookup"><span data-stu-id="385e3-108">These operators can be seen in `III.1.5` of the Common Language Infrastructure specification (`ECMA-335`).</span></span> <span data-ttu-id="385e3-109">Tuttavia, questi operatori non sono supportati da C#.</span><span class="sxs-lookup"><span data-stu-id="385e3-109">However, these operators are not supported by C#.</span></span>

<span data-ttu-id="385e3-110">È necessario fornire supporto per la lingua per il set completo di operatori supportati da `System.IntPtr` e `System.UIntPtr`.</span><span class="sxs-lookup"><span data-stu-id="385e3-110">Language support should be provided for the full set of operators supported by `System.IntPtr` and `System.UIntPtr`.</span></span> <span data-ttu-id="385e3-111">Questi operatori sono: `Add`, `Divide`, `Multiply`, `Remainder`, `Subtract`, `Negate`, `Equals`, `Compare`, `And`, `Not`, `Or`, `XOr`, `ShiftLeft``ShiftRight`.</span><span class="sxs-lookup"><span data-stu-id="385e3-111">These operators are: `Add`, `Divide`, `Multiply`, `Remainder`, `Subtract`, `Negate`, `Equals`, `Compare`, `And`, `Not`, `Or`, `XOr`, `ShiftLeft`, `ShiftRight`.</span></span>

## <a name="motivation"></a><span data-ttu-id="385e3-112">Motivazione</span><span class="sxs-lookup"><span data-stu-id="385e3-112">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="385e3-113">Attualmente, gli utenti possono scrivere C# facilmente applicazioni destinate a più piattaforme usando diversi strumenti e Framework, ad esempio: `Xamarin`, `.NET Core`, `Mono`e così via.</span><span class="sxs-lookup"><span data-stu-id="385e3-113">Today, users can easily write C# applications targeting multiple platforms using various tools and frameworks, such as: `Xamarin`, `.NET Core`, `Mono`, etc...</span></span>

<span data-ttu-id="385e3-114">Quando si scrive codice multipiattaforma, spesso è necessario scrivere codice di interoperabilità che interagisce con una determinata piattaforma di destinazione in modo specifico.</span><span class="sxs-lookup"><span data-stu-id="385e3-114">When writing cross-platform code, it is often necessary to write interop code that interacts with a particular target platform in a specific manner.</span></span> <span data-ttu-id="385e3-115">Questo potrebbe includere la scrittura di codice grafico, la chiamata di alcune API di sistema o l'interazione con una libreria nativa esistente.</span><span class="sxs-lookup"><span data-stu-id="385e3-115">This could include writing graphics code, calling some System API, or interacting with an existing native library.</span></span>

<span data-ttu-id="385e3-116">Questo codice di interoperabilità spesso deve gestire handle, memoria non gestita o anche solo Integer di dimensioni specifiche della piattaforma.</span><span class="sxs-lookup"><span data-stu-id="385e3-116">This interop code often has to deal with handles, unmanaged memory, or even just platform-specific sized integers.</span></span>

<span data-ttu-id="385e3-117">Il runtime fornisce supporto a questo scopo definendo un set di operatori che possono essere usati nei tipi primitivi `native int` (`System.IntPtr`) e `native unsigned int` (`System.UIntPtr`).</span><span class="sxs-lookup"><span data-stu-id="385e3-117">The runtime provides support for this by defining a set of operators that can be used on the `native int` (`System.IntPtr`) and `native unsigned int` (`System.UIntPtr`) primitive types.</span></span>

<span data-ttu-id="385e3-118">C#non ha mai supportato questi operatori, quindi gli utenti devono aggirare il problema.</span><span class="sxs-lookup"><span data-stu-id="385e3-118">C# has never supported these operators and so users have to work around the issue.</span></span> <span data-ttu-id="385e3-119">Questo aumenta spesso la complessità del codice e riduce la gestibilità del codice.</span><span class="sxs-lookup"><span data-stu-id="385e3-119">This often increases code complexity and lowers code maintainability.</span></span>

<span data-ttu-id="385e3-120">Di conseguenza, il linguaggio deve iniziare a supportare questi operatori per contribuire al miglioramento del linguaggio in modo da supportare meglio tali requisiti.</span><span class="sxs-lookup"><span data-stu-id="385e3-120">As such, the language should begin to support these operators to help advance the language to better support these requirements.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="385e3-121">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="385e3-121">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="385e3-122">Il set completo di operatori supportati è definito in `III.1.5` della specifica Common Language Infrastructure (`ECMA-335`).</span><span class="sxs-lookup"><span data-stu-id="385e3-122">The full set of operators supported are defined in `III.1.5` of the Common Language Infrastructure specification (`ECMA-335`).</span></span> <span data-ttu-id="385e3-123">La specifica è disponibile qui: [https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf).</span><span class="sxs-lookup"><span data-stu-id="385e3-123">The specification is available here: [https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf).</span></span>

* <span data-ttu-id="385e3-124">Di seguito è riportato un riepilogo degli operatori per praticità.</span><span class="sxs-lookup"><span data-stu-id="385e3-124">A summary of the operators is provided below for convenience.</span></span>
* <span data-ttu-id="385e3-125">Gli operatori non verificabili definiti dalla specifica dell'interfaccia della riga di comando non sono elencati e non fanno attualmente parte di questa proposta (anche se è opportuno considerare anche questi).</span><span class="sxs-lookup"><span data-stu-id="385e3-125">The unverifiable operators defined by the CLI spec are not listed and are not currently part of this proposal (although it may be worth considering these as well).</span></span>
* <span data-ttu-id="385e3-126">La specifica di una parola chiave, ad esempio `nint` e `nuint`, né la possibilità di dichiarare i valori letterali per `System.IntPtr` e `System.UIntPtr` (ad esempio 0n) non fa parte di questa proposta (anche se può essere utile considerare anche questi).</span><span class="sxs-lookup"><span data-stu-id="385e3-126">Providing a keyword (such as `nint` and `nuint`) nor providing a way to for literals to be declared for `System.IntPtr` and `System.UIntPtr` (such as 0n) is not part of this proposal (although it may be worth considering these as well).</span></span>

### <a name="unary-plus-operator"></a><span data-ttu-id="385e3-127">Operatore più unario</span><span class="sxs-lookup"><span data-stu-id="385e3-127">Unary Plus Operator</span></span>

```csharp
System.IntPtr operator +(System.IntPtr)
```

```csharp
System.UIntPtr operator +(System.UIntPtr)
```

### <a name="unary-minus-operator"></a><span data-ttu-id="385e3-128">Operatore meno unario</span><span class="sxs-lookup"><span data-stu-id="385e3-128">Unary Minus Operator</span></span>

```csharp
System.IntPtr operator -(System.IntPtr)
```

### <a name="bitwise-complement-operator"></a><span data-ttu-id="385e3-129">Operatore di complemento bit per bit</span><span class="sxs-lookup"><span data-stu-id="385e3-129">Bitwise Complement Operator</span></span>

```csharp
System.IntPtr operator ~(System.IntPtr)
```

```csharp
System.UIntPtr operator ~(System.UIntPtr)
```

### <a name="cast-operators"></a><span data-ttu-id="385e3-130">Operatori di cast</span><span class="sxs-lookup"><span data-stu-id="385e3-130">Cast Operators</span></span>

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

### <a name="multiplication-operator"></a><span data-ttu-id="385e3-131">Operatore di moltiplicazione</span><span class="sxs-lookup"><span data-stu-id="385e3-131">Multiplication Operator</span></span>

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

### <a name="division-operator"></a><span data-ttu-id="385e3-132">Operatore di divisione</span><span class="sxs-lookup"><span data-stu-id="385e3-132">Division Operator</span></span>

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

### <a name="remainder-operator"></a><span data-ttu-id="385e3-133">Operatore resto</span><span class="sxs-lookup"><span data-stu-id="385e3-133">Remainder Operator</span></span>

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

### <a name="addition-operator"></a><span data-ttu-id="385e3-134">Operatore di addizione</span><span class="sxs-lookup"><span data-stu-id="385e3-134">Addition Operator</span></span>

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

### <a name="subtraction-operator"></a><span data-ttu-id="385e3-135">Operatore di sottrazione</span><span class="sxs-lookup"><span data-stu-id="385e3-135">Subtraction Operator</span></span>

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

### <a name="shift-operators"></a><span data-ttu-id="385e3-136">Operatori shift</span><span class="sxs-lookup"><span data-stu-id="385e3-136">Shift Operators</span></span>

```csharp
System.IntPtr operator <<(System.IntPtr, int)
System.IntPtr operator >>(System.IntPtr, int)
```

```csharp
System.UIntPtr operator <<(System.UIntPtr, int)
System.UIntPtr operator >>(System.UIntPtr, int)
```

### <a name="integer-comparison-operators"></a><span data-ttu-id="385e3-137">Operatori di confronto integer</span><span class="sxs-lookup"><span data-stu-id="385e3-137">Integer Comparison Operators</span></span>

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

### <a name="integer-logical-operators"></a><span data-ttu-id="385e3-138">Operatori logici integer</span><span class="sxs-lookup"><span data-stu-id="385e3-138">Integer Logical Operators</span></span>

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

## <a name="drawbacks"></a><span data-ttu-id="385e3-139">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="385e3-139">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="385e3-140">L'utilizzo effettivo di questi operatori può essere ridotto e limitato agli utenti finali che scrivono librerie di livello inferiore o codice di interoperabilità.</span><span class="sxs-lookup"><span data-stu-id="385e3-140">The actual use of these operators may be small and limited to end-users who are writing lower level libraries or interop code.</span></span> <span data-ttu-id="385e3-141">La maggior parte degli utenti finali utilizzerebbe probabilmente queste librerie di basso livello, che avrebbero ricavato i numeri interi, gli handle e il codice di interoperabilità nativi.</span><span class="sxs-lookup"><span data-stu-id="385e3-141">Most end-users would likely be consuming these lower level libraries themselves which would have the native sized integers, handles, and interop code abstracted away.</span></span> <span data-ttu-id="385e3-142">Di conseguenza, non avrebbero bisogno degli operatori stessi.</span><span class="sxs-lookup"><span data-stu-id="385e3-142">As such, they would not have need of the operators themselves.</span></span>

## <a name="alternatives"></a><span data-ttu-id="385e3-143">Alternativi</span><span class="sxs-lookup"><span data-stu-id="385e3-143">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="385e3-144">Fare in modo che il Framework implementi gli operatori necessari scrivendoli direttamente in IL.</span><span class="sxs-lookup"><span data-stu-id="385e3-144">Have the framework implement the required operators by writing them directly in IL.</span></span> <span data-ttu-id="385e3-145">Inoltre, il runtime può fornire il supporto intrinseco per gli operatori definiti dal Framework, in modo da ottimizzare al meglio le prestazioni finali.</span><span class="sxs-lookup"><span data-stu-id="385e3-145">Additionally, the runtime could provide intrinsic support for the operators defined by the framework, so as to better optimize the end performance.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="385e3-146">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="385e3-146">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="385e3-147">Quali parti della progettazione sono ancora da definire?</span><span class="sxs-lookup"><span data-stu-id="385e3-147">What parts of the design are still TBD?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="385e3-148">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="385e3-148">Design meetings</span></span>

<span data-ttu-id="385e3-149">Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="385e3-149">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>