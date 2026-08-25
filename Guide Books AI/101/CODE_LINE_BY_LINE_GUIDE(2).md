# Scalar Matrix Multiplication and Image Convolution
## Submitted-Code-Based Line-by-Line Guide, Correctness Proof, Complexity, Optimization, Experiment, and C++ Viva Notes

This guide corresponds to the exact files:

- `problem01_scalar_matrix.cpp`
- `problem02_image_convolution.cpp`

The optimized algorithms are based directly on the code submitted earlier in **Assignment 3**.

The assignment requirement is interpreted as:

1. obtain a straightforward algorithm first;
2. identify inefficiencies;
3. improve the implementation without changing correctness;
4. compare baseline and improved versions experimentally.

---

# 0. What was submitted earlier

The earlier submitted Assignment 3 source contains:

- a flat contiguous `scaleImage(...)` implementation that multiplies every matrix/image element by a scalar;
- a cache-aware `convolve3x3(...)` implementation;
- flat contiguous image storage;
- multiple border modes;
- cache-width tiling;
- an interior sliding window that reuses six of nine values;
- optional fusion of scalar multiplication into the convolution stencil.

The current files preserve those implementation ideas and put them beside a simple baseline so the improvement is explicit.

---

# 1. Important terminology

## Scalar multiplication

For a mathematical matrix \(A\) and scalar \(\alpha\):

\[
B_{ij}=\alpha A_{ij}
\]

Every output element depends on exactly one input element.

For an \(R\times C\) matrix:

\[
\Theta(RC)
\]

work is necessary and sufficient.

For an \(N\times N\) matrix:

\[
\Theta(N^2)
\]

---

## Important representation detail

The submitted code represented the matrix as an **8-bit grayscale image**:

```cpp
using Pixel = uint8_t;
```

Therefore its scalar multiplication is not unconstrained real-number matrix multiplication.

After multiplying by `alpha`, the result is:

1. rounded,
2. clamped to `[0,255]`,
3. stored again as an 8-bit pixel.

For a purely mathematical floating-point matrix, one would instead use something such as:

```cpp
vector<double>
```

and would not call `toPixel`.

This distinction is worth mentioning if asked in a viva.

---

# 2. Four proof stages used throughout

For loops:

1. **Initialization**
2. **Maintenance**
3. **Progress**
4. **Termination**

For nested image loops, the invariant normally describes the prefix of pixels already processed.

---

# 3. Shared advanced C++ concepts

## `uint8_t`

Used as:

```cpp
using Pixel = uint8_t;
```

An unsigned integer type with exactly 8 bits where available.

Range:

\[
0\ldots255
\]

That exactly matches ordinary 8-bit grayscale pixels.

---

## `size_t`

Used for vector indexing and sizes.

`vector::size()` returns `size_t`.

It is an unsigned integer type large enough to represent object sizes.

---

## `static_cast`

Examples:

```cpp
static_cast<size_t>(r)
static_cast<Pixel>(rounded)
static_cast<double>(n)
```

This is an explicit C++ type conversion.

It is preferred over a C-style cast because the intended conversion category is clearer and easier to audit.

---

## `lround`

```cpp
lround(value)
```

rounds a floating-point value to the nearest integer-valued `long`.

This is required before storing the scaled/convolved result as a pixel.

---

## `clamp`

```cpp
clamp(rounded, 0L, 255L)
```

forces a value into the legal pixel range.

If:

```text
rounded < 0   -> 0
rounded > 255 -> 255
```

---

## `vector::data()`

In the convolution optimization:

```cpp
input.data.data()
```

returns a raw pointer to the first element of the contiguous vector storage.

This allows direct pointer arithmetic in the hot loop.

The vector still owns the memory.

The pointer is only a faster/direct view into it.

---

## Raw pointers

Examples:

```cpp
const Pixel* top;
Pixel* destination;
```

`const Pixel*` means:

> pointer to a pixel that cannot be modified through this pointer.

`Pixel*` permits writes.

The improved convolution uses three read pointers and one write pointer for the active rows.

---

## `constexpr`

Examples:

```cpp
constexpr int cacheLineBytes = 64;
constexpr int usableCacheBytes = 24 * 1024;
```

These values are compile-time constants.

---

## `array<double,9>`

A 3×3 kernel always contains exactly nine coefficients.

Therefore:

```cpp
using Kernel = array<double, 9>;
```

avoids dynamic allocation and expresses the fixed stencil size directly.

---

## `enum class`

```cpp
enum class BorderMode
```

