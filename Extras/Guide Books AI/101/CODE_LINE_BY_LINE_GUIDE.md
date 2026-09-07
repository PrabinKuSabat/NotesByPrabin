# Fibonacci Dynamic Table
## Exact-Code Line-by-Line Guide, CLRS 4e Context, Load-Factor Analysis, Amortized Analysis, and C++ Notes

**Source file:** `fibonacci_dynamic_table.cpp`

This guide is based on the dynamic-table framework from:

> Cormen, Leiserson, Rivest, Stein, *Introduction to Algorithms*, 4th ed.,  
> Section 16.4, **Dynamic Tables**.

The textbook gives the general dynamic-table model and analyzes the standard
doubling/halving policy. The **Fibonacci capacity policy in this assignment is an
adaptation**, not an algorithm stated by CLRS.

---

# 1. What CLRS 4e contributes to this implementation

CLRS Section 16.4 studies a table whose storage must remain contiguous. When the
current array is too small, a larger array is allocated and all items are copied.
When enough items have been deleted, a smaller array may similarly be allocated.

CLRS defines three important attributes conceptually:

```text
T.table  -> allocated storage
T.num    -> number of items currently stored
T.size   -> number of available slots
```

This implementation maps them to:

```text
data
numberOfItems
capacityValue
```

respectively.

CLRS defines the load factor of a nonempty dynamic table as

\[
\alpha(T)=\frac{T.num}{T.size}.
\]

It also adopts the convention that an empty table has:

\[
T.num=T.size=0
\]

and defines its load factor to be 1.

That convention is implemented exactly in `loadFactor()`.

---

# 2. CLRS standard policy versus this assignment

## CLRS standard policy

For the textbook's usual dynamic table:

- expand when full;
- double the capacity;
- after deletion, contract only when load factor falls below \(1/4\);
- halve the capacity.

The \(1/4\) threshold is deliberate. Shrinking immediately near \(1/2\) can make
a short alternating sequence of insertions and deletions repeatedly expand and
contract the table, destroying constant amortized cost.

The textbook therefore introduces **hysteresis**: the expansion and contraction
thresholds are separated.

Its potential function for expansion and contraction is

\[
\Phi(T)=
\begin{cases}
2T.num-T.size, & \alpha(T)\ge1/2,\\[4pt]
T.size/2-T.num, & \alpha(T)<1/2.
\end{cases}
\]

The potential is zero at load factor \(1/2\), and it grows as the table approaches
either resizing boundary.

## Fibonacci adaptation used here

The capacities are:

\[
1,2,3,5,8,13,21,\ldots
\]

Let consecutive capacities be denoted:

\[
C_i=C_{i-1}+C_{i-2}.
\]

### Expansion

At capacity \(C_i\), if the table is full and another insertion arrives:

\[
C_i\rightarrow C_{i+1}.
\]

### Contraction

At capacity \(C_i\), after deletion the table shrinks only when

\[
T.num\le C_{i-2}.
\]

It then changes capacity to:

\[
C_{i-1}.
\]

This is the Fibonacci analogue of CLRS hysteresis.

The table does **not** shrink merely because one previous Fibonacci capacity
would be large enough. It waits until the item count reaches the capacity two
Fibonacci levels below.

---

# 3. Why this Fibonacci contraction threshold is mathematically natural

Let:

\[
\varphi=\frac{1+\sqrt5}{2}\approx1.618034.
\]

Successive Fibonacci ratios satisfy:

\[
\frac{C_{i-1}}{C_i}\rightarrow\frac1\varphi
\approx0.618034.
\]

Two-level ratios satisfy:

\[
\frac{C_{i-2}}{C_i}
\rightarrow\frac1{\varphi^2}
\approx0.381966.
\]

Therefore:

- resizing tends to place the table near load \(1/\varphi\);
- contraction is not considered until the load approaches \(1/\varphi^2\).

This gives a natural Fibonacci hysteresis band analogous to CLRS's separation
between \(1/2\) and \(1/4\).

---

# 4. Exact load-factor behavior

Suppose the current capacity is \(C_i\).

## Immediately after expansion

A full table has `C_{i-1}` items before growing from \(C_{i-1}\) to \(C_i\).
The insertion that caused growth then adds one item.

Thus load becomes:

\[
\alpha_{\text{expand}}
=
\frac{C_{i-1}+1}{C_i}
\rightarrow
\frac1\varphi.
\]

## Just above the contraction threshold

At capacity \(C_i\), contraction occurs as soon as the item count falls to
\(C_{i-2}\).

So the lowest noncontracting count is:

\[
C_{i-2}+1.
\]

Its load is:

\[
\alpha_{\min,i}
=
\frac{C_{i-2}+1}{C_i}.
\]

As \(i\) grows:

\[
\alpha_{\min,i}
\rightarrow
\frac1{\varphi^2}
\approx0.381966.
\]

## Immediately after contraction

The capacity changes:

\[
C_i\rightarrow C_{i-1}
\]

when the item count has just become approximately \(C_{i-2}\).

Therefore:

\[
\alpha_{\text{contract-after}}
=
\frac{C_{i-2}}{C_{i-1}}
\rightarrow
\frac1\varphi
\approx0.618034.
\]

## Maximum load

The table is allowed to become completely full:

\[
\alpha_{\max}=1.
\]

The next insertion triggers growth.

Therefore the asymptotic nonempty load-factor range is approximately:

\[
\boxed{
\frac1{\varphi^2}
<
\alpha
\le1
}
\]

with post-resize states near \(1/\varphi\).

---

# 5. Why the average load factor has no single theoretical constant

The **minimum and maximum** are determined by the resizing policy.

The **average** load factor depends on the operation sequence.

Examples:

- insertion-only workload spends more time climbing toward load 1;
- deletion-only phase spends more time falling toward the contraction threshold;
- an application oscillating around a particular size has a different average.

Therefore the program reports average load experimentally for the chosen workload
but does not claim that the measured average is a universal Fibonacci constant.

---

# 6. Four-stage correctness framework

For each key operation, use:

1. **Initialization**
2. **Maintenance**
3. **Progress**
4. **Termination**

For one-shot operations such as insertion, this framework applies mainly to the
copy loop and to the class invariant across calls.

The central class invariant is:

> `0 <= numberOfItems <= capacityValue`, all live items occupy
> `data[0 .. numberOfItems-1]`, and every nonzero capacity is a member of the
> Fibonacci capacity sequence.

