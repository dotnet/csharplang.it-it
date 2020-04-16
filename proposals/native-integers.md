---
ms.openlocfilehash: 42839a8c233468dd0b5ec6dad436dc71f056a6d9
ms.sourcegitcommit: d414836632ba2730545e0b058373a94696bba5e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81447818"
---
# <a name="native-sized-integers"></a><span data-ttu-id="5715d-101">Numeri interi di dimensioni native</span><span class="sxs-lookup"><span data-stu-id="5715d-101">Native-sized integers</span></span>

## <a name="summary"></a><span data-ttu-id="5715d-102">Summary</span><span class="sxs-lookup"><span data-stu-id="5715d-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="5715d-103">Supporto del linguaggio per tipi integer con e senza segno di dimensioni native.</span><span class="sxs-lookup"><span data-stu-id="5715d-103">Language support for a native-sized signed and unsigned integer types.</span></span>

<span data-ttu-id="5715d-104">La motivazione è per gli scenari di interoperabilità e per le librerie di basso livello.</span><span class="sxs-lookup"><span data-stu-id="5715d-104">The motivation is for interop scenarios and for low-level libraries.</span></span>

## <a name="design"></a><span data-ttu-id="5715d-105">Progettazione</span><span class="sxs-lookup"><span data-stu-id="5715d-105">Design</span></span>
[design]: #design

<span data-ttu-id="5715d-106">Gli `nint` identificatori `nuint` e sono nuove parole chiave contestuali che rappresentano tipi integer con segno e senza segno nativi.</span><span class="sxs-lookup"><span data-stu-id="5715d-106">The identifiers `nint` and `nuint` are new contextual keywords that represent native signed and unsigned integer types.</span></span>
<span data-ttu-id="5715d-107">Gli identificatori vengono trattati come parole chiave solo quando la ricerca del nome non trova un risultato fattibile in tale percorso del programma.</span><span class="sxs-lookup"><span data-stu-id="5715d-107">The identifiers are only treated as keywords when name lookup does not find a viable result at that program location.</span></span>
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

<span data-ttu-id="5715d-108">I `nint` tipi `nuint` e sono rappresentati dai tipi `System.IntPtr` sottostanti e `System.UIntPtr` con il compilatore che visualizza conversioni e operazioni aggiuntive per tali tipi come int nativi.</span><span class="sxs-lookup"><span data-stu-id="5715d-108">The types `nint` and `nuint` are represented by the underlying types `System.IntPtr` and `System.UIntPtr` with compiler surfacing additional conversions and operations for those types as native ints.</span></span>

### <a name="constants"></a><span data-ttu-id="5715d-109">Costanti</span><span class="sxs-lookup"><span data-stu-id="5715d-109">Constants</span></span>

<span data-ttu-id="5715d-110">Le espressioni costanti `nint` possono `nuint`essere di tipo o .</span><span class="sxs-lookup"><span data-stu-id="5715d-110">Constant expressions may be of type `nint` or `nuint`.</span></span>
<span data-ttu-id="5715d-111">Non esiste una sintassi diretta per i valori letterali int nativi.</span><span class="sxs-lookup"><span data-stu-id="5715d-111">There is no direct syntax for native int literals.</span></span> <span data-ttu-id="5715d-112">È invece possibile utilizzare cast impliciti o `const nint i = (nint)42;`espliciti di altri valori costanti integrali: .</span><span class="sxs-lookup"><span data-stu-id="5715d-112">Implicit or explicit casts of other integral constant values can be used instead: `const nint i = (nint)42;`.</span></span>

<span data-ttu-id="5715d-113">`nint`le costanti sono nell'intervallo [ `int.MinValue`, `int.MaxValue` ].</span><span class="sxs-lookup"><span data-stu-id="5715d-113">`nint` constants are in the range [ `int.MinValue`, `int.MaxValue` ].</span></span>

<span data-ttu-id="5715d-114">`nuint`le costanti sono nell'intervallo [ `uint.MinValue`, `uint.MaxValue` ].</span><span class="sxs-lookup"><span data-stu-id="5715d-114">`nuint` constants are in the range [ `uint.MinValue`, `uint.MaxValue` ].</span></span>

<span data-ttu-id="5715d-115">Non sono `MinValue` `MaxValue` presenti `nint` campi `nuint` o perché, ad esempio, `nuint.MinValue`tali valori non possono essere emessi come costanti.</span><span class="sxs-lookup"><span data-stu-id="5715d-115">There are no `MinValue` or `MaxValue` fields on `nint` or `nuint` because, other than `nuint.MinValue`, those values cannot be emitted as constants.</span></span>

