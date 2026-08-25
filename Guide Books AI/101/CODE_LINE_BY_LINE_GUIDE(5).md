# Binary Search Assignment — Line-by-Line Code, Proof, Experiment, and C++ Guide

This guide corresponds to the exact source files in this folder. Line numbers below refer to the current `.cpp` files.

The goal is to be able to answer four kinds of questions from the same code:

1. **What does this exact line do in C++?**
2. **Why is this line needed by the algorithm?**
3. **Where are Initialization, Maintenance, Progress, and Termination visible in the code?**
4. **How does the benchmark support the theoretical time-complexity claim?**

> **CLRS connection.** CLRS formally presents the three loop-invariant proof obligations as **Initialization, Maintenance, and Termination**. In this guide, **Progress** is stated separately because a total-correctness argument also needs to show that the loop cannot stall forever. For Binary Search, the progress measure is usually the size of the candidate interval.

---

# 0. How to read this guide

A line range such as `L25–L33` refers to the exact lines in the named source file.

Multiline C++ statements are explained as a logical unit. Every executable part of the algorithm and benchmark is mapped. Repeated `#include` boilerplate is explained once below instead of being duplicated ten times.

The experimental sections **support/validate** the theoretical model; they do not replace a mathematical proof. A finite experiment can show that observed operation counts behave like `log n`, `n log n`, and so on over large tested inputs, but only the mathematical argument establishes the asymptotic result for all admissible inputs.

---

# 1. Shared C++ language and benchmark concepts

The first nine assignment files share essentially the same header block.

## 1.1 Header files

| Header | Why it appears |
|---|---|
| `<algorithm>` | `sort`, `max`, `max_element`, `min` and other standard algorithms. |
| `<cassert>` | Assertion facilities. It is included in these files but currently not used. |
| `<chrono>` | Monotonic high-resolution timing for the experiments. |
| `<cmath>` | `log`, `log2`, `abs` and mathematical functions. |
| `<cstdint>` | Fixed-width integer types such as `uint64_t`. |
| `<iomanip>` | Terminal-table formatting with `setw`, `fixed`, `setprecision`, `left`. |
| `<iostream>` | `cout` and `cerr`. |
| `<limits>` | `numeric_limits<T>::lowest()` and `max()` in Problem 5. |
| `<numeric>` | `iota` and `accumulate`. |
| `<random>` | `mt19937`, `mt19937_64`, and `uniform_int_distribution`. |
| `<string>` | Standard string type. Included in most files even where not directly needed. |
| `<vector>` | Dynamic contiguous arrays used throughout. |

### `using namespace std;`

This makes names such as `vector`, `cout`, and `max` available without the `std::` prefix.

For a compact classroom `.cpp` file this is convenient. In production headers or large systems, industry style normally prefers explicit `std::vector`, `std::cout`, etc., because `using namespace std;` can create name collisions.

### `using Clock = chrono::steady_clock;`

This is a **type alias**. It gives the long name `chrono::steady_clock` the short local name `Clock`.

`steady_clock` is the correct standard clock for elapsed-time benchmarking because it is **monotonic**: it never runs backward when the system wall clock changes.

It is not a calendar clock. Use `system_clock` when actual date/time is required; use `steady_clock` when elapsed duration is required.

---

## 1.2 `const vector<T>&`

Functions such as

```cpp
int leftmostOccurrence(const vector<int>& a, ...)
```

take the vector as a **const reference**.

- `&` means the whole vector is not copied.
- `const` means the function cannot modify the vector through that reference.

This is both efficient and expressive: the function reads the input but does not own or mutate it.

---

## 1.3 `static_cast<T>(value)`

Examples:

```cpp
static_cast<int>(a.size())
static_cast<double>(totalIters)
static_cast<size_t>(index)
```

`static_cast` performs an explicit compile-time checked conversion.

Why it is preferable to a C-style cast:

```cpp
(int)a.size()
```

`static_cast` states the programmer's intent and disallows several unsafe categories of conversions that a C-style cast may silently attempt.

Important details:

- `vector::size()` returns `size_t`, an unsigned type.
- Binary-search code often uses signed indices because it needs expressions like `mid - 1`.
- The assignment therefore casts sizes to `int`.

**Industry caveat:** this is safe for the tested sizes (up to one million), but if a vector could contain more than `INT_MAX` elements, the conversion could overflow/narrow. Large-scale library code would usually use `size_t`, `ptrdiff_t`, or a carefully chosen signed index type.

---

## 1.4 Overflow-safe midpoint

All Binary Search implementations use the pattern

```cpp
mid = lo + (hi - lo) / 2;
```

rather than

```cpp
mid = (lo + hi) / 2;
```

The two formulas are mathematically equivalent when no overflow occurs. The first is preferred because `lo + hi` can overflow a fixed-width integer even when both endpoints are individually valid.

This is a standard production-quality Binary Search detail.

---

## 1.5 `long long`, `uint64_t`, `long double`, suffixes

- `long long` provides a large signed integer range and is used for counts, capacities, distances, and accumulated sums.
- `uint64_t` is an exactly 64-bit unsigned integer when supported; useful for bit encodings.
- `long double` gives at least as much precision as `double` and is used in Problem 7's accumulated ratios.
- `0LL`, `1LL` mean integer literals of type `long long`.
- `1ULL` means an `unsigned long long` literal. It is important before bit shifting so the operation has a wide unsigned type.

---

## 1.6 `auto`

Example:

```cpp
auto start = Clock::now();
```

`auto` asks the compiler to deduce the exact type from the initializer.

`Clock::now()` returns a verbose `time_point` type. Using `auto` improves readability without losing static typing.

---

## 1.7 `<chrono>` timing

Typical benchmark pattern:

```cpp
auto start = Clock::now();
// repeated algorithm calls
auto end = Clock::now();

double avgNs =
    chrono::duration<double, nano>(end - start).count() / trials;
```

- `end - start` is a duration.
- `chrono::duration<double, nano>` expresses that duration in nanoseconds using a `double`.
- `.count()` extracts the numeric magnitude.
- Dividing by `trials` gives average time per run.

Problem 8 uses `milli` instead of `nano` because each run scans much larger input and takes milliseconds.

**Industry note:** timing should be secondary to operation counts for asymptotic validation. CPU caches, branch prediction, frequency scaling, compiler optimization, and OS scheduling all influence wall-clock timings.

---

## 1.8 Operation counters and `Stats`

The files use small structs such as:

```cpp
struct Stats {
    long long iterations = 0;
};
```

or:

```cpp
struct Stats {
    long long binaryIterations = 0;
    long long packageChecks = 0;
};
```

This makes the experiment measure **algorithmic work**, not only elapsed time.

For asymptotic experiments, this is important because the counted operation is much closer to the mathematical cost model.

---

## 1.9 `volatile ... sink`

Examples:

```cpp
volatile int sink = 0;
sink ^= pos;
(void)sink;
```

The result of each benchmarked search is mixed into `sink` so the compiler has less freedom to conclude that the returned value is unused and eliminate work.

`^=` is bitwise XOR assignment.

`(void)sink;` explicitly marks the variable as intentionally used at the end.

**Industry note:** `volatile` is not a general-purpose benchmarking barrier and is not meant for thread synchronization. Dedicated benchmarking frameworks such as Google Benchmark provide stronger anti-optimization facilities. For this assignment, the `sink` technique keeps the code simple and is sufficient to demonstrate intent.

---

## 1.10 Random-number generation

### `mt19937 rng(123456);`

`mt19937` is the Mersenne Twister pseudo-random number generator.

- Fixed seed `123456` makes experiments **reproducible**.
- It is suitable for simulations and algorithm tests.
- It is **not cryptographically secure**.

### `uniform_int_distribution<int> d(lo, hi);`

Produces integers uniformly over the inclusive interval `[lo, hi]`.

This is preferable to `rng() % range` when statistical uniformity matters because modulo reduction can introduce bias.

Some correctness-oracle test code uses `%` simply to generate varied small cases; the main randomized-pivot experiment correctly uses `uniform_int_distribution`.

---

## 1.11 `iota`

```cpp
iota(a.begin(), a.end(), 0);
```

fills the range with increasing values:

```text
0, 1, 2, 3, ...
```

It is a concise standard-library way to generate a sorted benchmark array.

---

## 1.12 `accumulate`

```cpp
long long sum = accumulate(weights.begin(), weights.end(), 0LL);
```

adds all values.

The initial value `0LL` is important: it causes accumulation to happen in `long long`. If the initial value were plain `0`, the accumulator type would be `int`, which could overflow for large total weights.

---

## 1.13 `max_element`

```cpp
long long maxWeight = *max_element(weights.begin(), weights.end());
```

`max_element` returns an **iterator** pointing to the largest element.

The leading `*` dereferences that iterator to obtain the actual value.

---

## 1.14 `numeric_limits`

Problem 5 uses:

```cpp
numeric_limits<long long>::lowest()
numeric_limits<long long>::max()
```

as safe conceptual `-infinity` and `+infinity` sentinels.

This is better than a magic value such as `-1e9`, because the actual arrays may legally contain values below or above an arbitrary hand-picked sentinel.

---

## 1.15 `move`

Problem 4 uses:

```cpp
UnknownSizeArray a(move(v));
```

and the constructor uses:

```cpp
data(move(v))
```

`std::move` does not physically move data by itself. It converts an object to an rvalue, allowing the receiving `vector` to transfer ownership of its allocated buffer instead of copying all elements.

After moving, the source vector remains valid but its contents are unspecified.

---

## 1.16 `explicit` constructor

```cpp
explicit UnknownSizeArray(vector<int> v)
```

prevents unintended implicit conversion from `vector<int>` to `UnknownSizeArray`.

It is a good C++ interface habit for single-argument constructors.

---

## 1.17 Structured binding

Problem 2 uses:

```cpp
auto [first, last] = allOccurrences(...);
```

This is a C++17 **structured binding**. It unpacks a returned `pair<int,int>` into two named local variables.

---

## 1.18 Exceptions

Problem 5 uses:

```cpp
throw invalid_argument("k out of range");
throw logic_error("Unreachable");
```

- `invalid_argument` means the caller violated a precondition.
- `logic_error` marks a state that should be impossible if the algorithm and preconditions are correct.

For competitive-programming code, returning a sentinel is common. For reusable library-style code, an exception can communicate invalid API usage more explicitly.

---

## 1.19 Terminal formatting

Example:

```cpp
cout << left << setw(16) << fixed << setprecision(3) << value;
```

- `left`: left-align the next formatted field.
- `setw(16)`: minimum width 16 for the next insertion.
- `fixed`: print floating-point values in fixed decimal notation.
- `setprecision(3)`: with `fixed`, show 3 digits after the decimal point.

This affects presentation only, not the algorithm.

---

# 2. Experimental complexity validation

For a claimed tight growth `Theta(g(n))`, the programs print a normalized ratio

```text
measured_operation_count / g(n)
```

Examples:

- `iterations / log2(n)` for Binary Search.
- `packageChecks / (n * log2(R))` for shipping capacity.
- `travelled / D` for the infinite-wall problem.
- `bitChecks / (B * log2(B))` for poison-test preparation.

If this ratio remains bounded and roughly stable as `n` grows through large values, the observations are consistent with the theoretical `Theta(g(n))` prediction.

This is stronger than comparing raw time alone, but it remains **experimental evidence**, not a universal mathematical proof.

---

# 3. Correctness vocabulary used throughout

## Precondition
A property assumed before the algorithm begins.

Example: Problem 1 assumes the input array is sorted.

## Postcondition
What the algorithm guarantees upon return.

Example: Problem 1 returns the smallest index containing `target`, or `-1` if none exists.

## Loop invariant
A property that remains true before every loop iteration.

## Initialization
Show the invariant is true before the first iteration.

## Maintenance
Assume the invariant is true at the beginning of one iteration and show the iteration preserves it.

## Progress
Show that some integer-valued measure moves strictly toward termination.

For Binary Search the standard variant is the interval size.

## Termination
Show the loop stops, then combine the invariant with the stopping condition to obtain the postcondition.

## Partial correctness
If the algorithm terminates, its result is correct.

## Total correctness
Partial correctness **plus** proof that it terminates.

## Soundness
Any answer returned as a solution really is a valid solution.

## Completeness
Whenever a solution exists under the preconditions, the algorithm is guaranteed to find one.

## Correctness oracle
A simple independent implementation used in tests to verify a more sophisticated implementation.

Problems 5 and 8 use explicit oracles.

---

# 4. Problem 1 — Leftmost occurrence

**File:** `problem01_leftmost.cpp`

**Precondition:** `a` is sorted in nondecreasing order.

**Postcondition:** return the least index `i` with `a[i] == target`; return `-1` if absent.

**Core invariant:** the lower-bound position (first index whose value is `>= target`) remains in the half-open interval `[lo, hi)`.

### Lines 18–20

```cpp
  18: struct Stats {
  19:     long long iterations = 0;
  20: };
```

Defines the operation counter. Each pass through the Binary Search loop increments `iterations`, giving a machine-independent measure of search work.

**C++ / implementation concepts:** aggregate initialization; struct; long long

**Algorithm-proof role:** Benchmark instrumentation, not part of correctness.

### Lines 22

```cpp
  22: int leftmostOccurrence(const vector<int>& a, int target, Stats& s) {
```

Declares the search function. `const vector<int>&` avoids copying and prevents mutation; `Stats&` lets the function update the caller's counter.

**C++ / implementation concepts:** const reference; reference parameter

**Algorithm-proof role:** Defines the algorithm's interface.

### Lines 23

```cpp
  23:     int lo = 0, hi = static_cast<int>(a.size());
```

Initializes the candidate interval to `[0, n)`. `hi` is one-past-the-last valid index, which is the standard lower-bound formulation.

**C++ / implementation concepts:** static_cast; half-open interval

**Algorithm-proof role:** **Initialization:** every possible lower-bound position lies in `[0,n)`.

### Lines 25

```cpp
  25:     while (lo < hi) {
```