---

## Headers and aliases — L1–L13

```cpp
   1: #include <algorithm>
   2: #include <chrono>
   3: #include <cmath>
   4: #include <cstddef>
   5: #include <iomanip>
   6: #include <iostream>
   7: #include <limits>
   8: #include <memory>
   9: #include <stdexcept>
  10: #include <vector>
  11: 
  12: using namespace std;
  13: using Clock = chrono::steady_clock;
```

The source imports algorithms, timing, mathematics, size types, terminal
formatting, numeric limits, smart pointers, exceptions, and vectors.
`Clock` abbreviates `chrono::steady_clock`.

**C++ concepts:** `#include`, namespace, type alias, monotonic benchmark clock.

## Per-operation result structure — L15–L22

```cpp
  15: struct OperationResult {
  16:     size_t actualCost = 0;
  17:     size_t copiedItems = 0;
  18:     bool resized = false;
  19:     size_t oldCapacity = 0;
  20:     size_t newCapacity = 0;
  21:     double loadAfter = 1.0;
  22: };
```

Every insert/delete returns diagnostic information: actual-cost model,
number of copied items, whether resizing occurred, old/new capacities, and the
load factor after the operation.

**Correctness / analysis role:** This keeps algorithm execution and experiment instrumentation in the same file without changing the resizing policy.

**C++ concepts:** `struct`, default member initialization, `bool`, `size_t`, `double`.

## Core dynamic-table state — L24–L35

```cpp
  24: class FibonacciDynamicTable {
  25:     vector<size_t> fibonacci;
  26:     unique_ptr<int[]> data;
  27: 
  28:     size_t numberOfItems = 0;
  29:     size_t capacityValue = 0;
  30:     size_t capacityIndex = 0;
  31: 
  32:     size_t totalActualCostValue = 0;
  33:     size_t totalCopiedItemsValue = 0;
  34:     size_t expansionCountValue = 0;
  35:     size_t contractionCountValue = 0;
```

`fibonacci` stores the legal capacities. `data` owns the current
contiguous array. The next three fields correspond closely to CLRS `T.num`,
`T.size`, and the Fibonacci capacity index. Remaining fields accumulate
experimental costs.

**Correctness / analysis role:** The representation invariant requires `numberOfItems <= capacityValue` and a Fibonacci capacity whenever capacity is nonzero.

**C++ concepts:** class-private members, `vector<size_t>`, `unique_ptr<int[]>`.

## Build the Fibonacci capacity sequence — L37–L51

```cpp
  37:     static vector<size_t> buildFibonacciCapacities() {
  38:         vector<size_t> capacities = {1, 2};
  39: 
  40:         while (true) {
  41:             const size_t a = capacities[capacities.size() - 2];
  42:             const size_t b = capacities.back();
  43: 
  44:             if (numeric_limits<size_t>::max() - b < a)
  45:                 break;
  46: 
  47:             capacities.push_back(a + b);
  48:         }
  49: 
  50:         return capacities;
  51:     }
```

The legal sequence begins `1,2` rather than `1,1`, because capacities
must strictly increase. Each later capacity is the sum of the previous two.
Before addition, the code explicitly checks whether `a+b` would overflow
`size_t`.

**Correctness / analysis role:** Loop invariant: before each iteration, `capacities` contains a valid
strictly increasing Fibonacci-style prefix. Appending `a+b` preserves the
recurrence. Progress occurs because one new larger capacity is appended each
iteration. Termination occurs when the next capacity cannot be represented.

**C++ concepts:** static member function, `numeric_limits<size_t>::max()`, overflow-safe addition, `vector::back()`.

**Viva point:** Why `1,2,3,5,...` and not `1,1,2,...`? A dynamic capacity sequence must increase; duplicate capacity 1 would perform a resize with no added storage.

## Resize and copy — L53–L69

```cpp
  53:     size_t resizeTo(size_t newCapacity) {
  54:         unique_ptr<int[]> newData;
  55: 
  56:         if (newCapacity > 0)
  57:             newData = make_unique<int[]>(newCapacity);
  58: 
  59:         for (size_t i = 0; i < numberOfItems; ++i)
  60:             newData[i] = data[i];
  61: 
  62:         const size_t copied = numberOfItems;
  63: 
  64:         data = move(newData);
  65:         capacityValue = newCapacity;
  66: 
  67:         totalCopiedItemsValue += copied;
  68:         return copied;
  69:     }
```

A fresh contiguous array is allocated with `newCapacity`. Every live
item is copied from the old storage. Ownership is then transferred to the new
array and the capacity field is updated.

**Correctness / analysis role:** Initialization: the new array exists with sufficient capacity.
Maintenance: after copy iteration `i`, positions `0..i` in the new array equal
the corresponding old live items. Progress: `i` increases. Termination: all
`numberOfItems` live items have been preserved.

**C++ concepts:** `unique_ptr<int[]>`, `make_unique`, `move`, RAII, contiguous allocation.

**Viva point:** Why is resize expensive? Copying `numberOfItems` items costs Θ(T.num), even though ordinary insert/delete is constant-time.

## Growth operation — L71–L84

```cpp
  71:     size_t grow() {
  72:         if (capacityValue == 0) {
  73:             capacityIndex = 0;
  74:             ++expansionCountValue;
  75:             return resizeTo(fibonacci[capacityIndex]);
  76:         }
  77: 
  78:         if (capacityIndex + 1 >= fibonacci.size())
  79:             throw overflow_error("No larger Fibonacci capacity available");
  80: 
  81:         ++capacityIndex;
  82:         ++expansionCountValue;
  83:         return resizeTo(fibonacci[capacityIndex]);
  84:     }
```

The empty table grows to capacity 1. Otherwise the Fibonacci index moves
one position forward and storage is resized to the next Fibonacci capacity.
An overflow exception protects the finite machine representation.

**Correctness / analysis role:** Every nonzero new capacity remains in the legal Fibonacci sequence and is strictly larger than the old capacity.

**C++ concepts:** exception throwing, vector bounds condition.

## Empty-table contraction — L86–L92

```cpp
  86:     size_t shrinkIfNeeded() {
  87:         if (numberOfItems == 0) {
  88:             const size_t copied = resizeTo(0);
  89:             capacityIndex = 0;
  90:             ++contractionCountValue;
  91:             return copied;
  92:         }
```