creates a scoped enumeration.

Values are referred to as:

```cpp
BorderMode::Reflect
```

rather than leaking names such as `Reflect` into the surrounding namespace.

---

## `chrono::steady_clock`

Used for elapsed-time measurement because it is monotonic.

System time changes do not make the benchmark clock go backward.

---

## `volatile checksum`

The benchmark reads one output pixel into:

```cpp
volatile unsigned checksum
```

This makes the produced result observable and discourages the optimizer from deciding that the entire computation is unused.

`volatile` is not a universal benchmark barrier, but it is a compact classroom technique.

---

## `ULL`

Expressions such as:

```cpp
37ULL
255ULL
```

use unsigned-long-long integer literals.

This gives wide unsigned arithmetic before the final cast to `Pixel`.

---

# 4. Problem 1 — Scalar multiplication

**File:** `problem01_scalar_matrix.cpp`

---

# 4.1 Lines 1–7 — headers

```cpp
1: #include <algorithm>
2: #include <chrono>
3: #include <cmath>
4: #include <cstdint>
5: #include <iomanip>
6: #include <iostream>
7: #include <vector>
```

### L1

Provides `clamp`.

### L2

Benchmark timing.

### L3

Provides `lround`.

### L4

Provides `uint8_t`.

### L5

Terminal formatting.

### L6

Terminal I/O.

### L7

Contiguous dynamic matrix storage.

---

# 4.2 Lines 9–11 — aliases

```cpp
9: using namespace std;
10: using Clock = chrono::steady_clock;
11: using Pixel = uint8_t;
```

The key representation decision is L11.

One matrix element occupies one byte.

---

# 4.3 Lines 15–32 — matrix representation

```cpp
15: struct Matrix {
16:     int rows;
17:     int columns;
18:     vector<Pixel> data;
...
32: };
```

Instead of:

```cpp
vector<vector<Pixel>>
```

the matrix uses one flat vector.

Element \((r,c)\) is stored at:

\[
r\times columns+c
\]

This is **row-major contiguous storage**.

---

## Lines 20–23 — constructor

```cpp
20: Matrix(int r, int c)
21:     : rows(r),
22:       columns(c),
23:       data(static_cast<size_t>(r) * static_cast<size_t>(c)) {}
```

This is a constructor initializer list.

It initializes:

- `rows`,
- `columns`,
- one vector containing \(r\times c\) pixels.

Using `size_t` avoids signed-size arithmetic for the allocation count.

---

## Lines 25–30 — accessors

```cpp
25: Pixel& at(int r, int c) {
26:     return data[static_cast<size_t>(r) * columns + c];
27: }

29: Pixel at(int r, int c) const {
30:     return data[static_cast<size_t>(r) * columns + c];
31: }
```

The non-const version returns:

```cpp
Pixel&
```

so the caller can assign into the matrix.

The const version returns a value for read-only access.

Note that this custom `at` is **not** `vector::at()` and performs no bounds check.

---

# 4.4 Lines 34–38 — convert arithmetic result to pixel

```cpp
34: Pixel toPixel(double value) {
35:     long rounded = lround(value);
36:     rounded = clamp(rounded, 0L, 255L);
37:     return static_cast<Pixel>(rounded);
38: }
```

Three stages:

1. floating-point result,
2. round to nearest integer,
3. saturate to valid 8-bit range.

This avoids unsigned wraparound.

Example:

```text
300 -> 255
-20 -> 0
127.7 -> 128
```

---

# 5. Baseline scalar algorithm

## Lines 41–52

```cpp
41: Matrix scalarMultiplyBaseline(const Matrix& input, double alpha) {
42:     Matrix output(input.rows, input.columns);

44:     for (int row = 0; row < input.rows; ++row) {
45:         for (int column = 0; column < input.columns; ++column) {
46:             output.at(row, column) =
47:                 toPixel(alpha * input.at(row, column));
48:         }
49:     }

51:     return output;
52: }
```

This is the natural algorithm obtained directly from:

\[
B_{ij}=\alpha A_{ij}
\]

For each row and each column:

```text
output[row][column] = alpha * input[row][column]
```

---

# 5.1 Correctness invariant

During the nested loops:

> Every element before the current `(row,column)` position in row-major order already equals the correctly scaled input element.

### Initialization

Before L44 begins, no output element has yet been claimed as processed.

The invariant is true vacuously.

### Maintenance

L46–L47 writes exactly:

\[
toPixel(\alpha A_{row,column})
\]

for the current element.

