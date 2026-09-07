# Counting Sort vs Radix Sort for Student Heights
## Line-by-Line Code, Correctness Proof, Complexity Experiment, and C++ Viva Guide

**Source file:** `student_height_sort_compare.cpp`

This guide refers to the exact line numbers of the source file in this folder.

---

# 0. Submission-history check

The sent assignment emails to N. Uday Kiran were checked before writing this program.

No previously submitted **Counting Sort** or **Radix Sort** implementation was found in those assignment submissions. Therefore this file is a fresh C++ implementation rather than a replacement of an already-submitted version.

---

# 1. Problem statement

Sort student heights using:

1. **Counting Sort**
2. **Radix Sort**

Then compare their performance.

The implementation assumes student heights are integers in:

\[
140 \le h \le 210
\]

centimeters.

The benchmark tests:

\[
n=100,\ 1000,\ 10000,\ 100000,\ 1000000
\]

and prints all results directly in the terminal.

No graph or CSV file is generated.

---

# 2. Main theoretical result

Let:

- \(n\) = number of student heights,
- \(k\) = number of possible height values,
- \(d\) = number of decimal digits,
- \(b\) = radix base.

For the chosen height range:

\[
k = 210-140+1 = 71
\]

and every height has 3 decimal digits, so:

\[
d=3,\qquad b=10
\]

## Counting Sort

General:

\[
\Theta(n+k)
\]

Here \(k=71\) is fixed, so:

\[
\Theta(n+71)=\Theta(n)
\]

## Radix Sort

General LSD radix sort with stable counting sort:

\[
\Theta(d(n+b))
\]

Here:

\[
d=3,\qquad b=10
\]

so:

\[
\Theta(3(n+10))=\Theta(n)
\]

Both are asymptotically linear for this specific problem.

However, Counting Sort is expected to be substantially faster because the domain is extremely narrow and it needs only one direct frequency-counting reconstruction, whereas Radix Sort performs three stable digit passes.

---

# 3. Four stages of correctness

For each important loop, use:

1. **Initialization**
2. **Maintenance**
3. **Progress**
4. **Termination**

CLRS normally lists Initialization, Maintenance, and Termination for loop invariants. Progress is separated here because it explicitly explains why the loop cannot continue forever.

---

# 4. C++ concepts used in this program

## `constexpr`

Lines 13–16:

```cpp
constexpr int MIN_HEIGHT = 140;
constexpr int MAX_HEIGHT = 210;
constexpr int HEIGHT_RANGE = MAX_HEIGHT - MIN_HEIGHT + 1;
constexpr int RADIX_BASE = 10;
```

`constexpr` means the value is intended to be known at compile time.

This is stronger than an ordinary mutable variable.

Because `HEIGHT_RANGE` is compile-time constant, it can be used as the size of:

```cpp
array<int, HEIGHT_RANGE>
```

---

## `std::array`

Counting Sort uses:

```cpp
array<int, HEIGHT_RANGE> count{};
```

Unlike `vector`, `array` has a size known at compile time.

For this problem:

```text
HEIGHT_RANGE = 71
```

so it is ideal for the fixed frequency table.

The `{}` value-initializes all entries to zero.

Thus:

```cpp
array<int, HEIGHT_RANGE> count{};
```

is conceptually equivalent to starting with 71 zero counters.

---

## `vector<int>&`

The sorting functions receive:

```cpp
vector<int>& heights
```

The ampersand means reference.

The original vector is modified directly without copying.

---

## `const vector<int>&`

The validation function receives:

```cpp
const vector<int>& heights
```

This avoids a copy and prevents modification.

---

## `long long`

Operation totals may become larger than normal 32-bit integer range, so:

```cpp
long long workUnits
```

is used.

---

## Digit separators

C++ allows:

```cpp
1'000
10'000
100'000
1'000'000
```

The apostrophes improve readability and do not alter the numerical value.

---

## `static_cast`

Example:

```cpp
static_cast<int>(values.size())
```

and:

```cpp
static_cast<double>(countingOpsTotal)
```

`static_cast` performs an explicit type conversion.

### Why convert `values.size()`?

`vector::size()` returns `size_t`, an unsigned type.

The reverse Radix Sort loop needs:

```cpp
i >= 0
```

so a signed integer index is convenient.

### Why convert operation totals to `double`?

Without the cast:

```cpp
integer / integer
```

would use integer division.

With:

```cpp
static_cast<double>(...)
```

the result keeps fractional values.

---

## `size_t`

Line 71:

```cpp
for (size_t i = 0; i < values.size(); ++i)
```

`size_t` is the standard unsigned type used for sizes and nonnegative indices.

It matches the return type of `vector::size()`.

---

## `chrono::steady_clock`

Line 11:

```cpp
using Clock = chrono::steady_clock;
```

Used for elapsed-time measurement.

`steady_clock` is monotonic, so wall-clock adjustments cannot make benchmark time move backward.

---

## `chrono::duration<double, milli>`

Example lines 214–215:

```cpp
chrono::duration<double, milli>(end - start).count();
```

This converts the elapsed duration into milliseconds represented as `double`.

---

## `mt19937`

Line 115:

```cpp
mt19937 rng(9);
```

Mersenne Twister pseudo-random generator.

Seed `9` makes the random sequence reproducible.

---

## `uniform_int_distribution`

Line 116:

```cpp
uniform_int_distribution<int> heightDist(MIN_HEIGHT, MAX_HEIGHT);
```

Every integer height from 140 through 210 has equal probability.

---

## Lambda expression

Lines 97–99:

```cpp
[](int h) {
    return MIN_HEIGHT <= h && h <= MAX_HEIGHT;
}
```

This is an anonymous function.

It is passed to `all_of` to test every height.

---

## `all_of`

Lines 94–100:

```cpp
return all_of(...);
```

Returns `true` only if the predicate is true for every element.

---

## `max_element`

Line 84:

```cpp
int maximum = *max_element(heights.begin(), heights.end());
```

`max_element` returns an iterator.

The leading `*` dereferences it to obtain the actual largest height.

---

## Ternary operator

Line 263:

```cpp
(countingMs > 0.0 ? radixMs / countingMs : 0.0)
```

Syntax:

```text
condition ? value_if_true : value_if_false
```

It prevents division by zero if a timing measurement were ever reported as exactly zero.

---

## Output formatting

Lines 252–264 use:

- `left`
- `setw`
- `fixed`
- `setprecision`

These only affect terminal formatting.

They do not change the sorting algorithms.

---

# 5. Lines 1–8 — headers

```cpp
1: #include <algorithm>
2: #include <array>
3: #include <chrono>
4: #include <cmath>
5: #include <iomanip>
6: #include <iostream>
7: #include <random>
8: #include <vector>
```

### Line 1

Provides:

- `sort`
- `is_sorted`
- `all_of`
- `max_element`

### Line 2

Provides fixed-size `array`.

### Line 3

Provides benchmark timing.

### Line 4

Mathematical utilities. The current file does not require a complicated function from it after simplification, but retaining it is harmless.

### Line 5

Terminal table formatting.

### Line 6

Terminal input/output streams.

### Line 7

Pseudo-random input generation.

### Line 8

Dynamic array storage.

---

# 6. Lines 10–16 — aliases and problem constants

```cpp
10: using namespace std;
11: using Clock = chrono::steady_clock;

13: constexpr int MIN_HEIGHT = 140;
14: constexpr int MAX_HEIGHT = 210;
15: constexpr int HEIGHT_RANGE = MAX_HEIGHT - MIN_HEIGHT + 1;
16: constexpr int RADIX_BASE = 10;
```

The important mathematical value is:

\[
HEIGHT\_RANGE=210-140+1=71
\]

The `+1` is required because both endpoints are valid heights.

Without it, the range size would incorrectly be 70.

---

# 7. Lines 18–21 — statistics

```cpp
18: struct SortStats {
19:     long long workUnits = 0;
20:     int passes = 0;
21: };
```

`workUnits` counts abstract operations used for empirical complexity validation.

`passes` is mainly relevant to Radix Sort.

For the 140–210 range it should be:

```text
3
```

for every nonempty dataset.

---

# 8. Counting Sort

## Lines 23–24 — function and count array

```cpp
23: void countingSortHeights(vector<int>& heights, SortStats& stats) {
24:     array<int, HEIGHT_RANGE> count{};
```

### Precondition

Every height must satisfy:

\[
140\le h\le210
\]

because line 27 computes:

```cpp
height - MIN_HEIGHT
```

and uses the result as an array index.

### Postcondition

`heights` contains the same multiset of heights in nondecreasing order.

---

# 8.1 Lines 26–29 — frequency counting

```cpp
26: for (int height : heights) {
27:     ++count[height - MIN_HEIGHT];
28:     ++stats.workUnits;
29: }
```

For each input height:

```text
bucket index = height - 140
```

Examples:

```text
140 -> 0
141 -> 1
...
210 -> 70
```

Therefore every legal height maps to one of exactly 71 buckets.

---

## Counting-loop invariant

Before each iteration:

> `count[x]` equals the number of already-processed students whose height is `x + MIN_HEIGHT`.

### Initialization

Before the first element:

```text
all counters = 0
```

No elements have been processed, so the invariant is true.

### Maintenance

Suppose the invariant is true before processing `height`.

Line 27 increments exactly the bucket corresponding to that height.

Therefore the counter table correctly represents the enlarged processed prefix.

