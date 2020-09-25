---
ms.openlocfilehash: b69d71e76a9cb07d5f0141c903abb162a3eedfdd
ms.sourcegitcommit: b2699bff42ae273d71d26310e3595f7598e63afb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249147"
---
# <a name="native-sized-integers"></a><span data-ttu-id="c73ea-101">Integer con dimensione nativa</span><span class="sxs-lookup"><span data-stu-id="c73ea-101">Native-sized integers</span></span>

## <a name="summary"></a><span data-ttu-id="c73ea-102">Summary</span><span class="sxs-lookup"><span data-stu-id="c73ea-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="c73ea-103">Supporto delle lingue per tipi firmati e Unsigned Integer di dimensioni native.</span><span class="sxs-lookup"><span data-stu-id="c73ea-103">Language support for a native-sized signed and unsigned integer types.</span></span>

<span data-ttu-id="c73ea-104">La motivazione è per gli scenari di interoperabilità e per le librerie di basso livello.</span><span class="sxs-lookup"><span data-stu-id="c73ea-104">The motivation is for interop scenarios and for low-level libraries.</span></span>

## <a name="design"></a><span data-ttu-id="c73ea-105">Progettazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-105">Design</span></span>
[design]: #design

<span data-ttu-id="c73ea-106">Gli identificatori `nint` e `nuint` sono nuove parole chiave contestuali che rappresentano tipi nativi firmati e unsigned integer.</span><span class="sxs-lookup"><span data-stu-id="c73ea-106">The identifiers `nint` and `nuint` are new contextual keywords that represent native signed and unsigned integer types.</span></span>
<span data-ttu-id="c73ea-107">Gli identificatori vengono trattati come parole chiave solo quando la ricerca del nome non trova un risultato valido in tale posizione del programma.</span><span class="sxs-lookup"><span data-stu-id="c73ea-107">The identifiers are only treated as keywords when name lookup does not find a viable result at that program location.</span></span>
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

<span data-ttu-id="c73ea-108">I tipi `nint` e `nuint` sono rappresentati dai tipi sottostanti `System.IntPtr` e `System.UIntPtr` con il compilatore che presenta le conversioni e le operazioni aggiuntive per tali tipi come int native.</span><span class="sxs-lookup"><span data-stu-id="c73ea-108">The types `nint` and `nuint` are represented by the underlying types `System.IntPtr` and `System.UIntPtr` with compiler surfacing additional conversions and operations for those types as native ints.</span></span>

### <a name="constants"></a><span data-ttu-id="c73ea-109">Costanti</span><span class="sxs-lookup"><span data-stu-id="c73ea-109">Constants</span></span>

<span data-ttu-id="c73ea-110">Le espressioni costanti possono essere di tipo `nint` o `nuint` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-110">Constant expressions may be of type `nint` or `nuint`.</span></span>
<span data-ttu-id="c73ea-111">Non esiste alcuna sintassi diretta per i valori letterali int nativi.</span><span class="sxs-lookup"><span data-stu-id="c73ea-111">There is no direct syntax for native int literals.</span></span> <span data-ttu-id="c73ea-112">È invece possibile usare cast impliciti o espliciti di altri valori di costanti integrali: `const nint i = (nint)42;` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-112">Implicit or explicit casts of other integral constant values can be used instead: `const nint i = (nint)42;`.</span></span>

<span data-ttu-id="c73ea-113">`nint` le costanti sono comprese nell'intervallo [ `int.MinValue` , `int.MaxValue` ].</span><span class="sxs-lookup"><span data-stu-id="c73ea-113">`nint` constants are in the range [ `int.MinValue`, `int.MaxValue` ].</span></span>

<span data-ttu-id="c73ea-114">`nuint` le costanti sono comprese nell'intervallo [ `uint.MinValue` , `uint.MaxValue` ].</span><span class="sxs-lookup"><span data-stu-id="c73ea-114">`nuint` constants are in the range [ `uint.MinValue`, `uint.MaxValue` ].</span></span>

<span data-ttu-id="c73ea-115">Non sono presenti `MinValue` `MaxValue` campi o in `nint` o `nuint` perché, oltre a `nuint.MinValue` , tali valori non possono essere emessi come costanti.</span><span class="sxs-lookup"><span data-stu-id="c73ea-115">There are no `MinValue` or `MaxValue` fields on `nint` or `nuint` because, other than `nuint.MinValue`, those values cannot be emitted as constants.</span></span>