When the last item disappears, storage is released and the table returns
to the CLRS empty state: zero items and zero slots.

**Correctness / analysis role:** This matches the textbook convention used for dynamic-table amortized analysis.

**C++ concepts:** smart-pointer deallocation through `resizeTo(0)`.

## Fibonacci contraction hysteresis — L94–L112

```cpp
  94:         // Fibonacci hysteresis:
  95:         //
  96:         // current capacity       = F_i
  97:         // previous capacity      = F_{i-1}
  98:         // contraction threshold  = F_{i-2}
  99:         //
 100:         // Shrink only after the number of items falls to F_{i-2}.
 101:         // This leaves enough distance between expansion and contraction
 102:         // to avoid resize thrashing.
 103:         if (capacityIndex >= 2 &&
 104:             numberOfItems <= fibonacci[capacityIndex - 2]) {
 105: 
 106:             --capacityIndex;
 107:             ++contractionCountValue;
 108:             return resizeTo(fibonacci[capacityIndex]);
 109:         }
 110: 
 111:         return 0;
 112:     }
```

For current capacity `C_i`, the table shrinks to `C_{i-1}` only when the
item count reaches `C_{i-2}`. The comments deliberately state the capacity
relationship directly beside the branch.

**Correctness / analysis role:** Because `C_{i-2} < C_{i-1}`, all live items fit after contraction.
The two-level threshold also guarantees a constant-fraction number of ordinary
operations between large opposite-direction resizes.

**C++ concepts:** compound `&&` condition, indexed Fibonacci thresholds.

**Viva point:** This threshold is the key design choice. Shrinking as soon as the previous capacity fits would reduce hysteresis and can create unnecessary resizing.

## Constructor and basic observers — L114–L128

```cpp
 114: public:
 115:     FibonacciDynamicTable()
 116:         : fibonacci(buildFibonacciCapacities()) {}
 117: 
 118:     size_t size() const {
 119:         return numberOfItems;
 120:     }
 121: 
 122:     size_t capacity() const {
 123:         return capacityValue;
 124:     }
 125: 
 126:     bool empty() const {
 127:         return numberOfItems == 0;
 128:     }
```

The constructor builds the Fibonacci sequence automatically, avoiding the
earlier C++ problem of having no default constructor. `size`, `capacity`, and
`empty` are read-only observer methods.

**Correctness / analysis role:** A newly constructed table has zero items, zero capacity, and a prepared capacity policy.

**C++ concepts:** constructor initializer list, `const` member functions.

**Viva point:** A user can now write `FibonacciDynamicTable table;` because an explicit zero-argument constructor exists.

## CLRS load-factor convention — L130–L138

```cpp
 130:     // CLRS convention:
 131:     // an empty table with zero slots has load factor 1.
 132:     double loadFactor() const {
 133:         if (numberOfItems == 0)
 134:             return 1.0;
 135: 
 136:         return static_cast<double>(numberOfItems) /
 137:                static_cast<double>(capacityValue);
 138:     }
```

For a nonempty table, the implementation returns
`numberOfItems/capacityValue`. For the empty zero-slot state it returns 1,
matching CLRS's convention.

**Correctness / analysis role:** \[
\alpha(T)=T.num/T.size
\]
for nonempty tables; empty load is defined rather than divided by zero.

**C++ concepts:** `static_cast<double>` to force floating-point division.

**Viva point:** Without the casts, integer division would destroy fractional load factors.

## Indexed read and bounds checking — L140–L145

```cpp
 140:     int operator[](size_t index) const {
 141:         if (index >= numberOfItems)
 142:             throw out_of_range("Dynamic-table index out of range");
 143: 
 144:         return data[index];
 145:     }
```

The read operator verifies that the requested index is live before
returning the stored item.

**C++ concepts:** operator overloading, `out_of_range` exception.

## Insertion setup and growth condition — L147–L154

```cpp
 147:     OperationResult insert(int value) {
 148:         OperationResult result;
 149:         result.oldCapacity = capacityValue;
 150: 
 151:         if (numberOfItems == capacityValue) {
 152:             result.copiedItems += grow();
 153:             result.resized = true;
 154:         }
```

The old capacity is recorded. Growth occurs exactly when
`numberOfItems == capacityValue`, i.e. when the table is full.

**Correctness / analysis role:** A write is never attempted into a nonexistent slot: full tables grow before the new item is written.

**Viva point:** This preserves CLRS's 'expand when full' principle while changing only the target capacity.

## Elementary insertion and cost accounting — L156–L167

```cpp
 156:         data[numberOfItems] = value;
 157:         ++numberOfItems;
 158: 
 159:         // One elementary TABLE-INSERT plus all copies caused by resizing.
 160:         result.actualCost = 1 + result.copiedItems;
 161: 
 162:         totalActualCostValue += result.actualCost;
 163: 
 164:         result.newCapacity = capacityValue;
 165:         result.loadAfter = loadFactor();
 166:         return result;
 167:     }
```

The new value is written into the first free slot and `T.num` increases.
Actual cost is modeled as one elementary insertion plus every item copy caused
by growth.

**Correctness / analysis role:** If no resize occurs, actual cost is 1. If `k` live items are copied,
actual cost is `k+1`, matching the CLRS elementary-operation cost model.

**C++ concepts:** post-state diagnostics, cumulative counters.

## Constant-time unordered deletion design — L169–L183

```cpp
 169:     // Deletion does not preserve element order:
 170:     // move the last item into the deleted slot, then decrease T.num.
 171:     // This keeps the elementary deletion itself O(1), isolating the
 172:     // dynamic-table resizing cost studied in CLRS.
 173:     OperationResult eraseAt(size_t index) {
 174:         if (index >= numberOfItems)
 175:             throw out_of_range("Dynamic-table deletion index out of range");
 176: 
 177:         OperationResult result;
 178:         result.oldCapacity = capacityValue;
 179: 
 180:         if (index + 1 != numberOfItems)
 181:             data[index] = data[numberOfItems - 1];
 182: 
 183:         --numberOfItems;
```

Deletion deliberately does not preserve element order. If the erased
element is not last, the last live element is moved into its slot, then the live
count decreases.

**Correctness / analysis role:** Before resizing, all surviving elements remain packed in indices `0..numberOfItems-1`, so the table has no holes.