Continues while at least one candidate position remains. When `lo == hi`, the lower-bound position has been isolated.

**C++ / implementation concepts:** while loop

**Algorithm-proof role:** **Termination condition** and interval-variant check.

### Lines 26

```cpp
  26:         ++s.iterations;
```

Counts one Binary Search iteration.

**C++ / implementation concepts:** pre-increment

**Algorithm-proof role:** Experimental operation count.

### Lines 27

```cpp
  27:         int mid = lo + (hi - lo) / 2;
```

Computes an overflow-resistant midpoint inside `[lo,hi)`.

**C++ / implementation concepts:** integer division; overflow-safe midpoint

**Algorithm-proof role:** **Progress:** `mid` divides the candidate interval.

### Lines 29–30

```cpp
  29:         if (a[mid] < target)
  30:             lo = mid + 1;
```

If `a[mid] < target`, sorted order proves every index `<= mid` is too small, so the first value `>= target` must be to the right.

**C++ / implementation concepts:** sorted-order implication

**Algorithm-proof role:** **Maintenance:** safely discards `[lo,mid]`; new interval is `[mid+1,hi)`.

### Lines 31–32

```cpp
  31:         else
  32:             hi = mid;
```

Otherwise `a[mid] >= target`. The lower bound may be `mid` itself or somewhere to its left, so keep `mid` by setting `hi = mid`.

**Algorithm-proof role:** **Maintenance:** safely discards `(mid,hi)` while retaining `mid`.

### Lines 33

```cpp
  33:     }
```

Ends the loop body. Because either `lo` increases or `hi` decreases, the interval strictly shrinks.

**Algorithm-proof role:** **Progress:** `hi-lo` decreases.

### Lines 35–37

```cpp
  35:     if (lo < static_cast<int>(a.size()) && a[lo] == target)
  36:         return lo;
  37:     return -1;
```

After convergence, verifies that the lower-bound position actually contains the target. If the lower bound is `n` or holds a larger value, the target is absent.

**C++ / implementation concepts:** short-circuit `&&`; static_cast

**Algorithm-proof role:** **Termination/postcondition:** return leftmost occurrence or `-1`.

### Lines 40–42

```cpp
  40: int main() {
  41:     cout << "Problem 1: Leftmost occurrence\n";
  42:     cout << "Theory: O(log n)\n\n";
```

Starts the experiment and prints the theoretical claim.

**C++ / implementation concepts:** iostream

**Algorithm-proof role:** Separates mathematical expectation from measured results.

### Lines 44–47

```cpp
  44:     mt19937 rng(123456);
  45:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
  46:     const int trials = 100000;
  47:     const int duplicates = 4;
```

Creates a reproducible PRNG, chooses large input sizes, runs 100,000 searches per size, and makes every value appear four times.

**C++ / implementation concepts:** mt19937; const vector

**Algorithm-proof role:** Experimental design: duplicates specifically test the leftmost-occurrence requirement.

### Lines 49–53

```cpp
  49:     cout << left << setw(12) << "n"
  50:          << setw(16) << "avg_iters"
  51:          << setw(16) << "log2(n)"
  52:          << setw(18) << "iters/log2(n)"
  53:          << setw(16) << "avg_ns" << '\n';
```

Prints terminal column headings. The important normalized column is `iters/log2(n)`.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Experimental complexity validation.

### Lines 55

```cpp
  55:     volatile int sink = 0;
```

Creates an anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 57–60

```cpp
  57:     for (int n : sizes) {
  58:         vector<int> a(n);
  59:         for (int i = 0; i < n; ++i)
  60:             a[i] = i / duplicates;
```

For each `n`, builds a sorted array where `0,0,0,0,1,1,1,1,...`. Because each value has exactly four copies, the expected first index is easy to calculate.

**C++ / implementation concepts:** range-for; vector construction

**Algorithm-proof role:** Constructs a controlled correctness test with duplicates.

### Lines 62

```cpp
  62:         uniform_int_distribution<int> targetDist(0, a.back());
```

Chooses search targets uniformly from valid values present in the array.

**C++ / implementation concepts:** uniform_int_distribution

**Algorithm-proof role:** Avoids bias toward one region.

### Lines 64–65

```cpp
  64:         long long totalIters = 0;
  65:         auto start = Clock::now();
```

Resets accumulated iteration count and starts the monotonic timer.

**C++ / implementation concepts:** chrono

**Algorithm-proof role:** Benchmark setup.

### Lines 67–70

```cpp
  67:         for (int t = 0; t < trials; ++t) {
  68:             int target = targetDist(rng);
  69:             Stats s;
  70:             int pos = leftmostOccurrence(a, target, s);
```

Repeats the search. A fresh `Stats` object resets the per-search iteration count.

**Algorithm-proof role:** High-iteration averaging.

### Lines 71

```cpp
  71:             totalIters += s.iterations;
```

Adds this search's Binary Search iterations to the total.

**Algorithm-proof role:** Operation-count aggregation.

### Lines 73–77

```cpp
  73:             int expected = target * duplicates;
  74:             if (pos != expected) {
  75:                 cerr << "Correctness failure\n";
  76:                 return 1;
  77:             }
```

Computes the mathematically known expected leftmost index (`target * 4`) and aborts immediately if the algorithm disagrees.

**Algorithm-proof role:** Correctness oracle for this specially constructed dataset; establishes empirical soundness/completeness over tested cases.

### Lines 78

```cpp
  78:             sink ^= pos;
```

Mixes the result into `sink` so returned values remain observably used.

**C++ / implementation concepts:** volatile; XOR

**Algorithm-proof role:** Benchmark hygiene.

### Lines 81–84

```cpp
  81:         auto end = Clock::now();
  82:         double avgIters = static_cast<double>(totalIters) / trials;
  83:         double theory = log2(static_cast<double>(n));
  84:         double avgNs = chrono::duration<double, nano>(end - start).count() / trials;
```

Stops timing, computes mean iterations, computes the theoretical `log2(n)` growth term, and converts elapsed duration to average nanoseconds.

**C++ / implementation concepts:** chrono; static_cast; log2

**Algorithm-proof role:** Direct theoretical-versus-observed comparison.

### Lines 86–90

```cpp
  86:         cout << left << setw(12) << n
  87:              << setw(16) << fixed << setprecision(3) << avgIters
  88:              << setw(16) << theory
  89:              << setw(18) << avgIters / theory
  90:              << setw(16) << avgNs << '\n';
```

Prints one result row, including `avgIters/log2(n)`. A bounded ratio supports logarithmic growth.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Experimental complexity evidence.

### Lines 93–96

```cpp
  93:     cout << "\nInterpretation: iters/log2(n) stays approximately constant,"
  94:             " experimentally matching O(log n).\n";
  95:     (void)sink;
  96:     return 0;
```

Prints the interpretation, explicitly consumes `sink`, and returns success from `main`.

**C++ / implementation concepts:** void cast; return code

**Algorithm-proof role:** Program termination.

## Problem 1 proof map

- **Initialization:** L23.
- **Maintenance:** L29–L32.
- **Progress / variant:** L27, L30, L32. The measure `hi - lo` strictly decreases.
- **Termination:** L25 and L35–L37.
- **Partial correctness:** invariant + final equality check.
- **Total correctness:** partial correctness + shrinking finite interval.
- **Soundness:** L35–L36 returns an index only after checking `a[lo] == target`.
- **Completeness:** lower-bound invariant ensures the leftmost valid index cannot be discarded.
- **Experimental check:** L57–L90.
- **Theoretical complexity:** at most a constant amount of work per interval halving, so `Theta(log n)` search work.


# 5. Problem 2 — All occurrences

**Precondition:** sorted nondecreasing array.

**Postcondition:** return the inclusive first/last occurrence interval, or `{-1,-1}` if absent.

### Lines 18–20

```cpp
  18: struct Stats {
  19:     long long iterations = 0;
  20: };
```

Shared iteration counter used by both boundary searches.

**C++ / implementation concepts:** struct

**Algorithm-proof role:** Benchmark instrumentation.

### Lines 22–23

```cpp
  22: int lowerBoundIndex(const vector<int>& a, int target, Stats& s) {
  23:     int lo = 0, hi = static_cast<int>(a.size());
```

Begins lower-bound search and initializes `[lo,hi)` to the full array.

**C++ / implementation concepts:** const reference; static_cast

**Algorithm-proof role:** **Initialization:** first index with value `>= target` is in `[0,n)`.

### Lines 24–29

```cpp
  24:     while (lo < hi) {
  25:         ++s.iterations;
  26:         int mid = lo + (hi - lo) / 2;
  27:         if (a[mid] < target) lo = mid + 1;
  28:         else hi = mid;
  29:     }
```

Standard lower-bound Binary Search. Values strictly below `target` are discarded from the left; otherwise `mid` remains a candidate by moving `hi` to `mid`.

**C++ / implementation concepts:** overflow-safe midpoint

**Algorithm-proof role:** **Maintenance + Progress:** preserves boundary candidate and shrinks interval.

### Lines 30–31

```cpp
  30:     return lo;
  31: }
```

Returns the first index whose value is at least `target`.

**Algorithm-proof role:** Lower-bound postcondition.

### Lines 33–34

```cpp
  33: int upperBoundIndex(const vector<int>& a, int target, Stats& s) {
  34:     int lo = 0, hi = static_cast<int>(a.size());
```

Starts upper-bound search on the full half-open interval.

**Algorithm-proof role:** **Initialization:** first index with value `> target` is in `[0,n)`.

### Lines 35–40

```cpp
  35:     while (lo < hi) {
  36:         ++s.iterations;
  37:         int mid = lo + (hi - lo) / 2;
  38:         if (a[mid] <= target) lo = mid + 1;
  39:         else hi = mid;
  40:     }
```

Upper-bound Binary Search differs at L38: `a[mid] <= target` is discarded, because the desired boundary is strictly greater than target.

**Algorithm-proof role:** **Maintenance + Progress:** preserves first `> target` boundary.

### Lines 41–42

```cpp
  41:     return lo;
  42: }
```

Returns one-past-the-last occurrence.

**Algorithm-proof role:** Upper-bound postcondition.

### Lines 44–45

```cpp
  44: pair<int,int> allOccurrences(const vector<int>& a, int target, Stats& s) {
  45:     int first = lowerBoundIndex(a, target, s);
```

Wrapper calls lower bound to locate the candidate first occurrence.

**C++ / implementation concepts:** pair return type

**Algorithm-proof role:** Combines two boundary algorithms.

### Lines 46–47

```cpp
  46:     if (first == static_cast<int>(a.size()) || a[first] != target)
  47:         return {-1, -1};
```

Checks whether the target is absent. Short-circuiting prevents reading `a[first]` when `first == n`.

**C++ / implementation concepts:** short-circuit `||`; static_cast

**Algorithm-proof role:** Soundness for absent case.

### Lines 48–49

```cpp
  48:     int afterLast = upperBoundIndex(a, target, s);
  49:     return {first, afterLast - 1};
```

Finds one-past-last occurrence and returns inclusive `[first, afterLast-1]`.

**C++ / implementation concepts:** pair aggregate return

**Algorithm-proof role:** Completeness: every target occurrence lies in this interval.

### Lines 52–59

```cpp
  52: int main() {
  53:     cout << "Problem 2: All occurrences\n";
  54:     cout << "Theory: search O(log n), reporting k indices O(k), total O(log n + k)\n\n";
  55: 
  56:     mt19937 rng(123456);
  57:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
  58:     const int trials = 50000;
  59:     const int group = 8;
```

Prints theory and configures four input sizes, 50,000 trials, and groups of eight duplicates.

**C++ / implementation concepts:** mt19937

**Algorithm-proof role:** Experiment explicitly makes output size `k=8`.

### Lines 61–66

```cpp
  61:     cout << left << setw(12) << "n"
  62:          << setw(16) << "avg_search"
  63:          << setw(16) << "2log2(n)"
  64:          << setw(18) << "search/theory"
  65:          << setw(12) << "k"
  66:          << setw(16) << "avg_ns" << '\n';
```

Defines terminal columns. Two Binary Searches predict approximately `2 log2(n)` iterations.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Complexity normalization.

### Lines 68

```cpp
  68:     volatile int sink = 0;
```

Anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 70–74

```cpp
  70:     for (int n : sizes) {
  71:         n -= n % group;
  72:         vector<int> a(n);
  73:         for (int i = 0; i < n; ++i)
  74:             a[i] = i / group;
```

Rounds `n` down to a multiple of eight and constructs eight copies of every key.

**C++ / implementation concepts:** modulo; vector

**Algorithm-proof role:** Controlled duplicate dataset.

### Lines 76–79

```cpp
  76:         uniform_int_distribution<int> targetDist(0, a.back());
  77:         long long totalIters = 0;
  78: 
  79:         auto start = Clock::now();
```

Chooses valid targets, resets total count, starts timer.

**C++ / implementation concepts:** uniform_int_distribution; chrono

**Algorithm-proof role:** Benchmark setup.

### Lines 81–84

```cpp
  81:         for (int t = 0; t < trials; ++t) {
  82:             int target = targetDist(rng);
  83:             Stats s;
  84:             auto [first, last] = allOccurrences(a, target, s);
```

Runs the combined algorithm and uses C++17 structured binding to unpack the returned pair.

**C++ / implementation concepts:** structured binding

**Algorithm-proof role:** Exercise of the actual algorithm.

### Lines 85

```cpp
  85:             totalIters += s.iterations;
```

Accumulates operations from both boundary searches.

**Algorithm-proof role:** Measures search component only.

### Lines 87–92

```cpp
  87:             int expectedFirst = target * group;
  88:             int expectedLast = expectedFirst + group - 1;
  89:             if (first != expectedFirst || last != expectedLast) {
  90:                 cerr << "Correctness failure\n";
  91:                 return 1;
  92:             }
```

Computes exact expected first/last indices and aborts on mismatch.

**Algorithm-proof role:** Correctness oracle.

### Lines 93