<span data-ttu-id="c73ea-116">La riduzione delle costanti è supportata per tutti gli operatori unari { `+` , `-` , `~` } e gli operatori binari { `+` , `-` , `*` , `/` , `%` , `==` , `!=` , `<` , `<=` , `>` , `>=` , `&` , `|` , `^` ,, `<<` `>>` }.</span><span class="sxs-lookup"><span data-stu-id="c73ea-116">Constant folding is supported for all unary operators { `+`, `-`, `~` } and binary operators { `+`, `-`, `*`, `/`, `%`, `==`, `!=`, `<`, `<=`, `>`, `>=`, `&`, `|`, `^`, `<<`, `>>` }.</span></span>
<span data-ttu-id="c73ea-117">Le operazioni di riduzione costanti vengono valutate con `Int32` `UInt32` operandi e anziché come int nativi per un comportamento coerente indipendentemente dalla piattaforma del compilatore.</span><span class="sxs-lookup"><span data-stu-id="c73ea-117">Constant folding operations are evaluated with `Int32` and `UInt32` operands rather than native ints for consistent behavior regardless of compiler platform.</span></span>
<span data-ttu-id="c73ea-118">Se l'operazione restituisce un valore costante in 32 bit, la riduzione continua viene eseguita in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c73ea-118">If the operation results in a constant value in 32-bits, constant folding is performed at compile-time.</span></span>
<span data-ttu-id="c73ea-119">In caso contrario, l'operazione viene eseguita in fase di esecuzione e non è considerata una costante.</span><span class="sxs-lookup"><span data-stu-id="c73ea-119">Otherwise the operation is executed at runtime and not considered a constant.</span></span>