<span data-ttu-id="5715d-116">La riduzione costante è supportata `+` `-`per `~` tutti gli `+`operatori `-` `*`unari , , , e gli operatori binari , , , , `/`, `%`, `==`, `!=`, `<` `<=`, `>`, `>=`, , , `&`, , `|`, , `^`, , `<<`, , `>>` , .</span><span class="sxs-lookup"><span data-stu-id="5715d-116">Constant folding is supported for all unary operators { `+`, `-`, `~` } and binary operators { `+`, `-`, `*`, `/`, `%`, `==`, `!=`, `<`, `<=`, `>`, `>=`, `&`, `|`, `^`, `<<`, `>>` }.</span></span>
<span data-ttu-id="5715d-117">Le operazioni di piegatura costante vengono valutate con `Int32` e `UInt32` operandi anziché int nativi per un comportamento coerente indipendentemente dalla piattaforma del compilatore.</span><span class="sxs-lookup"><span data-stu-id="5715d-117">Constant folding operations are evaluated with `Int32` and `UInt32` operands rather than native ints for consistent behavior regardless of compiler platform.</span></span>
<span data-ttu-id="5715d-118">Se l'operazione restituisce un valore costante in 32 bit, la riduzione costante viene eseguita in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5715d-118">If the operation results in a constant value in 32-bits, constant folding is performed at compile-time.</span></span>
<span data-ttu-id="5715d-119">In caso contrario, l'operazione viene eseguita in fase di esecuzione e non viene considerata una costante.</span><span class="sxs-lookup"><span data-stu-id="5715d-119">Otherwise the operation is executed at runtime and not considered a constant.</span></span>

### <a name="conversions"></a><span data-ttu-id="5715d-120">Conversioni</span><span class="sxs-lookup"><span data-stu-id="5715d-120">Conversions</span></span>
<span data-ttu-id="5715d-121">Esiste una conversione `nint` `IntPtr`di identità tra e , e tra `nuint` e `UIntPtr`.</span><span class="sxs-lookup"><span data-stu-id="5715d-121">There is an identity conversion between `nint` and `IntPtr`, and between `nuint` and `UIntPtr`.</span></span>
<span data-ttu-id="5715d-122">Esiste una conversione di identità tra tipi composti che differiscono solo `Nullable<>`in formato nativo e tipi sottostanti: matrici, tipi costruiti e tuple.</span><span class="sxs-lookup"><span data-stu-id="5715d-122">There is an identity conversion between compound types that differ by native ints and underlying types only: arrays, `Nullable<>`, constructed types, and tuples.</span></span>