```cpp
  93:             sink ^= first ^ last;
```

Consumes result in the anti-optimization sink.

**C++ / implementation concepts:** XOR

**Algorithm-proof role:** Benchmark hygiene.

### Lines 96–99

```cpp
  96:         auto end = Clock::now();
  97:         double avgOps = static_cast<double>(totalIters) / trials;
  98:         double theory = 2.0 * log2(static_cast<double>(n));
  99:         double avgNs = chrono::duration<double, nano>(end - start).count() / trials;
```

Computes average operations, theoretical `2 log2(n)`, and average nanoseconds.

**C++ / implementation concepts:** static_cast; chrono

**Algorithm-proof role:** Experimental asymptotic comparison.

### Lines 101–106

```cpp
 101:         cout << left << setw(12) << n
 102:              << setw(16) << fixed << setprecision(3) << avgOps
 103:              << setw(16) << theory
 104:              << setw(18) << avgOps / theory
 105:              << setw(12) << group
 106:              << setw(16) << avgNs << '\n';
```

Prints measured search work, theoretical work, normalized ratio, output size `k`, and timing.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Shows search `Theta(log n)` separately from reporting cost.

### Lines 109–113

```cpp
 109:     cout << "\nInterpretation: the two boundary searches scale as O(log n)."
 110:             " If k positions are printed, O(k) extra work is unavoidable.\n";
 111:     (void)sink;
 112:     return 0;
 113: }
```

Explains the output-sensitive bound and exits.

**Algorithm-proof role:** `Theta(log n + k)` when all `k` indices must actually be emitted.

## Problem 2 proof and complexity map

- **Lower-bound invariant:** L23–L30.
- **Upper-bound invariant:** L34–L41.
- **Initialization:** L23 and L34.
- **Maintenance:** L27–L28 and L38–L39.
- **Progress:** interval length strictly decreases in both loops.
- **Termination:** L24/L35 stop at a unique boundary.
- **Output-sensitive complexity:** L48–L49 identifies a range of `k` items. Locating it is `Theta(log n)`; physically printing all `k` positions requires `Omega(k)`, giving `Theta(log n + k)`.
- **Correctness oracle:** L87–L92.
- **Experiment:** L70–L106.


# 6. Problem 3 — Minimum in a cyclically sorted list

**Precondition:** array is a rotation of a nondecreasing sequence. Distinct values give logarithmic worst-case behavior; duplicates create ambiguity.

### Lines 18–20

```cpp
  18: struct Stats {
  19:     long long iterations = 0;
  20: };
```

Counts search-loop iterations.

**C++ / implementation concepts:** struct

**Algorithm-proof role:** Benchmark instrumentation.

### Lines 22–23

```cpp
  22: int findMinimumPosition(const vector<int>& a, Stats& s) {
  23:     if (a.empty()) return -1;
```

Starts the function and handles empty input immediately.

**Algorithm-proof role:** Boundary case.

### Lines 25

```cpp
  25:     int lo = 0, hi = static_cast<int>(a.size()) - 1;
```

Initializes an inclusive candidate interval `[lo,hi]` containing the minimum.

**C++ / implementation concepts:** static_cast

**Algorithm-proof role:** **Initialization:** minimum is somewhere in the whole nonempty array.

### Lines 27–29

```cpp
  27:     while (lo < hi) {
  28:         ++s.iterations;
  29:         int mid = lo + (hi - lo) / 2;
```

Loops until one position remains and computes midpoint.

**Algorithm-proof role:** **Progress framework:** candidate interval shrinks.

### Lines 31–32

```cpp
  31:         if (a[mid] < a[hi])
  32:             hi = mid;
```

If `a[mid] < a[hi]`, the segment from `mid` to `hi` is ordered with `mid` no larger than the right endpoint, so the rotation minimum cannot lie strictly right of `mid`; keep `mid`.

**Algorithm-proof role:** **Maintenance:** set `hi=mid`.

### Lines 33–34

```cpp
  33:         else if (a[mid] > a[hi])
  34:             lo = mid + 1;
```

If `a[mid] > a[hi]`, the rotation break must be strictly to the right of `mid`; discard through `mid`.

**Algorithm-proof role:** **Maintenance:** set `lo=mid+1`.

### Lines 35–36

```cpp
  35:         else
  36:             --hi; // duplicate-safe; may cause O(n) worst case
```

If equal, direction cannot be inferred. Dropping one equal right endpoint is safe because an equal value remains represented, but only one element may be removed.

**C++ / implementation concepts:** duplicate handling

**Algorithm-proof role:** Correct but creates `O(n)` worst case.

### Lines 37–39

```cpp
  37:     }
  38: 
  39:     return lo;
```

When `lo==hi`, exactly one candidate remains; return it.

**Algorithm-proof role:** **Termination/postcondition.**

### Lines 42–47

```cpp
  42: int main() {
  43:     cout << "Problem 3: Minimum in a cyclically sorted list\n";
  44:     cout << "Theory: O(log n) for distinct elements; O(n) worst case with many duplicates\n\n";
  45: 
  46:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
  47:     const int trials = 100000;
```

Prints theory and chooses four large sizes with 100,000 repeated searches.

**Algorithm-proof role:** Benchmark setup.

### Lines 49–54

```cpp
  49:     cout << "Distinct values:\n";
  50:     cout << left << setw(12) << "n"
  51:          << setw(16) << "avg_iters"
  52:          << setw(16) << "log2(n)"
  53:          << setw(18) << "iters/log2(n)"
  54:          << setw(16) << "avg_ns" << '\n';
```

Prints columns for the distinct-value experiment, normalized by `log2(n)`.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Experimental `Theta(log n)` check.

### Lines 56

```cpp
  56:     volatile int sink = 0;
```

Anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 58–62

```cpp
  58:     for (int n : sizes) {
  59:         int rotation = n / 3;
  60:         vector<int> a(n);
  61:         for (int i = 0; i < n; ++i)
  62:             a[i] = (i + rotation) % n;
```

Builds a rotated array by storing `(i+rotation)%n`. With distinct values `0..n-1`, the value `0` marks the minimum.

**C++ / implementation concepts:** modulo

**Algorithm-proof role:** Constructs a known-answer rotated input.

### Lines 64–65

```cpp
  64:         int expected = n - rotation;
  65:         if (expected == n) expected = 0;
```

Computes the analytically known position of value `0`.

**Algorithm-proof role:** Correctness oracle.

### Lines 67–72

```cpp
  67:         long long totalIters = 0;
  68:         auto start = Clock::now();
  69: 
  70:         for (int t = 0; t < trials; ++t) {
  71:             Stats s;
  72:             int pos = findMinimumPosition(a, s);
```

Starts timing and repeatedly runs the algorithm.

**C++ / implementation concepts:** chrono

**Algorithm-proof role:** High-iteration experiment.

### Lines 73–78

```cpp
  73:             totalIters += s.iterations;
  74:             if (pos != expected) {
  75:                 cerr << "Correctness failure\n";
  76:                 return 1;
  77:             }
  78:             sink ^= pos;
```

Accumulates iterations, checks exact minimum position, and consumes result.

**Algorithm-proof role:** Correctness + measurement.

### Lines 81–90

```cpp
  81:         auto end = Clock::now();
  82:         double avgOps = static_cast<double>(totalIters) / trials;
  83:         double theory = log2(static_cast<double>(n));
  84:         double avgNs = chrono::duration<double, nano>(end - start).count() / trials;
  85: 
  86:         cout << left << setw(12) << n
  87:              << setw(16) << fixed << setprecision(3) << avgOps
  88:              << setw(16) << theory
  89:              << setw(18) << avgOps / theory
  90:              << setw(16) << avgNs << '\n';
```

Calculates average operations, `log2(n)`, normalized ratio, and timing, then prints.

**C++ / implementation concepts:** static_cast; chrono

**Algorithm-proof role:** Validates distinct-case logarithmic trend.

### Lines 93–96

```cpp
  93:     cout << "\nDuplicate-heavy adversarial case:\n";
  94:     cout << left << setw(12) << "n"
  95:          << setw(16) << "iterations"
  96:          << setw(16) << "iterations/n" << '\n';
```

Starts a separate adversarial duplicate experiment and prints `iterations/n`.

**Algorithm-proof role:** Tests the stated worst-case degradation.

### Lines 98–100

```cpp
  98:     for (int n : sizes) {
  99:         vector<int> a(n, 7);
 100:         a[0] = 0; // rotated/nondecreasing form with massive ambiguity
```

Creates an array of mostly `7`s with a unique minimum `0` at the front. Equality comparisons repeatedly trigger `--hi`.

**C++ / implementation concepts:** fill constructor

**Algorithm-proof role:** Adversarial ambiguity.

### Lines 101–106

```cpp
 101:         Stats s;
 102:         int pos = findMinimumPosition(a, s);
 103:         if (pos != 0) {
 104:             cerr << "Duplicate-case correctness failure\n";
 105:             return 1;
 106:         }
```

Runs once for the adversarial size and checks that the returned index is still correct.

**Algorithm-proof role:** Correctness under duplicates.

### Lines 107–110

```cpp
 107:         cout << left << setw(12) << n
 108:              << setw(16) << s.iterations
 109:              << setw(16) << fixed << setprecision(6)
 110:              << static_cast<double>(s.iterations) / n << '\n';
```

Prints iteration count normalized by `n`. A near-constant ratio indicates linear degradation.

**Algorithm-proof role:** Experimental `Theta(n)` evidence for this family.

### Lines 113–116

```cpp
 113:     cout << "\nInterpretation: distinct inputs follow O(log n); duplicates can force O(n).\n";
 114:     (void)sink;
 115:     return 0;
 116: }
```

Prints conclusion and exits.

**Algorithm-proof role:** Summarizes best distinction: distinct vs duplicate-heavy.

## Problem 3 proof map

- **Invariant:** the minimum index remains in `[lo,hi]`.
- **Initialization:** L25.
- **Maintenance:** L31–L36.
- **Progress:** distinct case halves the interval; duplicate equality at L36 reduces it by only one.
- **Termination:** L27 and L39.
- **Worst-case distinction:** L36 is exactly why duplicates can force `Theta(n)`.
- **Adversarial experiment:** L93–L110.
- **Likely viva question:** “Why can’t you always claim `O(log n)`?” Answer: `a[mid] == a[hi]` gives no directional information, so the safe reduction can be only one element.


# 7. Problem 4 — Search in a sorted list of unknown size

**Idea:** first discover a finite search interval by exponential growth, then run Binary Search inside it.

### Lines 18–20

```cpp
  18: struct Stats {
  19:     long long probes = 0;
  20: };
```

Stores the number of data-source probes rather than only Binary Search iterations.

**Algorithm-proof role:** Operation metric matches the unknown-size access model.

### Lines 22–23

```cpp
  22: class UnknownSizeArray {
  23:     vector<int> data;
```

Defines a wrapper class that owns the hidden vector. The search routine is not given its size.

**C++ / implementation concepts:** class

**Algorithm-proof role:** Simulates an unknown-length indexed data source.

### Lines 25–26

```cpp
  25: public:
  26:     explicit UnknownSizeArray(vector<int> v) : data(move(v)) {}
```

Public constructor takes a vector by value and moves its storage into `data`. `explicit` blocks accidental implicit conversion.

**C++ / implementation concepts:** explicit constructor; move

**Algorithm-proof role:** Interface/ownership design.

### Lines 28–29

```cpp
  28:     bool get(long long index, int& value, Stats& s) const {
  29:         ++s.probes;
```

`get` is the only indexed-access interface and increments the probe counter on every attempted access.

**C++ / implementation concepts:** const member function; reference output

**Algorithm-proof role:** Models access cost.

### Lines 30–31

```cpp
  30:         if (index < 0 || index >= static_cast<long long>(data.size()))
  31:             return false;
```

Checks whether the requested index is outside the hidden data source and returns `false` instead of exposing its size.

**C++ / implementation concepts:** static_cast<long long>

**Algorithm-proof role:** Unknown-size boundary handling.

### Lines 32–33

```cpp
  32:         value = data[static_cast<size_t>(index)];
  33:         return true;
```

For a valid index, converts to `size_t`, reads the value through the output reference, and returns success.

**C++ / implementation concepts:** static_cast<size_t>

**Algorithm-proof role:** Safe indexing after validity check.

### Lines 37–38

```cpp
  37: long long searchUnknownSize(const UnknownSizeArray& a, int target, Stats& s) {
  38:     int value;
```

Begins search and allocates a variable receiving probed values.

**C++ / implementation concepts:** const reference

**Algorithm-proof role:** Algorithm interface.

### Lines 40–42

```cpp
  40:     if (!a.get(0, value, s)) return -1;
  41:     if (value == target) return 0;
  42:     if (value > target) return -1;
```

Handles index 0, immediate match, and the sorted-array case where the first value already exceeds target.

**Algorithm-proof role:** Boundary cases and early postconditions.

### Lines 44

```cpp
  44:     long long lo = 1, hi = 1;
```

Initializes exponential-search bounds at index 1.

**Algorithm-proof role:** **Initialization** for range discovery.

### Lines 46–49

```cpp
  46:     while (a.get(hi, value, s) && value < target) {
  47:         lo = hi + 1;
  48:         hi *= 2;
  49:     }
```

While the current probe exists and is below target, move `lo` just beyond it and double `hi`.

**C++ / implementation concepts:** short-circuit `&&`

**Algorithm-proof role:** **Maintenance + Progress:** discovered range expands geometrically `1,2,4,...`.

### Lines 51–52

```cpp
  51:     while (lo <= hi) {
  52:         long long mid = lo + (hi - lo) / 2;
```

After bracketing/overshooting, runs ordinary Binary Search on `[lo,hi]`.

**Algorithm-proof role:** Second phase initialization.

### Lines 54–55

```cpp
  54:         if (!a.get(mid, value, s)) {
  55:             hi = mid - 1;
```

If `mid` is outside the real hidden array, treat it like a value larger than any valid position by moving `hi` left.