### <a name="conversions"></a><span data-ttu-id="c73ea-120">Conversioni</span><span class="sxs-lookup"><span data-stu-id="c73ea-120">Conversions</span></span>
<span data-ttu-id="c73ea-121">Esiste una conversione di identità tra `nint` e e `IntPtr` tra `nuint` e `UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-121">There is an identity conversion between `nint` and `IntPtr`, and between `nuint` and `UIntPtr`.</span></span>
<span data-ttu-id="c73ea-122">Esiste una conversione di identità tra tipi composti che differiscono solo per i tipi int e i tipi sottostanti nativi: matrici, `Nullable<>` , tipi costruiti e Tuple.</span><span class="sxs-lookup"><span data-stu-id="c73ea-122">There is an identity conversion between compound types that differ by native ints and underlying types only: arrays, `Nullable<>`, constructed types, and tuples.</span></span>

<span data-ttu-id="c73ea-123">Le tabelle seguenti coprono le conversioni tra tipi speciali.</span><span class="sxs-lookup"><span data-stu-id="c73ea-123">The tables below cover the conversions between special types.</span></span>
<span data-ttu-id="c73ea-124">Il linguaggio il per ogni conversione include le varianti per `unchecked` e i `checked` contesti se diversi.</span><span class="sxs-lookup"><span data-stu-id="c73ea-124">(The IL for each conversion includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="c73ea-125">Operando</span><span class="sxs-lookup"><span data-stu-id="c73ea-125">Operand</span></span> | <span data-ttu-id="c73ea-126">Destinazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-126">Target</span></span> | <span data-ttu-id="c73ea-127">Conversione</span><span class="sxs-lookup"><span data-stu-id="c73ea-127">Conversion</span></span> | <span data-ttu-id="c73ea-128">IL</span><span class="sxs-lookup"><span data-stu-id="c73ea-128">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | <span data-ttu-id="c73ea-129">Unboxing</span><span class="sxs-lookup"><span data-stu-id="c73ea-129">Unboxing</span></span> | `unbox` |
| `void*` | `nint` | <span data-ttu-id="c73ea-130">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="c73ea-130">PointerToVoid</span></span> | `conv.i` |
| `sbyte` | `nint` | <span data-ttu-id="c73ea-131">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-131">ImplicitNumeric</span></span> | `conv.i` |
| `byte` | `nint` | <span data-ttu-id="c73ea-132">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-132">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nint` | <span data-ttu-id="c73ea-133">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-133">ImplicitNumeric</span></span> | `conv.i` |
| `ushort` | `nint` | <span data-ttu-id="c73ea-134">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-134">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nint` | <span data-ttu-id="c73ea-135">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-135">ImplicitNumeric</span></span> | `conv.i` |
| `uint` | `nint` | <span data-ttu-id="c73ea-136">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-136">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | <span data-ttu-id="c73ea-137">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-137">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | <span data-ttu-id="c73ea-138">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-138">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | <span data-ttu-id="c73ea-139">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-139">ImplicitNumeric</span></span> | `conv.i` |
| `float` | `nint` | <span data-ttu-id="c73ea-140">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-140">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | <span data-ttu-id="c73ea-141">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-141">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | <span data-ttu-id="c73ea-142">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-142">ExplicitNumeric</span></span> | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | <span data-ttu-id="c73ea-143">Identità</span><span class="sxs-lookup"><span data-stu-id="c73ea-143">Identity</span></span> | |
| `UIntPtr` | `nint` | <span data-ttu-id="c73ea-144">nessuno</span><span class="sxs-lookup"><span data-stu-id="c73ea-144">None</span></span> | |
| `object` | `nuint` | <span data-ttu-id="c73ea-145">Unboxing</span><span class="sxs-lookup"><span data-stu-id="c73ea-145">Unboxing</span></span> | `unbox` |
| `void*` | `nuint` | <span data-ttu-id="c73ea-146">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="c73ea-146">PointerToVoid</span></span> | `conv.u` |
| `sbyte` | `nuint` | <span data-ttu-id="c73ea-147">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-147">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | <span data-ttu-id="c73ea-148">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-148">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nuint` | <span data-ttu-id="c73ea-149">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-149">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | <span data-ttu-id="c73ea-150">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-150">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nuint` | <span data-ttu-id="c73ea-151">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-151">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | <span data-ttu-id="c73ea-152">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-152">ImplicitNumeric</span></span> | `conv.u` |
| `long` | `nuint` | <span data-ttu-id="c73ea-153">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-153">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | <span data-ttu-id="c73ea-154">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-154">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | <span data-ttu-id="c73ea-155">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-155">ImplicitNumeric</span></span> | `conv.u` |
| `float` | `nuint` | <span data-ttu-id="c73ea-156">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-156">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | <span data-ttu-id="c73ea-157">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-157">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | <span data-ttu-id="c73ea-158">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-158">ExplicitNumeric</span></span> | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | <span data-ttu-id="c73ea-159">nessuno</span><span class="sxs-lookup"><span data-stu-id="c73ea-159">None</span></span> | |
| `UIntPtr` | `nuint` | <span data-ttu-id="c73ea-160">Identità</span><span class="sxs-lookup"><span data-stu-id="c73ea-160">Identity</span></span> | |
|<span data-ttu-id="c73ea-161">Enumerazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-161">Enumeration</span></span>|`nint`|<span data-ttu-id="c73ea-162">ExplicitEnumeration</span><span class="sxs-lookup"><span data-stu-id="c73ea-162">ExplicitEnumeration</span></span>||
|<span data-ttu-id="c73ea-163">Enumerazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-163">Enumeration</span></span>|`nuint`|<span data-ttu-id="c73ea-164">ExplicitEnumeration</span><span class="sxs-lookup"><span data-stu-id="c73ea-164">ExplicitEnumeration</span></span>||