### Progress

The range-based `for` advances to one new student every iteration.

### Termination

After all \(n\) students are processed:

> `count[b]` equals the total number of occurrences of height `b + 140`.

This is the exact frequency table needed for reconstruction.

---

# 8.2 Line 31 — output index

```cpp
31: int out = 0;
```

`out` indicates the next position of the original vector to overwrite.

---

# 8.3 Lines 33–42 — sorted reconstruction

```cpp
33: for (int bucket = 0; bucket < HEIGHT_RANGE; ++bucket) {
34:     ++stats.workUnits;

36:     while (count[bucket] > 0) {
37:         heights[out++] = bucket + MIN_HEIGHT;
38:         --count[bucket];

40:         ++stats.workUnits;
41:     }
42: }
```

Buckets are processed from:

```text
0 -> 70
```

which corresponds to heights:

```text
140 -> 210
```

Therefore values are written in ascending order.

---

## Reconstruction invariant

Before processing bucket `b`:

> The prefix `heights[0 .. out-1]` contains exactly all heights belonging to earlier buckets, in sorted order.

### Initialization

At:

```cpp
int out = 0;
```

the sorted output prefix is empty.

### Maintenance

For bucket `b`, write:

```cpp
b + MIN_HEIGHT
```

exactly `count[b]` times.

Because buckets are increasing, all newly written values are at least as large as previous values.

### Progress

Either:

- `count[bucket]` decreases, or
- outer `bucket` increases.

Both are finite.

### Termination

After bucket 70:

- all counts have been written,
- exactly \(n\) output positions have been filled,
- output is nondecreasing.

Hence Counting Sort is correct.

---

# 8.4 Exact operation count of this implementation

The chosen `workUnits` model counts:

### Frequency phase

One unit per input item:

\[
n
\]

### Bucket phase

One unit per bucket:

\[
k
\]

### Reconstruction

One unit per written item:

\[
n
\]

Therefore:

\[
W_C(n,k)=2n+k
\]

For this problem:

\[
W_C(n)=2n+71
\]

This explains the terminal result at \(n=1,000,000\):

```text
count_ops = 2,000,071
```

Exactly:

\[
2(1,000,000)+71
=
2,000,071
\]

The benchmark normalizes by:

\[
n+k
\]

so:

\[
\frac{2n+k}{n+k}
\to 2
\]

as \(n\to\infty\).

This is why the observed terminal ratio approaches `2.000`.

---

# 8.5 Counting Sort complexity

General time:

\[
\Theta(n+k)
\]

For fixed \(k=71\):

\[
\Theta(n)
\]

Extra space here:

\[
\Theta(k)
\]

because only the count table is required.

### Stability note

This implementation sorts **only integer heights** and reconstructs values directly.

It does not preserve the identity/order of students having equal heights.

If the records were:

```text
{name, height}
```

and stable ordering among equal-height students mattered, use the standard stable Counting Sort output-array form.

For this assignment, only heights are being sorted, so direct reconstruction is more efficient and appropriate.

---

# 9. Radix Sort helper — stable Counting Sort by one digit

Radix Sort cannot arbitrarily sort each digit.

The per-digit sort must be **stable**.

Why?

After sorting by units digit, the later tens-digit pass must preserve units ordering among equal tens digits.

The same condition continues through hundreds.

---

# 9.1 Lines 45–50 — interface

```cpp
45: void countingSortByDigit(
46:     vector<int>& values,
47:     int exponent,
48:     vector<int>& output,
49:     SortStats& stats
50: ) {
```

`exponent` determines the digit:

```text
1   -> units
10  -> tens
100 -> hundreds
```

`output` is reused between passes.

---

# 9.2 Line 51 — ten digit buckets

```cpp
51: array<int, RADIX_BASE> count{};
```

Because decimal digits are:

```text
0..9
```

there are exactly:

\[
b=10
\]

buckets.

---

# 9.3 Lines 53–57 — count current digits

```cpp
53: for (int value : values) {
54:     int digit = (value / exponent) % RADIX_BASE;
55:     ++count[digit];
56:     ++stats.workUnits;
57: }
```

Digit extraction:

\[
digit=\left\lfloor\frac{value}{exponent}\right\rfloor\bmod10
\]

Example for:

```text
value = 175
```

### Units

```text
exponent = 1
175 / 1 % 10 = 5
```

### Tens

```text
exponent = 10
175 / 10 % 10 = 7
```

### Hundreds

```text
exponent = 100
175 / 100 % 10 = 1
```

---

# 9.4 Lines 59–62 — prefix sums

```cpp
59: for (int digit = 1; digit < RADIX_BASE; ++digit) {
60:     count[digit] += count[digit - 1];
61:     ++stats.workUnits;
62: }
```