Thus one more output entry becomes correct.

### Progress

- `column++` advances through a row.
- when a row finishes, `row++`.

Exactly \(RC\) matrix positions are visited.

### Termination

After both loops finish, every output position has been assigned its required scaled value.

---

# 5.2 Baseline time complexity

For an \(R\times C\) matrix:

\[
RC
\]

loop iterations.

Each performs constant work.

Therefore:

\[
\Theta(RC)
\]

For square \(N\times N\):

\[
\Theta(N^2)
\]

---

# 6. Improved submitted scalar algorithm

## Lines 55–62

```cpp
55: Matrix scalarMultiplyImproved(const Matrix& input, double alpha) {
56:     Matrix output(input.rows, input.columns);

58:     for (size_t i = 0; i < input.data.size(); ++i)
59:         output.data[i] = toPixel(alpha * input.data[i]);

61:     return output;
62: }
```

This is the key idea preserved from the submitted Assignment 3 source.

Since the matrix is already one contiguous block, there is no algorithmic need to express traversal as separate rows and columns.

---

# 6.1 What is improved

The direct flat loop:

```cpp
output.data[i]
```

removes the explicit two-dimensional indexing expression from the source-level hot loop.

It also gives the compiler a particularly simple contiguous pattern:

```text
read input[i]
compute
write output[i]
i++
```

This is friendly to:

- hardware prefetching,
- cache lines,
- compiler auto-vectorization,
- simple loop control.

---

# 6.2 What does NOT improve

The asymptotic complexity cannot improve.

The output has \(RC\) elements.

Every one must be produced.

Therefore any correct general algorithm has lower bound:

\[
\Omega(RC)
\]

The flat loop achieves:

\[
O(RC)
\]

Hence it is asymptotically optimal:

\[
\Theta(RC)
\]

---

# 6.3 Important performance critique

A modern optimizing compiler may inline the baseline accessor and transform the nested loop into code almost as good as the flat loop.

Therefore:

> **The flat traversal is structurally simpler and optimization-friendly, but it is not mathematically guaranteed to be faster on every compiler, size, or machine.**

The experiment intentionally prints actual speedup rather than assuming one.

This is the correct industry interpretation.

---

# 7. Scalar benchmark configuration

## Lines 64–71

```cpp
64: int repeatsFor(int n) {
65:     if (n <= 128) return 300;
66:     if (n <= 256) return 150;
67:     if (n <= 512) return 80;
68:     if (n <= 1024) return 30;
69:     if (n <= 2048) return 10;
70:     return 4;
71: }
```

Small matrices need many repetitions because individual timings are tiny.

Large matrices use fewer repetitions because each run processes millions of elements.

---

## Lines 77–78

```cpp
77: const vector<int> sizes = {64, 128, 256, 512, 1024, 2048, 4096};
78: constexpr double alpha = 1.25;
```

Largest matrix:

\[
4096^2=16,777,216
\]

elements.

---

# 8. Scalar deterministic data

## Lines 97–102

```cpp
97: for (int n : sizes) {
98:     Matrix input(n, n);

100:     for (size_t i = 0; i < input.data.size(); ++i)
101:         input.data[i] =
102:             static_cast<Pixel>((37ULL * i + 17ULL) & 255ULL);
```

A deterministic arithmetic pattern is used instead of calling a random generator millions of times.

Advantages:

- reproducible,
- fast data setup,
- broad range of byte values,
- setup is outside timed algorithm loops.

---

# 9. Scalar correctness oracle

## Lines 104–110

```cpp
104: Matrix expected = scalarMultiplyBaseline(input, alpha);
105: Matrix actual = scalarMultiplyImproved(input, alpha);

107: if (expected.data != actual.data) {
108:     cerr << "Correctness failure at N=" << n << '\n';
109:     return 1;
110: }
```

The baseline is treated as the specification implementation.

The improved output must be byte-for-byte identical.

This verifies that the optimization has not changed semantics.

---

# 10. Scalar timing

## Baseline — Lines 116–124

```cpp
116: for (int r = 0; r < repeats; ++r) {
117:     auto start = Clock::now();
118:     Matrix output = scalarMultiplyBaseline(input, alpha);
119:     auto end = Clock::now();
...
124: }
```

## Improved — Lines 126–134

```cpp
126: for (int r = 0; r < repeats; ++r) {
127:     auto start = Clock::now();
128:     Matrix output = scalarMultiplyImproved(input, alpha);
129:     auto end = Clock::now();
...
134: }
```

Both benchmark structures are symmetric.