| <span data-ttu-id="c73ea-165">Operando</span><span class="sxs-lookup"><span data-stu-id="c73ea-165">Operand</span></span> | <span data-ttu-id="c73ea-166">Destinazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-166">Target</span></span> | <span data-ttu-id="c73ea-167">Conversione</span><span class="sxs-lookup"><span data-stu-id="c73ea-167">Conversion</span></span> | <span data-ttu-id="c73ea-168">IL</span><span class="sxs-lookup"><span data-stu-id="c73ea-168">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `nint` | `object` | <span data-ttu-id="c73ea-169">Boxing</span><span class="sxs-lookup"><span data-stu-id="c73ea-169">Boxing</span></span> | `box` |
| `nint` | `void*` | <span data-ttu-id="c73ea-170">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="c73ea-170">PointerToVoid</span></span> | `conv.i` |
| `nint` | `nuint` | <span data-ttu-id="c73ea-171">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-171">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `nint` | `sbyte` | <span data-ttu-id="c73ea-172">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-172">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nint` | `byte` | <span data-ttu-id="c73ea-173">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-173">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nint` | `short` | <span data-ttu-id="c73ea-174">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-174">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nint` | `ushort` | <span data-ttu-id="c73ea-175">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-175">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `int` | <span data-ttu-id="c73ea-176">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-176">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nint` | `uint` | <span data-ttu-id="c73ea-177">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-177">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nint` | `long` | <span data-ttu-id="c73ea-178">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-178">ImplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | <span data-ttu-id="c73ea-179">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-179">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | <span data-ttu-id="c73ea-180">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-180">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | <span data-ttu-id="c73ea-181">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-181">ImplicitNumeric</span></span> | `conv.r4` |
| `nint` | `double` | <span data-ttu-id="c73ea-182">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-182">ImplicitNumeric</span></span> | `conv.r8` |
| `nint` | `decimal` | <span data-ttu-id="c73ea-183">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-183">ImplicitNumeric</span></span> | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | <span data-ttu-id="c73ea-184">Identità</span><span class="sxs-lookup"><span data-stu-id="c73ea-184">Identity</span></span> | |
| `nint` | `UIntPtr` | <span data-ttu-id="c73ea-185">nessuno</span><span class="sxs-lookup"><span data-stu-id="c73ea-185">None</span></span> | |
| `nint` |<span data-ttu-id="c73ea-186">Enumerazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-186">Enumeration</span></span>|<span data-ttu-id="c73ea-187">ExplicitEnumeration</span><span class="sxs-lookup"><span data-stu-id="c73ea-187">ExplicitEnumeration</span></span>||
| `nuint` | `object` | <span data-ttu-id="c73ea-188">Boxing</span><span class="sxs-lookup"><span data-stu-id="c73ea-188">Boxing</span></span> | `box` |
| `nuint` | `void*` | <span data-ttu-id="c73ea-189">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="c73ea-189">PointerToVoid</span></span> | `conv.u` |
| `nuint` | `nint` | <span data-ttu-id="c73ea-190">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-190">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `nuint` | `sbyte` | <span data-ttu-id="c73ea-191">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-191">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nuint` | `byte` | <span data-ttu-id="c73ea-192">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-192">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nuint` | `short` | <span data-ttu-id="c73ea-193">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-193">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nuint` | `ushort` | <span data-ttu-id="c73ea-194">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-194">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nuint` | `int` | <span data-ttu-id="c73ea-195">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-195">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nuint` | `uint` | <span data-ttu-id="c73ea-196">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-196">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nuint` | `long` | <span data-ttu-id="c73ea-197">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-197">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nuint` | `ulong` | <span data-ttu-id="c73ea-198">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-198">ImplicitNumeric</span></span> | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | <span data-ttu-id="c73ea-199">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-199">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | <span data-ttu-id="c73ea-200">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-200">ImplicitNumeric</span></span> | `conv.r.un conv.r4` |
| `nuint` | `double` | <span data-ttu-id="c73ea-201">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-201">ImplicitNumeric</span></span> | `conv.r.un conv.r8` |
| `nuint` | `decimal` | <span data-ttu-id="c73ea-202">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="c73ea-202">ImplicitNumeric</span></span> | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | <span data-ttu-id="c73ea-203">nessuno</span><span class="sxs-lookup"><span data-stu-id="c73ea-203">None</span></span> | |
| `nuint` | `UIntPtr` | <span data-ttu-id="c73ea-204">Identità</span><span class="sxs-lookup"><span data-stu-id="c73ea-204">Identity</span></span> | |
| `nuint` |<span data-ttu-id="c73ea-205">Enumerazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-205">Enumeration</span></span>|<span data-ttu-id="c73ea-206">ExplicitEnumeration</span><span class="sxs-lookup"><span data-stu-id="c73ea-206">ExplicitEnumeration</span></span>||

<span data-ttu-id="c73ea-207">La conversione da `A` a `Nullable<B>` è:</span><span class="sxs-lookup"><span data-stu-id="c73ea-207">Conversion from `A` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="c73ea-208">conversione implicita nullable in presenza di una conversione di identità o di conversione implicita da `A` a `B` ;</span><span class="sxs-lookup"><span data-stu-id="c73ea-208">an implicit nullable conversion if there is an identity conversion or implicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="c73ea-209">conversione esplicita di valori null in caso di conversione esplicita da `A` a `B` ;</span><span class="sxs-lookup"><span data-stu-id="c73ea-209">an explicit nullable conversion if there is an explicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="c73ea-210">in caso contrario, non valido.</span><span class="sxs-lookup"><span data-stu-id="c73ea-210">otherwise invalid.</span></span>