Before this loop:

```text
count[d] = frequency of digit d
```

After this loop:

```text
count[d] = number of elements whose digit <= d
```

Thus `count[d]` identifies the end position of digit `d` in the stable output.

---

## Prefix-sum invariant

Before processing digit `d`:

> `count[d-1]` already equals the cumulative number of elements with digits at most `d-1`.

Line 60 adds that cumulative total to the frequency of `d`.

Therefore `count[d]` becomes cumulative as well.

---

# 9.5 Lines 64–69 — stable output construction

```cpp
64: for (int i = static_cast<int>(values.size()) - 1; i >= 0; --i) {
65:     int digit = (values[i] / exponent) % RADIX_BASE;
66:     output[--count[digit]] = values[i];

68:     ++stats.workUnits;
69: }
```

This loop runs **right to left**.

That direction is essential for stability.

---

## Why pre-decrement?

Suppose:

```text
count[d] = 8
```

means positions before index 8 contain all values up through digit `d`.

The last available zero-based position for digit `d` is:

```text
7
```

Therefore:

```cpp
--count[digit]
```

first changes 8 to 7, then uses index 7.

---

## Why reverse traversal gives stability

Suppose two equal-digit values appear:

```text
A ... B
```

with `A` earlier than `B`.

Scanning right-to-left places `B` into the later free slot first.

Then `A` receives the earlier slot.

Thus their original relative order is preserved:

```text
A ... B
```

after the digit pass.

This is exactly stability.

---

# 9.6 Lines 71–75 — copy output back

```cpp
71: for (size_t i = 0; i < values.size(); ++i) {
72:     values[i] = output[i];

74:     ++stats.workUnits;
75: }
```

After this pass, `values` is sorted with respect to all digits processed so far.

---

# 9.7 Line 77 — pass counter

```cpp
77: ++stats.passes;
```

One complete stable digit sort has finished.

For heights 140–210:

```text
passes = 3
```

---

# 10. Radix Sort driver

## Lines 80–82 — empty case

```cpp
80: void radixSortHeights(vector<int>& heights, SortStats& stats) {
81:     if (heights.empty())
82:         return;
```

An empty vector is already sorted.

The check also prevents dereferencing `max_element` on an empty range.

---

# 10.1 Lines 84–85 — find maximum

```cpp
84: int maximum = *max_element(heights.begin(), heights.end());
85: stats.workUnits += static_cast<long long>(heights.size());
```

The maximum determines how many digits need to be processed.

The program counts the maximum scan as approximately one work unit per element.

---

# 10.2 Line 87 — reusable output buffer

```cpp
87: vector<int> output(heights.size());
```

Radix Sort needs temporary storage of size:

\[
\Theta(n)
\]

for stable digit sorting.

The buffer is allocated once and reused across all digit passes.

That is better than allocating a new buffer inside each pass.

---

# 10.3 Lines 89–90 — digit passes

```cpp
89: for (int exponent = 1; maximum / exponent > 0; exponent *= RADIX_BASE)
90:     countingSortByDigit(heights, exponent, output, stats);
```

Exponent sequence:

```text
1
10
100
1000
...
```

For values up to 210:

```text
1, 10, 100
```

are processed.

At:

```text
exponent = 1000
```

we get:

```text
maximum / 1000 = 0
```

so the loop terminates.

---

# 10.4 Radix Sort correctness invariant

After finishing the pass for exponent \(10^j\):

> The array is stably sorted by the lowest \(j+1\) decimal digits.

### Initialization

After the units pass:

> array is sorted by units digit.

### Maintenance

Assume the array is sorted by all lower digits.

The next pass stably sorts by the next higher digit.

Because the pass is stable, equal new digits retain their previous ordering by lower digits.

Therefore after the pass, the array is sorted by one additional digit.

### Progress

`exponent` multiplies by 10:

```text
1 -> 10 -> 100
```

### Termination

When no more digits exist, all digits of every number have been considered.

Therefore the complete numbers are sorted.

---

# 10.5 Exact Radix work count in this benchmark model

The program first scans for maximum:

\[
n
\]

For each digit pass:

### Count digits

\[
n
\]

### Prefix sums

The loop runs for digits 1 through 9:

\[
9
\]

### Stable output

\[
n
\]

### Copy back

\[
n
\]

Thus one pass:

\[
3n+9
\]

There are exactly 3 passes:

\[
3(3n+9)=9n+27
\]

Add maximum scan:

\[
W_R(n)=10n+27
\]

At \(n=1,000,000\):

\[
10(1,000,000)+27
=
10,000,027
\]

which exactly matches the terminal result:

```text
radix_ops = 10000027
```

Hence:

\[
\frac{W_R(n)}{n}
=
10+\frac{27}{n}
\to10
\]