**C++ concepts:** swap-with-last deletion pattern.

**Viva point:** Why not shift all later elements? Shifting would make elementary deletion Θ(n) and would obscure the resizing amortized analysis that this assignment is studying.

## Deletion cost and possible contraction — L185–L201

```cpp
 185:         // One elementary TABLE-DELETE.
 186:         result.actualCost = 1;
 187: 
 188:         const size_t copied = shrinkIfNeeded();
 189: 
 190:         if (copied > 0 || capacityValue != result.oldCapacity) {
 191:             result.copiedItems = copied;
 192:             result.actualCost += copied;
 193:             result.resized = true;
 194:         }
 195: 
 196:         totalActualCostValue += result.actualCost;
 197: 
 198:         result.newCapacity = capacityValue;
 199:         result.loadAfter = loadFactor();
 200:         return result;
 201:     }
```

The elementary deletion costs one. Then `shrinkIfNeeded()` may copy live
items into the previous Fibonacci capacity. Any copied items are added to actual
cost.

**Correctness / analysis role:** Contraction occurs after deletion, consistent with the CLRS analysis framework.

**C++ concepts:** returned copy count, capacity-change detection.

## Pop-back convenience operation — L203–L208

```cpp
 203:     OperationResult popBack() {
 204:         if (numberOfItems == 0)
 205:             throw underflow_error("Cannot delete from an empty table");
 206: 
 207:         return eraseAt(numberOfItems - 1);
 208:     }
```

The benchmark deletes the last item repeatedly, but all capacity logic is
shared through `eraseAt`. Empty deletion is rejected explicitly.

**C++ concepts:** `underflow_error`, method reuse.

## Cumulative metric accessors — L210–L225

```cpp
 210:     size_t totalActualCost() const {
 211:         return totalActualCostValue;
 212:     }
 213: 
 214:     size_t totalCopiedItems() const {
 215:         return totalCopiedItemsValue;
 216:     }
 217: 
 218:     size_t expansions() const {
 219:         return expansionCountValue;
 220:     }
 221: 
 222:     size_t contractions() const {
 223:         return contractionCountValue;
 224:     }
 225: };
```

These read-only methods expose total actual cost, copies, expansion count,
and contraction count after a workload.

**C++ concepts:** small `const` accessors.

## Experiment result structure — L227–L240

```cpp
 227: struct ExperimentResult {
 228:     size_t operations = 0;
 229:     size_t totalCost = 0;
 230:     size_t totalCopies = 0;
 231:     size_t expansions = 0;
 232:     size_t contractions = 0;
 233: 
 234:     double minimumNonemptyLoad = 1.0;
 235:     double maximumLoad = 0.0;
 236:     long double loadSum = 0.0L;
 237:     size_t nonemptySamples = 0;
 238: 
 239:     double milliseconds = 0.0;
 240: };
```

The benchmark separately tracks algorithmic cost, copies, resize counts,
minimum/maximum/average load information, and elapsed milliseconds.

**Viva point:** Operation counts are the primary asymptotic evidence; wall-clock time is secondary.

## Load-factor sampling — L242–L258

```cpp
 242: void recordLoad(
 243:     const FibonacciDynamicTable& table,
 244:     ExperimentResult& result
 245: ) {
 246:     if (!table.empty()) {
 247:         const double alpha = table.loadFactor();
 248: 
 249:         result.minimumNonemptyLoad =
 250:             min(result.minimumNonemptyLoad, alpha);
 251: 
 252:         result.maximumLoad =
 253:             max(result.maximumLoad, alpha);
 254: 
 255:         result.loadSum += alpha;
 256:         ++result.nonemptySamples;
 257:     }
 258: }
```

Only nonempty states are included when reporting minimum/maximum/average
load. This avoids letting CLRS's special empty-table load factor 1 distort the
space-utilization statistics.

**Correctness / analysis role:** `minimumNonemptyLoad` directly tests the theoretical positive lower bound.

**C++ concepts:** reference parameters, `min`, `max`, long-double accumulation.

## Insertion phase of high-n experiment — L260–L273

```cpp
 260: ExperimentResult runInsertDeleteExperiment(size_t n) {
 261:     FibonacciDynamicTable table;
 262:     ExperimentResult result;
 263: 
 264:     auto start = Clock::now();
 265: 
 266:     for (size_t i = 0; i < n; ++i) {
 267:         table.insert(static_cast<int>(i));
 268: 
 269:         if (table[i] != static_cast<int>(i))
 270:             throw logic_error("Insertion/copy correctness failure");
 271: 
 272:         recordLoad(table, result);
 273:     }
```

A fresh table inserts values `0..n-1`. After every insertion it checks
that the corresponding stored value is still correct, catching copy failures
during Fibonacci expansions.

**Correctness / analysis role:** This is a correctness oracle for all monotone-growth resizes in the experiment.

**C++ concepts:** `static_cast<int>`, exception-based test failure, `chrono`.

## Deletion phase and final-state check — L275–L294

```cpp
 275:     for (size_t i = n; i > 0; --i) {
 276:         table.popBack();
 277:         recordLoad(table, result);
 278:     }
 279: 
 280:     auto end = Clock::now();
 281: 
 282:     if (!table.empty() || table.capacity() != 0)
 283:         throw logic_error("Table did not return to the empty CLRS state");
 284: 
 285:     result.operations = 2 * n;
 286:     result.totalCost = table.totalActualCost();
 287:     result.totalCopies = table.totalCopiedItems();
 288:     result.expansions = table.expansions();
 289:     result.contractions = table.contractions();
 290: 
 291:     result.milliseconds =
 292:         chrono::duration<double, milli>(end - start).count();
 293: 
 294:     return result;
```

All elements are deleted from the end. Load is sampled after each
operation. The final assertion requires both zero items and zero allocated
capacity.

**Correctness / analysis role:** This verifies contraction all the way back to the CLRS empty state.

**C++ concepts:** descending unsigned-safe loop `i > 0`, duration conversion.

## Golden ratio and theoretical load limits — L297–L317