<span data-ttu-id="c73ea-211">La conversione da `Nullable<A>` a `B` è:</span><span class="sxs-lookup"><span data-stu-id="c73ea-211">Conversion from `Nullable<A>` to `B` is:</span></span>
- <span data-ttu-id="c73ea-212">conversione esplicita di valori null in caso di conversione di identità o conversione numerica implicita o esplicita da `A` a `B` ;</span><span class="sxs-lookup"><span data-stu-id="c73ea-212">an explicit nullable conversion if there is an identity conversion or implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="c73ea-213">in caso contrario, non valido.</span><span class="sxs-lookup"><span data-stu-id="c73ea-213">otherwise invalid.</span></span>

<span data-ttu-id="c73ea-214">La conversione da `Nullable<A>` a `Nullable<B>` è:</span><span class="sxs-lookup"><span data-stu-id="c73ea-214">Conversion from `Nullable<A>` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="c73ea-215">una conversione di identità se è presente una conversione di identità da `A` a `B` ;</span><span class="sxs-lookup"><span data-stu-id="c73ea-215">an identity conversion if there is an identity conversion from `A` to `B`;</span></span>
- <span data-ttu-id="c73ea-216">conversione esplicita nullable se è presente una conversione numerica implicita o esplicita da `A` a `B` ;</span><span class="sxs-lookup"><span data-stu-id="c73ea-216">an explicit nullable conversion if there is an implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="c73ea-217">in caso contrario, non valido.</span><span class="sxs-lookup"><span data-stu-id="c73ea-217">otherwise invalid.</span></span>

### <a name="operators"></a><span data-ttu-id="c73ea-218">Operatori</span><span class="sxs-lookup"><span data-stu-id="c73ea-218">Operators</span></span>

<span data-ttu-id="c73ea-219">Gli operatori predefiniti sono i seguenti.</span><span class="sxs-lookup"><span data-stu-id="c73ea-219">The predefined operators are as follows.</span></span>
<span data-ttu-id="c73ea-220">Questi operatori vengono considerati durante la risoluzione dell'overload in base alle regole normali per le conversioni implicite _se almeno uno degli operandi è di `nint` tipo `nuint` o _.</span><span class="sxs-lookup"><span data-stu-id="c73ea-220">These operators are considered during overload resolution based on normal rules for implicit conversions _if at least one of the operands is of type `nint` or `nuint`_.</span></span>

