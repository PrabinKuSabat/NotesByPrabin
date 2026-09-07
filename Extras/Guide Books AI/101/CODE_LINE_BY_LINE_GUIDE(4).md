# Quick Sort and Max-Heap Assignment — Line-by-Line Code, Proof, Complexity, Experiment, and Viva Guide

This guide refers to the **exact line numbers** of the three C++ source files in this folder:

- `problem01_quicksort_two_way.cpp`
- `problem02_quicksort_three_way.cpp`
- `problem03_heap_build_compare.cpp`

The code is intentionally kept simple. The guide adds the formal reasoning that may be asked separately.

---

# 0. Four stages used to prove an algorithm

For iterative algorithms, use:

1. **Initialization** — show the invariant is true before the first iteration.
2. **Maintenance** — assuming the invariant is true before an iteration, show the body preserves it.
3. **Progress** — show that some measure strictly moves toward completion.
4. **Termination** — show that when the loop stops, the invariant implies the required result.

For recursive Quick Sort, use the analogous structure:

1. **Initialization** — the first call represents the entire subproblem.
2. **Maintenance** — partition establishes the required split and recursive calls correctly solve the remaining subproblems.
3. **Progress** — every recursive call receives a strictly smaller subarray.
4. **Termination** — a subarray of size 0 or 1 reaches the base case.

A standard CLRS loop-invariant proof explicitly names **Initialization, Maintenance, and Termination**. Here, **Progress** is separated because it makes the termination argument easier to see.

---

# 1. Shared C++ concepts

## `#include`

A C++ header exposes library declarations.

Common headers used here:

| Header | Used for |
|---|---|
| `<algorithm>` | `swap`, `is_sorted` |
| `<chrono>` | elapsed-time measurement |
| `<cmath>` | `log2` |
| `<iomanip>` | `setw`, `fixed`, `setprecision`, `left` |
| `<iostream>` | `cout`, `cerr` |
| `<numeric>` | `iota` |
| `<random>` | `mt19937`, `uniform_int_distribution` |
| `<utility>` | `pair` |
| `<vector>` | dynamic arrays |

---

## `using namespace std;`

Allows:

```cpp
vector<int>
cout
swap(...)
```

instead of:

```cpp
std::vector<int>
std::cout
std::swap(...)
```

It is acceptable for a small assignment source file. Large production projects often avoid it to reduce namespace collisions.

---

## `using Clock = chrono::steady_clock;`

This creates a type alias:

```cpp
Clock
```

for:

```cpp
chrono::steady_clock
```

`steady_clock` is appropriate for benchmarking because it is **monotonic**. Changing the system date/time cannot make elapsed time run backward.

---

## `const vector<int>&`

Example:

```cpp
const vector<int>& values
```

means:

- `&`: pass by reference, so the vector is not copied.
- `const`: the function promises not to modify the vector.

This is a standard efficient read-only function parameter.

---

## `vector<int>&`

Example:

```cpp
vector<int>& heap
```

means the function receives the original vector and can modify it directly.

Partitioning and heap operations require this.

---

## `static_cast<int>(...)`

Example:

```cpp
static_cast<int>(heap.size())
```

`vector::size()` returns `size_t`, an unsigned integer type. The algorithms here use signed `int` indices because expressions such as:

```cpp
pivotIndex - 1
```

and:

```cpp
n / 2 - 1
```

are natural.

`static_cast` makes the conversion explicit and is preferred over a C-style cast.

For the tested sizes up to 1,000,000, conversion to `int` is safe.

---

## `long long`

Operation counters use:

```cpp
long long comparisons;
long long swaps;
```

because counts can become much larger than individual array values.

A 32-bit `int` can overflow at roughly 2.1 billion. Using `long long` gives substantially more headroom.

---

## `auto`

Example:

```cpp
auto start = Clock::now();
```

The compiler deduces the exact type.

The actual type returned by `Clock::now()` is verbose, so `auto` improves readability while remaining statically typed.

---

## `chrono::duration`

Example:

```cpp
chrono::duration<double, micro>(end - start).count()
```

means:

1. `end - start` produces an elapsed duration.
2. `duration<double, micro>` expresses it in microseconds.
3. `.count()` extracts the numerical value.

Problem 3 uses:

```cpp
chrono::duration<double, milli>
```

because the large heap experiments take longer.

---

## `mt19937 rng(9)`

`mt19937` is the Mersenne Twister pseudo-random number generator.

The fixed seed:

```cpp
9
```

means the same random sequence is generated every run. This makes the experiment **reproducible**.

It is suitable for algorithm tests, but it is not a cryptographic random generator.

---

## `uniform_int_distribution<int> valueDist(1, 10)`

Generates integers uniformly from 1 through 10 inclusive.

The small range deliberately creates many duplicate values. This is useful for contrasting two-way and three-way partitioning.

---

## Range-based `for`

Example:

```cpp
for (int& x : a)
```

iterates through every element by reference, allowing modification.

Example:

```cpp
for (int x : a)
```

copies each integer value for read-only output.

---

## `swap`

```cpp
swap(a[i], a[j]);
```

exchanges two elements.

The standard library version is clearer than manually writing:

```cpp
int temp = a[i];
a[i] = a[j];
a[j] = temp;
```

---

## `pair<int,int>` and structured binding

Problem 2 returns two indices:

```cpp
return {less, greater};
```

The caller reads them using C++17 structured binding:

```cpp
auto [equalStart, equalEnd] = ...
```

This is equivalent to storing a `pair<int,int>` and separately reading `.first` and `.second`, but it is clearer.

---

## `reserve`

Problem 3 uses:

```cpp
heap.reserve(values.size());
```

This allocates enough capacity before repeated `push_back` operations.

It does **not** change `heap.size()`.

It prevents vector reallocations from adding unrelated memory-allocation overhead to the top-down heap experiment.

---

## `size_t`

`isMaxHeap` uses:

```cpp
size_t parent
```

because `vector::size()` returns `size_t`.