---

# 10.1 Allocation caveat

Both functions construct a new output matrix inside the timed region.

That matches the submitted function interface and measures end-to-end function cost.

However it also means measured time includes:

\[
\Theta(N^2)
\]

output allocation/initialization effects.

If the professor asks for a **pure computational-kernel microbenchmark**, a stricter alternative would preallocate the output buffer and time only the scaling loop.

The present benchmark instead evaluates the complete function as written.

---

# 11. Experimental complexity validation

## Lines 136–146

```cpp
136: const double elements =
137:     static_cast<double>(n) * static_cast<double>(n);
...
142: const double baseNsPerElement =
143:     baseMs * 1.0e6 / elements;
...
145: const double improvedNsPerElement =
146:     improvedMs * 1.0e6 / elements;
```

For:

\[
T(N)=\Theta(N^2)
\]

we examine:

\[
\frac{T(N)}{N^2}
\]

The program expresses this as:

```text
nanoseconds per element
```

If it remains bounded as \(N\) grows, measured growth agrees with the theoretical \(N^2\) model.

---

# 12. Scalar proof map

| Concept | Exact lines |
|---|---|
| Matrix representation | L15–L32 |
| Pixel saturation | L34–L38 |
| Baseline algorithm | L41–L52 |
| Baseline initialization | L42–L45 |
| Baseline maintenance | L46–L47 |
| Baseline progress | L44–L45 |
| Baseline termination | L49–L51 |
| Improved submitted loop | L55–L62 |
| Improved progress | L58 |
| Correctness oracle | L104–L110 |
| Baseline benchmark | L116–L124 |
| Improved benchmark | L126–L134 |
| \(N^2\) normalizer | L136–L146 |
| Speedup | L156–L157 |
| `chrono` | L117–L122, L127–L132 |
| `static_cast` | L23, L26, L30, L37, L102, L137, L150 |
| `size_t` | L23, L26, L30, L58, L100 |
| `volatile` checksum | L95 |
| asymptotic interpretation | L161–L166 |

---

# 13. Problem 2 — Image convolution

**File:** `problem02_image_convolution.cpp`

The code implements a fixed 3×3 convolution.

For each output pixel:

\[
Y[r,c]
=
\sum_{i=-1}^{1}
\sum_{j=-1}^{1}
K[i,j]X[r+i,c+j]
\]

with a chosen border rule.

---

# 14. Lines 12–13 — compact types

```cpp
12: using Pixel = uint8_t;
13: using Kernel = array<double, 9>;
```

Kernel coefficients are floating point.

Pixels remain 8-bit.

---

# 15. Lines 15–32 — flat image representation

Same row-major idea as Problem 1.

A flat vector is important for the later raw-pointer optimization.

---

# 16. Border modes

## Lines 34–38

```cpp
34: enum class BorderMode {
35:     ConstantZero = 0,
36:     Replicate = 1,
37:     Reflect = 2
38: };
```

Convolution needs a definition outside the image boundary.

---

## Constant zero

Outside image:

\[
X=0
\]

---

## Replicate

Use nearest edge value.

Example left of column 0 maps to column 0.

---

## Reflect-101

The submitted convention behaves like:

```text
... 2 1 | 0 1 2 3 | 2 1 ...
```

The edge pixel itself is not duplicated in the reflection.

---

# 17. `mapIndex`

## Lines 46–60

```cpp
46: int mapIndex(int index, int size, BorderMode mode) {
...
59: return index < 0 ? -index : 2 * size - index - 2;
60: }
```

This function maps an out-of-range coordinate to the correct source index.

The ternary operator on L59 is:

```text
condition ? value_if_true : value_if_false
```

---

# 18. `borderPixel`

## Lines 62–75

```cpp
62: Pixel borderPixel(...)
...
68: row = mapIndex(...);
69: column = mapIndex(...);
...
74: return input.at(row, column);
```

This packages boundary handling into one accessor.

It makes the baseline algorithm very easy to write and verify.

Its disadvantage is that the baseline calls it repeatedly for **all nine samples of every pixel**, even when the pixel is deep inside the image.

That repeated generality is one of the main optimization targets.

---

# 19. Baseline convolution

## Lines 78–109

```cpp
78: Image convolveBaseline(...)
...
85: for (int row = 0; row < input.rows; ++row) {
86:     for (int column = 0; column < input.columns; ++column) {
87:         double sum = 0.0;
...
89:         for (int kr = -1; kr <= 1; ++kr) {
90:             for (int kc = -1; kc <= 1; ++kc) {
...
94:                 sum += kernel[kernelIndex] *
95:                        borderPixel(...);
...
104:        output.at(row, column) = toPixel(sum);
```