**Algorithm-proof role:** Maintenance with unknown right boundary.

### Lines 56–57

```cpp
  56:         } else if (value == target) {
  57:             return mid;
```

If probed value equals target, return index immediately.

**Algorithm-proof role:** Sound successful termination.

### Lines 58–61

```cpp
  58:         } else if (value < target) {
  59:             lo = mid + 1;
  60:         } else {
  61:             hi = mid - 1;
```

Sorted order discards the left or right half according to comparison.

**Algorithm-proof role:** Binary Search maintenance and progress.

### Lines 63–65

```cpp
  63:     }
  64: 
  65:     return -1;
```

Loop ends when interval is empty; return `-1`.

**Algorithm-proof role:** Unsuccessful termination.

### Lines 68–73

```cpp
  68: int main() {
  69:     cout << "Problem 4: Search when list size is unknown\n";
  70:     cout << "Theory: Exponential Search + Binary Search = O(log p), p = target position\n\n";
  71: 
  72:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
  73:     const int trials = 100000;
```

Prints theory, sizes, and trial count.

**Algorithm-proof role:** Benchmark setup.

### Lines 75–79

```cpp
  75:     cout << left << setw(12) << "p"
  76:          << setw(16) << "avg_probes"
  77:          << setw(16) << "2log2(p)"
  78:          << setw(18) << "probes/theory"
  79:          << setw(16) << "avg_ns" << '\n';
```

Prints probe count versus approximately `2log2(p)` because exponential bracketing and Binary Search each cost logarithmic probes.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Theoretical normalization.

### Lines 81

```cpp
  81:     volatile long long sink = 0;
```

Anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 83–86

```cpp
  83:     for (int n : sizes) {
  84:         vector<int> v(n);
  85:         iota(v.begin(), v.end(), 0);
  86:         UnknownSizeArray a(move(v));
```

Builds `0..n-1` using `iota`, then moves the vector into the unknown-size wrapper.

**C++ / implementation concepts:** iota; move

**Algorithm-proof role:** Creates sorted hidden data.

### Lines 88–89

```cpp
  88:         int target = n - 1;
  89:         long long totalProbes = 0;
```

Chooses the last valid element as target, forcing range discovery near position `p=n-1`.

**Algorithm-proof role:** Harder search position.

### Lines 91–95

```cpp
  91:         auto start = Clock::now();
  92: 
  93:         for (int t = 0; t < trials; ++t) {
  94:             Stats s;
  95:             long long pos = searchUnknownSize(a, target, s);
```

Starts timing and repeats the search with a fresh probe counter.

**C++ / implementation concepts:** chrono

**Algorithm-proof role:** Benchmark execution.

### Lines 96–102

```cpp
  96:             totalProbes += s.probes;
  97: 
  98:             if (pos != target) {
  99:                 cerr << "Correctness failure\n";
 100:                 return 1;
 101:             }
 102:             sink ^= pos;
```

Adds probe count, verifies exact returned position, and consumes the result.

**Algorithm-proof role:** Correctness + operation count.

### Lines 105–108

```cpp
 105:         auto end = Clock::now();
 106:         double avgProbes = static_cast<double>(totalProbes) / trials;
 107:         double theory = 2.0 * log2(static_cast<double>(max(2, target)));
 108:         double avgNs = chrono::duration<double, nano>(end - start).count() / trials;
```

Computes mean probes, theoretical `2log2(p)` scale, and mean nanoseconds.

**C++ / implementation concepts:** static_cast; max; log2; chrono

**Algorithm-proof role:** Experimental complexity comparison.

### Lines 110–114

```cpp
 110:         cout << left << setw(12) << target
 111:              << setw(16) << fixed << setprecision(3) << avgProbes
 112:              << setw(16) << theory
 113:              << setw(18) << avgProbes / theory
 114:              << setw(16) << avgNs << '\n';
```

Prints one row including `probes/theory`.

**Algorithm-proof role:** Evidence for `Theta(log p)`.

### Lines 117–120

```cpp
 117:     cout << "\nInterpretation: probes/log2(p) stays bounded, matching O(log p).\n";
 118:     (void)sink;
 119:     return 0;
 120: }
```

Prints interpretation and exits.

**Algorithm-proof role:** Program conclusion.

## Problem 4 proof map

- **Exponential-search invariant:** every discarded probed position is strictly below target.
- **Initialization:** L44.
- **Maintenance:** L46–L49.
- **Progress:** `hi` doubles, so after `k` expansions `hi = 2^k`.
- **Bracketing termination:** the first failed/outsize/large probe bounds the relevant position.
- **Binary-search invariant:** target, if present, remains in `[lo,hi]`.
- **Binary-search progress:** L55, L59, L61.
- **Total complexity:** if target position is `p`, bracketing needs `Theta(log p)` and the bracket width is `O(p)`, so Binary Search also needs `Theta(log p)`.
- **Experiment:** L83–L114.


# 8. Problem 5 — k-th smallest element from two sorted lists

**Precondition:** `A` and `B` are individually sorted; `1 <= k <= A.size()+B.size()`.

**Key idea:** choose a partition with exactly `k` total elements on the left. Binary Search only the cut position in the smaller array.

### Lines 18–20

```cpp
  18: struct Stats {
  19:     long long iterations = 0;
  20: };
```

Counts partition-search iterations.

**Algorithm-proof role:** Benchmark instrumentation.

### Lines 22–25

```cpp
  22: long long kthElement(const vector<long long>& A,
  23:                      const vector<long long>& B,
  24:                      int k,
  25:                      Stats& s) {
```

Declares the optimized k-th-element function with two read-only vectors, one-based rank `k`, and a mutable statistics object.

**C++ / implementation concepts:** const references

**Algorithm-proof role:** Algorithm interface.

### Lines 26–27

```cpp
  26:     if (A.size() > B.size())
  27:         return kthElement(B, A, k, s);
```

If `A` is larger, recursively swaps argument roles so the Binary Search always occurs on the smaller array.

**C++ / implementation concepts:** recursion; reference arguments

**Algorithm-proof role:** Reduces time bound to `Theta(log min(m,n))`.

### Lines 29–30

```cpp
  29:     int m = static_cast<int>(A.size());
  30:     int n = static_cast<int>(B.size());
```

Stores sizes in signed `int` variables for partition arithmetic.

**C++ / implementation concepts:** static_cast

**Algorithm-proof role:** Implementation convenience; safe for assignment sizes.

### Lines 32–33

```cpp
  32:     if (k < 1 || k > m + n)
  33:         throw invalid_argument("k out of range");
```

Enforces the rank precondition and throws `invalid_argument` when violated.

**C++ / implementation concepts:** exception

**Algorithm-proof role:** Precondition checking.

### Lines 35–36

```cpp
  35:     int lo = max(0, k - n);
  36:     int hi = min(k, m);
```

Computes the only legal range of `cutA`. `cutB=k-cutA` must stay between `0` and `n`.

**Algorithm-proof role:** **Initialization:** every feasible k-element partition is represented in `[lo,hi]`.

### Lines 38–39

```cpp
  38:     const long long NEG = numeric_limits<long long>::lowest();
  39:     const long long POS = numeric_limits<long long>::max();
```

Creates extreme sentinels used when a cut is at the beginning or end of an array.

**C++ / implementation concepts:** numeric_limits

**Algorithm-proof role:** Avoids out-of-range access without arbitrary magic numbers.

### Lines 41–45

```cpp
  41:     while (lo <= hi) {
  42:         ++s.iterations;
  43: 
  44:         int cutA = lo + (hi - lo) / 2;
  45:         int cutB = k - cutA;
```

Starts Binary Search over partition positions, counts an iteration, chooses midpoint cut in `A`, and derives `cutB` so exactly `k` elements are left of the two cuts combined.

**C++ / implementation concepts:** overflow-safe midpoint

**Algorithm-proof role:** **Progress framework** and partition-size invariant `cutA+cutB=k`.

### Lines 47–50

```cpp
  47:         long long leftA  = (cutA == 0) ? NEG : A[cutA - 1];
  48:         long long rightA = (cutA == m) ? POS : A[cutA];
  49:         long long leftB  = (cutB == 0) ? NEG : B[cutB - 1];
  50:         long long rightB = (cutB == n) ? POS : B[cutB];
```

Reads the four values adjacent to the two cuts. Sentinels model missing neighbors at the boundaries.

**C++ / implementation concepts:** ternary operator; numeric_limits

**Algorithm-proof role:** Makes all partition cases use the same inequalities.

### Lines 52–53

```cpp
  52:         if (leftA <= rightB && leftB <= rightA)
  53:             return max(leftA, leftB);
```

Checks the valid-partition conditions: every left-side element is <= every right-side element. If true, the largest left-side value is exactly the k-th smallest overall.

**C++ / implementation concepts:** logical `&&`; max

**Algorithm-proof role:** **Termination + postcondition.**

### Lines 55–56

```cpp
  55:         if (leftA > rightB)
  56:             hi = cutA - 1;
```

If `leftA > rightB`, too many large elements were taken from `A`; move `cutA` left.

**Algorithm-proof role:** **Maintenance:** discard current/right partition choices.

### Lines 57–58

```cpp
  57:         else
  58:             lo = cutA + 1;
```

Otherwise `leftB > rightA`; too few elements were taken from `A`, so move `cutA` right.

**Algorithm-proof role:** **Maintenance:** discard current/left choices.

### Lines 59–61

```cpp
  59:     }
  60: 
  61:     throw logic_error("Unreachable");
```

Ends search; reaching the throw would contradict valid sorted inputs and valid `k`.

**Algorithm-proof role:** Total-correctness sanity assertion.

### Lines 64–68

```cpp
  64: long long kthByMerge(const vector<long long>& A,
  65:                      const vector<long long>& B,
  66:                      int k) {
  67:     size_t i = 0, j = 0;
  68:     long long value = 0;
```

Defines a much simpler merge-based method used only as a correctness oracle. `size_t` matches vector indices.

**C++ / implementation concepts:** size_t

**Algorithm-proof role:** Reference implementation.

### Lines 70–75

```cpp
  70:     for (int count = 1; count <= k; ++count) {
  71:         if (j == B.size() || (i < A.size() && A[i] <= B[j]))
  72:             value = A[i++];
  73:         else
  74:             value = B[j++];
  75:     }
```

Advances through the two sorted vectors exactly `k` selections, always taking the smaller current front.

**Algorithm-proof role:** Oracle complexity `Theta(k)`, intentionally simpler than optimized method.

### Lines 76–77

```cpp
  76:     return value;
  77: }
```

Returns the k-th selected value.

**Algorithm-proof role:** Oracle postcondition.

### Lines 79–84

```cpp
  79: int main() {
  80:     cout << "Problem 5: k-th smallest element from two sorted lists\n";
  81:     cout << "Theory: O(log(min(m,n))) time, O(1) extra space\n\n";
  82: 
  83:     // Randomized correctness check against a simple merge oracle.
  84:     mt19937 rng(123456);
```

Prints theory and creates reproducible random generator for correctness testing.

**C++ / implementation concepts:** mt19937

**Algorithm-proof role:** Test setup.

### Lines 85–93

```cpp
  85:     for (int test = 0; test < 5000; ++test) {
  86:         int m = 1 + rng() % 50;
  87:         int n = 1 + rng() % 50;
  88: 
  89:         vector<long long> A(m), B(n);
  90:         for (auto& x : A) x = rng() % 1000;
  91:         for (auto& x : B) x = rng() % 1000;
  92:         sort(A.begin(), A.end());
  93:         sort(B.begin(), B.end());
```

Generates 5,000 small random pairs, fills them, and sorts both arrays.

**C++ / implementation concepts:** range-for by reference; sort

**Algorithm-proof role:** Wide randomized correctness coverage.

### Lines 95–100

```cpp
  95:         int k = 1 + rng() % (m + n);
  96:         Stats s;
  97:         if (kthElement(A, B, k, s) != kthByMerge(A, B, k)) {
  98:             cerr << "Correctness failure\n";
  99:             return 1;
 100:         }
```

Chooses a valid random rank and compares optimized result against merge oracle; aborts on disagreement.

**Algorithm-proof role:** Correctness oracle: strong empirical validation.

### Lines 103–106

```cpp
 103:     cout << "Random correctness tests against merge oracle: PASSED (5000 cases)\n\n";
 104: 
 105:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
 106:     const int trials = 100000;
```

Reports oracle-test success and defines benchmark sizes/trials.

**Algorithm-proof role:** Separates correctness tests from complexity benchmark.

### Lines 108–112

```cpp
 108:     cout << left << setw(12) << "m=n"
 109:          << setw(16) << "avg_iters"
 110:          << setw(20) << "log2(min(m,n))"
 111:          << setw(18) << "iters/theory"
 112:          << setw(16) << "avg_ns" << '\n';
```

Prints operation counts normalized by `log2(min(m,n))`.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Asymptotic validation.

### Lines 114

```cpp
 114:     volatile long long sink = 0;
```

Anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 116–124

```cpp
 116:     for (int n : sizes) {
 117:         // Hard partition case: every A element is smaller than every B element.
 118:         // For k=n, the correct cut in A is at the far right, so Binary Search
 119:         // must genuinely shrink the partition range logarithmically.
 120:         vector<long long> A(n), B(n);
 121:         for (int i = 0; i < n; ++i) {
 122:             A[i] = i;
 123:             B[i] = static_cast<long long>(n) + i;
 124:         }
```

Constructs a deliberately difficult partition family: all `A` values are below all `B` values, so for `k=n` the correct cut in `A` is at the extreme right. This prevents the benchmark from accidentally finishing on the first midpoint.

**C++ / implementation concepts:** static_cast<long long>

**Algorithm-proof role:** Benchmark design quality: forces genuine logarithmic partition search.

### Lines 126–128

```cpp
 126:         int k = n;
 127:         long long expected = n - 1;
 128:         long long totalIters = 0;
```

Sets rank, exact expected answer, and total iteration accumulator.