It is an unsigned type intended for object sizes and array indices.

---

## `iota`

Problem 3:

```cpp
iota(values.begin(), values.end(), 1);
```

generates:

```text
1, 2, 3, ..., n
```

The ascending array is intentional: it is a worst-style input for top-down max-heap construction because each newly inserted value is larger than all previous values and therefore rises toward the root.

---

## Why operation counts are more important than only timing

Problem 3 counts:

```cpp
comparisons + swaps
```

and also measures elapsed time.

For asymptotic analysis, operation counts are more useful because they correspond directly to the mathematical work performed.

Raw time is influenced by:

- cache effects,
- branch prediction,
- CPU frequency,
- compiler optimization,
- memory allocator,
- OS scheduling,
- other processes.

Therefore:

> **Operation counts validate the complexity model; timing shows practical behavior on the current machine.**

---

# 2. Problem 1 — Quick Sort with two-way partitioning

**File:** `problem01_quicksort_two_way.cpp`

The implementation uses **Lomuto partitioning** and chooses the last element as pivot.

---

## Lines 1–6 — library headers

```cpp
1: #include <algorithm>
2: #include <chrono>
3: #include <iomanip>
4: #include <iostream>
5: #include <random>
6: #include <vector>
```

- L1: `is_sorted`, `swap`.
- L2: elapsed-time measurement.
- L3: formatted decimal output.
- L4: terminal I/O.
- L5: reproducible random input.
- L6: dynamic array storage.

---

## Lines 8–9 — namespace and clock alias

```cpp
8: using namespace std;
9: using Clock = chrono::steady_clock;
```

L9 makes later timing lines shorter.

---

## Lines 11–14 — experiment statistics

```cpp
11: struct Stats {
12:     long long comparisons = 0;
13:     long long swaps = 0;
14: };
```

A `struct` groups related counters.

The `= 0` member initializers mean:

```cpp
Stats stats;
```

automatically starts with both counters zero.

These counters do not change Quick Sort's logic; they measure its behavior.

---

# 2.1 Two-way partition

## Line 16 — function interface

```cpp
16: int twoWayPartition(vector<int>& a, int low, int high, Stats& stats) {
```

Inputs:

- `a`: array being rearranged.
- `low`, `high`: inclusive subarray boundaries.
- `stats`: operation counters.

Output:

- final pivot position.

Because `a` is passed by non-const reference, swaps directly modify the caller's vector.

---

## Line 17 — choose pivot

```cpp
17: int pivot = a[high];
```

The last element is chosen as the pivot.

This is the Lomuto convention used in the supplied Python solution.

### Consequence

Choosing the last element deterministically is simple but can produce poor splits for already sorted or adversarial data.

Worst case:

\[
T(n)=T(n-1)+\Theta(n)=\Theta(n^2)
\]

---

## Line 18 — initialize boundary

```cpp
18: int i = low - 1;
```

`i` represents the final position of the current "`<= pivot`" region.

Initially that region is empty.

### Partition invariant

At the start of each loop iteration at index `j`:

```text
a[low .. i]       <= pivot
a[i+1 .. j-1]     > pivot
a[j .. high-1]    unprocessed
a[high]            pivot
```

### Initialization

Before the first iteration:

```text
j = low
i = low - 1
```

Both processed regions are empty, so the invariant is trivially true.

---

## Lines 20–21 — scan each nonpivot element

```cpp
20: for (int j = low; j < high; ++j) {
21:     ++stats.comparisons;
```

`j < high` deliberately excludes the pivot at `a[high]`.

Every element in the subarray except the pivot is examined once.

Therefore partitioning takes:

\[
\Theta(n)
\]

for a subarray of length \(n\).

---

## Lines 23–29 — element belongs to left region

```cpp
23: if (a[j] <= pivot) {
24:     ++i;
25:     if (i != j) {
26:         swap(a[i], a[j]);
27:         ++stats.swaps;
28:     }
29: }
```

If `a[j] <= pivot`:

1. L24 enlarges the left region.
2. L26 moves the qualifying element into that region.
3. The previous element at `a[i]` is moved to the scanning position.

The `i != j` test avoids a useless self-swap.

### Maintenance

After these lines:

```text
a[low .. i] <= pivot
```

still holds.

If `a[j] > pivot`, nothing moves and the element naturally joins the "`> pivot`" region.

---

## Line 30 — end of loop body

```cpp
30: }
```

After each iteration, `j` advances automatically because the `for` header contains:

```cpp
++j
```

### Progress

The number of unprocessed nonpivot elements decreases by one every iteration.

The loop must terminate after exactly:

\[
high-low
\]

iterations.

---

## Lines 32–35 — put pivot in final position

```cpp
32: if (i + 1 != high) {
33:     swap(a[i + 1], a[high]);
34:     ++stats.swaps;
35: }
```

At loop termination:

```text
a[low .. i]       <= pivot
a[i+1 .. high-1]  > pivot
```

The pivot is still at `high`.

Swapping it with `a[i+1]` yields:

```text
a[low .. i]       <= pivot
a[i+1]             pivot
a[i+2 .. high]    > pivot
```

Therefore the pivot is now in its **final sorted position**.

---

## Line 37 — return pivot index

```cpp
37: return i + 1;
```

This index splits Quick Sort's recursive calls.

---

# 2.2 Partition correctness stages

### Initialization

L17–L18:

- pivot fixed at `a[high]`,
- "`<= pivot`" region is empty.

### Maintenance

L23–L29:

- if current value is small, extend the left region and swap it there;
- if current value is large, it remains in the right processed region.

### Progress

L20:

- `j` increases every iteration.
- unprocessed region shrinks by one.

### Termination

After L20–L30 completes:

- every nonpivot value has been classified.
- L32–L35 places pivot between the two regions.

Thus partition postcondition is established.

---

# 2.3 Recursive Quick Sort

## Lines 40–42 — base case

```cpp
40: void quickSortTwoWay(vector<int>& a, int low, int high, Stats& stats) {
41:     if (low >= high)
42:         return;
```