This is why the terminal `radix/n` column approaches exactly `10.000`.

---

# 10.6 Radix Sort complexity

General:

\[
\Theta(d(n+b))
\]

For:

\[
d=3,\qquad b=10
\]

we obtain:

\[
\Theta(3(n+10))
=
\Theta(n)
\]

Extra memory:

\[
\Theta(n+b)
\]

because of:

- output vector of size \(n\),
- 10 digit counters.

---

# 11. Height validation

## Lines 93–101

```cpp
93: bool validHeights(const vector<int>& heights) {
94:     return all_of(
95:         heights.begin(),
96:         heights.end(),
97:         [](int h) {
98:             return MIN_HEIGHT <= h && h <= MAX_HEIGHT;
99:         }
100:     );
101: }
```

This explicitly checks the Counting Sort precondition.

The lambda receives each height and returns `true` only if it is inside the legal range.

---

# 12. Repeat-count selection

## Lines 103–108

```cpp
103: int repeatsFor(int n) {
104:     if (n <= 1'000) return 300;
105:     if (n <= 10'000) return 150;
106:     if (n <= 100'000) return 50;
107:     return 15;
108: }
```

Small tests are repeated more often because their times are tiny and therefore noisier.

Large tests use fewer repeats to keep total runtime reasonable.

This is an experiment-design choice, not part of either sorting algorithm.

---

# 13. Demonstration input

## Lines 115–120

```cpp
115: mt19937 rng(9);
116: uniform_int_distribution<int> heightDist(MIN_HEIGHT, MAX_HEIGHT);

118: vector<int> demo(30);
119: for (int& height : demo)
120:     height = heightDist(rng);
```

Creates 30 reproducible random student heights.

---

# 13.1 Lines 127–134 — same input for both algorithms

```cpp
127: vector<int> demoCounting = demo;
128: vector<int> demoRadix = demo;

130: SortStats demoCountStats;
131: SortStats demoRadixStats;

133: countingSortHeights(demoCounting, demoCountStats);
134: radixSortHeights(demoRadix, demoRadixStats);
```

Both algorithms receive copies of exactly the same unsorted data.

This is required for a fair comparison.

---

# 13.2 Lines 136–141 — demo correctness

```cpp
136: if (!is_sorted(demoCounting.begin(), demoCounting.end()) ||
137:     !is_sorted(demoRadix.begin(), demoRadix.end()) ||
138:     demoCounting != demoRadix) {
139:     cerr << "Demo correctness failure\n";
140:     return 1;
141: }
```

Three conditions are checked:

1. Counting output sorted.
2. Radix output sorted.
3. Both outputs identical.

`||` is logical OR.

If any check fails, the program terminates with status 1.

---

# 14. Benchmark sizes

## Lines 150–156

```cpp
150: const vector<int> sizes = {
151:     100,
152:     1'000,
153:     10'000,
154:     100'000,
155:     1'000'000
156: };
```

Growth by powers of ten makes asymptotic trends easy to recognize.

The million-element case is large enough that constant startup effects become relatively small.

---

# 15. Theoretical statements printed before experiment

## Lines 158–162

```cpp
158: cout << "Theoretical complexity:\n";
159: cout << "Counting Sort: O(n + k), k = " << HEIGHT_RANGE
160:      << " -> O(n) for fixed height range\n";
161: cout << "Radix Sort   : O(d(n + b)), d <= 3 and b = "
162:      << RADIX_BASE << " -> O(n) for these heights\n\n";
```

This is important experimentally:

> theoretical prediction is stated before looking at measured results.

That prevents fitting a complexity claim only after observing the data.

---

# 16. Terminal columns

Lines 164–175 define:

```text
n
repeats
count_ops
count/(n+k)
count_ms
radix_ops
radix/n
passes
radix_ms
time_ratio
```

The most important columns for asymptotic validation are:

```text
count/(n+k)
radix/n
```

Timing is secondary.

---

# 17. Generate one dataset per n

## Lines 177–186

```cpp
177: for (int n : sizes) {
178:     vector<int> original(n);

180:     for (int& height : original)
181:         height = heightDist(rng);

183:     if (!validHeights(original)) {
184:         cerr << "Generated invalid height\n";
185:         return 1;
186:     }
```

For each `n`, one random dataset is generated.

Both algorithms repeatedly sort copies of this same original vector.

That controls the input while comparing implementations.

---

# 18. `std::sort` correctness oracle

## Lines 188–189

```cpp
188: vector<int> expected = original;
189: sort(expected.begin(), expected.end());
```

The standard library sort is **not part of the measured algorithm**.

It is used as an independent reference result.

This is called a **correctness oracle**.

Why use an oracle?

Checking only:

```cpp
is_sorted(values)
```