<span data-ttu-id="c73ea-221">Il linguaggio il per ogni operatore include le varianti per `unchecked` i `checked` contesti e se diverso.</span><span class="sxs-lookup"><span data-stu-id="c73ea-221">(The IL for each operator includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="c73ea-222">Unario</span><span class="sxs-lookup"><span data-stu-id="c73ea-222">Unary</span></span> | <span data-ttu-id="c73ea-223">Firma operatore</span><span class="sxs-lookup"><span data-stu-id="c73ea-223">Operator Signature</span></span> | <span data-ttu-id="c73ea-224">IL</span><span class="sxs-lookup"><span data-stu-id="c73ea-224">IL</span></span> |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint value)` | `nop` |
| `+` | `nuint operator +(nuint value)` | `nop` |
| `-` | `nint operator -(nint value)` | `neg` |
| `~` | `nint operator ~(nint value)` | `not` |
| `~` | `nuint operator ~(nuint value)` | `not` |

| <span data-ttu-id="c73ea-225">Binary</span><span class="sxs-lookup"><span data-stu-id="c73ea-225">Binary</span></span> | <span data-ttu-id="c73ea-226">Firma operatore</span><span class="sxs-lookup"><span data-stu-id="c73ea-226">Operator Signature</span></span> | <span data-ttu-id="c73ea-227">IL</span><span class="sxs-lookup"><span data-stu-id="c73ea-227">IL</span></span> |
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

<span data-ttu-id="c73ea-228">Per alcuni operatori binari, gli operatori IL supportano i tipi di operandi aggiuntivi (vedere la tabella del tipo di operando [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III. 1.5).</span><span class="sxs-lookup"><span data-stu-id="c73ea-228">For some binary operators, the IL operators support additional operand types (see [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5 Operand type table).</span></span>
<span data-ttu-id="c73ea-229">Tuttavia, il set di tipi di operando supportati da C# è limitato per semplicità e per coerenza con gli operatori esistenti nel linguaggio.</span><span class="sxs-lookup"><span data-stu-id="c73ea-229">But the set of operand types supported by C# is limited for simplicity and for consistency with existing operators in the language.</span></span>

<span data-ttu-id="c73ea-230">Sono supportate le versioni sollevate degli operatori, in cui gli argomenti e i tipi restituiti sono `nint?` e `nuint?` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-230">Lifted versions of the operators, where the arguments and return types are `nint?` and `nuint?`, are supported.</span></span>

<span data-ttu-id="c73ea-231">Le operazioni di assegnazione composta in `x op= y` cui `x` o `y` sono int nativi seguono le stesse regole di altri tipi primitivi con operatori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="c73ea-231">Compound assignment operations `x op= y` where `x` or `y` are native ints follow the same rules as with other primitive types with pre-defined operators.</span></span>
<span data-ttu-id="c73ea-232">In particolare, l'espressione viene associata come `x = (T)(x op y)` dove `T` è il tipo di `x` e dove `x` viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="c73ea-232">Specifically the expression is bound as `x = (T)(x op y)` where `T` is the type of `x` and where `x` is only evaluated once.</span></span>

<span data-ttu-id="c73ea-233">Gli operatori shift devono mascherare il numero di bit per passare a 5 bit se `sizeof(nint)` è 4 e a 6 bit se `sizeof(nint)` è 8.</span><span class="sxs-lookup"><span data-stu-id="c73ea-233">The shift operators should mask the number of bits to shift - to 5 bits if `sizeof(nint)` is 4, and to 6 bits if `sizeof(nint)` is 8.</span></span>
<span data-ttu-id="c73ea-234">(vedere [operatori shift](../../spec/expressions.md#shift-operators) in C# spec).</span><span class="sxs-lookup"><span data-stu-id="c73ea-234">(see [shift operators](../../spec/expressions.md#shift-operators) in C# spec).</span></span>

<span data-ttu-id="c73ea-235">Il compilatore C# 9 segnalerà gli errori di associazione agli operatori Integer nativi predefiniti durante la compilazione con una versione del linguaggio precedente, ma consentirà l'utilizzo di conversioni predefinite da e verso numeri interi nativi.</span><span class="sxs-lookup"><span data-stu-id="c73ea-235">The C#9 compiler will report errors binding to predefined native integer operators when compiling with an earlier language version, but will allow use of predefined conversions to and from native integers.</span></span>

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

### <a name="dynamic"></a><span data-ttu-id="c73ea-236">Dinamico</span><span class="sxs-lookup"><span data-stu-id="c73ea-236">Dynamic</span></span>

<span data-ttu-id="c73ea-237">Le conversioni e gli operatori vengono sintetizzati dal compilatore e non fanno parte dei `IntPtr` tipi e sottostanti `UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-237">The conversions and operators are synthesized by the compiler and are not part of the underlying `IntPtr` and `UIntPtr` types.</span></span>
<span data-ttu-id="c73ea-238">Di conseguenza, le conversioni e gli operatori _non sono disponibili_ dal gestore di associazione di runtime per `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-238">As a result those conversions and operators _are not available_ from the runtime binder for `dynamic`.</span></span> 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a><span data-ttu-id="c73ea-239">Membri dei tipi</span><span class="sxs-lookup"><span data-stu-id="c73ea-239">Type members</span></span>

<span data-ttu-id="c73ea-240">L'unico costruttore per `nint` o `nuint` è il costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="c73ea-240">The only constructor for `nint` or `nuint` is the parameter-less constructor.</span></span>

<span data-ttu-id="c73ea-241">I membri seguenti di `System.IntPtr` e `System.UIntPtr` _sono esclusi in modo esplicito_ da `nint` o `nuint` :</span><span class="sxs-lookup"><span data-stu-id="c73ea-241">The following members of `System.IntPtr` and `System.UIntPtr` _are explicitly excluded_ from `nint` or `nuint`:</span></span>
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

<span data-ttu-id="c73ea-242">I membri rimanenti di `System.IntPtr` e `System.UIntPtr` _sono inclusi in modo implicito_ in `nint` e `nuint` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-242">The remaining members of `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`.</span></span> <span data-ttu-id="c73ea-243">Per .NET Framework 4.7.2:</span><span class="sxs-lookup"><span data-stu-id="c73ea-243">For .NET Framework 4.7.2:</span></span>
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

<span data-ttu-id="c73ea-244">Le interfacce implementate da `System.IntPtr` e `System.UIntPtr` _sono incluse in modo implicito_ in `nint` e `nuint` , con le occorrenze dei tipi sottostanti sostituiti dai tipi Integer nativi corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="c73ea-244">Interfaces implemented by `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`, with occurrences of the underlying types replaced by the corresponding native integer types.</span></span>
<span data-ttu-id="c73ea-245">Ad esempio `IntPtr` , se implementa `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>` , `nint` implementa `ISerializable, IEquatable<nint>, IComparable<nint>` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-245">For instance if `IntPtr` implements `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`, then `nint` implements `ISerializable, IEquatable<nint>, IComparable<nint>`.</span></span>

### <a name="overriding-hiding-and-implementing"></a><span data-ttu-id="c73ea-246">Override, nascondere e implementare</span><span class="sxs-lookup"><span data-stu-id="c73ea-246">Overriding, hiding, and implementing</span></span>

<span data-ttu-id="c73ea-247">`nint` e `System.IntPtr` , e `nuint` e `System.UIntPtr` sono considerati equivalenti per l'override, l'occultamento e l'implementazione di.</span><span class="sxs-lookup"><span data-stu-id="c73ea-247">`nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, are considered equivalent for overriding, hiding, and implementing.</span></span>