A subarray with:

- zero elements, or
- one element

is already sorted.

This is the recursive termination condition.

---

## Line 44 — partition current subarray

```cpp
44: int pivotIndex = twoWayPartition(a, low, high, stats);
```

After this returns, the pivot will never move again.

---

## Lines 46–47 — recursively sort both sides

```cpp
46: quickSortTwoWay(a, low, pivotIndex - 1, stats);
47: quickSortTwoWay(a, pivotIndex + 1, high, stats);
```

The pivot itself is excluded because its final location is already known.

---

# 2.4 Recursive correctness stages

### Initialization

Initial call at L70:

```cpp
quickSortTwoWay(a, 0, static_cast<int>(a.size()) - 1, stats);
```

represents the entire array.

### Maintenance

L44 establishes:

```text
left side <= pivot < right side
```

and the recursive calls sort each side.

If both recursive subarrays are sorted, then:

```text
sorted left + pivot + sorted right
```

is sorted.

### Progress

Each recursive call excludes at least the pivot.

Therefore the recursive subproblem contains fewer elements than the caller.

### Termination

L41–L42 stops recursion for subarrays of size 0 or 1.

---

# 2.5 Complexity

Partitioning one subarray costs:

\[
\Theta(n)
\]

### Balanced/average-style recurrence

\[
T(n)=2T(n/2)+\Theta(n)
\]

which gives:

\[
\Theta(n\log n)
\]

### Worst case

If the pivot is always smallest or largest:

\[
T(n)=T(n-1)+\Theta(n)
\]

Therefore:

\[
\Theta(n^2)
\]

### Stack space

- balanced recursion depth: \(\Theta(\log n)\),
- worst recursion depth: \(\Theta(n)\).

---

# 2.6 Input generation and test

## Lines 58–65

```cpp
58: mt19937 rng(9);
59: uniform_int_distribution<int> valueDist(1, 10);

61: const int n = 100;
62: vector<int> a(n);

64: for (int& x : a)
65:     x = valueDist(rng);
```

Creates 100 reproducible random integers in `[1,10]`.

Using only 10 possible values creates many duplicates.

This is actually a somewhat unfavorable situation for ordinary two-way Lomuto partitioning because equal values are repeatedly included in recursive partitions.

---

## Lines 69–71 — timing

```cpp
69: auto start = Clock::now();
70: quickSortTwoWay(a, 0, static_cast<int>(a.size()) - 1, stats);
71: auto end = Clock::now();
```

Only the sorting call is timed.

Input generation and printing are outside the measured region.

This is correct benchmark practice.

---

## Lines 73–76 — correctness oracle

```cpp
73: if (!isSorted(a)) {
74:     cerr << "Correctness failure\n";
75:     return 1;
76: }
```

`isSorted` at L50–L52 calls the standard library's `is_sorted`.

This acts as a correctness oracle.

---

## Line 83 — convert time to microseconds

```cpp
83: double us = chrono::duration<double, micro>(end - start).count();
```

The measured interval is expressed in microseconds.

---

# 3. Problem 2 — Quick Sort with three-way partitioning

**File:** `problem02_quicksort_three_way.cpp`

Three-way partitioning is also called **Dutch National Flag partitioning**.

It creates:

```text
< pivot | == pivot | unknown | > pivot
```

During execution, and finally:

```text
< pivot | == pivot | > pivot
```

This is particularly useful when many duplicate keys exist.

---

## Lines 1–7 — headers

The only important addition compared with Problem 1 is:

```cpp
6: #include <utility>
```

which provides `pair`.

---

## Lines 17–22 — function interface

```cpp
17: pair<int, int> threeWayPartition(
18:     vector<int>& a,
19:     int low,
20:     int high,
21:     Stats& stats
22: ) {
```

Unlike two-way partitioning, this function returns **two boundaries**:

```text
less      = first index equal to pivot
greater   = last index equal to pivot
```

Everything between them equals the pivot.

---

## Line 23 — pivot

```cpp
23: int pivot = a[high];
```

Same pivot policy as Problem 1.

---

## Lines 25–27 — initialize three pointers

```cpp
25: int less = low;
26: int current = low;
27: int greater = high;
```

At any point maintain:

```text
a[low .. less-1]          < pivot
a[less .. current-1]      == pivot
a[current .. greater]     unknown
a[greater+1 .. high]      > pivot
```

Initially:


```
- `< pivot` region empty,
- `== pivot` region empty,
- entire subarray unknown,
- `> pivot` region empty.
```

So the invariant is true.

---

## Line 29 — process unknown region

```cpp
29: while (current <= greater) {
```

Continue while at least one unknown element remains.

### Progress measure

The size of the unknown region is:

\[
greater-current+1
\]

Every iteration reduces it.

---

## Lines 30–32 — first comparison

```cpp
30: ++stats.comparisons;

32: if (a[current] < pivot) {
```

Classifies whether current value belongs in the left region.

---

## Lines 33–38 — `< pivot` case

```cpp
33: if (less != current) {
34:     swap(a[less], a[current]);
35:     ++stats.swaps;
36: }
37: ++less;
38: ++current;
```

The current element belongs in the left region.

After swapping:

- `< pivot` region grows by one.
- equal region boundary also moves right.
- `current` moves right because the new current position has been classified.

### Maintenance

The invariant remains true.

---

## Lines 39–42 — not smaller, test greater

```cpp
39: } else {
40:     ++stats.comparisons;

42:     if (a[current] > pivot) {
```

If the first comparison failed, value is either:

- equal to pivot, or
- greater than pivot.

A second comparison distinguishes them.

---

## Lines 43–47 — `> pivot` case

```cpp
43: if (current != greater) {
44:     swap(a[current], a[greater]);
45:     ++stats.swaps;
46: }
47: --greater;
```

The large element is moved into the right region.

### Critical detail: `current` is NOT incremented

Why?

The value swapped from `a[greater]` into `a[current]` has **not yet been classified**.

Therefore it must be examined in the next iteration.