**Algorithm-proof role:** Known-answer benchmark.

### Lines 130–134

```cpp
 130:         auto start = Clock::now();
 131: 
 132:         for (int t = 0; t < trials; ++t) {
 133:             Stats s;
 134:             long long ans = kthElement(A, B, k, s);
```

Starts timer and repeatedly runs the optimized algorithm.

**C++ / implementation concepts:** chrono

**Algorithm-proof role:** High-iteration timing.

### Lines 135–141

```cpp
 135:             totalIters += s.iterations;
 136: 
 137:             if (ans != expected) {
 138:                 cerr << "Correctness failure\n";
 139:                 return 1;
 140:             }
 141:             sink ^= ans;
```

Accumulates iterations, checks exact answer, and consumes it.

**Algorithm-proof role:** Correctness + operation count.

### Lines 144–147

```cpp
 144:         auto end = Clock::now();
 145:         double avgOps = static_cast<double>(totalIters) / trials;
 146:         double theory = log2(static_cast<double>(n));
 147:         double avgNs = chrono::duration<double, nano>(end - start).count() / trials;
```

Computes mean iterations, theoretical logarithm, and average nanoseconds.

**C++ / implementation concepts:** static_cast; chrono

**Algorithm-proof role:** Experimental comparison.

### Lines 149–153

```cpp
 149:         cout << left << setw(12) << n
 150:              << setw(16) << fixed << setprecision(3) << avgOps
 151:              << setw(20) << theory
 152:              << setw(18) << avgOps / theory
 153:              << setw(16) << avgNs << '\n';
```

Prints measured and theoretical values plus normalized ratio.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Evidence for `Theta(log min(m,n))`.

### Lines 156–159

```cpp
 156:     cout << "\nInterpretation: partition iterations grow logarithmically in the smaller list size.\n";
 157:     (void)sink;
 158:     return 0;
 159: }
```

Prints interpretation and exits.

**Algorithm-proof role:** Conclusion.

## Problem 5 proof map

- **Partition invariant:** `cutA + cutB = k` at L44–L45.
- **Search-space initialization:** L35–L36.
- **Maintenance:** L55–L58.
- **Progress:** `hi=cutA-1` or `lo=cutA+1`; partition interval strictly shrinks.
- **Termination:** L52–L53.
- **Why answer is `max(leftA,leftB)`:** exactly `k` elements are on the left and all are `<=` every right-side element; the largest left element therefore has rank `k`.
- **Why search smaller array:** L26–L27.
- **Correctness oracle:** L64–L100.
- **Worst-style benchmark:** L116–L153.
- **Space:** `Theta(1)` algorithmic extra space excluding recursion used only once to swap arrays.


# 9. Problem 6 — Consecutive values differ by at most 1

**Important assumption used by this implementation:** `A[0]=x`, `A[n-1]=y`, `x <= z <= y`, and `|A[i+1]-A[i]| <= 1`.

The array is **not required to be sorted**. Correctness comes from a discrete intermediate-value property, not ordinary sorted-order Binary Search.

### Lines 18–20

```cpp
  18: struct Stats {
  19:     long long iterations = 0;
  20: };
```

Counts interval-halving iterations.

**Algorithm-proof role:** Benchmark instrumentation.

### Lines 22–35

```cpp
  22: /*
  23: Assumption from the problem statement:
  24:     A[0] = x, A[n-1] = y, x <= z <= y
  25: and |A[i+1] - A[i]| <= 1.
  26: 
  27: The array need NOT be sorted.
  28: 
  29: Invariant:
  30:     A[left] <= z <= A[right].
  31: 
  32: If A[mid] < z, the discrete intermediate-value property guarantees
  33: z occurs in [mid, right], so left = mid.
  34: If A[mid] > z, z occurs in [left, mid], so right = mid.
  35: */
```

Documents the mathematical preconditions and the central invariant directly beside the implementation. This comment is part of the algorithm explanation, not executable code.

**Algorithm-proof role:** States the proof obligation explicitly.

### Lines 36–38

```cpp
  36: int findZ(const vector<int>& a, int z, Stats& s) {
  37:     if (a.empty() || !(a.front() <= z && z <= a.back()))
  38:         return -1;
```

Begins search and rejects empty/invalid endpoint-straddling inputs. `front()` and `back()` access the first and last values.

**C++ / implementation concepts:** short-circuit `||`; vector::front/back

**Algorithm-proof role:** Precondition enforcement.

### Lines 40–41

```cpp
  40:     int left = 0;
  41:     int right = static_cast<int>(a.size()) - 1;
```

Initializes endpoint indices to the full array.

**C++ / implementation concepts:** static_cast

**Algorithm-proof role:** **Initialization:** by precondition, `A[left] <= z <= A[right]`.

### Lines 43–44

```cpp
  43:     if (a[left] == z) return left;
  44:     if (a[right] == z) return right;
```

Returns immediately if either endpoint already equals `z`.

**Algorithm-proof role:** Boundary-case termination.

### Lines 46–48

```cpp
  46:     while (right - left > 1) {
  47:         ++s.iterations;
  48:         int mid = left + (right - left) / 2;
```

While at least one interior position exists, count an iteration and select the midpoint index.

**Algorithm-proof role:** **Progress framework:** interval will be replaced by one half.

### Lines 50–51

```cpp
  50:         if (a[mid] == z)
  51:             return mid;
```

If midpoint itself is `z`, return immediately.

**Algorithm-proof role:** Sound successful termination.

### Lines 53–54

```cpp
  53:         if (a[mid] < z)
  54:             left = mid;
```

If `A[mid] < z`, preserve the right endpoint and replace `left` with `mid`. Because values change by at most one per step and right endpoint is at least `z`, the path from `A[mid]` to `A[right]` cannot cross from below `z` to above/equal `z` without hitting `z`.

**Algorithm-proof role:** **Maintenance:** new endpoints still straddle `z`.

### Lines 55–56

```cpp
  55:         else
  56:             right = mid;
```

Otherwise `A[mid] > z`; symmetrically, the left-to-mid segment must contain `z`, so replace `right` with `mid`.

**Algorithm-proof role:** **Maintenance:** invariant preserved.

### Lines 57

```cpp
  57:     }
```

Ends one iteration. `right-left` is roughly halved because one endpoint becomes `mid`.

**Algorithm-proof role:** **Progress:** positive integer variant decreases.

### Lines 59–61

```cpp
  59:     if (a[left] == z) return left;
  60:     if (a[right] == z) return right;
  61:     return -1; // unreachable when preconditions hold
```

Once endpoints are adjacent, explicitly checks them. Under the stated preconditions, the final `-1` should be unreachable.

**Algorithm-proof role:** **Termination/postcondition.**

### Lines 64–69

```cpp
  64: bool validAdjacentDifference(const vector<int>& a) {
  65:     for (size_t i = 1; i < a.size(); ++i)
  66:         if (abs(a[i] - a[i - 1]) > 1)
  67:             return false;
  68:     return true;
  69: }
```

Defines a verifier for the adjacent-difference precondition. `size_t` matches the vector's unsigned index type.

**C++ / implementation concepts:** size_t; abs

**Algorithm-proof role:** Input/test validation helper.

### Lines 71–81

```cpp
  71: int main() {
  72:     cout << "Problem 6: Search when consecutive values differ by at most 1\n";
  73:     cout << "Theory under A[0]=x <= z <= y=A[n-1]: O(log n)\n\n";
  74: 
  75:     // Demonstrates that the array does not need to be monotone.
  76:     vector<int> demo = {0,1,2,3,2,3,4,3,4,5,6};
  77:     Stats demoStats;
  78:     int demoPos = findZ(demo, 4, demoStats);
  79:     cout << "Non-monotone demo index for z=4: " << demoPos << '\n';
  80:     cout << "Adjacent-difference condition: "
  81:          << (validAdjacentDifference(demo) ? "valid" : "invalid") << "\n\n";
```

Prints theory and runs a small nonmonotone demonstration. This is important: success does not rely on sorted order.

**C++ / implementation concepts:** ternary operator

**Algorithm-proof role:** Demonstrates the actual theorem being used.

### Lines 83–90

```cpp
  83:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
  84:     const int trials = 100000;
  85: 
  86:     cout << left << setw(12) << "n"
  87:          << setw(16) << "avg_iters"
  88:          << setw(16) << "log2(n)"
  89:          << setw(18) << "iters/log2(n)"
  90:          << setw(16) << "avg_ns" << '\n';
```

Defines large sizes, 100,000 trials, and terminal columns normalized by `log2(n)`.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Complexity experiment.

### Lines 92

```cpp
  92:     volatile int sink = 0;
```

Anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 94–103

```cpp
  94:     for (int n : sizes) {
  95:         // Non-monotone random-walk-like sequence:
  96:         // +1,+1,-1 repeated => adjacent difference exactly 1,
  97:         // yet the long-term trend is upward.
  98:         vector<int> a(n);
  99:         a[0] = 0;
 100:         for (int i = 1; i < n; ++i) {
 101:             if (i % 3 == 0) a[i] = a[i - 1] - 1;
 102:             else            a[i] = a[i - 1] + 1;
 103:         }
```

Constructs a nonmonotone sequence using `+1,+1,-1` repeatedly. Every adjacent difference is exactly one, but local decreases occur.

**C++ / implementation concepts:** modulo

**Algorithm-proof role:** Adversarially relevant construction showing monotonicity is unnecessary.

### Lines 105–108

```cpp
 105:         if (!validAdjacentDifference(a) || a.front() > a.back()) {
 106:             cerr << "Benchmark construction failure\n";
 107:             return 1;
 108:         }
```

Checks that the generated benchmark really satisfies the preconditions before trusting its results.

**Algorithm-proof role:** Benchmark validity guard.

### Lines 110–111

```cpp
 110:         int z = a.back() / 2;
 111:         long long totalIters = 0;
```

Chooses an interior `z` and resets iteration total.

**Algorithm-proof role:** Search target setup.

### Lines 113–117

```cpp
 113:         auto start = Clock::now();
 114: 
 115:         for (int t = 0; t < trials; ++t) {
 116:             Stats s;
 117:             int pos = findZ(a, z, s);
```

Starts timing and repeatedly executes the search.

**C++ / implementation concepts:** chrono

**Algorithm-proof role:** Benchmark execution.

### Lines 118–124

```cpp
 118:             totalIters += s.iterations;
 119: 
 120:             if (pos < 0 || a[pos] != z) {
 121:                 cerr << "Correctness failure\n";
 122:                 return 1;
 123:             }
 124:             sink ^= pos;
```

Accumulates operations and validates the returned index by checking `a[pos] == z`.

**Algorithm-proof role:** Correctness validation.

### Lines 127–130

```cpp
 127:         auto end = Clock::now();
 128:         double avgOps = static_cast<double>(totalIters) / trials;
 129:         double theory = log2(static_cast<double>(n));
 130:         double avgNs = chrono::duration<double, nano>(end - start).count() / trials;
```

Computes average operations, theoretical logarithm, and average nanoseconds.

**C++ / implementation concepts:** static_cast; chrono

**Algorithm-proof role:** Experimental complexity comparison.

### Lines 132–136

```cpp
 132:         cout << left << setw(12) << n
 133:              << setw(16) << fixed << setprecision(3) << avgOps
 134:              << setw(16) << theory
 135:              << setw(18) << avgOps / theory
 136:              << setw(16) << avgNs << '\n';
```

Prints result row and normalized ratio.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Evidence for logarithmic interval halving.

### Lines 139–143

```cpp
 139:     cout << "\nInterpretation: the maintained endpoint-straddling interval halves each iteration,"
 140:             " experimentally matching O(log n).\n";
 141:     (void)sink;
 142:     return 0;
 143: }
```

Prints interpretation and exits.

**Algorithm-proof role:** Conclusion.

## Problem 6 proof map

- **Precondition:** documented L23–L25 and checked L37–L38.
- **Invariant:** L29–L34 in the comment, implemented through L40–L56.
- **Initialization:** L40–L44.
- **Maintenance:** L53–L56, justified by `|A[i+1]-A[i]| <= 1`.
- **Progress:** L48, L54, L56; the index distance `right-left` decreases to at most about half.
- **Termination:** L46 plus L59–L61.
- **Partial correctness:** endpoint-straddling invariant ensures a solution remains in the retained interval.
- **Total correctness:** finite interval + halving progress.
- **Nonmonotone evidence:** L75–L81 and L94–L103.
- **Benchmark-precondition check:** L105–L108.

**Likely professor question:** “Why is comparing `A[mid]` with `z` legal if the array is not sorted?”  
Because the code is not using the sorted-array implication. It uses the endpoint values as witnesses and the bounded-step property to invoke a discrete intermediate-value argument.


# 10. Problem 7 — Door on an infinite wall

**Search sequence:** turning points `+1, -2, +4, -8, ...`.

### Lines 18–21

```cpp
  18: struct Result {
  19:     long long travelled = 0;
  20:     int turns = 0;
  21: };
```

Defines a result object containing total physical distance travelled and number of excursions/turns.

**C++ / implementation concepts:** struct

**Algorithm-proof role:** Multiple experimental metrics returned together.

### Lines 23–24

```cpp
  23: Result reachDoor(long long door) {
  24:     if (door == 0) return {0, 0};
```

Begins search simulation and handles a door at the origin with zero work.

**C++ / implementation concepts:** aggregate return `{0,0}`

**Algorithm-proof role:** Boundary case.

### Lines 26–29

```cpp
  26:     long long current = 0;
  27:     long long magnitude = 1;
  28:     int direction = 1;
  29:     Result result;
```

Initializes current position `0`, first excursion magnitude `1`, positive direction, and zeroed result.

**Algorithm-proof role:** **Initialization:** no position has yet been searched beyond the origin.

### Lines 31–33

```cpp
  31:     while (true) {
  32:         ++result.turns;
  33:         long long destination = direction * magnitude;
```

Begins an unbounded loop, counts an excursion, and computes its destination as signed magnitude.

**Algorithm-proof role:** Search progression.