<span data-ttu-id="c73ea-248">Gli overload non possono differire da `nint` and `System.IntPtr` , and `nuint` e `System.UIntPtr` , alone.</span><span class="sxs-lookup"><span data-stu-id="c73ea-248">Overloads cannot differ by `nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="c73ea-249">Le sostituzioni e le implementazioni possono variare `nint` solo per and `System.IntPtr` , or `nuint` e `System.UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-249">Overrides and implementations may differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="c73ea-250">I metodi nascondono altri metodi che differiscono `nint` solo per and `System.IntPtr` , or `nuint` e `System.UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-250">Methods hide other methods that differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="c73ea-251">Varie</span><span class="sxs-lookup"><span data-stu-id="c73ea-251">Miscellaneous</span></span>

<span data-ttu-id="c73ea-252">`nint` le `nuint` espressioni e utilizzate come indici di matrice vengono emesse senza conversione.</span><span class="sxs-lookup"><span data-stu-id="c73ea-252">`nint` and `nuint` expressions used as array indices are emitted without conversion.</span></span>
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

<span data-ttu-id="c73ea-253">`nint` e `nuint` possono essere usati come `enum` tipo di base.</span><span class="sxs-lookup"><span data-stu-id="c73ea-253">`nint` and `nuint` can be used as an `enum` base type.</span></span>
```C#
enum E : nint // ok
{
}
```

<span data-ttu-id="c73ea-254">Le letture e le Scritture sono atomiche per i tipi `nint` , `nuint` e `enum` con il tipo di base `nint` o `nuint` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-254">Reads and writes are atomic for types `nint`, `nuint`, and `enum` with base type `nint` or `nuint`.</span></span>

<span data-ttu-id="c73ea-255">I campi possono essere contrassegnati `volatile` per i tipi `nint` e `nuint` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-255">Fields may be marked `volatile` for types `nint` and `nuint`.</span></span>
<span data-ttu-id="c73ea-256">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 non include `enum` tuttavia il tipo di `System.IntPtr` base `System.UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-256">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 does not include `enum` with base type `System.IntPtr` or `System.UIntPtr` however.</span></span>

<span data-ttu-id="c73ea-257">`default(nint)` e `new nint()` sono equivalenti a `(nint)0` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-257">`default(nint)` and `new nint()` are equivalent to `(nint)0`.</span></span>

<span data-ttu-id="c73ea-258">`typeof(nint)` è `typeof(IntPtr)`.</span><span class="sxs-lookup"><span data-stu-id="c73ea-258">`typeof(nint)` is `typeof(IntPtr)`.</span></span>

<span data-ttu-id="c73ea-259">`sizeof(nint)` è supportato ma richiede la compilazione in un contesto non sicuro (così com'è `sizeof(IntPtr)` ).</span><span class="sxs-lookup"><span data-stu-id="c73ea-259">`sizeof(nint)` is supported but requires compiling in an unsafe context (as does `sizeof(IntPtr)`).</span></span>
<span data-ttu-id="c73ea-260">Il valore non è una costante in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c73ea-260">The value is not a compile-time constant.</span></span>
<span data-ttu-id="c73ea-261">`sizeof(nint)` viene implementato come `sizeof(IntPtr)` anziché `IntPtr.Size` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-261">`sizeof(nint)` is implemented as `sizeof(IntPtr)` rather than `IntPtr.Size`.</span></span>

<span data-ttu-id="c73ea-262">Diagnostica del compilatore per i riferimenti ai tipi che coinvolgono o `nint` `nuint` segnalano o `nint` `nuint` invece di `IntPtr` o `UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-262">Compiler diagnostics for type references involving `nint` or `nuint` report `nint` or `nuint` rather than `IntPtr` or `UIntPtr`.</span></span>