This is a classic viva question.

---

## Lines 48–50 — == pivot case

```cpp
48: } else {
49:     ++current;
50: }
```

If value is neither less nor greater, it equals the pivot.

The equal region expands by advancing `current`.

---

## Line 52 — loop termination point

```cpp
52: }
```

When loop condition becomes false:

```text
current > greater
```

there is no unknown region left.

Hence:

```text
a[low .. less-1]      < pivot
a[less .. greater]    == pivot
a[greater+1 .. high]  > pivot
```

---

## Line 54 — return equal block

```cpp
54: return {less, greater};
```

Every element in this range is already in its final relative partition class and does not need recursive sorting.

---

# 3.1 Three-way partition correctness stages

### Initialization

L25–L27 establish four regions, with only the unknown region nonempty.

### Maintenance

Three cases:

```
- `< pivot`: L32–L38,
- `> pivot`: L42–L47,
- `== pivot`: L48–L50.
```

Each preserves the four-region invariant.

### Progress

Each iteration reduces:

\[
greater-current+1
\]

because either:

- `current++`, or
- `greater--`.

### Termination

At `current > greater`, unknown region has size zero.

Therefore all elements are correctly partitioned.

---

# 3.2 Recursive three-way Quick Sort

## Lines 57–59 — base case

```cpp
57: void quickSortThreeWay(vector<int>& a, int low, int high, Stats& stats) {
58:     if (low >= high)
59:         return;
```

Same recursive base case.

---

## Lines 61–62 — structured binding

```cpp
61: auto [equalStart, equalEnd] =
62:     threeWayPartition(a, low, high, stats);
```

The returned `pair<int,int>` is unpacked into two variables.

---

## Lines 64–65 — recursive calls

```cpp
64: quickSortThreeWay(a, low, equalStart - 1, stats);
65: quickSortThreeWay(a, equalEnd + 1, high, stats);
```

Notice what is missing:

```text
[equalStart .. equalEnd]
```

is **not recursively sorted**.

All those elements equal the pivot, so they are already correctly positioned relative to everything else.

This is exactly why three-way partitioning can outperform ordinary two-way partitioning on duplicate-heavy data.

---

# 3.3 Complexity

General Quick Sort complexity remains:

- average: \(\Theta(n\log n)\),
- worst: \(\Theta(n^2)\).

However, if many elements equal the pivot, one partition call can eliminate a large equal block from future recursion.

Extreme example:

```text
5 5 5 5 5 5 5 5
```

Three-way partition:

- one \(\Theta(n)\) pass,
- entire array falls into equal region,
- both recursive sides are empty.

Total:

\[
\Theta(n)
\]

For two-way Lomuto using `<= pivot`, the same all-equal input can repeatedly produce a partition of size \(n-1\), causing:

\[
\Theta(n^2)
\]

This is a major practical reason to use three-way partitioning for low-cardinality or duplicate-heavy keys.

---

# 3.4 Test and correctness

Lines 76–83 generate exactly the same style of input as Problem 1:

```cpp
76: mt19937 rng(9);
77: uniform_int_distribution<int> valueDist(1, 10);
...
82: for (int& x : a)
83:     x = valueDist(rng);
```

Using the same seed and distribution gives a fair qualitative comparison.

Lines 87–89 time only sorting.

Lines 91–94 verify using `is_sorted`.

---

# 4. Problem 3 — Max Heap: top-down vs bottom-up

**File:** `problem03_heap_build_compare.cpp`

This file contains:

1. `siftUp`
2. top-down heap construction
3. `siftDown`
4. bottom-up/Floyd heap construction
5. insertion into an existing heap
6. correctness checker
7. high-\(n\) performance comparison

---

# 4.1 Array representation of a binary heap

For 0-based indexing:

For node `i`:

\[
parent(i)=\left\lfloor\frac{i-1}{2}\right\rfloor
\]

\[
left(i)=2i+1
\]

\[
right(i)=2i+2
\]

A max heap satisfies:

\[
heap[parent] \ge heap[child]
\]

for every existing child.

---

# 4.2 Heap operation counters

## Lines 12–15

```cpp
12: struct HeapStats {
13:     long long comparisons = 0;
14:     long long swaps = 0;
15: };
```

Problem 3's complexity experiment uses:

```text
operations = comparisons + swaps
```

This gives a concrete machine-independent measure of heap work.

---

# 4.3 `siftUp`

## Line 17

```cpp
17: void siftUp(vector<int>& heap, int child, HeapStats& stats) {
```

Used by:

- top-down construction,
- insertion.

### Precondition

Before insertion/sift-up:

- the existing prefix is already a max heap,
- only the newly inserted node may violate heap order with its ancestors.

---

## Line 18 — continue until root

```cpp
18: while (child > 0) {
```

Root is index 0 and has no parent.

---

## Line 19 — find parent

```cpp
19: int parent = (child - 1) / 2;
```

Integer division performs floor automatically for positive values.

---

## Lines 21–23 — check heap property

```cpp
21: ++stats.comparisons;
22: if (heap[parent] >= heap[child])
23:     break;
```

If parent already dominates child, heap property is restored.

Why can the loop stop immediately?

Because before insertion, all ancestors above `parent` already satisfied heap order. Once `parent >= child`, no violation remains.

---

## Lines 25–27 — move violation upward

```cpp
25: swap(heap[parent], heap[child]);
26: ++stats.swaps;
27: child = parent;
```

If child is larger:

1. swap child with parent,
2. the possible violation moves upward,
3. continue checking at the new child index.

---

# 4.4 `siftUp` proof

### Invariant

At the start of each iteration:

> The heap property holds everywhere except possibly between `child` and its parent.

### Initialization

When an element is appended as the last leaf, no existing parent-child relationship changes.

Only the new leaf-to-parent edge may violate the max-heap property.

### Maintenance

If parent is smaller, swapping them fixes that edge.

The only possible new violation is one level higher.

Thus the invariant is preserved.

### Progress

L27 sets:

```cpp
child = parent;
```