would prove order but not necessarily prove that no values were accidentally lost or duplicated.

Comparing:

```cpp
values == expected
```

checks both:

- sorted order,
- exact multiset preservation.

This is stronger.

---

# 19. Counting Sort benchmark

## Lines 200–206

```cpp
200: for (int r = 0; r < repeats; ++r) {
201:     vector<int> values = original;
202:     SortStats stats;

204:     auto start = Clock::now();
205:     countingSortHeights(values, stats);
206:     auto end = Clock::now();
```

### Important fairness detail

The input copy:

```cpp
vector<int> values = original;
```

happens **before** timing starts.

Therefore vector-copy overhead is not charged to Counting Sort.

The same policy is used for Radix Sort.

---

# 19.1 Lines 208–215 — correctness and accumulation

```cpp
208: if (values != expected) {
209:     cerr << "Counting Sort correctness failure at n=" << n << '\n';
210:     return 1;
211: }

213: countingOpsTotal += stats.workUnits;
214: countingTimeTotal +=
215:     chrono::duration<double, milli>(end - start).count();
```

Correctness validation occurs **after** timing ends.

Therefore validation does not contaminate measured sort time.

This is good benchmark practice.

---

# 20. Radix Sort benchmark

## Lines 218–224

```cpp
218: for (int r = 0; r < repeats; ++r) {
219:     vector<int> values = original;
220:     SortStats stats;

222:     auto start = Clock::now();
223:     radixSortHeights(values, stats);
224:     auto end = Clock::now();
```

Same structure as Counting Sort.

---

# 20.1 Lines 226–234

```cpp
226: if (values != expected) {
227:     cerr << "Radix Sort correctness failure at n=" << n << '\n';
228:     return 1;
229: }

231: radixOpsTotal += stats.workUnits;
232: radixPassesTotal += stats.passes;
233: radixTimeTotal +=
234:     chrono::duration<double, milli>(end - start).count();
```

Tracks:

- operation count,
- number of digit passes,
- timing.

---

# 21. Average calculations

## Lines 237–247

```cpp
237: double countingOps =
238:     static_cast<double>(countingOpsTotal) / repeats;

240: double radixOps =
241:     static_cast<double>(radixOpsTotal) / repeats;

243: double countingMs = countingTimeTotal / repeats;
244: double radixMs = radixTimeTotal / repeats;

246: double averagePasses =
247:     static_cast<double>(radixPassesTotal) / repeats;
```

Repeated runs are averaged.

For operation counts on a fixed dataset these values are deterministic, but averaging keeps the experiment format parallel with timing.

---

# 22. Counting theoretical normalizer

## Lines 249–250

```cpp
249: double countTheory =
250:     static_cast<double>(n + HEIGHT_RANGE);
```

The theoretical function for Counting Sort is:

\[
n+k
\]

not merely \(n\).

Because:

```text
k = 71
```

the distinction becomes insignificant at large \(n\), but keeping it in the benchmark shows the general theory correctly.

---

# 23. Terminal normalization

## Counting

Line 256:

```cpp
countingOps / countTheory
```

means:

\[
\frac{W_C(n)}{n+k}
\]

Observed values:

```text
1.585
1.934
1.993
1.999
2.000
```

approach 2.

That agrees exactly with:

\[
W_C(n)=2n+k
\]

---

## Radix

Line 259:

```cpp
radixOps / n
```

means:

\[
\frac{W_R(n)}{n}
\]

Observed:

```text
10.270
10.027
10.003
10.000
10.000
```

approaches 10.

That agrees exactly with:

\[
W_R(n)=10n+27
\]

---

# 24. Actual benchmark result

A representative run produced:

```text
n          repeats  count_ops       count/(n+k)     count_ms       radix_ops       radix/n        passes    radix_ms       time_ratio
100        300      271.00          1.585           0.000312       1027.00         10.270         3.00      0.001501       4.81
1000       300      2071.00         1.934           0.000747       10027.00        10.027         3.00      0.014149       18.95
10000      150      20071.00        1.993           0.007408       100027.00       10.003         3.00      0.145153       19.59
100000     50       200071.00       1.999           0.077856       1000027.00      10.000         3.00      1.383062       17.76
1000000    15       2000071.00      2.000           0.750538       10000027.00     10.000         3.00      14.309655      19.07
```

Timing values are machine-dependent.

The operation ratios are the stronger theoretical evidence.

---

# 25. What the experiment demonstrates

## Counting Sort

The measured operation count is:

\[
2n+71
\]

and:

\[
\frac{2n+71}{n+71}
\]

remains bounded and approaches 2.

Therefore the implementation behaves as predicted:

\[
\Theta(n+k)
\]

and with fixed \(k\):

\[
\Theta(n)
\]

---

## Radix Sort