### Lines 35–37

```cpp
  35:         bool crosses =
  36:             (current <= door && door <= destination) ||
  37:             (destination <= door && door <= current);
```

Tests whether the closed line segment from `current` to `destination` contains the door, regardless of travel direction.

**C++ / implementation concepts:** boolean expression

**Algorithm-proof role:** Correctness condition: walking the segment physically visits every point between endpoints.

### Lines 39–42

```cpp
  39:         if (crosses) {
  40:             result.travelled += llabs(door - current);
  41:             return result;
  42:         }
```

If the current excursion crosses the door, add only the distance needed to reach the door and return.

**C++ / implementation concepts:** llabs

**Algorithm-proof role:** Successful termination; does not overcount travel past the door.

### Lines 44–45

```cpp
  44:         result.travelled += llabs(destination - current);
  45:         current = destination;
```

Otherwise add the full excursion distance and update current position.

**C++ / implementation concepts:** llabs

**Algorithm-proof role:** Maintenance of exact travelled-distance accounting.

### Lines 46–47

```cpp
  46:         magnitude *= 2;
  47:         direction *= -1;
```

Double search radius and reverse direction.

**Algorithm-proof role:** **Progress:** geometric expansion plus alternating sides.

### Lines 48–49

```cpp
  48:     }
  49: }
```

Repeats until a crossing occurs. Because magnitude grows without bound, a finite-distance door must eventually be crossed.

**Algorithm-proof role:** Total-correctness termination argument.

### Lines 51–60

```cpp
  51: int main() {
  52:     cout << "Problem 7: Door on an infinite wall\n";
  53:     cout << "Strategy: +1, -2, +4, -8, ...\n";
  54:     cout << "Theory: travelled distance O(D), with deterministic competitive ratio <= 9\n\n";
  55: 
  56:     mt19937_64 rng(123456);
  57:     const vector<long long> scales = {
  58:         10LL, 100LL, 1000LL, 10000LL, 100000LL, 1000000LL, 10000000LL
  59:     };
  60:     const int trials = 50000;
```

Prints strategy/theory, creates 64-bit PRNG, tests distance scales up to ten million, and uses 50,000 trials per scale.

**C++ / implementation concepts:** mt19937_64; long long literals

**Algorithm-proof role:** Large-scale randomized experiment.

### Lines 62–66

```cpp
  62:     cout << left << setw(14) << "D scale"
  63:          << setw(18) << "avg(travel/D)"
  64:          << setw(18) << "max(travel/D)"
  65:          << setw(18) << "avg_turns"
  66:          << setw(20) << "turns/log2(D)" << '\n';
```

Prints travel ratio and turn-count normalization columns.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Competitive-analysis metrics.

### Lines 68–70

```cpp
  68:     for (long long D : scales) {
  69:         uniform_int_distribution<long long> dist(max(1LL, D / 2), D);
  70:         uniform_int_distribution<int> signDist(0, 1);
```

For each scale, chooses door distance uniformly from the upper half of the scale and an independent random sign.

**C++ / implementation concepts:** uniform_int_distribution

**Algorithm-proof role:** Tests both directions.

### Lines 72–74

```cpp
  72:         long double ratioSum = 0.0;
  73:         long double maxRatio = 0.0;
  74:         long double turnsSum = 0.0;
```

Uses `long double` accumulators for ratio statistics.

**C++ / implementation concepts:** long double

**Algorithm-proof role:** Precision for many summed ratios.

### Lines 76–80

```cpp
  76:         for (int t = 0; t < trials; ++t) {
  77:             long long door = dist(rng);
  78:             if (signDist(rng)) door = -door;
  79: 
  80:             Result r = reachDoor(door);
```

Runs 50,000 random door placements and calls the search.

**Algorithm-proof role:** Experimental sampling.

### Lines 81–82

```cpp
  81:             long double ratio =
  82:                 static_cast<long double>(r.travelled) / llabs(door);
```

Computes competitive ratio `travelled / |door|`, with explicit cast to floating type.

**C++ / implementation concepts:** static_cast<long double>; llabs

**Algorithm-proof role:** Main performance metric.

### Lines 84–86

```cpp
  84:             ratioSum += ratio;
  85:             maxRatio = max(maxRatio, ratio);
  86:             turnsSum += r.turns;
```

Accumulates average ratio, maximum observed ratio, and turns.

**C++ / implementation concepts:** max

**Algorithm-proof role:** Experiment aggregation.

### Lines 88–91

```cpp
  88:             if (ratio > 9.0000001L) {
  89:                 cerr << "Competitive-ratio failure\n";
  90:                 return 1;
  91:             }
```

Asserts the theoretical deterministic ratio bound is never exceeded in tested cases (small epsilon allows floating representation tolerance).

**Algorithm-proof role:** Runtime sanity check of competitive bound.

### Lines 94–96

```cpp
  94:         long double avgRatio = ratioSum / trials;
  95:         long double avgTurns = turnsSum / trials;
  96:         long double lg = log2(static_cast<long double>(D));
```

Computes averages and `log2(D)` for turn-count normalization.

**C++ / implementation concepts:** log2

**Algorithm-proof role:** The number of doubling stages is logarithmic.

### Lines 98–102

```cpp
  98:         cout << left << setw(14) << D
  99:              << setw(18) << fixed << setprecision(5) << static_cast<double>(avgRatio)
 100:              << setw(18) << static_cast<double>(maxRatio)
 101:              << setw(18) << static_cast<double>(avgTurns)
 102:              << setw(20) << static_cast<double>(avgTurns / lg) << '\n';
```

Prints statistics, converting long-double values for formatted output.

**C++ / implementation concepts:** static_cast<double>; iomanip

**Algorithm-proof role:** Terminal results.

### Lines 105–108

```cpp
 105:     cout << "\nInterpretation: travel/D remains bounded by a constant (< 9),"
 106:             " experimentally confirming O(D) movement.\n";
 107:     return 0;
 108: }
```

Interprets bounded `travel/D` as evidence for `Theta(D)` physical movement and exits.

**Algorithm-proof role:** Complexity conclusion.

## Problem 7 proof / competitive-analysis map

- **Initialization:** L26–L29.
- **Maintenance:** each completed excursion updates exact position/distance at L44–L45.
- **Progress:** L46–L47 doubles radius and alternates direction.
- **Termination:** finite `D` is eventually within a traversed segment because magnitudes go to infinity.
- **Why every intermediate point is covered:** the algorithm physically walks from one turning point to the next; L35–L37 checks the entire segment, not just endpoints.
- **Competitive analysis:** experiment measures `travelled/D` at L81–L88.
- **Why `+1,-2,+3,-4,...` is worse:** turning-point magnitude grows linearly, while distances between alternating endpoints grow linearly too; summing to reach distance `D` gives quadratic total travel. Doubling makes the sum geometric and therefore proportional to the largest term, `Theta(D)`.
- **Experiment:** L56–L102.


# 11. Problem 8 — Minimum shipping capacity

**Design pattern:** Binary Search on the answer, using a monotone feasibility predicate.

### Lines 18–21

```cpp
  18: struct Stats {
  19:     long long binaryIterations = 0;
  20:     long long packageChecks = 0;
  21: };
```

Tracks both outer Binary Search iterations and inner package inspections.

**C++ / implementation concepts:** struct

**Algorithm-proof role:** Separates the two multiplicative complexity factors.

### Lines 23–25

```cpp
  23: bool feasible(const vector<int>& weights, int days, long long capacity, Stats& s) {
  24:     int usedDays = 1;
  25:     long long load = 0;
```

Begins the feasibility predicate for a fixed capacity. Start on day 1 with zero current load.

**C++ / implementation concepts:** const reference

**Algorithm-proof role:** Greedy-check initialization.

### Lines 27–28

```cpp
  27:     for (int w : weights) {
  28:         ++s.packageChecks;
```

Scans packages in mandatory order and counts each inspection.

**C++ / implementation concepts:** range-for

**Algorithm-proof role:** Inner `Theta(n)` work.

### Lines 30–31

```cpp
  30:         if (load + w <= capacity) {
  31:             load += w;
```

If next package fits, place it on the current day.

**Algorithm-proof role:** Greedy maintenance.

### Lines 32–34

```cpp
  32:         } else {
  33:             ++usedDays;
  34:             load = w;
```

Otherwise start a new day and place this package there.

**Algorithm-proof role:** Greedy maintenance preserving package order.

### Lines 36–37

```cpp
  36:             if (usedDays > days)
  37:                 return false;
```

If used days exceed allowed days, immediately return false.

**Algorithm-proof role:** Early termination of predicate.

### Lines 39–41

```cpp
  39:     }
  40: 
  41:     return true;
```

If all packages were processed without exceeding days, capacity is feasible.

**Algorithm-proof role:** Predicate postcondition.

### Lines 44–46

```cpp
  44: long long minimumCapacity(const vector<int>& weights, int days, Stats& s) {
  45:     long long sum = accumulate(weights.begin(), weights.end(), 0LL);
  46:     long long maxWeight = *max_element(weights.begin(), weights.end());
```

Starts minimum-capacity search, computes total weight and heaviest package.

**C++ / implementation concepts:** accumulate; max_element

**Algorithm-proof role:** Builds answer-space bounds.

### Lines 48–50

```cpp
  48:     // Stronger lower bound than maxWeight alone.
  49:     long long averageBound = (sum + days - 1) / days;
  50:     long long lo = max(maxWeight, averageBound);
```

Computes a stronger lower bound: capacity must be at least both the heaviest package and `ceil(sum/days)`. Integer formula `(sum+days-1)/days` implements ceiling division.

**C++ / implementation concepts:** integer ceiling division

**Algorithm-proof role:** **Initialization:** no capacity below `lo` can possibly work.

### Lines 51

```cpp
  51:     long long hi = sum;
```

Sets upper bound to `sum`, which always works by shipping everything in one day (and therefore within any positive day limit).

**Algorithm-proof role:** Initialization of a known feasible upper bound.

### Lines 53–55

```cpp
  53:     while (lo < hi) {
  54:         ++s.binaryIterations;
  55:         long long mid = lo + (hi - lo) / 2;
```

Binary Searches capacity interval and counts iterations.

**C++ / implementation concepts:** overflow-safe midpoint

**Algorithm-proof role:** Binary Search on answer.

### Lines 57–58

```cpp
  57:         if (feasible(weights, days, mid, s))
  58:             hi = mid;
```

If `mid` is feasible, keep it as a possible optimum and discard larger unnecessary search space by setting `hi=mid`.

**Algorithm-proof role:** **Maintenance:** minimum feasible capacity remains in `[lo,hi]`.

### Lines 59–60

```cpp
  59:         else
  60:             lo = mid + 1;
```

If infeasible, monotonicity implies every smaller capacity is also infeasible, so set `lo=mid+1`.

**Algorithm-proof role:** **Maintenance + Progress.**

### Lines 61–63

```cpp
  61:     }
  62: 
  63:     return lo;
```

At `lo==hi`, lower and upper bounds meet at the smallest feasible capacity; return it.

**Algorithm-proof role:** **Termination/postcondition.**

### Lines 66–68

```cpp
  66: long long bruteCapacity(const vector<int>& weights, int days) {
  67:     long long sum = accumulate(weights.begin(), weights.end(), 0LL);
  68:     long long lo = *max_element(weights.begin(), weights.end());
```

Defines a slow reference method and its simple starting bound.

**Algorithm-proof role:** Correctness oracle.

### Lines 70–75

```cpp
  70:     for (long long c = lo; c <= sum; ++c) {
  71:         Stats s;
  72:         if (feasible(weights, days, c, s))
  73:             return c;
  74:     }
  75:     return -1;
```

Tries every capacity from heaviest package upward and returns the first feasible one.

**Algorithm-proof role:** Brute-force oracle; intentionally slow but obvious.

### Lines 78–83

```cpp
  78: int trialsFor(int n) {
  79:     if (n <= 1000) return 200;
  80:     if (n <= 10000) return 100;
  81:     if (n <= 100000) return 30;
  82:     return 10;
  83: }
```

Adapts benchmark repetition count downward as `n` grows so total execution remains practical.

**Algorithm-proof role:** Experiment engineering.

### Lines 85–89

```cpp
  85: int main() {
  86:     cout << "Problem 8: Minimum shipping capacity\n";
  87:     cout << "Theory: O(n log R), R = size of the capacity search interval\n\n";
  88: 
  89:     mt19937 rng(123456);
```

Prints theory and seeds PRNG for oracle tests.

**C++ / implementation concepts:** mt19937

**Algorithm-proof role:** Setup.

### Lines 91–96

```cpp
  91:     // Correctness against brute force on many small random cases.
  92:     for (int test = 0; test < 5000; ++test) {
  93:         int n = 1 + rng() % 12;
  94:         int days = 1 + rng() % n;
  95:         vector<int> w(n);
  96:         for (int& x : w) x = 1 + rng() % 20;
```

Generates 5,000 small random shipping instances.

**Algorithm-proof role:** Broad correctness testing.

### Lines 98–105

```cpp
  98:         Stats s;
  99:         long long fast = minimumCapacity(w, days, s);
 100:         long long slow = bruteCapacity(w, days);
 101: 
 102:         if (fast != slow) {
 103:             cerr << "Correctness failure\n";
 104:             return 1;
 105:         }
```

Runs optimized and brute-force solvers and aborts if their answers differ.

**Algorithm-proof role:** Correctness oracle.

### Lines 108–110

```cpp
 108:     cout << "Random correctness tests against brute force: PASSED (5000 cases)\n\n";
 109: 
 110:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
```

Reports oracle success and defines large performance sizes.

**Algorithm-proof role:** Transition to complexity benchmark.

### Lines 112–118

```cpp
 112:     cout << left << setw(12) << "n"
 113:          << setw(10) << "trials"
 114:          << setw(16) << "avg_bin_iter"
 115:          << setw(18) << "avg_pkg_checks"
 116:          << setw(18) << "n*log2(R)"
 117:          << setw(18) << "checks/theory"
 118:          << setw(16) << "avg_ms" << '\n';
```