This is the obvious algorithm obtained directly from the mathematical definition.

---

# 19.1 Baseline invariant

During the outer loops:

> Every output pixel before `(row,column)` in row-major order contains the correct 3×3 convolution value.

### Initialization

No pixel processed before the first loop iteration.

### Maintenance

The two kernel loops visit all nine offsets exactly once.

The sum is therefore exactly the defined 3×3 weighted sum.

Then L104 stores it.

### Progress

- kernel coordinates progress through 9 taps,
- output column advances,
- output row advances.

### Termination

Every image pixel receives exactly one complete convolution result.

---

# 19.2 Baseline complexity

For \(N\times N\):

Output pixels:

\[
N^2
\]

Kernel taps per output:

\[
9
\]

Therefore:

\[
9N^2
\]

weighted contributions.

Since 9 is constant:

\[
\Theta(N^2)
\]

For a general \(k\times k\) kernel:

\[
\Theta(N^2k^2)
\]

The assignment uses fixed \(3\times3\), so the correct bound is:

\[
\Theta(N^2)
\]

---

# 20. Border helper for optimized version

## Lines 111–137

`processBorderPixel` keeps the simple general implementation specifically for border pixels.

This is a deliberate optimization pattern:

> Use specialized fast code for the dominant common case, and simple general code for the small exceptional case.

For a square image, number of border pixels is:

\[
4N-4
\]

which is:

\[
\Theta(N)
\]

The interior contains:

\[
(N-2)^2=\Theta(N^2)
\]

So optimization effort should focus on the interior.

---

# 21. Improved submitted convolution

## Lines 140–145

```cpp
140: Image convolveImproved(
...
144:     double alpha = 1.0
145: ) {
```

The optional `alpha` is preserved from the submitted design.

It permits scalar multiplication and convolution to be **fused**.

Instead of:

```text
scaled = alpha * image
output = convolution(scaled)
```

the implementation can form:

\[
K'=\alpha K
\]