The node moves one level closer to the root.

Heap height is finite.

### Termination

Stops because:

- node reaches root, or
- parent is already large enough.

At that point no violating edge remains.

---

# 4.5 Cost of `siftUp`

A binary heap containing \(n\) elements has height:

\[
\lfloor\log_2 n\rfloor
\]

Therefore one insertion requires at most:

\[
O(\log n)
\]

sift-up steps.

---

# 4.6 Top-down heap construction

## Lines 31–34 — interface

```cpp
31: vector<int> buildMaxHeapTopDown(
32:     const vector<int>& values,
33:     HeapStats& stats
34: ) {
```

Input vector is read-only.

A new heap is returned.

---

## Lines 35–36 — empty heap and capacity reservation

```cpp
35: vector<int> heap;
36: heap.reserve(values.size());
```

Initial heap is empty.

`reserve` prevents repeated vector reallocation.

---

## Lines 38–41 — insert one item at a time

```cpp
38: for (int value : values) {
39:     heap.push_back(value);
40:     siftUp(heap, static_cast<int>(heap.size()) - 1, stats);
41: }
```

This is exactly the **top-down** approach:

1. append one new leaf,
2. repair heap property by moving it upward.

---

## Line 43 — return completed heap

```cpp
43: return heap;
```

---

# 4.7 Top-down correctness stages

### Initialization

L35 creates an empty heap.

An empty heap is valid.

### Maintenance

Assume first \(k\) inserted values form a max heap.

For value \(k+1\):

- L39 appends it as a leaf.
- L40 calls `siftUp`.
- `siftUp` restores max-heap property.

Therefore after each loop iteration, all inserted values form a max heap.

### Progress

L38 processes one new input value each iteration.

### Termination

After all \(n\) values are inserted, every input element is in the heap and the invariant says the whole structure is a max heap.

---

# 4.8 Why top-down build is \(O(n\log n)\)

Insertion \(i\) may travel height:

\[
O(\log i)
\]

Total:

\[
\sum_{i=1}^{n} O(\log i)
\]

Since:

\[
\log i \le \log n
\]

we have:

\[
\sum_{i=1}^{n} O(\log i)
\le
nO(\log n)
\]

so:

\[
O(n\log n)
\]

For ascending input in a max heap, every newly inserted value is larger than all preceding values and rises all the way toward the root.

Thus the experiment also gets a matching lower-style behavior:

\[
\Theta(n\log n)
\]

for that input family.

---

# 4.9 `siftDown`

## Lines 46–51 — interface

```cpp
46: void siftDown(
47:     vector<int>& heap,
48:     int root,
49:     int size,
50:     HeapStats& stats
51: ) {
```

### Precondition

The left and right subtrees of `root`, if they exist, are already max heaps.

Only `root` may violate the heap property.

---

## Line 52 — while a left child exists

```cpp
52: while (2 * root + 1 < size) {
```

If there is no left child, the node is a leaf.

A binary heap cannot have a right child without a left child.

---

## Lines 53–55 — child indices

```cpp
53: int leftChild = 2 * root + 1;
54: int rightChild = leftChild + 1;
55: int largerChild = leftChild;
```

Assume left child is larger initially.

---

## Lines 57–61 — choose larger child

```cpp
57: if (rightChild < size) {
58:     ++stats.comparisons;
59:     if (heap[rightChild] > heap[leftChild])
60:         largerChild = rightChild;
61: }
```

If right child exists, compare both children.

Why choose the **larger** child?

Suppose root is smaller than both children.

If you swap root with the smaller child, the larger child can still violate:

\[
parent \ge child
\]

Swapping with the larger child guarantees the moved-up value dominates both children.

---

## Lines 63–65 — stop if root already dominates

```cpp
63: ++stats.comparisons;
64: if (heap[root] >= heap[largerChild])
65:     break;
```

Because `largerChild` is the larger of the children:

```text
root >= largerChild
```

implies root is at least both children.

No violation remains.

---

## Lines 67–69 — push small root down

```cpp
67: swap(heap[root], heap[largerChild]);
68: ++stats.swaps;
69: root = largerChild;
```

After swapping, local heap property is repaired at the old root.

The potentially too-small value has moved one level downward.

Continue there.

---

# 4.10 `siftDown` proof

### Invariant

At each iteration:

> Both child subtrees are max heaps; only the current `root` may violate heap order with its children.

### Initialization

This is the precondition when bottom-up heapify calls `siftDown`.

### Maintenance

Choose larger child.

If root is too small, swap with that larger child.

Old root position becomes valid, and only the new root position lower in the tree may remain problematic.

### Progress

L69 moves `root` down one tree level.

### Termination

Stops at:

- a leaf, or
- a node that dominates its larger child.

Then it dominates both children, so the entire subtree is a max heap.

---

# 4.11 Bottom-up heap construction

## Lines 73–74

```cpp
73: void buildMaxHeapBottomUp(vector<int>& heap, HeapStats& stats) {
74:     int n = static_cast<int>(heap.size());
```

Bottom-up construction works **in place**.

---

## Line 76 — start from last internal node

```cpp
76: for (int root = n / 2 - 1; root >= 0; --root)
```

Why:

\[
n/2-1
\]

?

For 0-based binary heap indexing, all nodes from:

\[
\lfloor n/2\rfloor
\]

through:

\[
n-1
\]

are leaves.

Leaves are already valid one-node heaps.

Therefore only internal nodes need `siftDown`.

---

## Line 77

```cpp
77: siftDown(heap, root, n, stats);
```

Process internal nodes from deepest to root.

By the time a parent is processed, both its child subtrees have already been heapified.

That supplies exactly the precondition required by `siftDown`.

---

# 4.12 Bottom-up proof

### Invariant

Before processing index `root`:

> Every node with index greater than `root` is the root of a valid max-heap subtree.

### Initialization

All indices after `n/2 - 1` are leaves.

Every leaf is trivially a max heap.

### Maintenance

L77 heapifies the subtree rooted at `root`.