Prints columns including `n*log2(R)` and `checks/theory`.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Asymptotic normalization.

### Lines 120

```cpp
 120:     volatile long long sink = 0;
```

Anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 122–125

```cpp
 122:     for (int n : sizes) {
 123:         vector<int> weights(n);
 124:         for (int i = 0; i < n; ++i)
 125:             weights[i] = 1 + ((37LL * i + 17) % 1000);
```

Builds deterministic pseudo-varied package weights using arithmetic modulo 1000.

**C++ / implementation concepts:** long long literal `37LL`

**Algorithm-proof role:** Reproducible large data without PRNG overhead in setup.

### Lines 127–128

```cpp
 127:         int days = max(1, n / 100);
 128:         int trials = trialsFor(n);
```

Chooses number of days and size-dependent trial count.

**C++ / implementation concepts:** max

**Algorithm-proof role:** Benchmark configuration.

### Lines 130–134

```cpp
 130:         long long sum = accumulate(weights.begin(), weights.end(), 0LL);
 131:         long long maxW = *max_element(weights.begin(), weights.end());
 132:         long long avgBound = (sum + days - 1) / days;
 133:         long long lo = max(maxW, avgBound);
 134:         long long R = max(1LL, sum - lo + 1);
```

Recomputes lower bound and defines `R`, the number of candidate capacities in the Binary Search interval.

**C++ / implementation concepts:** accumulate; max_element

**Algorithm-proof role:** Theoretical search-domain size.

### Lines 136–139

```cpp
 136:         long long totalBin = 0;
 137:         long long totalChecks = 0;
 138: 
 139:         auto start = Clock::now();
```

Resets counters and starts timer.

**C++ / implementation concepts:** chrono

**Algorithm-proof role:** Benchmark setup.

### Lines 141–146

```cpp
 141:         for (int t = 0; t < trials; ++t) {
 142:             Stats s;
 143:             long long ans = minimumCapacity(weights, days, s);
 144:             totalBin += s.binaryIterations;
 145:             totalChecks += s.packageChecks;
 146:             sink ^= ans;
```

Runs algorithm repeatedly, accumulating Binary Search iterations and package checks, then consumes answer.

**Algorithm-proof role:** Measures both nested complexity components.

### Lines 149–154

```cpp
 149:         auto end = Clock::now();
 150: 
 151:         double avgBin = static_cast<double>(totalBin) / trials;
 152:         double avgChecks = static_cast<double>(totalChecks) / trials;
 153:         double theory = static_cast<double>(n) * log2(static_cast<double>(R));
 154:         double avgMs = chrono::duration<double, milli>(end - start).count() / trials;
```

Stops timer and computes averages. The theoretical operation scale is `n * log2(R)`.

**C++ / implementation concepts:** static_cast; chrono

**Algorithm-proof role:** Direct complexity model.

### Lines 156–162

```cpp
 156:         cout << left << setw(12) << n
 157:              << setw(10) << trials
 158:              << setw(16) << fixed << setprecision(3) << avgBin
 159:              << setw(18) << avgChecks
 160:              << setw(18) << theory
 161:              << setw(18) << avgChecks / theory
 162:              << setw(16) << avgMs << '\n';
```

Prints results and normalized operation ratio.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Experimental evidence for `Theta(n log R)`.

### Lines 165–169

```cpp
 165:     cout << "\nInterpretation: package inspections stay proportional to n*log(R),"
 166:             " matching O(n log R).\n";
 167:     (void)sink;
 168:     return 0;
 169: }
```

Prints interpretation, consumes sink, returns success.

**Algorithm-proof role:** Program conclusion.

## Problem 8 proof map

- **Feasibility predicate:** L23–L42.
- **Monotonicity:** if capacity `C` works, every `C' > C` also works; if `C` fails, every `C' < C` fails.
- **Lower bound:** L45–L50.
- **Known feasible upper bound:** L51.
- **Binary Search initialization:** L50–L51.
- **Maintenance:** L57–L60.
- **Progress:** interval shrinks every iteration.
- **Termination:** L53 and L63.
- **Correctness oracle:** L66–L105.
- **Time complexity:** one predicate call is `Theta(n)`; answer-space search takes `Theta(log R)` predicate calls, yielding `Theta(n log R)`.
- **Operation-count experiment:** L130–L162.

**Industry point:** this is a standard parametric-search / “Binary Search on answer” pattern. The searched domain is not an array index; it is the monotone space of capacities.


# 12. Problem 9 — Random pivot versus midpoint Binary Search

### Lines 18–20

```cpp
  18: struct Stats {
  19:     long long iterations = 0;
  20: };
```

Shared iteration counter.

**Algorithm-proof role:** Operation measurement.

### Lines 22–23

```cpp
  22: int midpointBinarySearch(const vector<int>& a, int target, Stats& s) {
  23:     int lo = 0, hi = static_cast<int>(a.size()) - 1;
```

Begins deterministic midpoint search and initializes inclusive candidate interval.

**C++ / implementation concepts:** static_cast

**Algorithm-proof role:** **Initialization:** target, if present, is in `[lo,hi]`.

### Lines 25–27

```cpp
  25:     while (lo <= hi) {
  26:         ++s.iterations;
  27:         int mid = lo + (hi - lo) / 2;
```

Loops while interval is nonempty, counts one comparison stage, and chooses deterministic midpoint.

**Algorithm-proof role:** Progress guarantee: midpoint balances the interval.

### Lines 29–31

```cpp
  29:         if (a[mid] == target) return mid;
  30:         if (a[mid] < target) lo = mid + 1;
  31:         else hi = mid - 1;
```

Returns on equality; otherwise sorted order discards one half.

**Algorithm-proof role:** **Maintenance:** target cannot be in discarded half.

### Lines 32–34

```cpp
  32:     }
  33:     return -1;
  34: }
```

If interval becomes empty, target is absent.

**Algorithm-proof role:** Unsuccessful termination.

### Lines 36–40

```cpp
  36: int randomPivotBinarySearch(const vector<int>& a,
  37:                             int target,
  38:                             mt19937& rng,
  39:                             Stats& s) {
  40:     int lo = 0, hi = static_cast<int>(a.size()) - 1;
```

Declares randomized variant. The PRNG is passed by reference so its state advances across calls.

**C++ / implementation concepts:** reference parameter

**Algorithm-proof role:** Same correctness interface, different pivot policy.

### Lines 42–45

```cpp
  42:     while (lo <= hi) {
  43:         ++s.iterations;
  44:         uniform_int_distribution<int> pivotDist(lo, hi);
  45:         int pivot = pivotDist(rng);
```

Each iteration creates a uniform distribution over current indices and samples one random pivot.

**C++ / implementation concepts:** uniform_int_distribution; mt19937

**Algorithm-proof role:** Randomized algorithm step.

### Lines 47–49

```cpp
  47:         if (a[pivot] == target) return pivot;
  48:         if (a[pivot] < target) lo = pivot + 1;
  49:         else hi = pivot - 1;
```

Uses the same equality and sorted-order elimination logic as standard Binary Search.

**Algorithm-proof role:** Correctness invariant identical to midpoint version.

### Lines 50–52

```cpp
  50:     }
  51:     return -1;
  52: }
```

Returns absent after interval empties.

**Algorithm-proof role:** Termination.

### Lines 54–56

```cpp
  54: int trialsFor(int n) {
  55:     return (n <= 100000) ? 100000 : 50000;
  56: }
```

Chooses 100,000 trials for sizes through 100,000 and 50,000 for one million to keep runtime manageable.

**C++ / implementation concepts:** ternary operator

**Algorithm-proof role:** Benchmark engineering.

### Lines 58–65

```cpp
  58: int main() {
  59:     cout << "Problem 9: Random pivot vs midpoint Binary Search\n";
  60:     cout << "Theory:\n";
  61:     cout << "  midpoint: worst-case O(log n)\n";
  62:     cout << "  random pivot: expected O(log n), worst-case O(n)\n\n";
  63: 
  64:     mt19937 rng(123456);
  65:     const vector<int> sizes = {1000, 10000, 100000, 1000000};
```

Prints theoretical distinction, seeds PRNG, and chooses sizes.

**Algorithm-proof role:** Experiment setup.

### Lines 67–73

```cpp
  67:     cout << left << setw(12) << "n"
  68:          << setw(10) << "trials"
  69:          << setw(16) << "mid_avg"
  70:          << setw(18) << "mid/log2(n)"
  71:          << setw(16) << "rand_avg"
  72:          << setw(18) << "rand/ln(n)"
  73:          << setw(14) << "rand_max" << '\n';
```

Prints deterministic and randomized average normalized by logarithmic functions, plus the maximum randomized iteration count.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Compares expected behavior and tail behavior.

### Lines 75

```cpp
  75:     volatile int sink = 0;
```

Anti-optimization sink.

**C++ / implementation concepts:** volatile

**Algorithm-proof role:** Benchmark hygiene.

### Lines 77–79

```cpp
  77:     for (int n : sizes) {
  78:         vector<int> a(n);
  79:         iota(a.begin(), a.end(), 0);
```

Builds sorted array `0..n-1` with `iota`.

**Algorithm-proof role:** Known position equals key.

### Lines 81–82

```cpp
  81:         int trials = trialsFor(n);
  82:         uniform_int_distribution<int> targetDist(0, n - 1);
```

Gets trial count and creates uniform target distribution.

**C++ / implementation concepts:** uniform_int_distribution

**Algorithm-proof role:** Successful targets sampled uniformly.

### Lines 84–86

```cpp
  84:         long long midTotal = 0;
  85:         long long randTotal = 0;
  86:         long long randMax = 0;
```

Resets totals and maximum observed random depth.

**Algorithm-proof role:** Statistics setup.

### Lines 88–95

```cpp
  88:         for (int t = 0; t < trials; ++t) {
  89:             int target = targetDist(rng);
  90: 
  91:             Stats midStats;
  92:             int p1 = midpointBinarySearch(a, target, midStats);
  93: 
  94:             Stats randStats;
  95:             int p2 = randomPivotBinarySearch(a, target, rng, randStats);
```

For each target, runs deterministic and randomized searches with separate counters.

**Algorithm-proof role:** Head-to-head comparison on identical target.

### Lines 97–100

```cpp
  97:             if (p1 != target || p2 != target) {
  98:                 cerr << "Correctness failure\n";
  99:                 return 1;
 100:             }
```

Checks both algorithms return the exact target index.

**Algorithm-proof role:** Correctness validation.

### Lines 102–105

```cpp
 102:             midTotal += midStats.iterations;
 103:             randTotal += randStats.iterations;
 104:             randMax = max(randMax, randStats.iterations);
 105:             sink ^= p1 ^ p2;
```

Accumulates means, tracks randomized maximum, consumes outputs.

**C++ / implementation concepts:** max; XOR

**Algorithm-proof role:** Expected vs tail statistics.

### Lines 108–109

```cpp
 108:         double midAvg = static_cast<double>(midTotal) / trials;
 109:         double randAvg = static_cast<double>(randTotal) / trials;
```

Computes average iteration counts.

**C++ / implementation concepts:** static_cast

**Algorithm-proof role:** Mean performance.

### Lines 111–117

```cpp
 111:         cout << left << setw(12) << n
 112:              << setw(10) << trials
 113:              << setw(16) << fixed << setprecision(4) << midAvg
 114:              << setw(18) << midAvg / log2(static_cast<double>(n))
 115:              << setw(16) << randAvg
 116:              << setw(18) << randAvg / log(static_cast<double>(n))
 117:              << setw(14) << randMax << '\n';
```

Prints midpoint average normalized by `log2(n)`, random average normalized by natural `ln(n)`, and maximum observed randomized depth.

**C++ / implementation concepts:** log2; log

**Algorithm-proof role:** Empirical expected-complexity comparison.

### Lines 120–124

```cpp
 120:     cout << "\nInterpretation: both averages grow logarithmically, but midpoint search"
 121:             " has the stronger deterministic O(log n) guarantee and smaller constants.\n";
 122:     (void)sink;
 123:     return 0;
 124: }
```

States conclusion and exits.

**Algorithm-proof role:** Midpoint has smaller constant and deterministic worst-case guarantee.

## Problem 9 proof / probability map

### Midpoint search
- **Initialization:** L23.
- **Maintenance:** L29–L31.
- **Progress:** midpoint guarantees candidate interval is at most about half as large after each unsuccessful iteration.
- **Worst-case:** `Theta(log n)`.

### Random-pivot search
- **Correctness invariant:** same as midpoint.
- **Progress:** interval always shrinks by at least one, but the split may be extremely unbalanced.
- **Worst-case:** `Theta(n)` is possible through repeatedly choosing an endpoint-like pivot.
- **Expected complexity:** `Theta(log n)` under the algorithm's uniform pivot randomness.
- **Experiment:** L88–L117 averages many random choices and records `randMax` to reveal the longer tail.

### Why randomization is not an improvement here
Unlike Quicksort, Binary Search already knows the mathematically best deterministic pivot for worst-case balance: the midpoint. Randomizing that choice cannot improve the worst-case split guarantee and generally increases expected comparisons.


# 13. Problem 10 — Poisoned bottle with 10 test strips

**Information model:** each strip has two final states, so `t` strips encode at most `2^t` distinguishable outcomes.

### Lines 1–10

```cpp
   1: #include <algorithm>
   2: #include <chrono>
   3: #include <cmath>
   4: #include <cstdint>
   5: #include <iomanip>
   6: #include <iostream>
   7: #include <vector>
   8: 
   9: using namespace std;
  10: using Clock = chrono::steady_clock;
```

Imports only the facilities required by this file, then defines `std` namespace convenience and the `Clock` alias.

**C++ / implementation concepts:** headers; type alias

**Algorithm-proof role:** Program infrastructure.

### Lines 12–14

```cpp
  12: int stripsNeeded(long long bottles) {
  13:     int strips = 0;
  14:     long long patterns = 1;
```