once and convolve the original image with \(K'\).

This can remove an entire intermediate image.

---

# 22. Precompute effective stencil

## Lines 148–150

```cpp
148: Kernel weight{};
149: for (int i = 0; i < 9; ++i)
150:     weight[i] = alpha * stencil[i];
```

Rather than multiplying every image sample by `alpha`, multiply only the nine kernel coefficients once.

Naive fused pipeline:

\[
9N^2
\]

extra alpha multiplications could be involved conceptually.

Precomputation needs only:

\[
9
\]

alpha multiplications.

This is a strong algebraic optimization.

For convolution alone, `alpha=1.0`.

---

# 23. Small-image fallback

## Lines 152–166

Images smaller than 3×3 have no interior.

Rather than forcing the optimized interior path to handle many corner cases, the code uses the general border helper everywhere.

This is simple and safe.

---

# 24. Cache model

## Lines 168–183

```cpp
168: constexpr int cacheLineBytes = 64;
169: constexpr int usableCacheBytes = 24 * 1024;
...
175: int tileWidth =
176:     usableCacheBytes / bytesPerActiveColumn - 2;
...
183: tileWidth = min(tileWidth, interiorWidth);
```

The submitted implementation assumes approximately:

- 64-byte cache lines,
- 24 KiB of usable L1 working set.

It estimates four active row portions:

1. top input,
2. middle input,
3. bottom input,
4. output.

Because a `Pixel` is one byte:

```cpp
bytesPerActiveColumn =
    4 * sizeof(Pixel)
```

The width is restricted so these active row portions fit in the selected cache budget.

---

# 24.1 Important industry caveat

The exact L1 size is machine-dependent.

Therefore `24*1024` is a tuning assumption, not a universal truth.

A production image library might:

- detect hardware,
- benchmark tile sizes,
- use architecture-specific kernels,
- rely on compiler/library dispatch.

For the assignment, the constant makes the cache-locality concept explicit.

---

# 25. Horizontal tiling

## Lines 185–190

```cpp
185: for (int firstColumn = 1;
186:      firstColumn < input.columns - 1;
187:      firstColumn += tileWidth) {

189:     const int endColumn =
190:         min(firstColumn + tileWidth, input.columns - 1);
```

The image interior is processed in vertical strips.

For a normal-width image the entire row may fit in one strip.

For very wide images, multiple strips keep the active working set smaller.

---

# 26. Direct row pointers

## Lines 192–207

```cpp
const Pixel* top = ...
const Pixel* middle = ...
const Pixel* bottom = ...
Pixel* destination = ...
```

The hot loop no longer repeatedly calculates:

\[
row\times columns+column
\]

through helper calls.

Instead it obtains direct pointers to the four active rows once per row.

This improves:

- address calculation simplicity,
- memory access predictability,
- compiler optimization opportunities.

---

# 27. Initial 3×3 window

## Lines 209–219

Nine pixels are loaded into local scalar variables:

```text
t0 t1 t2
m0 m1 m2
b0 b1 b2
```

Conceptually:

```text
t0 t1 t2
m0 m1 m2
b0 b1 b2
```

Local variables are candidates for register allocation.

---

# 28. Unrolled 3×3 arithmetic

## Lines 225–234

```cpp
const double sum =
    weight[0] * t0 +
    ...
    weight[8] * b2;
```

The baseline used nested kernel loops.

The optimized version explicitly writes the nine terms.

Benefits:

- no `kr/kc` loop-control overhead,
- no repeated kernel-index calculation,
- fixed operation structure visible to compiler.

Asymptotically nothing changes because there were always exactly nine terms.

---

# 29. Sliding-window reuse

## Lines 238–252

This is one of the most important submitted optimizations.

Suppose the current window is:

```text
a b c
d e f
g h i
```

After moving one column right:

```text
b c x
e f y
h i z
```

Six values are reused:

```text
b c
e f
h i
```

Only three new values are required:

```text
x y z
```

The code implements exactly that:

```cpp
t0 = t1;
t1 = t2;
t2 = top[column + 2];
```

and similarly for middle and bottom.

---

# 29.1 Data-load comparison

Naive conceptual window loading:

\[
9
\]

pixel accesses per interior output.

Sliding window after the first column:

\[
3
\]

new source values per output.

The cache may already make repeated baseline loads inexpensive, and the compiler can optimize aggressively, so this should not be interpreted as an unconditional 3× speedup.

But it materially reduces explicit source loading and exposes reuse clearly.

---

# 30. Interior invariant

During the inner column loop:

> `t0..b2` contain exactly the nine source values for the current 3×3 window centered at `(row,column)`.

### Initialization

L209–L219 load the first complete window in a strip.

### Maintenance

L225–L236 computes the current correct output.

L241–L251 shifts the window and loads exactly the next right column.

Thus the invariant holds for the next iteration.

### Progress

`column++`.

### Termination

Every interior column in the tile is processed.

Outer loops then cover every tile and every interior row.

---

# 31. Separate border processing

## Lines 257–288

The border path is executed only on:

- first row,
- last row,
- first column,
- last column.

Thus the expensive general boundary mapping is moved out of the \(\Theta(N^2)\) interior.

Border cost:

\[
\Theta(N)
\]

Interior cost:

\[
\Theta(N^2)
\]

Total:

\[
\Theta(N^2)
\]

---

# 32. Improved convolution complexity

For fixed 3×3:

Interior pixels:

\[
(N-2)^2
\]

Each receives constant work.

Borders:

\[
O(N)
\]

Therefore:

\[
T(N)
=
\Theta((N-2)^2)+O(N)
=
\Theta(N^2)
\]

This is asymptotically optimal because the output itself contains \(N^2\) pixels:

\[
\Omega(N^2)
\]

must at least be written.

---

# 33. Why the optimization matters despite same Big-O

Big-O ignores:

- memory loads,
- branch checks,
- address calculations,
- cache misses,
- loop overhead,
- allocation behavior,
- SIMD/vectorization opportunity.

The improved algorithm targets these constant factors.

This is an important systems/industry lesson:

> Two algorithms with the same asymptotic complexity can have substantially different practical performance.

---

# 34. Convolution benchmark sizes

## Lines 307–308

```cpp
const vector<int> sizes =
    {32, 64, 128, 256, 512, 1024, 2048};
```

Largest image:

\[
2048^2=4,194,304
\]

pixels.

Each convolution logically evaluates about:

\[
9\times4,194,304
\approx37.7\text{ million}
\]

kernel contributions.

---

# 35. Fixed Gaussian-like kernel

## Lines 310–314

```cpp
1 2 1
2 4 2
1 2 1
```

normalized by 16.

Coefficient sum:

\[
\frac{16}{16}=1
\]

This is a simple blur kernel.

Using a fixed kernel makes runs comparable.

---

# 36. Reflect border mode

## Line 316

```cpp
constexpr BorderMode mode = BorderMode::Reflect;
```

Both baseline and optimized versions use exactly the same border semantics.

That is necessary for a valid correctness comparison.

---

# 37. Deterministic image generation

## Lines 334–342

The same arithmetic byte pattern is used for every implementation.

No random-generator overhead occurs in the timed region.

---

# 38. Convolution correctness oracle

## Lines 344–353

```cpp
Image expected =
    convolveBaseline(input, kernel, mode);

Image actual =
    convolveImproved(input, kernel, mode);

if (expected.data != actual.data) ...
```

The optimized code must produce exactly the same pixels as the direct mathematical implementation.

This is the most important check when optimizing a low-level kernel.

Performance is irrelevant if semantics change.

---

# 39. Timing

## Baseline

L359–L373.

## Improved

L375–L389.

The two loops have symmetric benchmark structure.

---

# 40. Experimental \(N^2\) normalization

## Lines 391–405

```cpp
pixels = n*n
baseNsPerPixel = ...
improvedNsPerPixel = ...
```

The theory says:

\[
T(N)=\Theta(N^2)
\]

Therefore:

\[
\frac{T(N)}{N^2}
\]

should remain bounded.

The program reports exactly this in practical units:

```text
nanoseconds per pixel
```

---

# 41. Interpreting noisy timing correctly

Timing is not a mathematical proof.

It varies with:

- CPU frequency,
- compiler decisions,
- cache state,
- OS scheduling,
- branch prediction,
- memory allocator,
- concurrent system load.

Therefore the strongest conclusions are:

1. correctness is checked exactly;
2. theory proves both versions are \(\Theta(N^2)\);
3. bounded time-per-pixel is experimentally consistent with the theory;
4. measured speedup is platform-specific.

Do **not** claim the optimized version must be faster at every tested size.

That would be an unjustified conclusion.

---

# 42. Convolution proof map

| Concept | Exact lines |
|---|---|
| image representation | L15–L32 |
| border modes | L34–L38 |
| pixel conversion | L40–L44 |
| boundary mapping | L46–L60 |
| general border accessor | L62–L75 |
| AI/direct baseline | L78–L109 |
| baseline initialization | L83–L87 |
| baseline maintenance | L89–L104 |
| baseline progress | L85–L90 |
| baseline termination | L105–L108 |
| border helper | L111–L137 |
| submitted optimized function | L140–L291 |
| fused-alpha precomputation | L148–L150 |
| small-image fallback | L152–L166 |
| cache parameters | L168–L183 |
| tiling progress | L185–L190 |
| row pointers | L192–L207 |
| initial sliding window | L209–L219 |
| unrolled convolution | L225–L236 |
| sliding reuse maintenance | L238–L252 |
| border specialization | L257–L288 |
| correctness oracle | L344–L353 |
| baseline benchmark | L359–L373 |
| improved benchmark | L375–L389 |
| \(N^2\) normalizer | L391–L405 |
| speedup calculation | L415–L418 |
| `static_cast` | L23, L26, L30, L43, L173, L195–L207, L339, L392–L393, L409 |
| raw pointers | L193–L207 |
| `constexpr` cache tuning | L168–L169 |
| `enum class` | L34–L38 |
| `array<double,9>` | L13 |
| `chrono` | L360–L368, L376–L384 |
| `volatile` checksum | L332 |

---

# 43. AI baseline → improved algorithm summary

## Scalar multiplication

### Initial algorithm

```text
for every row
    for every column
        output[row][column] = alpha * input[row][column]
```

### Improvement

Recognize that storage is contiguous:

```text
for i = 0 .. elements-1
    output[i] = alpha * input[i]
```

### Effect

- simpler hot loop,
- sequential memory access,
- vectorization-friendly,
- same optimal \(\Theta(RC)\).

---

## Convolution

### Initial algorithm

```text
for every output pixel
    for each of 9 kernel positions
        map boundary
        load source
        multiply
        accumulate
```

### Improved algorithm

```text
precompute effective kernel

process interior separately:
    obtain direct top/middle/bottom row pointers
    load first 3x3 window
    compute output
    slide:
        reuse 6 values
        load only 3 new values

process border separately
```

### Effect

- boundary logic removed from dominant interior,
- window data explicitly reused,
- fixed kernel loops unrolled,
- cache-friendly strips,
- same optimal \(\Theta(N^2)\).

---

# 44. Likely viva questions

## Why can scalar multiplication not be asymptotically faster than \(O(N^2)\)?

An \(N\times N\) output has \(N^2\) elements.

Every output element must be produced.

Therefore:

\[
\Omega(N^2)
\]

is unavoidable.

---

## Why use flat storage?

A `vector<Pixel>` stores all data contiguously.

This improves spatial locality and makes row-major traversal predictable.

---

## What is spatial locality?

If one memory address is used, nearby addresses are likely to be used soon.

Sequential pixel processing exploits cache lines.

---

## Why is the convolution still \(O(N^2)\) after optimization?

Kernel size is fixed at 3×3.

Nine operations per pixel is a constant factor.

---

## What if kernel size were \(K\times K\)?

Direct convolution becomes:

\[
O(N^2K^2)
\]

for square \(N\times N\) image.

---

## Why not use FFT convolution?

FFT methods become attractive for large kernels.

For a tiny fixed 3×3 stencil, FFT setup and transform overhead are inappropriate.

Direct convolution is the right algorithmic family.

---

## Why separate borders?

Borders are only:

\[
O(N)
\]

pixels.

The interior is:

\[
O(N^2)
\]

So expensive generic border logic should not be executed on every interior sample.

---

## Why use three row pointers?

A 3×3 kernel needs the previous, current, and next source rows.

Keeping direct pointers exposes exactly those active rows.

---

## Why reuse six values?

Adjacent 3×3 windows overlap in two of their three columns.

Two columns × three rows = six shared pixels.

---

## Why tile by columns?

For very wide images, three complete source rows plus an output row may exceed the chosen L1 working-set budget.

Processing narrower strips increases locality.

---

## Why is `cacheLineBytes = 64` not universally correct?

It is a hardware assumption.

64 bytes is common on current general-purpose CPUs, but production code should not blindly assume all targets use it.

---

## Why is `alpha` folded into the stencil?

By linearity:

\[
K*(\alpha X)
=
(\alpha K)*X
\]

So scaling can be fused into convolution without creating an intermediate scaled image.

This saves memory bandwidth and storage.

---

## Does clipping affect this algebra?

If clipping is performed **between** scaling and convolution, strict linear equivalence can be broken.

The fused method is appropriate when the intended pipeline avoids intermediate clipping and applies final pixel conversion after convolution.

This is why the submitted source specifically described fusion as the path to use when there must be no clipping between operations.

---

## Why is the scalar benchmark speedup inconsistent at some sizes?

Possible reasons:

- compiler may optimize the nested baseline equally well,
- output allocation is timed,
- cache behavior changes with size,
- OS scheduling,
- CPU frequency scaling.

The correct conclusion is not “flat loop is always X times faster.”

The correct conclusion is:

> both are \(\Theta(N^2)\); flat storage gives a simpler optimization-friendly kernel, while measured constant-factor speedup is machine-dependent.

---

# 45. Theoretical proof vs experiment

## Mathematical proof

Scalar:

\[
N^2
\]

required outputs.

Convolution:

\[
N^2
\]

outputs × constant 9-tap stencil.

Therefore both:

\[
\Theta(N^2)
\]

for square data.

## Experiment

The code reports:

```text
milliseconds
nanoseconds per element/pixel
speedup
```

If:

```text
ns / N^2-item
```

remains bounded with increasing \(N\), observed growth is consistent with the proof.

Finite experiments **validate** the implementation behavior; they do not replace the asymptotic proof.

---

# 46. Makefile

Compile:

```bash
make
```

Run both:

```bash
make run-all
```

Clean:

```bash
make clean
```

Compiler:

```text
g++ -std=c++17 -O2 -Wall -Wextra -Wpedantic
```

---

# 47. What to remember

## Scalar multiplication

```text
Baseline:
row/column loops

Submitted improvement:
flat contiguous loop
```

Complexity:

\[
\Theta(RC)
\]

---

## Convolution

```text
Baseline:
general 3x3 nested loops everywhere

Submitted improvement:
specialized border + cache-aware interior + sliding window
```

Complexity for fixed 3×3:

\[
\Theta(RC)
\]

or for \(N\times N\):

\[
\Theta(N^2)
\]

---

# 48. Strongest architectural point

The convolution optimization is mainly a **data-movement optimization**.

Arithmetic count is already constant per pixel.

The primary goal is to:

- avoid repeated general border logic,
- reduce redundant loads,
- preserve active data in cache/registers,
- traverse contiguous memory,
- avoid unnecessary intermediate images.

That is the correct way to reason about optimization once the asymptotic algorithm is already optimal.