```cpp
 297: int main() {
 298:     cout << "Fibonacci Dynamic Table\n";
 299:     cout << "Reference model: CLRS 4e Section 16.4 Dynamic Tables\n";
 300:     cout << "Adaptation: capacities 1, 2, 3, 5, 8, 13, ...\n\n";
 301: 
 302:     const long double phi =
 303:         (1.0L + sqrt(5.0L)) / 2.0L;
 304: 
 305:     const long double postResizeLimit =
 306:         1.0L / phi;
 307: 
 308:     const long double lowerLoadLimit =
 309:         1.0L / (phi * phi);
 310: 
 311:     cout << fixed << setprecision(6);
 312:     cout << "Fibonacci ratio phi                = "
 313:          << static_cast<double>(phi) << '\n';
 314:     cout << "Asymptotic post-resize load 1/phi = "
 315:          << static_cast<double>(postResizeLimit) << '\n';
 316:     cout << "Asymptotic lower trigger 1/phi^2  = "
 317:          << static_cast<double>(lowerLoadLimit) << "\n\n";
```

The program computes \(arphi\), \(1/arphi\), and
\(1/arphi^2\) directly and prints them before running experiments.

**Correctness / analysis role:** These are derived from asymptotic ratios of consecutive Fibonacci capacities.

**C++ concepts:** `sqrt`, `long double`, formatted terminal output.

## Resize trace — L319–L362

```cpp
 319:     // -------------------------------------------------------------------------
 320:     // Small trace: show exactly when Fibonacci expansions/contractions happen.
 321:     // -------------------------------------------------------------------------
 322:     FibonacciDynamicTable demo;
 323: 
 324:     cout << "Resize trace\n";
 325:     cout << left
 326:          << setw(10) << "operation"
 327:          << setw(10) << "items"
 328:          << setw(12) << "old_cap"
 329:          << setw(12) << "new_cap"
 330:          << setw(14) << "load_after"
 331:          << setw(12) << "copied"
 332:          << '\n';
 333: 
 334:     for (int value = 1; value <= 40; ++value) {
 335:         const OperationResult r = demo.insert(value);
 336: 
 337:         if (r.resized) {
 338:             cout << left
 339:                  << setw(10) << "insert"
 340:                  << setw(10) << demo.size()
 341:                  << setw(12) << r.oldCapacity
 342:                  << setw(12) << r.newCapacity
 343:                  << setw(14) << r.loadAfter
 344:                  << setw(12) << r.copiedItems
 345:                  << '\n';
 346:         }
 347:     }
 348: 
 349:     while (!demo.empty()) {
 350:         const OperationResult r = demo.popBack();
 351: 
 352:         if (r.resized) {
 353:             cout << left
 354:                  << setw(10) << "delete"
 355:                  << setw(10) << demo.size()
 356:                  << setw(12) << r.oldCapacity
 357:                  << setw(12) << r.newCapacity
 358:                  << setw(14) << r.loadAfter
 359:                  << setw(12) << r.copiedItems
 360:                  << '\n';
 361:         }
 362:     }
```

The small trace inserts 40 items and then deletes everything, printing
only operations that actually resize. This makes the sequence
`1,2,3,5,8,...` visible in the terminal together with post-resize load and
copy cost.

**Viva point:** Use this trace to explain the mechanism before discussing asymptotic data.

## High-n input sizes and table headings — L364–L389

```cpp
 364:     // -------------------------------------------------------------------------
 365:     // High-n amortized-cost and load-factor experiment.
 366:     // -------------------------------------------------------------------------
 367:     const vector<size_t> sizes = {
 368:         1'000,
 369:         10'000,
 370:         100'000,
 371:         1'000'000,
 372:         5'000'000
 373:     };
 374: 
 375:     cout << "\nHigh-n experiment: insert n items, then delete all n items\n";
 376:     cout << "Actual cost = 1 per table operation + number of copied items.\n\n";
 377: 
 378:     cout << left
 379:          << setw(12) << "n"
 380:          << setw(14) << "operations"
 381:          << setw(16) << "cost/op"
 382:          << setw(16) << "copies/op"
 383:          << setw(12) << "expands"
 384:          << setw(12) << "shrinks"
 385:          << setw(16) << "min_load"
 386:          << setw(16) << "avg_load"
 387:          << setw(16) << "max_load"
 388:          << setw(14) << "ns/op"
 389:          << '\n';
```

The experiment reaches five million live items and ten million total
table operations in the largest case. It prints normalized cost and load metrics
directly to the terminal.

**C++ concepts:** C++ digit separators such as `5'000'000`.

## Normalized amortized and load metrics — L391–L424

```cpp
 391:     for (size_t n : sizes) {
 392:         const ExperimentResult result =
 393:             runInsertDeleteExperiment(n);
 394: 
 395:         const double averageCost =
 396:             static_cast<double>(result.totalCost) /
 397:             result.operations;
 398: 
 399:         const double copiesPerOperation =
 400:             static_cast<double>(result.totalCopies) /
 401:             result.operations;
 402: 
 403:         const double averageLoad =
 404:             static_cast<double>(
 405:                 result.loadSum / result.nonemptySamples
 406:             );
 407: 
 408:         const double nanosecondsPerOperation =
 409:             result.milliseconds * 1.0e6 /
 410:             result.operations;
 411: 
 412:         cout << left
 413:              << setw(12) << n
 414:              << setw(14) << result.operations
 415:              << setw(16) << setprecision(6)
 416:              << averageCost
 417:              << setw(16) << copiesPerOperation
 418:              << setw(12) << result.expansions
 419:              << setw(12) << result.contractions
 420:              << setw(16) << result.minimumNonemptyLoad
 421:              << setw(16) << averageLoad
 422:              << setw(16) << result.maximumLoad
 423:              << setw(14) << nanosecondsPerOperation
 424:              << '\n';
```

For every `n`, the program calculates total actual cost per operation,
copies per operation, average load, and nanoseconds per operation.

**Correctness / analysis role:** If `cost/op` and `copies/op` remain bounded as `n` grows, the
observed implementation behavior is consistent with constant amortized cost.
If `min_load` approaches but does not cross \(1/\varphi^2\), the experiment
supports the derived load-factor lower bound.

**C++ concepts:** floating-point normalization, `chrono`, `setw`, `setprecision`.

## Terminal conclusions — L427–L442