Therefore after the call, `root` also becomes the root of a max heap.

### Progress

L76 decrements `root`.

One more internal node is completed each iteration.

### Termination

After processing `root = 0`, the subtree rooted at 0 is the entire array.

Therefore the whole array is a max heap.

---

# 4.13 Why bottom-up is \(O(n)\), not \(O(n\log n)\)

A loose bound would say:

- there are \(n\) nodes,
- each `siftDown` can cost \(O(\log n)\),

so \(O(n\log n)\).

That bound is correct as an **upper bound**, but it is not tight.

Most nodes are near the bottom and can move only a few levels.

Approximately:

- \(n/2\) nodes have height 0,
- \(n/4\) nodes have height 1,
- \(n/8\) nodes have height 2,
- \(n/16\) nodes have height 3,
- ...

The work is bounded by:

\[
T(n)
\le
n\sum_{h=0}^{\infty}\frac{h}{2^{h+1}}
\]

and the convergent series:

\[
\sum_{h=0}^{\infty}\frac{h}{2^{h+1}}=1
\]

Therefore:

\[
T(n)=O(n)
\]

There is also an obvious \(\Omega(n)\) requirement for constructing/processing a heap from arbitrary input in this model, so Floyd heap construction is:

\[
\Theta(n)
\]

---

# 4.14 Insertion into an existing heap

## Lines 80–83

```cpp
80: void insertIntoMaxHeap(vector<int>& heap, int value, HeapStats& stats) {
81:     heap.push_back(value);
82:     siftUp(heap, static_cast<int>(heap.size()) - 1, stats);
83: }
```

Insertion consists of:

1. append new value at the next available leaf,
2. sift it upward.

Complexity:

\[
O(\log n)
\]

The program explicitly inserts `12` at L137 after bottom-up construction.

---

# 4.15 `isMaxHeap` correctness checker

## Lines 85–98

```cpp
85: bool isMaxHeap(const vector<int>& heap) {
86:     for (size_t parent = 0; parent < heap.size(); ++parent) {
87:         size_t left = 2 * parent + 1;
88:         size_t right = left + 1;

90:         if (left < heap.size() && heap[parent] < heap[left])
91:             return false;

93:         if (right < heap.size() && heap[parent] < heap[right])
94:             return false;
95:     }

97:     return true;
98: }
```

This independently verifies every existing parent-child edge.

If any child exceeds its parent, the array is not a max heap.

This serves as the experiment's **correctness oracle**.

---

# 4.16 Adaptive repeat count

## Lines 100–105

```cpp
100: int repeatsFor(int n) {
101:     if (n <= 1000) return 200;
102:     if (n <= 10000) return 100;
103:     if (n <= 100000) return 30;
104:     return 10;
105: }
```

Small inputs run many times because they are cheap and susceptible to timer noise.

Large inputs use fewer repetitions to keep total experiment duration practical.

The operation count is deterministic for the chosen input, so reducing repeats does not weaken the growth measurement.

---

# 4.17 Demonstration heap

Lines 112–119:

```cpp
112: vector<int> demo = {4, 10, 3, 5, 1, 8, 6};

114: HeapStats topDemoStats;
115: vector<int> topDemo = buildMaxHeapTopDown(demo, topDemoStats);

117: HeapStats bottomDemoStats;
118: vector<int> bottomDemo = demo;
119: buildMaxHeapBottomUp(bottomDemo, bottomDemoStats);
```

Both methods operate on the same values.

The exact array representation of a heap need not be unique.

The important property is:

\[
parent \ge children
\]

---

## Lines 131–134 — validate both heaps

```cpp
131: if (!isMaxHeap(topDemo) || !isMaxHeap(bottomDemo)) {
132:     cerr << "Heap correctness failure\n";
133:     return 1;
134: }
```

`||` means logical OR.

Failure of either method aborts the program.

---

## Lines 136–142 — insertion validation

```cpp
136: HeapStats insertStats;
137: insertIntoMaxHeap(bottomDemo, 12, insertStats);

139: if (!isMaxHeap(bottomDemo)) {
140:     cerr << "Insertion correctness failure\n";
141:     return 1;
142: }
```

This demonstrates that an existing bottom-up heap can still use normal heap insertion afterward.

---

# 4.18 Complexity experiment

## Line 151 — input sizes

```cpp
151: const vector<int> sizes = {100, 1000, 10000, 100000, 1000000};
```

Input grows by factors of 10.

This makes asymptotic growth easy to observe.

---

## Lines 153–154 — chosen input family

```cpp
153: cout << "Worst-style benchmark input: ascending values 1..n\n";
154: cout << "This forces every top-down insertion to travel toward the root.\n\n";
```

For a max heap, ascending insertion order is intentionally difficult for top-down construction.

Every new key is the largest seen so far.

---

## Lines 169–171 — build ascending input

```cpp
169: for (int n : sizes) {
170:     vector<int> values(n);
171:     iota(values.begin(), values.end(), 1);
```

`iota` fills:

```text
1..n
```

---

## Lines 175–176 — aggregate operation counts

```cpp
175: long long topOpsTotal = 0;
176: long long bottomOpsTotal = 0;
```

Across repeated trials, counts are accumulated before averaging.

---

# 4.19 Top-down measured section

## Lines 178–190

```cpp
178: auto topStart = Clock::now();

180: for (int r = 0; r < repeats; ++r) {
181:     HeapStats stats;
182:     vector<int> heap = buildMaxHeapTopDown(values, stats);

184:     if (!isMaxHeap(heap)) {
185:         cerr << "Top-down correctness failure at n=" << n << '\n';
186:         return 1;
187:     }

189:     topOpsTotal += stats.comparisons + stats.swaps;
190: }
```

Each repeat:

1. resets stats,
2. rebuilds the heap,
3. verifies it,
4. adds comparisons + swaps.

### Important timing detail

The `isMaxHeap` validation is inside the timed range.

Therefore absolute timing includes correctness-check overhead.