Measured work is:

\[
10n+27
\]

and:

\[
\frac{10n+27}{n}
\to10
\]

Therefore the implementation behaves linearly for fixed:

\[
d=3,\quad b=10
\]

consistent with:

\[
\Theta(d(n+b))
\]

---

# 26. Why Counting Sort wins here

The two algorithms are both asymptotically linear **for this constrained domain**, but their constants are very different.

Counting Sort work:

\[
2n+71
\]

Radix work:

\[
10n+27
\]

So the abstract work model itself already predicts approximately a factor of:

\[
\frac{10n}{2n}\approx5
\]

more counted work for Radix Sort.

Real timing may show an even larger ratio because Radix Sort also:

- repeatedly scans memory,
- writes to a second output vector,
- copies output back,
- performs division and modulo for digit extraction,
- performs three complete passes.

Counting Sort simply:

1. counts 71 possible values,
2. writes them back.

For a narrow bounded domain like student heights, Counting Sort is the more natural algorithm.

---

# 27. When Radix Sort would become more attractive

Counting Sort depends on the key range \(k\).

If values could be:

```text
0 ... 1,000,000,000
```

then a direct count array might be impractical even for relatively small \(n\).

Radix Sort depends on the number of digits instead:

\[
d
\]

and the base:

\[
b
\]

So Radix Sort is useful for large integer key ranges where allocating one bucket per possible key would be wasteful.

For heights only 140–210, that advantage does not apply.

---

# 28. Space-complexity comparison

## Counting Sort implementation

Frequency array:

\[
\Theta(k)
\]

No \(n\)-sized auxiliary output vector is needed.

For fixed 71 heights:

\[
\Theta(1)
\]

with respect to \(n\).

## Radix Sort

Output vector:

\[
\Theta(n)
\]

Digit count:

\[
\Theta(b)
\]

Total:

\[
\Theta(n+b)
\]

For base 10:

\[
\Theta(n)
\]

---

# 29. Stability

## Counting Sort used here

The direct reconstruction version is not meaningful as a stable record sort because only integer values are stored.

Equal heights are indistinguishable.

## Radix digit sort

Must be stable.

The reverse loop at L64 is the key implementation detail that guarantees stability.

This can be asked directly in a viva.

---

# 30. Boundary cases

## Empty array

`radixSortHeights` explicitly handles it at L81–L82.

Counting Sort naturally handles it because all loops simply perform zero input iterations and bucket reconstruction writes nothing.

## One height

Both algorithms return the same value.

## All heights equal

Counting Sort:
- one bucket receives all \(n\) counts.

Radix:
- still performs all 3 digit passes.

Counting Sort therefore has a major constant-factor advantage.

## Minimum height 140

Maps to bucket:

```text
0
```

## Maximum height 210

Maps to bucket:

```text
70
```

Both are valid because:

```text
HEIGHT_RANGE = 71
```

---

# 31. Exact proof map

| Concept | Exact code |
|---|---|
| Height-domain constants | L13–L16 |
| Counting frequency-array initialization | L24 |
| Counting frequency invariant | L26–L29 |
| Counting reconstruction initialization | L31 |
| Counting reconstruction maintenance | L33–L41 |
| Counting termination | L42–L43 |
| Digit count array initialization | L51 |
| Digit extraction | L53–L56 |
| Prefix sums | L59–L62 |
| Stable reverse traversal | L64–L69 |
| Copy-back | L71–L75 |
| Radix pass counter | L77 |
| Empty Radix case | L81–L82 |
| Maximum digit discovery | L84–L85 |
| Reusable output buffer | L87 |
| Radix progress | L89 |
| Radix per-digit maintenance | L90 |
| Height precondition checker | L93–L101 |
| Reproducible PRNG | L115–L116 |
| Same demo input for both sorts | L127–L134 |
| Demo correctness | L136–L141 |
| Benchmark sizes | L150–L156 |
| Theory printed | L158–L162 |
| Random dataset generation | L177–L181 |
| Precondition validation | L183–L186 |
| `std::sort` oracle | L188–L189 |
| Counting timed region | L204–L206 |
| Counting correctness after timer | L208–L211 |
| Radix timed region | L222–L224 |
| Radix correctness after timer | L226–L229 |
| `static_cast` for averages | L237–L247 |
| Counting theoretical term | L249–L250 |
| Counting normalized ratio | L256 |
| Radix normalized ratio | L259 |
| Pass count output | L260 |
| Runtime ratio | L262–L263 |

---

# 32. Initialization → Maintenance → Progress → Termination summary

## Counting frequency loop

**Initialization:** L24 gives zero counts.

**Maintenance:** L27 increments exactly the bucket of current height.

**Progress:** range-based loop consumes one input element.

**Termination:** after L29, all frequencies are exact.