### <a name="metadata"></a><span data-ttu-id="c73ea-263">Metadati</span><span class="sxs-lookup"><span data-stu-id="c73ea-263">Metadata</span></span>

<span data-ttu-id="c73ea-264">`nint` e `nuint` sono rappresentati nei metadati come `System.IntPtr` e `System.UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-264">`nint` and `nuint` are represented in metadata as `System.IntPtr` and `System.UIntPtr`.</span></span>

<span data-ttu-id="c73ea-265">I riferimenti ai tipi che includono `nint` o `nuint` vengono emessi con un oggetto `System.Runtime.CompilerServices.NativeIntegerAttribute` per indicare quali parti del riferimento al tipo sono native int.</span><span class="sxs-lookup"><span data-stu-id="c73ea-265">Type references that include `nint` or `nuint` are emitted with a `System.Runtime.CompilerServices.NativeIntegerAttribute` to indicate which parts of the type reference are native ints.</span></span>

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

<span data-ttu-id="c73ea-266">La codifica dei riferimenti ai tipi con `NativeIntegerAttribute` è analizzata in [NativeIntegerAttribute.MD](https://github.com/dotnet/roslyn/blob/master/docs/features/NativeIntegerAttribute.md).</span><span class="sxs-lookup"><span data-stu-id="c73ea-266">The encoding of type references with `NativeIntegerAttribute` is covered in [NativeIntegerAttribute.md](https://github.com/dotnet/roslyn/blob/master/docs/features/NativeIntegerAttribute.md).</span></span>

## <a name="alternatives"></a><span data-ttu-id="c73ea-267">Alternativi</span><span class="sxs-lookup"><span data-stu-id="c73ea-267">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="c73ea-268">Un'alternativa all'approccio precedente alla cancellazione dei tipi è l'introduzione di nuovi tipi: `System.NativeInt` e `System.NativeUInt` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-268">An alternative to the "type erasure" approach above is to introduce new types: `System.NativeInt` and `System.NativeUInt`.</span></span>
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
<span data-ttu-id="c73ea-269">I tipi distinti consentono l'overload di DISTINCT da `IntPtr` e consentiranno l'analisi DISTINCT e `ToString()` .</span><span class="sxs-lookup"><span data-stu-id="c73ea-269">Distinct types would allow overloading distinct from `IntPtr` and would allow distinct parsing and `ToString()`.</span></span>
<span data-ttu-id="c73ea-270">Tuttavia, il CLR potrà gestire questi tipi in modo efficiente, evitando allo scopo principale l'efficienza della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="c73ea-270">But there would be more work for the CLR to handle these types efficiently which defeats the primary purpose of the feature - efficiency.</span></span>
<span data-ttu-id="c73ea-271">E l'interoperabilità con il codice int nativo esistente che usa `IntPtr` sarebbe più difficile.</span><span class="sxs-lookup"><span data-stu-id="c73ea-271">And interop with existing native int code that uses `IntPtr` would be more difficult.</span></span> 

<span data-ttu-id="c73ea-272">Un'altra alternativa consiste nell'aggiungere un supporto nativo int per `IntPtr` nel Framework ma senza alcun supporto del compilatore specifico.</span><span class="sxs-lookup"><span data-stu-id="c73ea-272">Another alternative is to add more native int support for `IntPtr` in the framework but without any specific compiler support.</span></span>
<span data-ttu-id="c73ea-273">Tutte le nuove conversioni e le operazioni aritmetiche sono supportate automaticamente dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="c73ea-273">Any new conversions and arithmetic operations would be supported by the compiler automatically.</span></span>
<span data-ttu-id="c73ea-274">Tuttavia, il linguaggio non fornirebbe parole chiave, costanti o `checked` operazioni.</span><span class="sxs-lookup"><span data-stu-id="c73ea-274">But the language would not provide keywords, constants, or `checked` operations.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="c73ea-275">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="c73ea-275">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md
- https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md
- https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators
- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md