```cpp
 427:     cout << "\nAnalysis\n";
 428:     cout << "1. Fibonacci capacities satisfy C[i] = C[i-1] + C[i-2].\n";
 429:     cout << "2. Growth occurs only when the table is full.\n";
 430:     cout << "3. At capacity C[i], contraction occurs only when num <= C[i-2],\n";
 431:     cout << "   and the new capacity becomes C[i-1].\n";
 432:     cout << "4. This hysteresis prevents repeated grow/shrink thrashing.\n";
 433:     cout << "5. Fibonacci sums are geometric-scale: the total number of copied\n";
 434:     cout << "   items over a monotone growth or shrink phase is O(n).\n";
 435:     cout << "6. Therefore a sequence of m insert/delete operations has O(m)\n";
 436:     cout << "   resizing work, giving O(1) amortized table operations.\n";
 437:     cout << "7. Nonempty load factor stays bounded away from zero; asymptotically\n";
 438:     cout << "   the lower threshold tends to 1/phi^2 ~= "
 439:          << static_cast<double>(lowerLoadLimit) << ".\n";
 440: 
 441:     return 0;
 442: }
```

The program summarizes recurrence, resizing thresholds, hysteresis,
aggregate-copy argument, amortized complexity, and the asymptotic lower
load-factor threshold.

**Correctness / analysis role:** These claims are justified in detail in the following sections.


# 7. Amortized analysis of Fibonacci insertion

Consider monotone growth through capacities

\[
C_0,C_1,\ldots,C_k.
\]

Whenever growth occurs, all currently stored items are copied.

The total number copied across all growth events is proportional to

\[
C_0+C_1+\cdots+C_{k-1}.
\]

A Fibonacci sum is itself on the order of the next Fibonacci term. In standard
notation,

\[
\sum_{j=1}^{m}F_j=F_{m+2}-1.
\]

Therefore:

\[
C_0+C_1+\cdots+C_{k-1}
=
O(C_k).
\]

If \(n\) insertions have occurred and the final capacity is \(C_k=\Theta(n)\),
then total resize-copy work is:

\[
O(n).
\]

The ordinary insertions themselves cost \(n\).

Hence total cost of \(n\) insertions is:

\[
O(n),
\]

and amortized insertion cost is:

\[
\boxed{O(1)}.
\]

---

# 8. Why arbitrary insertion/deletion sequences do not thrash

A monotone proof is not enough when both operation types exist.

The important question is:

> Could one expensive expansion be followed almost immediately by one expensive
> contraction, repeatedly?

The hysteresis threshold prevents this.

## Expansion after a contraction

Suppose a contraction leaves capacity \(C_i\).

The item count after that contraction is around \(C_{i-1}\).

Before the table can expand from \(C_i\) to \(C_{i+1}\), it must become full and
then receive another insertion.

Required insertions are on the order of:

\[
C_i-C_{i-1}=C_{i-2}.
\]

The expansion copies \(C_i\) items.

But:

\[
\frac{C_i}{C_{i-2}}
\rightarrow\varphi^2,
\]

a constant.

So the resize cost can be charged across a constant-proportional number of
preceding insertions.

## Contraction after an expansion

After expansion to \(C_i\), the item count is around \(C_{i-1}\).

Contraction is delayed until the count falls to \(C_{i-2}\).

The number of required deletions is therefore on the order of:

\[
C_{i-1}-C_{i-2}=C_{i-3}.
\]

The contraction copies about \(C_{i-2}\) items.

Since:

\[
\frac{C_{i-2}}{C_{i-3}}
\rightarrow\varphi,
\]

the copy cost is again only a constant multiple of the number of operations that
had to occur before the resize.

Therefore every large resize can be amortized over a linear-in-resize-size block
of ordinary operations.

For any sequence of \(m\) insertions and deletions:

\[
\boxed{\text{total resizing work}=O(m)}
\]

and hence:

\[
\boxed{\text{amortized operation cost}=O(1)}.
\]

This is the same type of objective as CLRS's standard dynamic-table analysis,
although the numerical thresholds and capacity ratios are different.

---

# 9. Why the CLRS potential function is not copied unchanged

The textbook's potential

\[
\Phi(T)=
\begin{cases}
2T.num-T.size,&\alpha\ge1/2,\\
T.size/2-T.num,&\alpha<1/2
\end{cases}
\]

is tuned to:

- doubling,
- halving,
- ideal load \(1/2\),
- contraction threshold \(1/4\).

A Fibonacci table has different geometric ratios:

\[
C_{i+1}/C_i\to\varphi
\]

rather than 2.

Blindly copying the textbook potential would obscure the actual Fibonacci
resizing geometry.

For this assignment the cleanest proof is therefore an **aggregate/hysteresis
analysis**:

- Fibonacci sums bound total monotone resize work;
- the two-level contraction threshold guarantees enough operations between
opposite-direction resizes.

The conceptual lesson from CLRS is preserved even though the exact potential
function is not reused.

---

# 10. Experimental results from the validated run

The program compiled and ran successfully with `g++ -O2`.

A representative high-n run produced:

```text
n           operations    cost/op    copies/op   expands  shrinks   min_load   avg_load   max_load
1,000       2,000         3.088500   2.088500    16       15        0.382592   0.656380   1.000000
10,000      20,000        2.432650   1.432650    20       19        0.382057   0.690806   1.000000
100,000     200,000       2.589035   1.589035    25       24        0.381974   0.671127   1.000000
1,000,000   2,000,000     2.762287   1.762287    30       29        0.381967   0.658864   1.000000
5,000,000   10,000,000    2.493035   1.493035    33       32        0.381966   0.681918   1.000000
```

## What matters

### Minimum load

It approaches:

\[
0.381966
=
1/\varphi^2.
\]

This is an exceptionally clean match between theory and experiment.

### Maximum load

It is:

\[
1.
\]

This is expected because the table is allowed to become full.

### Cost per operation

It stays at a small constant-scale value even as the workload grows to ten
million operations.

That is experimental evidence consistent with:

\[
O(1)
\]

amortized operation cost.

### Copies per operation

Also remains bounded, directly testing the expensive part of dynamic resizing.

### Number of resizes

Only dozens of resizes are needed even for millions of items because Fibonacci
capacities grow exponentially with the capacity index:

\[
C_i=\Theta(\varphi^i).
\]

Thus the number of expansion levels required to reach size \(n\) is:

\[
\Theta(\log_\varphi n)=\Theta(\log n).
\]

---

# 11. Initialization → Maintenance → Progress → Termination map

## Fibonacci-capacity builder

**Initialization:** L38 gives valid capacities `1,2`.