Starts the generalized strip-count function with zero strips and one available pattern (the empty zero-bit code).

**Algorithm-proof role:** Initialization for powers-of-two counting.

### Lines 16–19

```cpp
  16:     while (patterns < bottles) {
  17:         ++strips;
  18:         patterns <<= 1;
  19:     }
```

While the number of available binary patterns is smaller than the number of bottles, add one strip and double pattern capacity using left shift.

**C++ / implementation concepts:** bit shift `<<=`

**Algorithm-proof role:** **Maintenance + Progress:** after `t` iterations, `patterns = 2^t`.

### Lines 20–21

```cpp
  20:     return strips;
  21: }
```

Returns the minimum `t` for which `2^t >= bottles`.

**Algorithm-proof role:** Termination gives `ceil(log2 B)`.

### Lines 23–25

```cpp
  23: uint64_t encodeBottle(uint64_t bottle) {
  24:     return bottle;
  25: }
```

Encoding is simply the bottle number itself interpreted as a bit pattern.

**C++ / implementation concepts:** uint64_t

**Algorithm-proof role:** Binary code construction.

### Lines 27–29

```cpp
  27: uint64_t decodeStrips(uint64_t positivePattern) {
  28:     return positivePattern;
  29: }
```

Decoding is the inverse identity: the pattern of positive strips is interpreted as the bottle number.

**C++ / implementation concepts:** uint64_t

**Algorithm-proof role:** One-to-one encoding/decoding.

### Lines 31–34

```cpp
  31: // Simulates preparing the strips.
  32: // For each bottle, inspect each strip bit once.
  33: // This is Theta(B log B) preparation work.
  34: long long prepareTests(long long bottles, int strips) {
```

Documents and declares a simulation of the physical preparation workload.

**Algorithm-proof role:** Benchmark model.

### Lines 35–36

```cpp
  35:     volatile uint64_t sink = 0;
  36:     long long bitChecks = 0;
```

Creates an anti-optimization sink and bit-operation counter.

**C++ / implementation concepts:** volatile uint64_t

**Algorithm-proof role:** Benchmark instrumentation.

### Lines 38–39

```cpp
  38:     for (long long bottle = 0; bottle < bottles; ++bottle) {
  39:         uint64_t code = static_cast<uint64_t>(bottle);
```

Iterates over every bottle and explicitly converts its index to an unsigned 64-bit code.

**C++ / implementation concepts:** static_cast<uint64_t>

**Algorithm-proof role:** Represents bottle identifier as bits.

### Lines 41–42

```cpp
  41:         for (int strip = 0; strip < strips; ++strip) {
  42:             ++bitChecks;
```

Checks every strip bit for every bottle and counts that check.

**C++ / implementation concepts:** nested loops

**Algorithm-proof role:** Operation count is exactly `B * strips`.

### Lines 43–44

```cpp
  43:             if (code & (1ULL << strip))
  44:                 sink ^= static_cast<uint64_t>(strip + 1);
```

Tests bit `strip` by shifting `1ULL` left and bitwise-ANDing it with the code. If present, mixes strip id into sink with XOR.

**C++ / implementation concepts:** bit shift; bitwise AND; XOR; ULL literal

**Algorithm-proof role:** Implements binary membership: bottle contributes to strip iff that bit is 1.

### Lines 46–49

```cpp
  46:     }
  47: 
  48:     (void)sink;
  49:     return bitChecks;
```

Ends loops, consumes sink, returns exact number of bit checks.

**Algorithm-proof role:** Preparation-work postcondition.

### Lines 52–59

```cpp
  52: int main() {
  53:     cout << "Problem 10: 1000 bottles, one poisoned, 10 test strips\n";
  54:     cout << "Theory:\n";
  55:     cout << "  strips required = Theta(log B)\n";
  56:     cout << "  physical test rounds = 1 (one week)\n";
  57:     cout << "  straightforward preparation work = Theta(B log B)\n\n";
  58: 
  59:     cout << "For B=1000: ceil(log2(1000)) = 10 and 2^10 = 1024.\n\n";
```

Prints the three distinct complexity statements: strip resource `Theta(log B)`, one physical testing round, and straightforward preparation `Theta(B log B)`.

**Algorithm-proof role:** Keeps resource complexity separate from elapsed test rounds.

### Lines 61–70

```cpp
  61:     // Exhaustive correctness for the actual problem.
  62:     for (uint64_t bottle = 0; bottle < 1000; ++bottle) {
  63:         uint64_t pattern = encodeBottle(bottle);
  64:         uint64_t decoded = decodeStrips(pattern);
  65: 
  66:         if (decoded != bottle) {
  67:             cerr << "Correctness failure at bottle " << bottle << '\n';
  68:             return 1;
  69:         }
  70:     }
```

Exhaustively tests every one of the actual 1000 bottle IDs by encoding and decoding it; any collision/mismatch aborts.

**C++ / implementation concepts:** uint64_t

**Algorithm-proof role:** Exhaustive correctness oracle, stronger than random sampling.

### Lines 72–76

```cpp
  72:     cout << "Exhaustive encode/decode verification: PASSED (1000/1000 bottles)\n\n";
  73: 
  74:     const vector<long long> bottleCounts = {
  75:         1000LL, 10000LL, 100000LL, 1000000LL
  76:     };
```

Reports exhaustive success and defines larger generalized bottle counts.

**Algorithm-proof role:** Transition to scaling experiment.

### Lines 78–84

```cpp
  78:     cout << left << setw(12) << "B"
  79:          << setw(10) << "strips"
  80:          << setw(18) << "bit_checks"
  81:          << setw(18) << "B*log2(B)"
  82:          << setw(18) << "checks/theory"
  83:          << setw(14) << "prep_ms"
  84:          << setw(12) << "rounds" << '\n';
```

Prints columns including exact checks, `B log2(B)`, normalized ratio, preparation time, and testing rounds.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Experimental complexity validation.

### Lines 86–87

```cpp
  86:     for (long long B : bottleCounts) {
  87:         int strips = stripsNeeded(B);
```

For each bottle count, computes the minimum number of strips.

**Algorithm-proof role:** Resource scaling.

### Lines 89–91

```cpp
  89:         auto start = Clock::now();
  90:         long long checks = prepareTests(B, strips);
  91:         auto end = Clock::now();
```

Times the simulated preparation work.

**C++ / implementation concepts:** chrono

**Algorithm-proof role:** Wall-clock secondary metric.

### Lines 93–94

```cpp
  93:         double theory = static_cast<double>(B) * log2(static_cast<double>(B));
  94:         double prepMs = chrono::duration<double, milli>(end - start).count();
```

Computes theoretical `B log2(B)` scale and elapsed milliseconds.

**C++ / implementation concepts:** static_cast; log2; chrono

**Algorithm-proof role:** Theory comparison.

### Lines 96–102

```cpp
  96:         cout << left << setw(12) << B
  97:              << setw(10) << strips
  98:              << setw(18) << checks
  99:              << setw(18) << fixed << setprecision(3) << theory
 100:              << setw(18) << static_cast<double>(checks) / theory
 101:              << setw(14) << prepMs
 102:              << setw(12) << 1 << '\n';
```

Prints exact/normalized work and constant one-round testing time.

**C++ / implementation concepts:** iomanip

**Algorithm-proof role:** Shows separate algorithmic resources.

### Lines 105–108

```cpp
 105:     cout << "\nInterpretation: preparation checks/(B log2 B) stays bounded;"
 106:             " strip count grows as log B, while testing still uses one round.\n";
 107:     return 0;
 108: }
```

Prints interpretation and returns success.

**Algorithm-proof role:** Conclusion.

## Problem 10 proof / information-theory map

- **Lower bound:** `t` binary strips give only `2^t` distinct result vectors. Distinguishing `B` bottles requires `2^t >= B`, so `t >= ceil(log2 B)`.
- **Matching construction:** L23–L29 assigns exactly one unique `t`-bit code to each bottle.
- **Therefore optimal:** lower bound and construction match.
- **Initialization / maintenance / progress / termination of strip-count loop:** L12–L20.
- **Exhaustive correctness oracle:** L61–L70.
- **Preparation complexity:** L38–L45 performs `B * ceil(log2 B)` bit checks, which is `Theta(B log B)`.
- **Physical test rounds:** still exactly one week/one round because all strip mixtures are tested simultaneously.


# 14. Cross-problem viva index — where to point in the code

| Concept | Best code location(s) |
|---|---|
| Loop invariant | P1 L23–L33; P6 comment L29–L34; P8 L50–L60 |
| Initialization | P1 L23; P3 L25; P6 L40–L44; P8 L50–L51 |
| Maintenance | P1 L29–L32; P5 L55–L58; P6 L53–L56 |
| Progress / variant | P1 `hi-lo`; P6 `right-left`; P10 `patterns` doubles |
| Termination | P1 L25/L35–L37; P8 L53/L63 |
| Preconditions | P5 L32–L33; P6 L23–L25 and L37–L38 |
| Postconditions | P1 L35–L37; P5 L52–L53; P8 L63 |
| Partial correctness | Most clearly P1 and P8 |
| Total correctness | P1 shrinking interval; P7 unbounded geometric expansion reaches finite `D` |
| Soundness | P1 equality check L35; P6 returned index checked in experiment L120 |
| Completeness | P1 lower-bound invariant; P6 endpoint-straddling invariant |
| Lower/upper bounds | P8 L45–L51 |
| Output-sensitive complexity | P2 L44–L50 and `Theta(log n+k)` |
| Binary Search on answer | P8 L44–L64 |
| Exponential search | P4 L44–L49 |
| Expected vs worst case | P9 |
| Competitive analysis | P7 L81–L91 |
| Information-theoretic lower bound | P10 |
| Correctness oracle | P5 L64–L100; P8 L66–L105 |
| Exhaustive verification | P10 L61–L70 |
| Experimental normalization | P1 L82–L90; P8 L151–L162 |
| Adversarial input | P3 L93–L110; P5 L116–L124 |
| `chrono` | P1 L65/L81/L84; P8 L139/L149/L154 |
| `static_cast` | P1 L23/L82–L83; P4 L30/L32; P7 L82 |
| `numeric_limits` | P5 L38–L39 |
| `move` / ownership | P4 L26 and L86 |
| `explicit` | P4 L26 |
| `size_t` | P4 L32; P5 L67; P6 L65 |
| Structured binding | P2 L84 |
| `mt19937` | P1 L44; P5 L84; P9 L64 |
| `uniform_int_distribution` | P1 L62; P7 L69–L70; P9 L82 |
| `iota` | P4 L85; P9 L79 |
| `accumulate` | P8 L45/L67/L130 |
| Bit operations | P10 L18, L43–L44 |
| Integer ceiling division | P8 L49 |
| Overflow-safe midpoint | Every Binary Search: e.g. P1 L27 |

---

# 15. Questions a professor may ask and concise code-grounded answers

## “Why are operation counts more important than only timing?”
Point to `Stats` and normalized ratios. Operation counts correspond directly to the RAM-model analysis, whereas time depends on hardware, cache, branch prediction, optimization, and OS noise.

## “Does an experiment prove Big-O?”
No. The mathematical invariant/progress argument proves the asymptotic statement. The high-`n` experiment checks whether implementation behavior agrees with that model over tested inputs.

## “Why use `Theta` instead of only `O` when possible?”
`O` is only an upper bound. If both upper and lower growth match, `Theta` is the tighter statement. Several terminal messages say `O(...)` informally; for the controlled worst-case families where the normalized ratio remains bounded away from zero and infinity, the intended growth model is often `Theta(...)`.

## “Why use a fixed random seed?”
Reproducibility. The same source code produces the same pseudo-random sequence, making debugging and result comparison repeatable.

## “Why not use only runtime averages?”
Runtime can vary across machines and runs. The code prints both operation counts and time. The operation count is the primary theoretical metric.

## “Why use a brute-force or merge oracle if it is slower?”
An oracle is chosen for obvious correctness, not performance. It is run only on small cases. Agreement between independently structured implementations catches subtle bugs in the optimized method.

## “Why `static_cast` instead of ordinary cast syntax?”
It makes the intended type conversion explicit and limits the conversion category at compile time. It is easier to audit in modern C++.

## “Why `steady_clock`?”
Elapsed-time measurements require monotonic time. System clock adjustments must not make a benchmark duration negative or discontinuous.

## “Why `long long` for sums/counters?”
Repeated counts, total weights, and travelled distances can exceed 32-bit `int` range even when individual array values do not.

## “What is the single most important correctness idea in Binary Search?”
Do not memorize pointer movements. State a precise invariant describing what the candidate interval means, then prove every update discards only impossible candidates and strictly shrinks that interval.

---

# 16. Recommended reading connection to CLRS

For the proof style used here, review **CLRS, 4th ed., Section 2.1**, especially the discussion of loop invariants and the three proof obligations:

- Initialization
- Maintenance
- Termination

For the asymptotic interpretation of the operation-count experiments, review **Sections 2.2 and 3.1–3.2**.

For the baseline Binary Search exercise, see **Exercise 2.3-6**.

The code in this assignment adds an explicit **Progress** item to make the termination argument operational: identify the integer-valued measure that must strictly shrink/grow each iteration.

---

# 17. Final study method

For each `.cpp` file, be able to do the following without looking at this guide:

1. State the **precondition**.
2. State the **postcondition**.
3. State the **loop invariant** in one sentence.
4. Point to the exact **Initialization** lines.
5. Point to the exact **Maintenance** lines.
6. Name the **Progress/variant** quantity.
7. Explain why **Termination** plus the invariant yields the answer.
8. Derive the theoretical complexity.
9. Identify the operation counter used by the benchmark.
10. Explain why the normalized terminal ratio should remain bounded.
11. Identify the correctness oracle/adversarial test, if present.
12. Explain any nontrivial C++ feature on the referenced lines.

If you can do these twelve steps for all ten files, you are prepared not only to submit the code but also to defend the design and experiments in a viva.