That is acceptable here because **operation counts, not timing, are the primary complexity evidence**.

If precise production benchmarking were required, correctness validation would normally be performed outside the measured region or in a separate test pass.

---

# 4.20 Bottom-up measured section

## Lines 193–207

```cpp
193: auto bottomStart = Clock::now();

195: for (int r = 0; r < repeats; ++r) {
196:     HeapStats stats;
197:     vector<int> heap = values;

199:     buildMaxHeapBottomUp(heap, stats);

201:     if (!isMaxHeap(heap)) {
202:         cerr << "Bottom-up correctness failure at n=" << n << '\n';
203:         return 1;
204:     }

206:     bottomOpsTotal += stats.comparisons + stats.swaps;
207: }
```

The original `values` remains unchanged because each trial copies it into `heap`.

This is necessary because bottom-up construction modifies its input.

---

# 4.21 Average operation counts

## Lines 211–215

```cpp
211: double topOps =
212:     static_cast<double>(topOpsTotal) / repeats;

214: double bottomOps =
215:     static_cast<double>(bottomOpsTotal) / repeats;
```

`static_cast<double>` is required to perform floating-point division.

Without it, dividing two integers would first produce integer division.

---

# 4.22 Theoretical top-down term

## Lines 217–218

```cpp
217: double topTheory =
218:     static_cast<double>(n) * log2(static_cast<double>(n));
```

The experiment directly computes:

\[
n\log_2 n
\]

for comparison with measured top-down operations.

---

# 4.23 Average timings

## Lines 220–226

```cpp
220: double topMs =
221:     chrono::duration<double, milli>(topEnd - topStart).count()
222:     / repeats;

224: double bottomMs =
225:     chrono::duration<double, milli>(bottomEnd - bottomStart).count()
226:     / repeats;
```

Elapsed time over all repeats is divided by number of repeats.

This gives average milliseconds per construction.

---

# 4.24 Normalized complexity ratios

## Lines 231–238

```cpp
231: << setw(15) << fixed << setprecision(2) << topOps
232: << setw(17) << topTheory
233: << setw(16) << topOps / topTheory
234: << setw(15) << bottomOps
235: << setw(13) << n
236: << setw(16) << bottomOps / n
237: << setw(14) << topMs
238: << setw(14) << bottomMs
```

The most important experimental columns are:

### Top-down

\[
\frac{\text{measured operations}}{n\log_2n}
\]

If this stays bounded as \(n\) increases, the observed growth is consistent with:

\[
\Theta(n\log n)
\]

for this input family.

### Bottom-up

\[
\frac{\text{measured operations}}{n}
\]

If this stays bounded, observed growth is consistent with:

\[
\Theta(n)
\]

---

# 4.25 Actual observed terminal results

A run of the supplied executable produced:

```text
n         repeats  top_ops        n*log2(n)        top/theory      bottom_ops     n            bottom/n
100       200      960.00         664.39           1.44            286.00         100          2.86
1000      200      15974.00       9965.78          1.60            2974.00        1000         2.97
10000     100      227262.00      132877.12        1.71            29974.00       10000        3.00
100000    30       2937892.00     1660964.05       1.77            299968.00      100000       3.00
1000000   10       35902890.00    19931568.57      1.80            2999962.00     1000000      3.00
```

Observe:

### Top-down normalized ratio

```text
1.44
1.60
1.71
1.77
1.80
```

It remains bounded and approaches a constant-like range.

This strongly supports the predicted:

\[
\Theta(n\log n)
\]

operation growth on the ascending family.

### Bottom-up normalized ratio

```text
2.86
2.97
3.00
3.00
3.00
```

This is even clearer.

Measured operations are approximately:

\[
3n
\]

for large tested \(n\).

Therefore the experiment very cleanly supports:

\[
\Theta(n)
\]

---

# 4.26 Why experiments do not mathematically prove Big-O

A finite experiment cannot test every possible \(n\) or every possible input.

Therefore the formal proof is:

### Top-down

\[
\sum_{i=1}^{n}O(\log i)=O(n\log n)
\]

and the ascending input demonstrates matching \(\Omega(n\log n)\)-style behavior.

### Bottom-up

\[
n\sum_{h\ge0}\frac{h}{2^{h+1}}=O(n)
\]

and processing/building the arbitrary array gives linear-scale work.

The experiment confirms that the **implementation behaves consistently with the theoretical derivation** over large tested inputs.

---

# 5. Two-way vs three-way Quick Sort — key comparison

| Property | Two-way Lomuto | Three-way |
|---|---|---|
| Regions | `<= pivot`, pivot, `> pivot` | `< pivot`, `== pivot`, `> pivot` |
| Partition time | \(\Theta(n)\) | \(\Theta(n)\) |
| Average Quick Sort | \(\Theta(n\log n)\) | \(\Theta(n\log n)\) |
| Worst case | \(\Theta(n^2)\) | \(\Theta(n^2)\) |
| Duplicate-heavy arrays | Can repeatedly repartition equals | Equal block removed at once |
| All equal values | Can become \(\Theta(n^2)\) | \(\Theta(n)\) with this partition scheme |

In the actual 100-element duplicate-heavy run:

```text
Two-way:
Comparisons = 901
Swaps       = 170

Three-way:
Comparisons = 664
Swaps       = 276
```

Do not conclude only from this one run that three-way is universally faster.

It used:

- fewer comparisons,
- more swaps.

Actual execution depends on data distribution and machine behavior.

The theoretical advantage of three-way partitioning is specifically strongest when duplicate keys are common.

---

# 6. Top-down vs bottom-up heap construction

| Property | Top-down | Bottom-up |
|---|---|---|
| Method | Insert elements one by one | Heapify existing array |
| Repair operation | `siftUp` | `siftDown` |
| Start structure | Empty heap | Complete array |
| Build time | \(O(n\log n)\) | \(O(n)\) |
| In-place | This implementation returns new vector | Yes |
| Natural for streaming insertion | Yes | No, assumes array already available |
| Individual later insertion | \(O(\log n)\) | Also \(O(\log n)\) using `siftUp` |