**Maintenance:** L41–L47 appends their sum.

**Progress:** vector length and capacity values increase.

**Termination:** L44–L45 stop before `size_t` overflow.

---

## Resize copy loop

**Initialization:** L54–L57 allocate destination storage.

**Maintenance:** L59–L60 copy one additional live item correctly.

**Progress:** index `i` increases.

**Termination:** once `i == numberOfItems`, every live item is copied.

---

## Insertion

**Initialization:** L148–L149 record current state.

**Maintenance:** L151–L154 ensure sufficient storage; L156–L157 add exactly one
new live item.

**Progress:** `numberOfItems` increases by one.

**Termination:** L164–L166 return a state with `numberOfItems <= capacityValue`.

---

## Deletion

**Initialization:** L174–L178 validate index and record old capacity.

**Maintenance:** L180–L183 preserve a packed live prefix; L188 may restore the
Fibonacci load policy through contraction.

**Progress:** `numberOfItems` decreases by one.

**Termination:** returned table is valid and, if sufficiently underloaded, has
already contracted.

---

## High-n insertion experiment

**Initialization:** empty table L261.

**Maintenance:** each insertion is checked L266–L272.

**Progress:** `i++`.

**Termination:** all `n` values have been inserted correctly.

---

## High-n deletion experiment

**Initialization:** fully populated table after L273.

**Maintenance:** each `popBack` leaves a valid Fibonacci dynamic table.

**Progress:** `i--`.

**Termination:** L282–L283 verify zero items and zero slots.

---

# 12. Precondition / postcondition map

## `insert(value)`

**Precondition:** table representation invariant holds.

**Postcondition:**

- old live items preserved;
- new value added;
- `size()` increases by exactly one;
- capacity is sufficient;
- capacity is Fibonacci or zero.

## `eraseAt(index)`

**Precondition:**

\[
0\le index<T.num.
\]

**Postcondition:**

- exactly one item removed;
- live storage remains packed;
- order may change;
- table may contract;
- surviving items fit the new Fibonacci capacity.

## `resizeTo(newCapacity)`

**Precondition:**

\[
newCapacity\ge numberOfItems.
\]

**Postcondition:**

- all live items preserved;
- storage capacity equals `newCapacity`.

---

# 13. Load-factor questions likely to be asked

## Why define empty load factor as 1?

Because `0/0` is undefined. CLRS explicitly chooses load 1 for the empty
zero-slot table so the dynamic-table formulas and conventions remain convenient.

The benchmark excludes empty states when reporting minimum/average nonempty
space utilization.

## Why is load factor important?

It measures storage utilization:

\[
\alpha=\frac{\text{used slots}}{\text{allocated slots}}.
\]

Unused fraction is:

\[
1-\alpha.
\]

If \(\alpha\) has a positive constant lower bound, unused storage can never be
arbitrarily larger than live data by more than a constant factor.

## What is the Fibonacci lower bound?

Asymptotically:

\[
\alpha_{\min}\to1/\varphi^2\approx0.381966.
\]

Thus maximum unused fraction approaches:

\[
1-\frac1{\varphi^2}
=
\frac1\varphi
\approx0.618034.
\]

So allocated capacity remains within a constant factor of the number of live
items.

## Why does load after resize approach 0.618?

Because adjacent Fibonacci capacities satisfy:

\[
C_{i-1}/C_i\to1/\varphi.
\]

## Why not shrink at 0.618?

That would place the contraction threshold too close to the post-expansion load,
reducing hysteresis. The table could resize more frequently under alternating
updates.

---

# 14. Comparison with ordinary doubling

| Property | CLRS doubling table | Fibonacci table here |
|---|---:|---:|
| Growth ratio | 2 | \(\varphi\approx1.618\) asymptotically |
| Growth capacities | 1,2,4,8,... | 1,2,3,5,8,... |
| Standard lower threshold | 1/4 | approaches \(1/\varphi^2\approx0.382\) |
| Post-resize load | near 1/2 | near \(1/\varphi\approx0.618\) |
| Amortized update | O(1) | O(1) |
| Resize frequency | lower | somewhat higher |
| Excess capacity after growth | larger | smaller |

### Tradeoff

Fibonacci growth generally allocates less spare capacity immediately after an
expansion than doubling.

The cost is that expansions occur more often.

Both remain constant-amortized because both capacity sequences grow
geometrically.

---

# 15. Advanced C++ concepts

## `unique_ptr<int[]>`

The table owns exactly one dynamically allocated array.

`unique_ptr` provides RAII:

> when ownership changes or the table is destroyed, the old array is freed
> automatically.

This avoids manual `new[]` / `delete[]` management.

## `make_unique<int[]>(n)`

Allocates an array owned by `unique_ptr`.

It is safer than writing raw allocation code.

## `move(newData)`

`unique_ptr` cannot be copied because ownership must be unique.

`move` transfers ownership from `newData` into `data`.

After the move, `newData` is empty.

## `size_t`

Used for capacities, counts, and indices because it is the standard unsigned type
for object sizes.

## `numeric_limits<size_t>::max()`

Provides the largest representable `size_t`, allowing explicit overflow checks
before calculating the next Fibonacci capacity.

## `static_cast<double>`

Required for fractional load factor and normalized metrics.

## `long double`

Used for accumulating many load-factor samples with more precision than an
ordinary `double`.

## exceptions

The implementation uses:

- `overflow_error`
- `out_of_range`
- `underflow_error`
- `logic_error`

to distinguish capacity overflow, invalid external indexing, empty deletion, and
internal experiment/correctness failures.

## `chrono`

`steady_clock` measures elapsed time.

The experiment reports nanoseconds per operation only as a secondary practical
metric.

---

# 16. Why `std::vector` is not used as the actual table storage

Using `std::vector<int>` directly for the dynamic table would hide its own
capacity-growth policy.

The assignment specifically needs to control capacity according to Fibonacci
numbers.

Therefore:

```cpp
unique_ptr<int[]> data;
```

is used for actual table storage.

A small `vector<size_t>` is still used only to store the precomputed legal
Fibonacci capacities. It is not the dynamic table being analyzed.

---

# 17. Why arbitrary deletion uses swap-with-last

CLRS's dynamic-table analysis treats elementary table deletion separately from
resizing.

If this implementation preserved array order by shifting all elements after an
erased index, deletion itself would cost:

\[
\Theta(n)
\]

even when no resize occurred.

That would answer a different question.

Instead:

```text
data[index] = data[last]
T.num--
```

keeps elementary deletion O(1).

The table is therefore an **unordered dynamic table**.

---

# 18. Operation-cost model

The experiment uses:

\[
\text{actual cost}
=
1+\text{number of items copied during resize}.
\]

This mirrors the CLRS dynamic-table abstraction:

- ordinary table insertion/deletion: unit cost;
- moving each existing item during reallocation: one additional elementary cost.

It intentionally does not count every machine instruction.

That would make the asymptotic analysis hardware/compiler dependent.

---

# 19. Why operation counts are stronger than raw timing

Timing depends on:

- allocator behavior,
- cache hierarchy,
- memory bandwidth,
- CPU frequency,
- compiler optimization,
- operating-system scheduling.

The copy count is directly tied to the mathematical source of expensive dynamic
table operations.

Therefore the strongest experimental columns are:

```text
cost/op
copies/op
min_load
```

not `ns/op`.

---

# 20. Likely viva questions

## Is this Fibonacci resizing policy from CLRS?

No.

CLRS provides the dynamic-table framework, load factor, amortized-analysis
motivation, and hysteresis principle. Its worked policy is doubling/halving.

The Fibonacci capacities and two-level Fibonacci contraction threshold are the
adaptation made for this assignment.

## Why use `1,2,3,5,...`?

The ordinary Fibonacci sequence has duplicate `1,1`. Capacities must strictly
increase, so the duplicate is omitted.

## Why contract at `C[i-2]`?

Because it creates a geometric separation between contraction and expansion and
produces a natural lower load threshold:

\[
C_{i-2}/C_i\to1/\varphi^2.
\]

## Why not shrink to exactly the number of live elements?

That would make the table full immediately after contraction. The next insertion
could immediately require another allocation.

Spare capacity is essential for amortization.

## Does Fibonacci growth improve Big-O over doubling?

No.

Both give O(1) amortized insertion/deletion.

Fibonacci growth changes constant factors and the memory-vs-resize-frequency
tradeoff.

## What is the worst-case cost of one insertion?

If it triggers resizing with n existing items:

\[
\Theta(n).
\]

The amortized cost over a sequence is O(1).

## What is amortized analysis?

It bounds the average cost per operation over **any sequence** of operations
without requiring a probability distribution on the operations.

It is not average-case probabilistic analysis.

## Why are expansions only logarithmically many?

Because:

\[
C_i=\Theta(\varphi^i).
\]

To reach capacity at least n:

\[
i=\Theta(\log_\varphi n)=\Theta(\log n).
\]

## What is the minimum nonempty load factor experimentally?

For large n the validated run approaches:

```text
0.381966
```

matching:

\[
1/\varphi^2.
\]

## What is the maximum?

1, because the table may become full before the next insertion triggers growth.

## Why is average load not equal to 1/phi?

`1/phi` describes the asymptotic post-resize region. Average load depends on how
many operations occur at each occupancy level.

---

# 21. Exact code map

| Concept | Exact source location |
|---|---:|
| operation result | L15–L22 |
| table state | L24–L35 |
| Fibonacci generation | L37–L51 |
| overflow protection | L44–L45 |
| physical resize | L53–L69 |
| copy loop | L59–L60 |
| empty growth | L72–L76 |
| next Fibonacci growth | L78–L83 |
| empty-table contraction | L87–L92 |
| Fibonacci hysteresis explanation | L94–L102 |
| contraction condition | L103–L109 |
| default constructor | L115–L116 |
| `T.num` equivalent | L28 |
| `T.size` equivalent | L29 |
| load factor | L130–L138 |
| checked index read | L140–L145 |
| insertion growth trigger | L151–L154 |
| elementary insertion | L156–L160 |
| unordered deletion rationale | L169–L172 |
| swap-with-last deletion | L180–L183 |
| contraction after deletion | L188–L194 |
| cost accumulation | L196 |
| empty deletion protection | L203–L208 |
| load experiment metrics | L227–L258 |
| insertion correctness check | L266–L273 |
| deletion workload | L275–L278 |
| final empty-state check | L282–L283 |
| golden ratio | L302–L309 |
| resize trace | L319–L362 |
| high-n sizes | L367–L373 |
| cost normalization | L395–L401 |
| average load | L403–L406 |
| timing normalization | L408–L410 |
| terminal experiment row | L412–L424 |
| final theoretical summary | L427–L439 |

---

# 22. How to compile and run

```bash
make
make run
```

Clean:

```bash
make clean
```

Compiler flags:

```text
-std=c++17
-O2
-Wall
-Wextra
-Wpedantic
```

---

# 23. Final formulas to remember

Capacity recurrence:

\[
C_i=C_{i-1}+C_{i-2}.
\]

Golden ratio:

\[
\varphi=\frac{1+\sqrt5}{2}.
\]

Post-resize load:

\[
\alpha\to1/\varphi\approx0.618034.
\]

Lower trigger:

\[
\alpha_{\min}\to1/\varphi^2\approx0.381966.
\]

Maximum load:

\[
\alpha_{\max}=1.
\]

Fibonacci growth level:

\[
C_i=\Theta(\varphi^i).
\]

Number of resize levels to capacity n:

\[
\Theta(\log n).
\]

Total resizing cost over m operations:

\[
O(m).
\]

Amortized insertion/deletion:

\[
\boxed{O(1)}.
\]

---

# 24. Best short explanation for submission/viva

> CLRS 4e Section 16.4 shows that a dynamic table should resize geometrically
> and should separate expansion and contraction thresholds so that expensive
> reallocations can be amortized over many ordinary operations. I preserve this
> principle but use Fibonacci capacities. A full table grows to the next
> Fibonacci capacity. At capacity \(C_i\), deletion contracts to \(C_{i-1}\)
> only when the number of items falls to \(C_{i-2}\). Consecutive Fibonacci
> ratios approach \(1/\varphi\), so the post-resize load approaches 0.618 and
> the lower load threshold approaches \(1/\varphi^2\approx0.382\). Fibonacci
> sums are O(the largest term), and the hysteresis guarantees a
> constant-proportional number of updates between expensive opposite-direction
> resizes. Therefore insertion and deletion remain O(1) amortized, while the
> table's unused space remains bounded by a constant fraction.