<span data-ttu-id="5715d-123">Le tabelle seguenti riguardano le conversioni tra tipi speciali.</span><span class="sxs-lookup"><span data-stu-id="5715d-123">The tables below cover the conversions between special types.</span></span>
<span data-ttu-id="5715d-124">(Il linguaggio il per ogni `unchecked` `checked` conversione include le varianti per e i contesti se diverso.)</span><span class="sxs-lookup"><span data-stu-id="5715d-124">(The IL for each conversion includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="5715d-125">Operando</span><span class="sxs-lookup"><span data-stu-id="5715d-125">Operand</span></span> | <span data-ttu-id="5715d-126">Destinazione</span><span class="sxs-lookup"><span data-stu-id="5715d-126">Target</span></span> | <span data-ttu-id="5715d-127">Conversione</span><span class="sxs-lookup"><span data-stu-id="5715d-127">Conversion</span></span> | <span data-ttu-id="5715d-128">IL</span><span class="sxs-lookup"><span data-stu-id="5715d-128">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | <span data-ttu-id="5715d-129">Conversione unboxing</span><span class="sxs-lookup"><span data-stu-id="5715d-129">Unboxing</span></span> | `unbox` |
| `void*` | `nint` | <span data-ttu-id="5715d-130">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="5715d-130">PointerToVoid</span></span> | `conv.i` |
| `sbyte` | `nint` | <span data-ttu-id="5715d-131">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-131">ImplicitNumeric</span></span> | `conv.i` |
| `byte` | `nint` | <span data-ttu-id="5715d-132">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-132">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nint` | <span data-ttu-id="5715d-133">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-133">ImplicitNumeric</span></span> | `conv.i` |
| `ushort` | `nint` | <span data-ttu-id="5715d-134">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-134">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nint` | <span data-ttu-id="5715d-135">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-135">ImplicitNumeric</span></span> | `conv.i` |
| `uint` | `nint` | <span data-ttu-id="5715d-136">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-136">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | <span data-ttu-id="5715d-137">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-137">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | <span data-ttu-id="5715d-138">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-138">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | <span data-ttu-id="5715d-139">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-139">ImplicitNumeric</span></span> | `conv.i` |
| `float` | `nint` | <span data-ttu-id="5715d-140">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-140">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | <span data-ttu-id="5715d-141">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-141">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | <span data-ttu-id="5715d-142">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-142">ExplicitNumeric</span></span> | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | <span data-ttu-id="5715d-143">Identità</span><span class="sxs-lookup"><span data-stu-id="5715d-143">Identity</span></span> | |
| `UIntPtr` | `nint` | <span data-ttu-id="5715d-144">nessuno</span><span class="sxs-lookup"><span data-stu-id="5715d-144">None</span></span> | |
| `object` | `nuint` | <span data-ttu-id="5715d-145">Conversione unboxing</span><span class="sxs-lookup"><span data-stu-id="5715d-145">Unboxing</span></span> | `unbox` |
| `void*` | `nuint` | <span data-ttu-id="5715d-146">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="5715d-146">PointerToVoid</span></span> | `conv.u` |
| `sbyte` | `nuint` | <span data-ttu-id="5715d-147">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-147">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | <span data-ttu-id="5715d-148">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-148">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nuint` | <span data-ttu-id="5715d-149">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-149">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | <span data-ttu-id="5715d-150">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-150">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nuint` | <span data-ttu-id="5715d-151">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-151">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | <span data-ttu-id="5715d-152">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-152">ImplicitNumeric</span></span> | `conv.u` |
| `long` | `nuint` | <span data-ttu-id="5715d-153">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-153">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | <span data-ttu-id="5715d-154">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-154">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | <span data-ttu-id="5715d-155">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-155">ImplicitNumeric</span></span> | `conv.u` |
| `float` | `nuint` | <span data-ttu-id="5715d-156">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-156">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | <span data-ttu-id="5715d-157">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-157">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | <span data-ttu-id="5715d-158">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-158">ExplicitNumeric</span></span> | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | <span data-ttu-id="5715d-159">nessuno</span><span class="sxs-lookup"><span data-stu-id="5715d-159">None</span></span> | |
| `UIntPtr` | `nuint` | <span data-ttu-id="5715d-160">Identità</span><span class="sxs-lookup"><span data-stu-id="5715d-160">Identity</span></span> | |

| <span data-ttu-id="5715d-161">Operando</span><span class="sxs-lookup"><span data-stu-id="5715d-161">Operand</span></span> | <span data-ttu-id="5715d-162">Destinazione</span><span class="sxs-lookup"><span data-stu-id="5715d-162">Target</span></span> | <span data-ttu-id="5715d-163">Conversione</span><span class="sxs-lookup"><span data-stu-id="5715d-163">Conversion</span></span> | <span data-ttu-id="5715d-164">IL</span><span class="sxs-lookup"><span data-stu-id="5715d-164">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `nint` | `object` | <span data-ttu-id="5715d-165">Boxing</span><span class="sxs-lookup"><span data-stu-id="5715d-165">Boxing</span></span> | `box` |
| `nint` | `void*` | <span data-ttu-id="5715d-166">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="5715d-166">PointerToVoid</span></span> | `conv.i` |
| `nint` | `nuint` | <span data-ttu-id="5715d-167">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-167">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `nint` | `sbyte` | <span data-ttu-id="5715d-168">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-168">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nint` | `byte` | <span data-ttu-id="5715d-169">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-169">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nint` | `short` | <span data-ttu-id="5715d-170">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-170">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nint` | `ushort` | <span data-ttu-id="5715d-171">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-171">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `int` | <span data-ttu-id="5715d-172">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-172">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nint` | `uint` | <span data-ttu-id="5715d-173">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-173">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nint` | `long` | <span data-ttu-id="5715d-174">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-174">ImplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | <span data-ttu-id="5715d-175">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-175">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | <span data-ttu-id="5715d-176">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-176">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | <span data-ttu-id="5715d-177">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-177">ImplicitNumeric</span></span> | `conv.r4` |
| `nint` | `double` | <span data-ttu-id="5715d-178">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-178">ImplicitNumeric</span></span> | `conv.r8` |
| `nint` | `decimal` | <span data-ttu-id="5715d-179">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-179">ImplicitNumeric</span></span> | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | <span data-ttu-id="5715d-180">Identità</span><span class="sxs-lookup"><span data-stu-id="5715d-180">Identity</span></span> | |
| `nint` | `UIntPtr` | <span data-ttu-id="5715d-181">nessuno</span><span class="sxs-lookup"><span data-stu-id="5715d-181">None</span></span> | |
| `nuint` | `object` | <span data-ttu-id="5715d-182">Boxing</span><span class="sxs-lookup"><span data-stu-id="5715d-182">Boxing</span></span> | `box` |
| `nuint` | `void*` | <span data-ttu-id="5715d-183">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="5715d-183">PointerToVoid</span></span> | `conv.u` |
| `nuint` | `nint` | <span data-ttu-id="5715d-184">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-184">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `nuint` | `sbyte` | <span data-ttu-id="5715d-185">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-185">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nuint` | `byte` | <span data-ttu-id="5715d-186">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-186">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nuint` | `short` | <span data-ttu-id="5715d-187">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-187">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nuint` | `ushort` | <span data-ttu-id="5715d-188">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-188">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nuint` | `int` | <span data-ttu-id="5715d-189">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-189">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nuint` | `uint` | <span data-ttu-id="5715d-190">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-190">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nuint` | `long` | <span data-ttu-id="5715d-191">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-191">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nuint` | `ulong` | <span data-ttu-id="5715d-192">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-192">ImplicitNumeric</span></span> | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | <span data-ttu-id="5715d-193">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="5715d-193">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | <span data-ttu-id="5715d-194">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-194">ImplicitNumeric</span></span> | `conv.r.un conv.r4` |
| `nuint` | `double` | <span data-ttu-id="5715d-195">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-195">ImplicitNumeric</span></span> | `conv.r.un conv.r8` |
| `nuint` | `decimal` | <span data-ttu-id="5715d-196">Numerico implicito</span><span class="sxs-lookup"><span data-stu-id="5715d-196">ImplicitNumeric</span></span> | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | <span data-ttu-id="5715d-197">nessuno</span><span class="sxs-lookup"><span data-stu-id="5715d-197">None</span></span> | |
| `nuint` | `UIntPtr` | <span data-ttu-id="5715d-198">Identità</span><span class="sxs-lookup"><span data-stu-id="5715d-198">Identity</span></span> | |

<span data-ttu-id="5715d-199">Conversione `A` `Nullable<B>` da a è:</span><span class="sxs-lookup"><span data-stu-id="5715d-199">Conversion from `A` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="5715d-200">una conversione implicita nullable se è `A` presente `B`una conversione di identità o una conversione implicita da a ;</span><span class="sxs-lookup"><span data-stu-id="5715d-200">an implicit nullable conversion if there is an identity conversion or implicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="5715d-201">una conversione esplicita nullable se `A` `B`è presente una conversione esplicita da a ;</span><span class="sxs-lookup"><span data-stu-id="5715d-201">an explicit nullable conversion if there is an explicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="5715d-202">in caso contrario, non è valido.</span><span class="sxs-lookup"><span data-stu-id="5715d-202">otherwise invalid.</span></span>

<span data-ttu-id="5715d-203">Conversione `Nullable<A>` `B` da a è:</span><span class="sxs-lookup"><span data-stu-id="5715d-203">Conversion from `Nullable<A>` to `B` is:</span></span>
- <span data-ttu-id="5715d-204">una conversione esplicita a nullable se è presente `A` `B`una conversione di identità o una conversione numerica implicita o esplicita da a ;</span><span class="sxs-lookup"><span data-stu-id="5715d-204">an explicit nullable conversion if there is an identity conversion or implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="5715d-205">in caso contrario, non è valido.</span><span class="sxs-lookup"><span data-stu-id="5715d-205">otherwise invalid.</span></span>

<span data-ttu-id="5715d-206">Conversione `Nullable<A>` `Nullable<B>` da a è:</span><span class="sxs-lookup"><span data-stu-id="5715d-206">Conversion from `Nullable<A>` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="5715d-207">una conversione di identità `A` se `B`c'è una conversione di identità da a ;</span><span class="sxs-lookup"><span data-stu-id="5715d-207">an identity conversion if there is an identity conversion from `A` to `B`;</span></span>
- <span data-ttu-id="5715d-208">una conversione esplicita a valore null se `A` `B`è presente una conversione numerica implicita o esplicita da a ;</span><span class="sxs-lookup"><span data-stu-id="5715d-208">an explicit nullable conversion if there is an implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="5715d-209">in caso contrario, non è valido.</span><span class="sxs-lookup"><span data-stu-id="5715d-209">otherwise invalid.</span></span>

### <a name="operators"></a><span data-ttu-id="5715d-210">Operatori</span><span class="sxs-lookup"><span data-stu-id="5715d-210">Operators</span></span>

<span data-ttu-id="5715d-211">Gli operatori predefiniti sono i seguenti.</span><span class="sxs-lookup"><span data-stu-id="5715d-211">The predefined operators are as follows.</span></span>
<span data-ttu-id="5715d-212">Questi operatori vengono considerati durante la risoluzione dell'overload in base alle regole normali per le conversioni implicite _se almeno uno degli operandi è di tipo `nint` o `nuint` _.</span><span class="sxs-lookup"><span data-stu-id="5715d-212">These operators are considered during overload resolution based on normal rules for implicit conversions _if at least one of the operands is of type `nint` or `nuint`_.</span></span>

<span data-ttu-id="5715d-213">(Il linguaggio degli altri per `unchecked` `checked` ogni operatore include le varianti per e i contesti se diverso.)</span><span class="sxs-lookup"><span data-stu-id="5715d-213">(The IL for each operator includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="5715d-214">Unaria</span><span class="sxs-lookup"><span data-stu-id="5715d-214">Unary</span></span> | <span data-ttu-id="5715d-215">Firma operatore</span><span class="sxs-lookup"><span data-stu-id="5715d-215">Operator Signature</span></span> | <span data-ttu-id="5715d-216">IL</span><span class="sxs-lookup"><span data-stu-id="5715d-216">IL</span></span> |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint value)` | `nop` |
| `+` | `nuint operator +(nuint value)` | `nop` |
| `-` | `nint operator -(nint value)` | `neg` |
| `~` | `nint operator ~(nint value)` | `not` |
| `~` | `nuint operator ~(nuint value)` | `not` |

| <span data-ttu-id="5715d-217">Binary</span><span class="sxs-lookup"><span data-stu-id="5715d-217">Binary</span></span> | <span data-ttu-id="5715d-218">Firma operatore</span><span class="sxs-lookup"><span data-stu-id="5715d-218">Operator Signature</span></span> | <span data-ttu-id="5715d-219">IL</span><span class="sxs-lookup"><span data-stu-id="5715d-219">IL</span></span> |
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

<span data-ttu-id="5715d-220">Per alcuni operatori binari, gli operatori IL supportano tipi di operando aggiuntivi (vedere la tabella di tipo [Operando ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5).</span><span class="sxs-lookup"><span data-stu-id="5715d-220">For some binary operators, the IL operators support additional operand types (see [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5 Operand type table).</span></span>
<span data-ttu-id="5715d-221">Tuttavia, il set di tipi di operando supportati da C, è limitato per semplicità e coerenza con gli operatori esistenti nel linguaggio.</span><span class="sxs-lookup"><span data-stu-id="5715d-221">But the set of operand types supported by C# is limited for simplicity and for consistency with existing operators in the language.</span></span>

<span data-ttu-id="5715d-222">Le versioni eliminate degli operatori, in `nint?` cui `nuint?`gli argomenti e i tipi restituiti sono e , sono supportati.</span><span class="sxs-lookup"><span data-stu-id="5715d-222">Lifted versions of the operators, where the arguments and return types are `nint?` and `nuint?`, are supported.</span></span>

<span data-ttu-id="5715d-223">Le operazioni `x op= y` `x` di `y` assegnazione composta in cui o sono int nativi seguono le stesse regole di altri tipi primitivi con operatori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="5715d-223">Compound assignment operations `x op= y` where `x` or `y` are native ints follow the same rules as with other primitive types with pre-defined operators.</span></span>
<span data-ttu-id="5715d-224">In particolare l'espressione è associata `x = (T)(x op y)` come where `T` è il tipo di `x` e dove `x` viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="5715d-224">Specifically the expression is bound as `x = (T)(x op y)` where `T` is the type of `x` and where `x` is only evaluated once.</span></span>

<span data-ttu-id="5715d-225">Gli operatori di spostamento devono mascherare il numero di `sizeof(nint)` bit da spostare - `sizeof(nint)` a 5 bit se è 4 e a 6 bit se è 8.</span><span class="sxs-lookup"><span data-stu-id="5715d-225">The shift operators should mask the number of bits to shift - to 5 bits if `sizeof(nint)` is 4, and to 6 bits if `sizeof(nint)` is 8.</span></span>
<span data-ttu-id="5715d-226">(vedere [gli operatori di spostamento](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators) nelle specifiche di C.</span><span class="sxs-lookup"><span data-stu-id="5715d-226">(see [shift operators](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators) in C# spec).</span></span>

### <a name="dynamic"></a><span data-ttu-id="5715d-227">Dinamico</span><span class="sxs-lookup"><span data-stu-id="5715d-227">Dynamic</span></span>

<span data-ttu-id="5715d-228">Le conversioni e gli operatori vengono sintetizzati dal `IntPtr` `UIntPtr` compilatore e non fanno parte dei tipi e sottostanti.</span><span class="sxs-lookup"><span data-stu-id="5715d-228">The conversions and operators are synthesized by the compiler and are not part of the underlying `IntPtr` and `UIntPtr` types.</span></span>
<span data-ttu-id="5715d-229">Di conseguenza, tali conversioni e operatori non `dynamic`sono _disponibili_ dal gestore di associazione di runtime per .</span><span class="sxs-lookup"><span data-stu-id="5715d-229">As a result those conversions and operators _are not available_ from the runtime binder for `dynamic`.</span></span> 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a><span data-ttu-id="5715d-230">Membri dei tipi</span><span class="sxs-lookup"><span data-stu-id="5715d-230">Type members</span></span>

<span data-ttu-id="5715d-231">L'unico `nint` costruttore per o `nuint` è il costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="5715d-231">The only constructor for `nint` or `nuint` is the parameter-less constructor.</span></span>

<span data-ttu-id="5715d-232">I seguenti `System.IntPtr` membri `System.UIntPtr` di e `nint` `nuint` _sono esplicitamente esclusi_ da o :</span><span class="sxs-lookup"><span data-stu-id="5715d-232">The following members of `System.IntPtr` and `System.UIntPtr` _are explicitly excluded_ from `nint` or `nuint`:</span></span>
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

<span data-ttu-id="5715d-233">I restanti `System.IntPtr` `System.UIntPtr` membri di e `nint` `nuint` _sono implicitamente inclusi_ in e .</span><span class="sxs-lookup"><span data-stu-id="5715d-233">The remaining members of `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`.</span></span> <span data-ttu-id="5715d-234">Per .NET Framework 4.7.2:</span><span class="sxs-lookup"><span data-stu-id="5715d-234">For .NET Framework 4.7.2:</span></span>
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

<span data-ttu-id="5715d-235">Le interfacce `System.IntPtr` `System.UIntPtr` implementate da `nint` e `nuint`sono incluse in _modo implicito_ in e , con occorrenze dei tipi sottostanti sostituite dai tipi integer nativi corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="5715d-235">Interfaces implemented by `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`, with occurrences of the underlying types replaced by the corresponding native integer types.</span></span>
<span data-ttu-id="5715d-236">Ad `IntPtr` esempio, `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`se `nint` `ISerializable, IEquatable<nint>, IComparable<nint>`implementa , implementa .</span><span class="sxs-lookup"><span data-stu-id="5715d-236">For instance if `IntPtr` implements `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`, then `nint` implements `ISerializable, IEquatable<nint>, IComparable<nint>`.</span></span>

### <a name="overriding-hiding-and-implementing"></a><span data-ttu-id="5715d-237">Sostituzione, occultamento e implementazione</span><span class="sxs-lookup"><span data-stu-id="5715d-237">Overriding, hiding, and implementing</span></span>

<span data-ttu-id="5715d-238">`nint`e `System.IntPtr`, `nuint` `System.UIntPtr`e , sono considerati equivalenti per l'override, l'occultamento e l'implementazione.</span><span class="sxs-lookup"><span data-stu-id="5715d-238">`nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, are considered equivalent for overriding, hiding, and implementing.</span></span>

<span data-ttu-id="5715d-239">Gli overload non `nint` `System.IntPtr`possono `nuint` essere `System.UIntPtr`diversi solo da e , e e , .</span><span class="sxs-lookup"><span data-stu-id="5715d-239">Overloads cannot differ by `nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="5715d-240">Gli override e le `nint` implementazioni possono differire solo per e `System.IntPtr`, o `nuint` e `System.UIntPtr`, .</span><span class="sxs-lookup"><span data-stu-id="5715d-240">Overrides and implementations may differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="5715d-241">I metodi nascondono `nint` `System.IntPtr`altri `nuint` metodi `System.UIntPtr`che differiscono da e , o e , da soli.</span><span class="sxs-lookup"><span data-stu-id="5715d-241">Methods hide other methods that differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="5715d-242">Varie</span><span class="sxs-lookup"><span data-stu-id="5715d-242">Miscellaneous</span></span>

<span data-ttu-id="5715d-243">`nint`e `nuint` le espressioni utilizzate come indici di matrice vengono generate senza conversione.</span><span class="sxs-lookup"><span data-stu-id="5715d-243">`nint` and `nuint` expressions used as array indices are emitted without conversion.</span></span>
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

<span data-ttu-id="5715d-244">`nint`e `nuint` può essere `enum` utilizzato come tipo di base.</span><span class="sxs-lookup"><span data-stu-id="5715d-244">`nint` and `nuint` can be used as an `enum` base type.</span></span>
```C#
enum E : nint // ok
{
}
```

<span data-ttu-id="5715d-245">Le letture e le `nint` `nuint`scritture `enum` sono `nint` atomiche per i tipi , e con il tipo di base o `nuint`.</span><span class="sxs-lookup"><span data-stu-id="5715d-245">Reads and writes are atomic for types `nint`, `nuint`, and `enum` with base type `nint` or `nuint`.</span></span>

<span data-ttu-id="5715d-246">I campi `volatile` possono `nint` essere `nuint`contrassegnati per i tipi e .</span><span class="sxs-lookup"><span data-stu-id="5715d-246">Fields may be marked `volatile` for types `nint` and `nuint`.</span></span>
<span data-ttu-id="5715d-247">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 non `enum` include `System.IntPtr` con `System.UIntPtr` il tipo di base o comunque.</span><span class="sxs-lookup"><span data-stu-id="5715d-247">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 does not include `enum` with base type `System.IntPtr` or `System.UIntPtr` however.</span></span>

<span data-ttu-id="5715d-248">`default(nint)`e `new nint()` sono `(nint)0`equivalenti a .</span><span class="sxs-lookup"><span data-stu-id="5715d-248">`default(nint)` and `new nint()` are equivalent to `(nint)0`.</span></span>

<span data-ttu-id="5715d-249">`typeof(nint)` è `typeof(IntPtr)`.</span><span class="sxs-lookup"><span data-stu-id="5715d-249">`typeof(nint)` is `typeof(IntPtr)`.</span></span>

<span data-ttu-id="5715d-250">`sizeof(nint)`è supportato ma richiede la compilazione in `sizeof(IntPtr)`un contesto unsafe (come fa ).</span><span class="sxs-lookup"><span data-stu-id="5715d-250">`sizeof(nint)` is supported but requires compiling in an unsafe context (as does `sizeof(IntPtr)`).</span></span>
<span data-ttu-id="5715d-251">Il valore non è una costante in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5715d-251">The value is not a compile-time constant.</span></span>
<span data-ttu-id="5715d-252">`sizeof(nint)`viene implementato `sizeof(IntPtr)` come `IntPtr.Size`anziché .</span><span class="sxs-lookup"><span data-stu-id="5715d-252">`sizeof(nint)` is implemented as `sizeof(IntPtr)` rather than `IntPtr.Size`.</span></span>

<span data-ttu-id="5715d-253">Diagnostica del compilatore `nint` `nuint` per `nint` `nuint` i riferimenti ai tipi che coinvolgono o report o anziché `IntPtr` o `UIntPtr`.</span><span class="sxs-lookup"><span data-stu-id="5715d-253">Compiler diagnostics for type references involving `nint` or `nuint` report `nint` or `nuint` rather than `IntPtr` or `UIntPtr`.</span></span>

### <a name="metadata"></a><span data-ttu-id="5715d-254">Metadati</span><span class="sxs-lookup"><span data-stu-id="5715d-254">Metadata</span></span>

<span data-ttu-id="5715d-255">`nint`e `nuint` sono rappresentati `System.IntPtr` `System.UIntPtr`nei metadati come e .</span><span class="sxs-lookup"><span data-stu-id="5715d-255">`nint` and `nuint` are represented in metadata as `System.IntPtr` and `System.UIntPtr`.</span></span>

<span data-ttu-id="5715d-256">Riferimenti ai `nint` `nuint` tipi che includono `System.Runtime.CompilerServices.NativeIntegerAttribute` o vengono generati con a per indicare quali parti del riferimento al tipo sono native ints.</span><span class="sxs-lookup"><span data-stu-id="5715d-256">Type references that include `nint` or `nuint` are emitted with a `System.Runtime.CompilerServices.NativeIntegerAttribute` to indicate which parts of the type reference are native ints.</span></span>

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

<span data-ttu-id="5715d-257">La codifica utilizza l'approccio `DynamicAttribute`utilizzato `DynamicAttribute` per codificare , sebbene `dynamic` ovviamente stia codificando i tipi all'interno del riferimento al tipo piuttosto che i tipi nativi int.</span><span class="sxs-lookup"><span data-stu-id="5715d-257">The encoding uses the approach as used to encode `DynamicAttribute`, although obviously `DynamicAttribute` is encoding which types within the type reference are `dynamic` rather than which types are native ints.</span></span>
<span data-ttu-id="5715d-258">Se la codifica restituisce `false` una `NativeIntegerAttribute` matrice di valori, non è necessario alcun valore.</span><span class="sxs-lookup"><span data-stu-id="5715d-258">If the encoding results in an array of `false` values, no `NativeIntegerAttribute` is needed.</span></span>
<span data-ttu-id="5715d-259">Il costruttore senza parametri `NativeIntegerAttribute` genera `true` una codifica con un singolo valore.</span><span class="sxs-lookup"><span data-stu-id="5715d-259">The parameterless `NativeIntegerAttribute` constructor generates an encoding with a single `true` value.</span></span>

```C#
nuint A;                    // [NativeInteger] UIntPtr A
(Stream, nint) B;           // [NativeInteger(new[] { false, false, true })] ValueType<Stream, IntPtr> B
```

## <a name="alternatives"></a><span data-ttu-id="5715d-260">Alternativi</span><span class="sxs-lookup"><span data-stu-id="5715d-260">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="5715d-261">Un'alternativa all'approccio "type erasure" di `System.NativeInt` `System.NativeUInt`cui sopra è quello di introdurre nuovi tipi: e .</span><span class="sxs-lookup"><span data-stu-id="5715d-261">An alternative to the "type erasure" approach above is to introduce new types: `System.NativeInt` and `System.NativeUInt`.</span></span>
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
<span data-ttu-id="5715d-262">Tipi distinti consentono l'overload distinto da `IntPtr` e `ToString()`consentirebbe l'analisi distinta e .</span><span class="sxs-lookup"><span data-stu-id="5715d-262">Distinct types would allow overloading distinct from `IntPtr` and would allow distinct parsing and `ToString()`.</span></span>
<span data-ttu-id="5715d-263">Ma ci sarebbe più lavoro per CLR per gestire questi tipi in modo efficiente che vanifica lo scopo principale della funzionalità - efficienza.</span><span class="sxs-lookup"><span data-stu-id="5715d-263">But there would be more work for the CLR to handle these types efficiently which defeats the primary purpose of the feature - efficiency.</span></span>
<span data-ttu-id="5715d-264">E l'interoperabilità con il `IntPtr` codice int nativo esistente che utilizza sarebbe più difficile.</span><span class="sxs-lookup"><span data-stu-id="5715d-264">And interop with existing native int code that uses `IntPtr` would be more difficult.</span></span> 

<span data-ttu-id="5715d-265">Un'altra alternativa consiste nell'aggiungere `IntPtr` più nativo int supporto per nel framework, ma senza alcun supporto del compilatore specifico.</span><span class="sxs-lookup"><span data-stu-id="5715d-265">Another alternative is to add more native int support for `IntPtr` in the framework but without any specific compiler support.</span></span>
<span data-ttu-id="5715d-266">Eventuali nuove conversioni e operazioni aritmetiche verrebbero supportate automaticamente dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="5715d-266">Any new conversions and arithmetic operations would be supported by the compiler automatically.</span></span>
<span data-ttu-id="5715d-267">Ma il linguaggio non fornirebbe `checked` parole chiave, costanti o operazioni.</span><span class="sxs-lookup"><span data-stu-id="5715d-267">But the language would not provide keywords, constants, or `checked` operations.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="5715d-268">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="5715d-268">Design meetings</span></span>

<span data-ttu-id="5715d-269">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md</span><span class="sxs-lookup"><span data-stu-id="5715d-269">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md</span></span>