### Important practical distinction

Bottom-up is better when:

> all \(n\) elements are already available and you want to build one heap.

Top-down is natural when:

> elements arrive incrementally and the structure must remain a heap after every insertion.

---

# 7. Likely viva questions

## Q: Why is Lomuto partition correct?

Because its invariant divides already processed elements into:

```text
<= pivot
> pivot
```

and after all nonpivot values are processed, placing the pivot between these regions gives its final sorted position.

---

## Q: Why does three-way partition not increment `current` after swapping with `greater`?

Because the replacement value coming from `greater` has not yet been classified.

Incrementing `current` would skip an unknown element and could break correctness.

See Problem 2 L42–L47.

---

## Q: Why does three-way Quick Sort help with duplicates?

The entire equal-pivot interval is excluded from recursion.

See Problem 2 L61–L65.

---

## Q: Why is a leaf automatically a heap?

A one-node subtree has no child that can violate:

\[
parent \ge child
\]

Therefore the heap property holds vacuously.

---

## Q: Why does bottom-up start at `n/2 - 1`?

All later indices are leaves in a 0-based complete binary tree.

Leaves need no heapification.

See Problem 3 L76.

---

## Q: Why choose the larger child in `siftDown`?

If root must move down, swapping with the larger child ensures the new parent dominates both children.

See L57–L67.

---

## Q: Why is top-down \(O(n\log n)\)?

Each of \(n\) insertions can move through a heap of height \(O(\log n)\).

More precisely:

\[
\sum_{i=1}^{n}O(\log i)=O(n\log n)
\]

---

## Q: Why is bottom-up \(O(n)\) even though one `siftDown` is \(O(\log n)\)?

Only a tiny fraction of nodes have large height.

Most are leaves or are near leaves.

Weighted total:

\[
\frac n4(1)+\frac n8(2)+\frac n{16}(3)+\cdots=O(n)
\]

---

## Q: What does `reserve` change?

Capacity only.

It avoids allocation/copying during repeated top-down `push_back`.

It does not create elements and does not affect the heap algorithm itself.

---

## Q: Why use operation counts and time?

Operation counts validate the asymptotic mathematical model.

Time demonstrates actual machine behavior.

The former is more portable and less noisy.

---

## Q: Why use ascending input for the heap comparison?

For max-heap top-down construction, each new item is larger than all earlier items and tends to rise through the entire heap height.

That exposes the \(\Theta(n\log n)\) behavior clearly.

Bottom-up remains linear.

---

## Q: Does a bounded experimental ratio prove complexity?

No.

It is experimental evidence consistent with the theory.

The asymptotic proof comes from the mathematical work summations.

---

# 8. Proof map by exact code location

| Concept | Code location |
|---|---|
| Two-way partition initialization | P1 L17–L18 |
| Two-way partition maintenance | P1 L20–L29 |
| Two-way partition progress | P1 L20 |
| Two-way partition termination | P1 L32–L37 |
| Quick Sort recursive base case | P1 L41–L42 |
| Quick Sort recursive progress | P1 L44–L47 |
| Three-way invariant initialization | P2 L25–L27 |
| `< pivot` maintenance | P2 L32–L38 |
| `> pivot` maintenance | P2 L42–L47 |
| `== pivot` maintenance | P2 L48–L50 |
| Three-way progress | P2 L29, L38, L47, L49 |
| Three-way termination | P2 L52–L54 |
| Equal block excluded from recursion | P2 L61–L65 |
| `siftUp` initialization/invariant | P3 L17–L21 |
| `siftUp` maintenance | P3 L22–L27 |
| `siftUp` progress | P3 L27 |
| `siftUp` termination | P3 L18/L22–L23 |
| Top-down build | P3 L31–L43 |
| `siftDown` initialization | P3 L46–L55 |
| choose larger child | P3 L57–L60 |
| `siftDown` maintenance | P3 L63–L69 |
| `siftDown` progress | P3 L69 |
| bottom-up build | P3 L73–L78 |
| insertion | P3 L80–L83 |
| correctness oracle | P3 L85–L98 |
| experiment sizes | P3 L151 |
| worst-style input | P3 L153–L154, L169–L171 |
| top-down experiment | P3 L178–L192 |
| bottom-up experiment | P3 L193–L209 |
| theoretical `n log n` term | P3 L217–L218 |
| normalized top ratio | P3 L233 |
| normalized bottom ratio | P3 L236 |
| `chrono` | P1 L69–L83; P3 L178–L226 |
| `static_cast` | P1 L70; P3 L40/L74/L82/L211–L218 |
| `mt19937` | P1 L58; P2 L76 |
| structured binding | P2 L61–L62 |
| `reserve` | P3 L36 |
| `iota` | P3 L171 |
| `size_t` | P3 L86–L88 |

---

# 9. How to compile and run

Compile all:

```bash
make
```

Run all three:

```bash
make run-all
```

Remove executables:

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

- `-std=c++17`: enables structured bindings and C++17 behavior.
- `-O2`: normal optimizing compilation.
- `-Wall -Wextra -Wpedantic`: enable useful compiler diagnostics.

---

# 10. What to remember before submission/viva

For **two-way Quick Sort**, remember:

```text
<= pivot | > pivot | unknown | pivot
```

For **three-way Quick Sort**, remember:

```text
< pivot | == pivot | unknown | > pivot
```

For **top-down heap build**, remember:

```text
append -> sift up
```

and:

\[
\sum \log i=\Theta(n\log n)
\]

For **bottom-up heap build**, remember:

```text
start at last internal node -> sift down toward root
```

and:

\[
\sum_h \frac{nh}{2^{h+1}}=\Theta(n)
\]

For the experiment, remember the two normalized quantities printed to the terminal:

\[
\frac{\text{top operations}}{n\log_2n}
\]

and:

\[
\frac{\text{bottom operations}}{n}
\]

Their bounded behavior is the experimental evidence that agrees with the theoretical complexity.