---

## Counting reconstruction

**Initialization:** L31 gives empty output prefix.

**Maintenance:** L36–L40 writes current bucket value while preserving order.

**Progress:** count decreases or bucket increases.

**Termination:** after L42 all \(n\) values have been written in ascending order.

---

## Radix digit counting

**Initialization:** L51 gives ten zeros.

**Maintenance:** L53–L56 increments the correct digit frequency.

**Progress:** one input value per iteration.

**Termination:** frequencies for current digit are complete.

---

## Prefix sums

**Initialization:** digit 0 frequency is already cumulative.

**Maintenance:** L60 converts the next frequency into a cumulative endpoint.

**Progress:** digit increases from 1 to 9.

**Termination:** all digit endpoint positions are known.

---

## Stable output

**Initialization:** cumulative counts identify free end positions.

**Maintenance:** L66 places each value into the correct digit region.

**Progress:** `i--`.

**Termination:** every element appears exactly once in stable digit order.

---

## Radix outer loop

**Initialization:** `exponent = 1`.

**Maintenance:** stable current-digit sort extends sorted suffix of digits by one position.

**Progress:** `exponent *= 10`.

**Termination:** `maximum / exponent == 0`; no higher digit remains.

---

# 33. Likely viva questions

## Why is Counting Sort not a comparison sort?

It never asks:

```text
A[i] < A[j]?
```

Instead, it uses the value itself to select a frequency bucket.

---

## Why does Counting Sort take `O(n+k)`?

It performs:

1. one pass over \(n\) inputs,
2. one pass over \(k\) buckets,
3. \(n\) reconstruction writes.

Total:

\[
O(n+k)
\]

---

## Why is it effectively `O(n)` here?

Because:

\[
k=71
\]

is fixed and independent of \(n\).

---

## Why must Radix Sort use a stable inner sort?

Higher-digit passes must preserve the ordering already established by lower digits for equal higher digits.

Without stability, the previous digit ordering could be destroyed.

---

## Which line guarantees stability?

Problem source:

```text
L64
```

The digit-output loop traverses from the end toward the beginning.

---

## Why process from right to left?

It preserves the relative order of equal-digit elements.

---

## Why is Radix Sort linear here?

\[
d=3,\quad b=10
\]

are constants, so:

\[
d(n+b)
=
3(n+10)
=
\Theta(n)
\]

---

## Why is Counting Sort still faster if both are `O(n)`?

Big-O hides constant factors and repeated passes.

Counting Sort performs about:

\[
2n
\]

dominant work units.

The implemented Radix Sort performs about:

\[
10n
\]

dominant work units.

---

## Why not use `std::sort` as the actual solution?

The assignment specifically asks for Counting Sort and Radix Sort.

`std::sort` is used only as an independent correctness oracle.

---

## Why is `std::sort` outside the timed loop?

It is not part of either algorithm being evaluated.

Including it would invalidate timing measurements.

---

## Why generate one original vector and copy it?

Both algorithms must receive exactly the same input to compare them fairly.

Copying occurs outside the timed region.

---

## Why use fixed seed 9?

Reproducibility.

Anyone running the code receives the same random sequence.

---

## Why operation counts instead of only milliseconds?

Operation counts correspond directly to theoretical analysis.

Milliseconds depend on hardware and runtime environment.

---

## Does the experiment prove the asymptotic complexity?

No.

The mathematical loop analysis proves the complexity.

The experiment validates that the implementation follows the predicted growth over large tested inputs.

---

# 34. Industry-quality observations

The implementation deliberately uses:

- fixed compile-time domain constants,
- no unnecessary dynamic count allocation for Counting Sort,
- one reusable Radix output buffer,
- correctness oracle,
- validation outside timed regions,
- same data for both algorithms,
- fixed random seed,
- large \(n\),
- repeated timing,
- explicit operation normalization,
- no graph/CSV dependency.

For a classroom performance experiment, this is stronger than timing one array once.

---

# 35. Makefile usage

Compile:

```bash
make
```

Run:

```bash
make run
```

Clean:

```bash
make clean
```

Compiler settings:

```text
-std=c++17
-O2
-Wall
-Wextra
-Wpedantic
```

`-O2` enables normal optimization.

The warning flags help catch common C++ mistakes.

---

# 36. Final result to remember

For **student heights from 140 to 210 cm**:

\[
\boxed{\text{Counting Sort is the better choice}}
\]

because:

\[
k=71
\]

is extremely small.

Both algorithms are asymptotically linear in \(n\) for this constrained problem, but Counting Sort has:

- fewer passes,
- lower memory use,
- fewer work units,
- lower measured runtime.

Radix Sort remains important when the integer key domain is much larger and direct Counting Sort would require an impractically large count table.
