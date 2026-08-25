# Randomized Algorithms Assignment
## Exact-Code Line-by-Line Guide, Proofs, Experiments, CLRS Reference, and C++ Notes

This guide corresponds to these exact files:

- `problem01_leetcode_randomized.cpp`
- `problem02_fibonacci_probabilistic_counting.cpp`
- `problem03_online_stream_selection.cpp`
- `problem04_hat_problem.cpp`

The assignment package deliberately keeps each question in one source file and sends all experiment results directly to the terminal.

---

# 0. Reuse from the earlier Assignment 4

The previous Assignment 4 submission contained four source files:

- `Random Pick with Weight.cpp`
- `Generate Random Point in a Circle.cpp`
- `randomPersonFromStreamOfPeople.cpp`
- `RandomHatProblem.cpp`

The current implementation preserves the core strategies from those submitted solutions:

- weighted prefix sums + random selection + binary search;
- rejection sampling for a point inside a circle;
- reservoir sampling with one stored stream element;
- random hat permutation and fixed-point counting.

The current versions add stronger experiment design, exact bounded random generation, correctness checks, probability checks, and theory-vs-experiment comparisons.

The Fibonacci probabilistic counter is the new part of this assignment.

---

# 1. CLRS 4e source used for Fibonacci probabilistic counting

Reference used:

> Thomas H. Cormen, Charles E. Leiserson, Ronald L. Rivest, Clifford Stein,  
> *Introduction to Algorithms*, Fourth Edition, Problem 5-1, “Probabilistic counting.”

The book describes R. Morris's probabilistic counter as follows.

A register state `i` represents a count `n_i`, where the represented values form an increasing sequence. When the register is in state `i`, the increment operation moves to state `i+1` with probability

\[
\frac{1}{n_{i+1}-n_i}
\]

and otherwise leaves the register unchanged.

The key expectation identity is

\[
E[\Delta \mid i]
=
(n_{i+1}-n_i)
\frac{1}{n_{i+1}-n_i}
=
1.
\]

Therefore, ignoring overflow, after `n` increment events,

\[
E[\text{represented count}]=n.
\]

CLRS explicitly mentions Fibonacci represented values as an interesting choice.

## Fibonacci indexing clarification

The standard Fibonacci sequence begins

\[
F_0=0,\quad F_1=1,\quad F_2=1,\quad F_3=2,\ldots
\]

The literal adjacent gap

\[
F_2-F_1=0
\]

would make the Morris transition probability undefined.

The implementation therefore removes the duplicate represented value by using the strictly increasing sequence

\[
n_0=0,\qquad
n_i=F_{i+1}\quad(i\ge1),
\]

which gives

\[
0,1,2,3,5,8,13,21,\ldots
\]

The Morris transition rule itself is unchanged.

This is an implementation clarification, not a change to the probabilistic-counting principle.

---

# 2. Four stages of algorithm reasoning

For every randomized loop in this assignment, distinguish:

1. **Initialization** — what is true before the first iteration?
2. **Maintenance** — assuming the property is true before one iteration, why does the randomized step preserve it?
3. **Progress** — what moves toward completion?
4. **Termination** — when the loop stops, what has been established?

For randomized algorithms, correctness may mean different things:

- **always-correct output with randomized runtime**, or
- **a randomized output distribution satisfying a probability law**.

Reservoir sampling, for example, does not return one predetermined element. Its correctness statement is:

\[
P(\text{any particular stream item is returned})=\frac1n.
\]

---

# 3. Shared probability concepts

## Random variable

A quantity whose value depends on a random experiment.

Examples:

- selected weighted index,
- number of reservoir replacements,
- number of fixed points in a random hat permutation.

## Expected value

For discrete `X`:

\[
E[X]=\sum_x xP(X=x).
\]

Linearity of expectation does **not** require independence.

This is crucial in the Hat problem.

## Indicator random variable

For event `A`:

\[
I_A=
\begin{cases}
1,&A\text{ occurs}\\
0,&A\text{ does not occur.}
\end{cases}
\]

Then:

\[
E[I_A]=P(A).
\]

For hats, define `X_i = 1` if student `i` gets their own hat.

Then:

\[
X=\sum_iX_i
\]

and

\[
E[X]
=
\sum_iE[X_i]
=
n\cdot\frac1n
=
1.
\]

## Variance

\[
Var(X)=E[X^2]-E[X]^2.
\]

The Fibonacci counter experiment reports standard deviation because unbiased expectation alone does not mean each individual estimate is accurate.

## Chi-square statistic

For categorical frequencies:

\[
\chi^2
=
\sum_i
\frac{(O_i-E_i)^2}{E_i}.
\]

The weighted picker and reservoir experiments use this as a diagnostic of whether observed frequencies are compatible with the desired probabilities.

It is not used as a formal proof of uniformity.

---

# 4. Shared C++ and RNG concepts

## `uint32_t` and `uint64_t`

Fixed-width unsigned integer types.

They are appropriate for xorshift generators because bit shifts and overflow are defined modulo \(2^{32}\) or \(2^{64}\).

## `ULL` and `LL`

Examples:

```cpp
1LL << 30
0xD1B54A32D192ED03ULL
```

`LL` means `long long`.

`ULL` means `unsigned long long`.

The suffix controls the arithmetic width before later conversions.

## Bitwise XOR `^`

Xorshift generators update internal state through XOR and shifts.

Example:

```cpp
state ^= state << 13;
```

means:

```cpp
state = state ^ (state << 13);
```

## Left and right shifts

```cpp
state << 13
state >> 17
```

move bits inside the fixed-width integer.

For the unsigned RNG state these operations have well-defined logical behavior.

## Why not simply use `% bound`?

If a uniformly random integer ranges over a number of values that is not divisible by `bound`, direct modulo reduction makes some residues occur one extra time.

That is **modulo bias**.

The assignment uses rejection to discard the incomplete residue region.

## `static_cast`

Used whenever the code intentionally converts between:

- fixed-width integers,
- floating-point values,
- `size_t`,
- index types.

It is explicit and easier to audit than a C-style cast.

## `chrono::steady_clock`

Used for elapsed-time benchmarking because it is monotonic.

## `long double`

Used for sums over many trials where extra accumulator precision is useful.

## Digit separators

C++ allows:

```cpp
2'000'000
1'000'000
```

which are ordinary numeric literals with readability separators.

---


# 5. Problem 1 — Two LeetCode randomized problems

The selected problems match the previous Assignment 4:

- **LeetCode 528 — Random Pick with Weight**
- **LeetCode 478 — Generate Random Point in a Circle**

The current file places both solutions and their statistical experiments together.

### Headers, namespace, and timing clock — `problem01_leetcode_randomized.cpp` L1–L14

```cpp
   1: #include <algorithm>
   2: #include <array>
   3: #include <chrono>
   4: #include <cmath>
   5: #include <cstdint>
   6: #include <iomanip>
   7: #include <iostream>
   8: #include <limits>
   9: #include <stdexcept>
  10: #include <vector>
  11: 
  12: using namespace std;
  13: using Clock = chrono::steady_clock;
  14: 
```

These lines import algorithms, fixed arrays, timing, floating-point mathematics,
fixed-width integers, terminal formatting, exception support, and vectors. `Clock`
is an alias for `chrono::steady_clock`.

**C++ / implementation concepts:** `#include`, namespace, type alias, `steady_clock`.

### 32-bit xorshift generator — `problem01_leetcode_randomized.cpp` L18–L30

```cpp
  18: class XorShift32 {
  19:     uint32_t state;
  20: 
  21: public:
  22:     explicit XorShift32(uint32_t seed = 0x9E3779B9u)
  23:         : state(seed ? seed : 0x9E3779B9u) {}
  24: 
  25:     uint32_t next() {
  26:         state ^= state << 13;
  27:         state ^= state >> 17;
  28:         state ^= state << 5;
  29:         return state;
  30:     }
```

`XorShift32` owns one 32-bit state. `next()` applies the three xor/shift
transformations and returns the updated state. A nonzero fallback seed is used
if the caller passes zero.

**Correctness / mathematical role:** Randomness quality is an implementation assumption; the deterministic algorithmic state transition itself is constant-time.

**C++ / implementation concepts:** `uint32_t`, `explicit` constructor, initializer list, XOR assignment, bit shifts.

**Viva point:** A PRNG is deterministic given its seed. Randomized algorithms require a source that behaves sufficiently like the assumed distribution; this is not cryptographic randomness.

### Exact bounded 32-bit random integer — `problem01_leetcode_randomized.cpp` L32–L49

```cpp
  32:     // Lemire-style rejection removes the tiny modulo/multiply-high bias.
  33:     uint32_t bounded(uint32_t bound) {
  34:         uint32_t x = next();
  35:         uint64_t product = static_cast<uint64_t>(x) * bound;
  36:         uint32_t low = static_cast<uint32_t>(product);
  37: 
  38:         if (low < bound) {
  39:             const uint32_t threshold = -bound % bound;
  40: 
  41:             while (low < threshold) {
  42:                 x = next();
  43:                 product = static_cast<uint64_t>(x) * bound;
  44:                 low = static_cast<uint32_t>(product);
  45:             }
  46:         }
  47: 
  48:         return static_cast<uint32_t>(product >> 32);
  49:     }
```

A 32-bit random word is multiplied by `bound`, producing a 64-bit product.
The high 32 bits provide a fast range reduction. The low 32 bits reveal whether
the sample lies in the small incomplete region that would cause bias. If it does,
the code resamples until the result belongs to the exactly divisible region.

**Correctness / mathematical role:** After rejection, every integer in `[0,bound-1]` receives the same number of source integers, so the bounded result is uniform under the uniform-PRNG model.

**C++ / implementation concepts:** 64-bit widening multiplication, low/high halves, unsigned wraparound in `-bound`, rejection sampling, `static_cast`.

**Viva point:** This is an improvement over accepting a tiny multiply-high/modulo mapping bias.

### 64-bit xorshift-star generator — `problem01_leetcode_randomized.cpp` L52–L65

```cpp
  52: class XorShift64Star {
  53:     uint64_t state;
  54: 
  55: public:
  56:     explicit XorShift64Star(uint64_t seed = 0x9E3779B97F4A7C15ULL)
  57:         : state(seed ? seed : 0x9E3779B97F4A7C15ULL) {}
  58: 
  59:     uint64_t next() {
  60:         state ^= state >> 12;
  61:         state ^= state << 25;
  62:         state ^= state >> 27;
  63:         return state * 0x2545F4914F6CDD1DULL;
  64:     }
  65: };
```

This generator is used for circle sampling. The state is transformed through
xorshift operations and then multiplied by a fixed odd constant before returning
the output.

**C++ / implementation concepts:** `uint64_t`, hexadecimal `ULL` literals, xorshift-star.

### Weighted-picker reuse and improvement note — `problem01_leetcode_randomized.cpp` L67–L76

```cpp
  67: // -----------------------------------------------------------------------------
  68: // LeetCode 528: Random Pick with Weight.
  69: //
  70: // Submitted core:
  71: //   prefix sums + random integer + upper_bound.
  72: //
  73: // Improvement:
  74: //   exact bounded generation with rejection instead of accepting the tiny
  75: //   mapping bias when total weight does not divide 2^32.
  76: // -----------------------------------------------------------------------------
```

The comment records the submitted algorithmic core: prefix sums, one random
integer in the total-weight range, and `upper_bound`. The improvement is the
exact bounded random mapping above.

**Correctness / mathematical role:** Changing the RNG range reduction does not change the prefix-sum correctness argument; it strengthens the probability implementation.

### Weighted prefix construction — `problem01_leetcode_randomized.cpp` L77–L103

```cpp
  77: class WeightedPicker {
  78:     vector<uint32_t> prefix;
  79:     XorShift32 rng;
  80: 
  81: public:
  82:     explicit WeightedPicker(
  83:         const vector<uint32_t>& weights,
  84:         uint32_t seed = 0x9E3779B9u
  85:     ) : rng(seed) {
  86:         if (weights.empty())
  87:             throw invalid_argument("Weight list must not be empty");
  88: 
  89:         uint64_t running = 0;
  90:         prefix.reserve(weights.size());
  91: 
  92:         for (uint32_t weight : weights) {
  93:             if (weight == 0)
  94:                 throw invalid_argument("Weights must be positive");
  95: 
  96:             running += weight;
  97: 
  98:             if (running > numeric_limits<uint32_t>::max())
  99:                 throw overflow_error("Total weight exceeds uint32_t range");
 100: 
 101:             prefix.push_back(static_cast<uint32_t>(running));
 102:         }
 103:     }
```

The constructor validates the input and builds cumulative sums. For weights
`[1,3,6,10]`, the prefix array becomes `[1,4,10,20]`. `reserve` prevents
unnecessary vector reallocations. A 64-bit running total is used before narrowing
to `uint32_t` so overflow can be detected explicitly.

**Correctness / mathematical role:** After processing weight `j`, `prefix[j]` equals the sum of weights `0..j`. This is the invariant used by selection.

**C++ / implementation concepts:** `vector`, `reserve`, exceptions, `numeric_limits`, checked narrowing.

**Viva point:** Why positive weights? A zero-weight category should never be selected; LeetCode's constraints use positive weights. This implementation rejects zero explicitly.

### Weighted selection — `problem01_leetcode_randomized.cpp` L105–L116

```cpp
 105:     int pickIndex() {
 106:         const uint32_t randomValue = rng.bounded(prefix.back());
 107: 
 108:         return static_cast<int>(
 109:             upper_bound(
 110:                 prefix.begin(),
 111:                 prefix.end(),
 112:                 randomValue
 113:             ) - prefix.begin()
 114:         );
 115:     }
 116: };
```

`rng.bounded(prefix.back())` samples an integer uniformly from
`[0,totalWeight-1]`. `upper_bound` returns the first cumulative total strictly
greater than that random integer. Subtracting `prefix.begin()` converts the
iterator to an index.

**Correctness / mathematical role:** Index `j` owns exactly the integers

\[
prefix[j-1],\ldots,prefix[j]-1,
\]

which are exactly `weight[j]` integers. Hence

\[
P(j)=\frac{weight[j]}{\sum_k weight[k]}.
\]

**C++ / implementation concepts:** `upper_bound`, random-access iterators, iterator subtraction, `prefix.back()`.

**Viva point:** Preprocessing is O(m); each pick is O(log m) because `upper_bound` performs binary search.

### Point representation and circle sampler state — `problem01_leetcode_randomized.cpp` L124–L142

```cpp
 124: struct Point {
 125:     double x;
 126:     double y;
 127: };
 128: 
 129: class RandomPointInCircle {
 130:     double radius;
 131:     double centerX;
 132:     double centerY;
 133:     XorShift64Star rng;
 134:     uint64_t attempts = 0;
 135: 
 136: public:
 137:     RandomPointInCircle(
 138:         double r,
 139:         double x,
 140:         double y,
 141:         uint64_t seed = 0xD1B54A32D192ED03ULL
 142:     ) : radius(r), centerX(x), centerY(y), rng(seed) {}
```

`Point` stores two doubles. `RandomPointInCircle` stores radius, center,
64-bit PRNG state, and a count of total rejection attempts.

**C++ / implementation concepts:** simple struct, class data members, constructor initializer list.

### Rejection sampling inside a circle — `problem01_leetcode_randomized.cpp` L144–L171

```cpp
 144:     Point randPoint() {
 145:         int64_t x;
 146:         int64_t y;
 147: 
 148:         do {
 149:             ++attempts;
 150:             const uint64_t bits = rng.next();
 151: 
 152:             x = static_cast<int64_t>(
 153:                     static_cast<uint32_t>(bits) >> 1
 154:                 ) - (1LL << 30);
 155: 
 156:             y = static_cast<int64_t>(
 157:                     static_cast<uint32_t>(bits >> 32) >> 1
 158:                 ) - (1LL << 30);
 159: 
 160:         } while (
 161:             x * x + y * y > (1LL << 60)
 162:         );
 163: 
 164:         constexpr double scale =
 165:             1.0 / static_cast<double>(1LL << 30);
 166: 
 167:         return {
 168:             centerX + radius * static_cast<double>(x) * scale,
 169:             centerY + radius * static_cast<double>(y) * scale
 170:         };
 171:     }
```

One 64-bit random word is split into two approximately 31-bit signed lattice
coordinates. The `do...while` loop rejects points outside the integer disk
`x^2+y^2 <= 2^60`. Accepted coordinates are then scaled into the requested
circle and translated to the requested center.

**Correctness / mathematical role:** The proposal distribution is uniform over the bounding square lattice.
Conditioning that uniform distribution on membership in the disk yields a uniform
distribution over accepted lattice points. In the continuous limit this is uniform
area sampling over the disk.

**C++ / implementation concepts:** `do...while`, integer-square overflow analysis, shifts, signed conversion, aggregate return.

**Viva point:** The expected acceptance probability is area(circle)/area(square) = \(\pi/4\), so expected attempts are \(4/\pi\), a constant. Thus expected time is O(1).

### Attempt counter accessor — `problem01_leetcode_randomized.cpp` L173–L176

```cpp
 173:     uint64_t totalAttempts() const {
 174:         return attempts;
 175:     }
 176: };
```

Returns the total number of proposal points generated so the experiment can
measure the rejection acceptance rate.

**C++ / implementation concepts:** `const` member function.

### Weighted-picker experiment setup and two million draws — `problem01_leetcode_randomized.cpp` L186–L197

```cpp
 186:     const vector<uint32_t> weights = {1, 3, 6, 10};
 187:     const uint64_t weightedTrials = 2'000'000;
 188: 
 189:     WeightedPicker picker(weights, 9u);
 190:     vector<uint64_t> frequency(weights.size(), 0);
 191: 
 192:     auto weightedStart = Clock::now();
 193: 
 194:     for (uint64_t trial = 0; trial < weightedTrials; ++trial)
 195:         ++frequency[picker.pickIndex()];
 196: 
 197:     auto weightedEnd = Clock::now();
```

The experiment fixes weights `[1,3,6,10]`, performs two million selections,
and stores observed frequency for each index. Timing brackets only the repeated
picks.

**Correctness / mathematical role:** Large repeated trials estimate the target categorical probabilities.

**C++ / implementation concepts:** digit separators, vector frequency table, `chrono`.

### Weighted probability and chi-square calculations — `problem01_leetcode_randomized.cpp` L199–L241

```cpp
 199:     const double totalWeight = 20.0;
 200:     double chiSquare = 0.0;
 201:     double maxAbsoluteProbabilityError = 0.0;
 202: 
 203:     cout << left
 204:          << setw(8)  << "index"
 205:          << setw(12) << "weight"
 206:          << setw(16) << "expected_p"
 207:          << setw(16) << "observed_p"
 208:          << setw(16) << "abs_error"
 209:          << '\n';
 210: 
 211:     for (size_t i = 0; i < weights.size(); ++i) {
 212:         const double expectedProbability =
 213:             weights[i] / totalWeight;
 214: 
 215:         const double observedProbability =
 216:             static_cast<double>(frequency[i]) / weightedTrials;
 217: 
 218:         const double error =
 219:             abs(observedProbability - expectedProbability);
 220: 
 221:         maxAbsoluteProbabilityError =
 222:             max(maxAbsoluteProbabilityError, error);
 223: 
 224:         const double expectedCount =
 225:             weightedTrials * expectedProbability;
 226: 
 227:         const double difference =
 228:             static_cast<double>(frequency[i]) - expectedCount;
 229: 
 230:         chiSquare +=
 231:             difference * difference / expectedCount;
 232: 
 233:         cout << left
 234:              << setw(8)  << i
 235:              << setw(12) << weights[i]
 236:              << setw(16) << fixed << setprecision(6)
 237:              << expectedProbability
 238:              << setw(16) << observedProbability
 239:              << setw(16) << error
 240:              << '\n';
 241:     }
```

For every category the code computes theoretical probability, observed
probability, absolute error, expected count, and a chi-square contribution.

**Correctness / mathematical role:** The exact theory is supplied by the prefix intervals; the experiment checks that observed frequencies are numerically compatible with it.

**C++ / implementation concepts:** floating-point division, `abs`, `max`, formatted output.

### Weighted timing and complexity report — `problem01_leetcode_randomized.cpp` L243–L255

```cpp
 243:     const double weightedNsPerPick =
 244:         chrono::duration<double, nano>(
 245:             weightedEnd - weightedStart
 246:         ).count() / weightedTrials;
 247: 
 248:     cout << "Trials                : " << weightedTrials << '\n';
 249:     cout << "Max probability error : "
 250:          << maxAbsoluteProbabilityError << '\n';
 251:     cout << "Chi-square (df=3)     : "
 252:          << chiSquare << '\n';
 253:     cout << "Average time/pick     : "
 254:          << weightedNsPerPick << " ns\n";
 255:     cout << "Complexity            : O(log m) per pick for m weights\n\n";
```

Elapsed nanoseconds are divided by two million picks. The printed complexity
is O(log m) per query after O(m) prefix preprocessing.

**C++ / implementation concepts:** `chrono::duration<double,nano>`.

### Circle experiment constants and accumulators — `problem01_leetcode_randomized.cpp` L262–L280

```cpp
 262:     constexpr double radius = 2.5;
 263:     constexpr double centerX = 3.0;
 264:     constexpr double centerY = -4.0;
 265:     const uint64_t pointTrials = 2'000'000;
 266: 
 267:     RandomPointInCircle circle(
 268:         radius,
 269:         centerX,
 270:         centerY,
 271:         0x123456789ABCDEF0ULL
 272:     );
 273: 
 274:     long double sumX = 0.0L;
 275:     long double sumY = 0.0L;
 276:     long double sumNormalizedRadiusSquared = 0.0L;
 277:     array<uint64_t, 4> quadrantCount{};
 278:     double maximumNormalizedRadiusSquared = 0.0;
 279: 
 280:     auto circleStart = Clock::now();
```

The circle has radius 2.5 and center `(3,-4)`. Two million accepted points are
generated. Long-double accumulators track means and normalized radial second
moment; four counters track quadrants.

**Correctness / mathematical role:** These statistics test several independent consequences of uniform disk area sampling.

### Circle sampling experiment loop — `problem01_leetcode_randomized.cpp` L282–L309

```cpp
 282:     for (uint64_t trial = 0; trial < pointTrials; ++trial) {
 283:         const Point point = circle.randPoint();
 284: 
 285:         const double dx = point.x - centerX;
 286:         const double dy = point.y - centerY;
 287: 
 288:         const double normalizedRadiusSquared =
 289:             (dx * dx + dy * dy) / (radius * radius);
 290: 
 291:         if (normalizedRadiusSquared > 1.000000000001) {
 292:             cerr << "Generated a point outside the circle\n";
 293:             return 1;
 294:         }
 295: 
 296:         sumX += point.x;
 297:         sumY += point.y;
 298:         sumNormalizedRadiusSquared += normalizedRadiusSquared;
 299: 
 300:         maximumNormalizedRadiusSquared =
 301:             max(maximumNormalizedRadiusSquared,
 302:                 normalizedRadiusSquared);
 303: 
 304:         const int quadrant =
 305:             (dx >= 0.0 ? 1 : 0) +
 306:             (dy >= 0.0 ? 2 : 0);
 307: 
 308:         ++quadrantCount[quadrant];
 309:     }
```

Every generated point is checked against the circle equation. The experiment
accumulates x/y means, normalized squared radius, largest radius, and quadrant
frequency.

**Correctness / mathematical role:** For a point uniform in a disk centered at `(cx,cy)`:

\[
E[x]=cx,\quad E[y]=cy,\quad E[r^2/R^2]=1/2,
\]

and symmetry gives quadrant probability \(1/4\).

**C++ / implementation concepts:** ternary operator, array indexing, long-double accumulation.

**Viva point:** Checking several moments/regions is stronger than only verifying that points lie inside the circle.

### Circle theoretical comparisons — `problem01_leetcode_randomized.cpp` L313–L334

```cpp
 313:     const double meanX =
 314:         static_cast<double>(sumX / pointTrials);
 315: 
 316:     const double meanY =
 317:         static_cast<double>(sumY / pointTrials);
 318: 
 319:     const double meanNormalizedRadiusSquared =
 320:         static_cast<double>(
 321:             sumNormalizedRadiusSquared / pointTrials
 322:         );
 323: 
 324:     const double acceptanceRate =
 325:         static_cast<double>(pointTrials) /
 326:         circle.totalAttempts();
 327: 
 328:     const double theoreticalAcceptance =
 329:         acos(-1.0) / 4.0;
 330: 
 331:     const double circleNsPerPoint =
 332:         chrono::duration<double, nano>(
 333:             circleEnd - circleStart
 334:         ).count() / pointTrials;
```

The sample means are computed, acceptance rate is accepted/attempted, and
`acos(-1.0)` supplies pi portably. Timing is converted to nanoseconds per
accepted point.

**Correctness / mathematical role:** Theoretical rejection acceptance is

\[
\frac{\pi R^2}{(2R)^2}=\frac{\pi}{4}.
\]

**C++ / implementation concepts:** `acos(-1.0)` for pi, `static_cast`, `chrono`.

### Circle terminal report — `problem01_leetcode_randomized.cpp` L336–L361

```cpp
 336:     cout << fixed << setprecision(6);
 337:     cout << "Trials                     : " << pointTrials << '\n';
 338:     cout << "Mean x                     : " << meanX
 339:          << "  theoretical " << centerX << '\n';
 340:     cout << "Mean y                     : " << meanY
 341:          << "  theoretical " << centerY << '\n';
 342:     cout << "E[r^2 / R^2]               : "
 343:          << meanNormalizedRadiusSquared
 344:          << "  theoretical 0.500000\n";
 345:     cout << "Max r^2 / R^2              : "
 346:          << maximumNormalizedRadiusSquared << '\n';
 347:     cout << "Rejection acceptance rate  : "
 348:          << acceptanceRate
 349:          << "  theoretical pi/4 = "
 350:          << theoreticalAcceptance << '\n';
 351: 
 352:     cout << "Quadrant probabilities     : ";
 353:     for (uint64_t count : quadrantCount)
 354:         cout << static_cast<double>(count) / pointTrials << ' ';
 355:     cout << " (theoretical 0.25 each)\n";
 356: 
 357:     cout << "Average time/accepted point: "
 358:          << circleNsPerPoint << " ns\n";
 359:     cout << "Expected complexity        : O(1) per accepted point\n";
 360: 
 361:     return 0;
```

The program prints all distribution checks and the expected O(1) complexity,
then exits successfully.

**Correctness / mathematical role:** The measurements validate the implementation over two million accepted samples; they do not replace the probability proof.


## Problem 1 proof map

| Concept | Exact code |
|---|---:|
| Weighted prefix invariant | L89–L102 |
| Exact bounded RNG | L32–L49 |
| Weighted selection | L105–L115 |
| Weighted probability experiment | L186–L255 |
| Circle rejection initialization | L145–L150 |
| Circle rejection maintenance | L152–L162 |
| Circle progress | each rejected proposal restarts L148 |
| Circle termination | condition at L160–L162 becomes false |
| Circle transformation | L164–L170 |
| Circle distribution experiment | L262–L359 |

---

# 6. Problem 2 — Fibonacci probabilistic counting

**File:** `problem02_fibonacci_probabilistic_counting.cpp`

This is the only question in this assignment that is not reused from Assignment 4.

### Headers and timing alias — `problem02_fibonacci_probabilistic_counting.cpp` L1–L12

```cpp
   1: #include <algorithm>
   2: #include <chrono>
   3: #include <cmath>
   4: #include <cstdint>
   5: #include <iomanip>
   6: #include <iostream>
   7: #include <limits>
   8: #include <stdexcept>
   9: #include <vector>
  10: 
  11: using namespace std;
  12: using Clock = chrono::steady_clock;
```

The file explicitly includes numeric limits and exception headers because the
Fibonacci table must avoid integer overflow and the counter reports overflow by
exception.

**C++ / implementation concepts:** `numeric_limits`, `overflow_error`, `uint8_t`, `uint64_t`.

### CLRS Morris rule and Fibonacci clarification in source — `problem02_fibonacci_probabilistic_counting.cpp` L14–L33

```cpp
  14: // -----------------------------------------------------------------------------
  15: // CLRS 4e, Problem 5-1: Morris probabilistic counting.
  16: //
  17: // Book rule:
  18: // if state i represents n_i, increment the state with probability
  19: //
  20: //          1 / (n_{i+1} - n_i).
  21: //
  22: // Then the conditional expected increase in represented value is exactly 1.
  23: //
  24: // The book mentions n_i = F_i. Standard Fibonacci numbers begin
  25: // 0,1,1,2,..., so the literal F_1 -> F_2 gap is zero. To keep the sequence
  26: // strictly increasing while preserving the Fibonacci idea and the Morris rule,
  27: // this implementation uses:
  28: //
  29: // n_0 = 0,
  30: // n_i = F_{i+1} for i >= 1,
  31: //
  32: // giving 0,1,2,3,5,8,13,...
  33: // -----------------------------------------------------------------------------
```

The source itself documents the Morris transition probability and the reason
for using a shifted strictly increasing Fibonacci representation. Keeping this
next to the implementation prevents the subtle duplicated-`1` issue from being
hidden.

**Correctness / mathematical role:** The represented sequence used in code is

\[
0,1,2,3,5,8,\ldots
\]

so every gap is positive and the Morris probability is defined.

### 64-bit PRNG with unbiased bounded reduction — `problem02_fibonacci_probabilistic_counting.cpp` L35–L60

```cpp
  35: class XorShift64Star {
  36:     uint64_t state;
  37: 
  38: public:
  39:     explicit XorShift64Star(uint64_t seed)
  40:         : state(seed ? seed : 0x9E3779B97F4A7C15ULL) {}
  41: 
  42:     uint64_t next() {
  43:         state ^= state >> 12;
  44:         state ^= state << 25;
  45:         state ^= state >> 27;
  46:         return state * 0x2545F4914F6CDD1DULL;
  47:     }
  48: 
  49:     uint64_t bounded(uint64_t bound) {
  50:         // Exact uniform reduction by rejecting the incomplete residue prefix.
  51:         const uint64_t threshold = -bound % bound;
  52: 
  53:         while (true) {
  54:             const uint64_t value = next();
  55: 
  56:             if (value >= threshold)
  57:                 return value % bound;
  58:         }
  59:     }
  60: };
```

The xorshift-star generator supplies 64-bit words. `bounded` computes
`threshold = 2^64 mod bound` through unsigned wraparound and rejects values in
the incomplete prefix. Accepted values are reduced modulo `bound`.

**Correctness / mathematical role:** Every residue receives exactly the same number of accepted 64-bit source words.

**C++ / implementation concepts:** unsigned modular arithmetic, modulo bias, rejection sampling.

### Build strictly increasing Fibonacci represented values — `problem02_fibonacci_probabilistic_counting.cpp` L62–L84

```cpp
  62: vector<uint64_t> buildFibonacciRepresentation() {
  63:     vector<uint64_t> represented;
  64:     represented.reserve(94);
  65: 
  66:     represented.push_back(0); // n_0
  67:     represented.push_back(1); // F_2
  68: 
  69:     uint64_t previous = 1; // F_2
  70:     uint64_t current = 2;  // F_3
  71: 
  72:     while (
  73:         current <=
  74:         numeric_limits<uint64_t>::max() - previous
  75:     ) {
  76:         represented.push_back(current);
  77: 
  78:         const uint64_t next = previous + current;
  79:         previous = current;
  80:         current = next;
  81:     }
  82: 
  83:     return represented;
  84: }
```

The vector starts with `0,1`, then iteratively appends `2,3,5,...`.
Before forming the next Fibonacci value, the condition checks whether addition
would overflow `uint64_t`.

**Correctness / mathematical role:** At each iteration `current` and `previous` are adjacent Fibonacci values; `next=previous+current` preserves the recurrence.

**C++ / implementation concepts:** `reserve`, `numeric_limits<uint64_t>::max()`, overflow-safe addition.

**Viva point:** The table is precomputed once; this avoids recursive Fibonacci computation in every counter increment.

### Probabilistic-counter state — `problem02_fibonacci_probabilistic_counting.cpp` L86–L95

```cpp
  86: class FibonacciMorrisCounter {
  87:     const vector<uint64_t>& represented;
  88:     uint8_t state = 0;
  89:     XorShift64Star rng;
  90: 
  91: public:
  92:     FibonacciMorrisCounter(
  93:         const vector<uint64_t>& values,
  94:         uint64_t seed
  95:     ) : represented(values), rng(seed) {}
```

The counter stores a reference to the representation table, an 8-bit state,
and a private RNG. The table is not copied for every trial.

**C++ / implementation concepts:** reference data member, `uint8_t`, constructor initializer list.

**Viva point:** The state is the compressed register; `estimate()` maps it back to a much larger represented count.

### Morris increment — `problem02_fibonacci_probabilistic_counting.cpp` L97–L108

```cpp
  97:     void increment() {
  98:         const size_t i = state;
  99: 
 100:         if (i + 1 >= represented.size())
 101:             throw overflow_error("Fibonacci representation overflow");
 102: 
 103:         const uint64_t gap =
 104:             represented[i + 1] - represented[i];
 105: 
 106:         if (rng.bounded(gap) == 0)
 107:             ++state;
 108:     }
```

At state `i`, compute the gap `n[i+1]-n[i]`. Draw uniformly from
`0..gap-1`. Advance the state only when the draw is zero, giving probability
exactly `1/gap`.

**Correctness / mathematical role:** Conditional on state \(i\),

\[
E[n_{\text{new}}-n_i]
=
\frac1{\Delta_i}\Delta_i
+
\left(1-\frac1{\Delta_i}\right)0
=
1.
\]

Hence each event contributes expected represented increase 1.

**C++ / implementation concepts:** `size_t`, checked overflow state, exact uniform bounded RNG.

### Estimate and raw state accessors — `problem02_fibonacci_probabilistic_counting.cpp` L110–L117

```cpp
 110:     uint64_t estimate() const {
 111:         return represented[state];
 112:     }
 113: 
 114:     int counterState() const {
 115:         return state;
 116:     }
 117: };
```

`estimate()` returns the represented Fibonacci-scale count. `counterState()`
returns the compact state for the state-growth experiment.

**C++ / implementation concepts:** `const` member functions.

### Adaptive trial count — `problem02_fibonacci_probabilistic_counting.cpp` L119–L125

```cpp
 119: int trialsFor(uint64_t n) {
 120:     if (n <= 100) return 20'000;
 121:     if (n <= 1'000) return 10'000;
 122:     if (n <= 10'000) return 3'000;
 123:     if (n <= 100'000) return 500;
 124:     return 100;
 125: }
```

Small event counts use many Monte Carlo trials; expensive million-event
experiments use fewer. This controls runtime while still averaging stochastic
estimates.

**Viva point:** Trial count affects statistical error, not the algorithm's asymptotic complexity.

### Representation construction and invariant validation — `problem02_fibonacci_probabilistic_counting.cpp` L127–L150

```cpp
 127: int main() {
 128:     cout << "Problem 2: Fibonacci probabilistic counting\n";
 129:     cout << "Reference: CLRS 4e, Problem 5-1 (Morris counting)\n\n";
 130: 
 131:     const vector<uint64_t> represented =
 132:         buildFibonacciRepresentation();
 133: 
 134:     cout << "First represented counts:\n";
 135:     for (size_t i = 0; i < min<size_t>(12, represented.size()); ++i)
 136:         cout << "state " << i << " -> " << represented[i] << '\n';
 137:     cout << '\n';
 138: 
 139:     // Verify the key expectation identity for every available transition:
 140:     //
 141:     // gap * (1/gap) = 1.
 142:     for (size_t i = 0; i + 1 < represented.size(); ++i) {
 143:         const uint64_t gap =
 144:             represented[i + 1] - represented[i];
 145: 
 146:         if (gap == 0) {
 147:             cerr << "Representation is not strictly increasing\n";
 148:             return 1;
 149:         }
 150:     }
```

The program prints early states and verifies every adjacent represented gap
is nonzero before beginning the stochastic experiment.

**Correctness / mathematical role:** This runtime check directly enforces the precondition required by `1/gap`.

### Event sizes, golden ratio, and theory printed — `problem02_fibonacci_probabilistic_counting.cpp` L152–L168

```cpp
 152:     const vector<uint64_t> eventCounts = {
 153:         100,
 154:         1'000,
 155:         10'000,
 156:         100'000,
 157:         1'000'000
 158:     };
 159: 
 160:     const long double phi =
 161:         (1.0L + sqrt(5.0L)) / 2.0L;
 162: 
 163:     cout << "Theory:\n";
 164:     cout << "E[represented increase | state i] = "
 165:          << "(n[i+1]-n[i]) * 1/(n[i+1]-n[i]) = 1.\n";
 166:     cout << "Therefore, ignoring overflow, E[estimate after n events] = n.\n";
 167:     cout << "Fibonacci values grow as Theta(phi^i), so state i = Theta(log n).\n";
 168:     cout << "Storing the state itself needs only Theta(log log n) adaptive bits.\n\n";
```

The experiment goes up to one million true events. `phi=(1+sqrt(5))/2`.
The program prints the unbiased-expectation identity and the compressed-state
growth prediction.

**Correctness / mathematical role:** Fibonacci growth gives

\[
F_k=\Theta(\phi^k).
\]

If represented count is about \(n\), then

\[
k=\Theta(\log_\phi n)=\Theta(\log n).
\]

Storing an integer state of magnitude \(k\) needs

\[
\Theta(\log k)=\Theta(\log\log n)
\]

adaptive bits.

**C++ / implementation concepts:** `long double`, `sqrt`.

### Fibonacci experiment columns — `problem02_fibonacci_probabilistic_counting.cpp` L170–L180

```cpp
 170:     cout << left
 171:          << setw(12) << "n"
 172:          << setw(10) << "trials"
 173:          << setw(16) << "mean_est"
 174:          << setw(14) << "mean/n"
 175:          << setw(16) << "rel_bias_%"
 176:          << setw(16) << "stddev/n"
 177:          << setw(14) << "mean_state"
 178:          << setw(16) << "approx_state"
 179:          << setw(14) << "ns/event"
 180:          << '\n';
```

The terminal table reports mean estimate, mean/true-count, relative bias,
normalized standard deviation, mean counter state, theoretical approximate
state, and nanoseconds per event.

**Correctness / mathematical role:** These columns separately test unbiasedness, variance, state growth, and O(1) per-event work.

### Monte Carlo counter trials — `problem02_fibonacci_probabilistic_counting.cpp` L182–L210

```cpp
 182:     for (uint64_t n : eventCounts) {
 183:         const int trials = trialsFor(n);
 184: 
 185:         long double sum = 0.0L;
 186:         long double sumSquares = 0.0L;
 187:         long double sumState = 0.0L;
 188: 
 189:         auto start = Clock::now();
 190: 
 191:         for (int trial = 0; trial < trials; ++trial) {
 192:             FibonacciMorrisCounter counter(
 193:                 represented,
 194:                 0x9E3779B97F4A7C15ULL ^
 195:                 (static_cast<uint64_t>(trial + 1) *
 196:                  0xD1B54A32D192ED03ULL)
 197:             );
 198: 
 199:             for (uint64_t event = 0; event < n; ++event)
 200:                 counter.increment();
 201: 
 202:             const long double estimate =
 203:                 counter.estimate();
 204: 
 205:             sum += estimate;
 206:             sumSquares += estimate * estimate;
 207:             sumState += counter.counterState();
 208:         }
 209: 
 210:         auto end = Clock::now();
```

For each true event count, every trial gets a different deterministic seed.
It processes exactly `n` increment events and accumulates first/second moments
and final state.

**Correctness / mathematical role:** `sum/trials` estimates expectation; `sumSquares/trials` permits variance estimation.

**C++ / implementation concepts:** seed mixing with 64-bit constants, nested loops, `chrono`.

### Mean, variance, standard deviation, and bias — `problem02_fibonacci_probabilistic_counting.cpp` L212–L225

```cpp
 212:         const long double mean =
 213:             sum / trials;
 214: 
 215:         const long double variance =
 216:             max(
 217:                 0.0L,
 218:                 sumSquares / trials - mean * mean
 219:             );
 220: 
 221:         const long double standardDeviation =
 222:             sqrt(variance);
 223: 
 224:         const long double relativeBiasPercent =
 225:             100.0L * (mean - n) / n;
```

The program computes the population-form Monte Carlo second central moment
from `E[X^2]-E[X]^2`, clamps tiny negative floating-point roundoff to zero, then
reports standard deviation and relative bias.

**Correctness / mathematical role:** For an unbiased estimator the true expected bias is zero; finite trials produce nonzero sample bias.

### Approximate Fibonacci state — `problem02_fibonacci_probabilistic_counting.cpp` L227–L230

```cpp
 227:         // n_i ~= F_{i+1} ~= phi^(i+1)/sqrt(5)
 228:         const long double approximateState =
 229:             log(static_cast<long double>(n) * sqrt(5.0L))
 230:             / log(phi) - 1.0L;
```

Using `F_{i+1} ≈ phi^(i+1)/sqrt(5)`, the code solves for the state `i`
corresponding to represented count `n`.

**Correctness / mathematical role:** \[
i\approx
\frac{\ln(n\sqrt5)}{\ln\phi}-1.
\]

**C++ / implementation concepts:** natural logarithm `log`.

### Per-event timing and terminal row — `problem02_fibonacci_probabilistic_counting.cpp` L232–L256

```cpp
 232:         const double nsPerEvent =
 233:             chrono::duration<double, nano>(
 234:                 end - start
 235:             ).count() /
 236:             (static_cast<double>(trials) *
 237:              static_cast<double>(n));
 238: 
 239:         cout << left
 240:              << setw(12) << n
 241:              << setw(10) << trials
 242:              << setw(16) << fixed << setprecision(2)
 243:              << static_cast<double>(mean)
 244:              << setw(14) << setprecision(5)
 245:              << static_cast<double>(mean / n)
 246:              << setw(16) << setprecision(3)
 247:              << static_cast<double>(relativeBiasPercent)
 248:              << setw(16) << setprecision(5)
 249:              << static_cast<double>(standardDeviation / n)
 250:              << setw(14) << setprecision(3)
 251:              << static_cast<double>(sumState / trials)
 252:              << setw(16) << setprecision(3)
 253:              << static_cast<double>(approximateState)
 254:              << setw(14) << setprecision(3)
 255:              << nsPerEvent
 256:              << '\n';
```

Total elapsed time is divided by `trials*n`, giving nanoseconds per stream
event. Other columns normalize stochastic behavior against the theoretical true
count.

**Correctness / mathematical role:** If per-event time stays bounded as n grows, the implementation's observed runtime is consistent with O(1) work per increment and O(n) total stream processing.

### Interpretation — `problem02_fibonacci_probabilistic_counting.cpp` L259–L266

```cpp
 259:     cout << "\nInterpretation:\n";
 260:     cout << "1. The sample mean should fluctuate around the true count n.\n";
 261:     cout << "2. A single probabilistic counter can have substantial variance.\n";
 262:     cout << "3. The counter state grows only logarithmically with n.\n";
 263:     cout << "4. Every stream event performs expected O(1) work.\n";
 264: 
 265:     return 0;
 266: }
```

The concluding lines deliberately note the high variance of a single counter
rather than claiming that unbiased means precise.

**Viva point:** Unbiasedness and low variance are different properties.


## Morris expectation proof in one line

Let the represented count before an increment be \(n_i\), and let

\[
\Delta_i=n_{i+1}-n_i.
\]

Then

\[
E[X_{t+1}\mid X_t=n_i]
=
\frac1{\Delta_i}n_{i+1}
+
\left(1-\frac1{\Delta_i}\right)n_i
=
n_i+1.
\]

Taking expectations again:

\[
E[X_{t+1}]
=
E[X_t]+1.
\]

Starting from \(X_0=0\), induction gives:

\[
\boxed{E[X_n]=n}.
\]

## Problem 2 proof map

| Concept | Exact code |
|---|---:|
| CLRS rule documented | L15–L22 |
| Fibonacci shift clarification | L24–L32 |
| Representation construction | L62–L84 |
| Increment probability | L97–L108 |
| Unbiased expectation identity | L163–L168 |
| Monte Carlo expectation check | L182–L256 |
| Variance/stddev check | L185–L222 |
| Fibonacci state asymptotic | L227–L230 |
| Per-event runtime | L232–L237 |

---

# 7. Problem 3 — Uniform online stream selection

**File:** `problem03_online_stream_selection.cpp`

This is reservoir sampling with a reservoir of size one.

### Shared 64-bit PRNG — `problem03_online_stream_selection.cpp` L1–L37

```cpp
   1: #include <algorithm>
   2: #include <chrono>
   3: #include <cmath>
   4: #include <cstdint>
   5: #include <iomanip>
   6: #include <iostream>
   7: #include <numeric>
   8: #include <vector>
   9: 
  10: using namespace std;
  11: using Clock = chrono::steady_clock;
  12: 
  13: class XorShift64Star {
  14:     uint64_t state;
  15: 
  16: public:
  17:     explicit XorShift64Star(uint64_t seed)
  18:         : state(seed ? seed : 0x9E3779B97F4A7C15ULL) {}
  19: 
  20:     uint64_t next() {
  21:         state ^= state >> 12;
  22:         state ^= state << 25;
  23:         state ^= state >> 27;
  24:         return state * 0x2545F4914F6CDD1DULL;
  25:     }
  26: 
  27:     uint64_t bounded(uint64_t bound) {
  28:         const uint64_t threshold = -bound % bound;
  29: 
  30:         while (true) {
  31:             const uint64_t value = next();
  32: 
  33:             if (value >= threshold)
  34:                 return value % bound;
  35:         }
  36:     }
  37: };
```

The stream selector uses the same exact bounded-rejection technique as the
other stochastic simulations.

**C++ / implementation concepts:** xorshift-star, modulo-bias rejection.

### Reservoir result — `problem03_online_stream_selection.cpp` L39–L42

```cpp
  39: struct ReservoirResult {
  40:     uint64_t selected = 0;
  41:     uint64_t replacements = 0;
  42: };
```

The result records the selected one-based stream item and the number of
replacements after the initial selection.

**C++ / implementation concepts:** aggregate struct with default member initialization.

### Reservoir sampling algorithm — `problem03_online_stream_selection.cpp` L44–L65

```cpp
  44: // Submitted reservoir-sampling rule:
  45: // for zero-based stream index i, draw r uniformly from [0, i].
  46: // Replace the stored element iff r == i.
  47: ReservoirResult selectOneFromStream(
  48:     uint64_t n,
  49:     XorShift64Star& rng
  50: ) {
  51:     ReservoirResult result;
  52: 
  53:     for (uint64_t i = 0; i < n; ++i) {
  54:         const uint64_t r = rng.bounded(i + 1);
  55: 
  56:         if (r == i) {
  57:             result.selected = i + 1;
  58: 
  59:             if (i > 0)
  60:                 ++result.replacements;
  61:         }
  62:     }
  63: 
  64:     return result;
  65: }
```

For zero-based arrival index `i`, draw uniformly from `0..i`. Replace the
stored element exactly when `r==i`. On the first item (`i=0`) this occurs with
probability 1, so initialization is automatic.

**Correctness / mathematical role:** At arrival number \(i+1\), the new item is selected with probability

\[
1/(i+1).
\]

Any old item had probability \(1/i\) before the arrival and survives with
probability \(i/(i+1)\). Thus its new probability is

\[
\frac1i\frac{i}{i+1}=\frac1{i+1}.
\]

Therefore every seen item is uniform.

**C++ / implementation concepts:** online processing, O(1) reservoir state, exact bounded RNG.

**Viva point:** No stream length needs to be known in advance for the update rule itself.

### Harmonic number helper — `problem03_online_stream_selection.cpp` L67–L74

```cpp
  67: long double harmonicNumber(uint64_t n) {
  68:     long double sum = 0.0L;
  69: 
  70:     for (uint64_t i = 1; i <= n; ++i)
  71:         sum += 1.0L / i;
  72: 
  73:     return sum;
  74: }
```

Computes the exact finite harmonic sum used to compare expected replacement
count.

**Correctness / mathematical role:** The probability of replacement at arrival \(i\) is \(1/i\).
Excluding the guaranteed first selection,

\[
E[R_n]
=
\sum_{i=2}^{n}\frac1i
=
H_n-1.
\]

**C++ / implementation concepts:** `long double` accumulation.

### Adaptive Monte Carlo trial count — `problem03_online_stream_selection.cpp` L76–L83

```cpp
  76: int trialsFor(uint64_t n) {
  77:     if (n <= 10) return 100'000;
  78:     if (n <= 100) return 50'000;
  79:     if (n <= 1'000) return 20'000;
  80:     if (n <= 10'000) return 2'000;
  81:     if (n <= 100'000) return 200;
  82:     return 30;
  83: }
```

Expensive million-element streams use fewer repeated trials; smaller streams
use many more.

**Viva point:** This is experiment engineering, not part of reservoir sampling.

### Uniformity experiment — `problem03_online_stream_selection.cpp` L92–L109

```cpp
  92:     constexpr uint64_t uniformityN = 20;
  93:     constexpr uint64_t uniformityTrials = 500'000;
  94: 
  95:     vector<uint64_t> frequency(uniformityN, 0);
  96:     XorShift64Star uniformityRng(9);
  97: 
  98:     for (uint64_t trial = 0;
  99:          trial < uniformityTrials;
 100:          ++trial) {
 101: 
 102:         const ReservoirResult result =
 103:             selectOneFromStream(
 104:                 uniformityN,
 105:                 uniformityRng
 106:             );
 107: 
 108:         ++frequency[result.selected - 1];
 109:     }
```

A 20-element stream is sampled 500,000 times. The selected location frequency
is recorded for all 20 possible items.

**Correctness / mathematical role:** Correct uniformity predicts exactly 0.05 probability for every item.

### Reservoir distribution diagnostics — `problem03_online_stream_selection.cpp` L111–L140

```cpp
 111:     const double theoreticalProbability =
 112:         1.0 / uniformityN;
 113: 
 114:     double maximumError = 0.0;
 115:     double chiSquare = 0.0;
 116:     uint64_t minimumFrequency = frequency[0];
 117:     uint64_t maximumFrequency = frequency[0];
 118: 
 119:     for (uint64_t count : frequency) {
 120:         const double probability =
 121:             static_cast<double>(count) /
 122:             uniformityTrials;
 123: 
 124:         maximumError =
 125:             max(maximumError,
 126:                 abs(probability - theoreticalProbability));
 127: 
 128:         const double expected =
 129:             static_cast<double>(uniformityTrials) /
 130:             uniformityN;
 131: 
 132:         const double difference =
 133:             static_cast<double>(count) - expected;
 134: 
 135:         chiSquare +=
 136:             difference * difference / expected;
 137: 
 138:         minimumFrequency = min(minimumFrequency, count);
 139:         maximumFrequency = max(maximumFrequency, count);
 140:     }
```

The experiment calculates maximum probability error, chi-square, and minimum
and maximum observed category frequencies.

**Correctness / mathematical role:** These provide finite-sample evidence for the exact induction proof above.

### Uniformity terminal output — `problem03_online_stream_selection.cpp` L142–L154

```cpp
 142:     cout << "Uniformity experiment\n";
 143:     cout << "Stream size              : " << uniformityN << '\n';
 144:     cout << "Trials                   : " << uniformityTrials << '\n';
 145:     cout << "Expected probability     : "
 146:          << fixed << setprecision(6)
 147:          << theoreticalProbability << '\n';
 148:     cout << "Maximum probability error: "
 149:          << maximumError << '\n';
 150:     cout << "Min / max frequency      : "
 151:          << minimumFrequency << " / "
 152:          << maximumFrequency << '\n';
 153:     cout << "Chi-square (df=19)       : "
 154:          << chiSquare << "\n\n";
```

Prints the theoretical probability and observed diagnostics.

**Viva point:** The chi-square statistic is a diagnostic, not the proof that reservoir sampling is uniform.

### Replacement asymptotic setup — `problem03_online_stream_selection.cpp` L159–L180

```cpp
 159:     const vector<uint64_t> sizes = {
 160:         10,
 161:         100,
 162:         1'000,
 163:         10'000,
 164:         100'000,
 165:         1'000'000
 166:     };
 167: 
 168:     cout << "Replacement experiment\n";
 169:     cout << "Theory: E[replacements after initial selection] = H_n - 1.\n";
 170:     cout << "Since H_n = ln n + gamma + o(1), replacements grow Theta(log n).\n";
 171:     cout << "Processing the stream itself remains Theta(n) time and O(1) space.\n\n";
 172: 
 173:     cout << left
 174:          << setw(12) << "n"
 175:          << setw(10) << "trials"
 176:          << setw(18) << "avg_replacements"
 177:          << setw(16) << "H_n-1"
 178:          << setw(16) << "avg/theory"
 179:          << setw(14) << "ns/element"
 180:          << '\n';
```

The stream size grows to one million. The terminal table compares average
replacements directly with `H_n-1` and reports nanoseconds per processed
element.

**Correctness / mathematical role:** Since

\[
H_n=\ln n+\gamma+o(1),
\]

expected replacement count is \(\Theta(\log n)\), even though all \(n\) arrivals
must still be processed.

**Viva point:** Do not confuse number of replacements Θ(log n) with runtime Θ(n).

### High-n replacement experiment — `problem03_online_stream_selection.cpp` L182–L213

```cpp
 182:     for (uint64_t n : sizes) {
 183:         const int trials = trialsFor(n);
 184:         long double totalReplacements = 0.0L;
 185: 
 186:         XorShift64Star rng(
 187:             0xA0761D6478BD642FULL ^ n
 188:         );
 189: 
 190:         auto start = Clock::now();
 191: 
 192:         for (int trial = 0; trial < trials; ++trial) {
 193:             const ReservoirResult result =
 194:                 selectOneFromStream(n, rng);
 195: 
 196:             totalReplacements += result.replacements;
 197:         }
 198: 
 199:         auto end = Clock::now();
 200: 
 201:         const long double average =
 202:             totalReplacements / trials;
 203: 
 204:         const long double theory =
 205:             harmonicNumber(n) - 1.0L;
 206: 
 207:         const double nsPerElement =
 208:             chrono::duration<double, nano>(
 209:                 end - start
 210:             ).count() /
 211:             (static_cast<double>(trials) *
 212:              static_cast<double>(n));
 213: 
```

Each trial processes the stream, accumulates replacement count, evaluates the
harmonic prediction, and normalizes elapsed time per input element.

**Correctness / mathematical role:** A bounded `avg/theory` ratio validates the expected logarithmic replacement-count model over the tested sizes; bounded ns/element supports linear total processing time.

### Results and proof reminder — `problem03_online_stream_selection.cpp` L214–L235

```cpp
 214:         cout << left
 215:              << setw(12) << n
 216:              << setw(10) << trials
 217:              << setw(18) << fixed << setprecision(4)
 218:              << static_cast<double>(average)
 219:              << setw(16)
 220:              << static_cast<double>(theory)
 221:              << setw(16)
 222:              << static_cast<double>(average / theory)
 223:              << setw(14) << setprecision(3)
 224:              << nsPerElement
 225:              << '\n';
 226:     }
 227: 
 228:     cout << "\nCorrectness proof idea:\n";
 229:     cout << "After item i arrives, it is selected with probability 1/i.\n";
 230:     cout << "Any earlier item survives with probability "
 231:             "(1/(i-1))*((i-1)/i)=1/i.\n";
 232:     cout << "Therefore every one of the i seen elements is uniform.\n";
 233: 
 234:     return 0;
 235: }
```

The terminal row prints average replacement count, theoretical harmonic value,
their ratio, and per-element time. The final three lines restate the induction
argument.

**Correctness / mathematical role:** The proof establishes exact uniformity for all n; experiment tests implementation behavior.


## Initialization → Maintenance → Progress → Termination

### Initialization

At `i=0`, `bounded(1)` must return zero. The first item is selected with
probability 1.

### Maintenance

Assume after `i` items each stored candidate has probability `1/i`.
The `(i+1)`st step preserves uniformity by the calculation above.

### Progress

The stream index increments once per arrival.

### Termination

After `n` arrivals, exactly one reservoir item is stored and each stream item has
probability `1/n`.

## Complexity

- time per arrival: expected O(1),
- whole stream: Θ(n),
- auxiliary reservoir storage: Θ(1),
- expected number of replacements: Θ(log n).

---

# 8. Problem 4 — Hat problem

**File:** `problem04_hat_problem.cpp`

The random assignment of hats is modeled as a uniformly random permutation.
A student receiving their own hat is a **fixed point** of that permutation.

### PRNG and exact bounded integer — `problem04_hat_problem.cpp` L1–L37

```cpp
   1: #include <algorithm>
   2: #include <chrono>
   3: #include <cmath>
   4: #include <cstdint>
   5: #include <iomanip>
   6: #include <iostream>
   7: #include <numeric>
   8: #include <vector>
   9: 
  10: using namespace std;
  11: using Clock = chrono::steady_clock;
  12: 
  13: class XorShift64Star {
  14:     uint64_t state;
  15: 
  16: public:
  17:     explicit XorShift64Star(uint64_t seed)
  18:         : state(seed ? seed : 0x9E3779B97F4A7C15ULL) {}
  19: 
  20:     uint64_t next() {
  21:         state ^= state >> 12;
  22:         state ^= state << 25;
  23:         state ^= state >> 27;
  24:         return state * 0x2545F4914F6CDD1DULL;
  25:     }
  26: 
  27:     uint64_t bounded(uint64_t bound) {
  28:         const uint64_t threshold = -bound % bound;
  29: 
  30:         while (true) {
  31:             const uint64_t value = next();
  32: 
  33:             if (value >= threshold)
  34:                 return value % bound;
  35:         }
  36:     }
  37: };
```

The Hat experiment uses the same 64-bit xorshift-star generator and exact
modulo-rejection mapping.

**C++ / implementation concepts:** fixed-width unsigned arithmetic, rejection sampling.

### Per-trial result — `problem04_hat_problem.cpp` L39–L42

```cpp
  39: struct HatTrial {
  40:     int fixedPoints = 0;
  41:     uint64_t operations = 0;
  42: };
```

Stores number of fixed points and explicit operation count.

**Correctness / mathematical role:** The operation counter is used to validate Θ(n) runtime independently of noisy wall-clock timing.

### Hat-array initialization — `problem04_hat_problem.cpp` L44–L52

```cpp
  44: // Preserves the submitted RandomHatProblem idea:
  45: // make a uniformly random permutation of hats, then count fixed points.
  46: HatTrial runHatTrial(int n, XorShift64Star& rng) {
  47:     vector<int> hats(n);
  48:     iota(hats.begin(), hats.end(), 0);
  49: 
  50:     HatTrial result;
  51:     result.operations += n; // initialize n hat labels
  52: 
```

The vector is filled with `0,1,...,n-1`; hat label `i` is initially associated
with student index `i`. The operation counter charges one unit per initialized
hat.

**C++ / implementation concepts:** `iota`, vector allocation.

### Fisher-Yates uniform shuffle — `problem04_hat_problem.cpp` L53–L62

```cpp
  53:     // Fisher-Yates shuffle: every permutation has probability 1/n!.
  54:     for (int i = n - 1; i > 0; --i) {
  55:         const int j =
  56:             static_cast<int>(
  57:                 rng.bounded(static_cast<uint64_t>(i) + 1)
  58:             );
  59: 
  60:         swap(hats[i], hats[j]);
  61:         ++result.operations;
  62:     }
```

For positions from the end toward one, choose `j` uniformly in `[0,i]` and
swap. This is the standard Fisher-Yates shuffle.

**Correctness / mathematical role:** Inductively, at step `i`, each of the remaining `i+1` items is equally likely
to be placed into position `i`. Multiplying the conditional choices gives every
complete permutation probability

\[
1/n!.
\]

**C++ / implementation concepts:** `swap`, exact bounded random integer, descending loop.

**Viva point:** Using an unbiased bounded random integer is important: biased `j` values would produce a biased permutation.

### Fixed-point scan — `problem04_hat_problem.cpp` L64–L73

```cpp
  64:     // A fixed point means student i receives hat i.
  65:     for (int student = 0; student < n; ++student) {
  66:         if (hats[student] == student)
  67:             ++result.fixedPoints;
  68: 
  69:         ++result.operations;
  70:     }
  71: 
  72:     return result;
  73: }
```

The program scans the shuffled assignment and increments `fixedPoints` when
student index equals hat label. One operation is charged per inspected student.

**Correctness / mathematical role:** This computes the number of fixed points of the generated permutation exactly.

### Exact derangement probability — `problem04_hat_problem.cpp` L75–L91

```cpp
  75: // Exact probability D_n / n! using
  76: // D_n / n! = sum_{k=0}^n (-1)^k / k!.
  77: long double exactNoFixedPointProbability(int n) {
  78:     long double probability = 1.0L;
  79:     long double inverseFactorial = 1.0L;
  80: 
  81:     for (int k = 1; k <= n; ++k) {
  82:         inverseFactorial /= k;
  83: 
  84:         if (k % 2 == 0)
  85:             probability += inverseFactorial;
  86:         else
  87:             probability -= inverseFactorial;
  88:     }
  89: 
  90:     return probability;
  91: }
```

The probability that no student gets their own hat is
`D_n/n!`. Instead of computing enormous factorials and derangement integers,
the code evaluates the equivalent alternating series
`sum (-1)^k/k!` in long double.

**Correctness / mathematical role:** \[
\frac{D_n}{n!}
=
\sum_{k=0}^{n}\frac{(-1)^k}{k!}
\longrightarrow e^{-1}.
\]

**C++ / implementation concepts:** alternating series, incremental inverse factorial, `long double`.

### High-n trial-count policy — `problem04_hat_problem.cpp` L93–L98

```cpp
  93: int complexityTrialsFor(int n) {
  94:     if (n <= 1'000) return 10'000;
  95:     if (n <= 10'000) return 2'000;
  96:     if (n <= 100'000) return 300;
  97:     return 50;
  98: }
```

The complexity experiment adapts repetitions so million-element trials remain
practical.

**Viva point:** The probability experiment is separated and uses a fixed 100,000 trials per n; this avoids using a small high-n sample to make a probability claim.

### Theory printed — `problem04_hat_problem.cpp` L100–L108

```cpp
 100: int main() {
 101:     cout << "Problem 4: Random Hat Problem\n";
 102:     cout << "Each of n students receives one uniformly shuffled hat.\n\n";
 103: 
 104:     cout << "Theory:\n";
 105:     cout << "P(a particular student gets own hat) = 1/n.\n";
 106:     cout << "E[number of students with own hat] = n*(1/n) = 1.\n";
 107:     cout << "P(no student gets own hat) = D_n/n! -> 1/e.\n";
 108:     cout << "Fisher-Yates shuffle + fixed-point scan is Theta(n) per trial.\n\n";
```

The program states the four theoretical claims before looking at data:
individual own-hat probability, expected fixed points, derangement limit, and
linear per-trial algorithmic work.

**Correctness / mathematical role:** For student \(i\), define indicator \(X_i\). Since a uniform permutation places
their own hat at them with probability \(1/n\),

\[
E[X_i]=1/n.
\]

Then by linearity,

\[
E[\sum_iX_i]=1.
\]

### Probability experiment design — `problem04_hat_problem.cpp` L114–L133

```cpp
 114:     const vector<int> probabilitySizes = {
 115:         2, 3, 4, 5, 10, 20, 50, 100
 116:     };
 117: 
 118:     constexpr int probabilityTrials = 100'000;
 119: 
 120:     cout << "A. Probability experiment (" << probabilityTrials
 121:          << " trials for every n)\n";
 122: 
 123:     cout << left
 124:          << setw(8)  << "n"
 125:          << setw(16) << "avg_fixed"
 126:          << setw(16) << "n*own_prob"
 127:          << setw(16) << "Pnone_exp"
 128:          << setw(16) << "Pnone_exact"
 129:          << setw(16) << "|error|"
 130:          << setw(16) << "|exact-1/e|"
 131:          << '\n';
 132: 
 133:     const long double inverseE = exp(-1.0L);
```

Probability tests use n from 2 through 100 and exactly 100,000 random
permutations for every n. This keeps sampling error controlled while still
showing the convergence to 1/e.

**Correctness / mathematical role:** Separating distribution testing from high-n runtime testing is statistically cleaner than using only a few million-element permutations.

### Probability Monte Carlo trials — `problem04_hat_problem.cpp` L135–L155

```cpp
 135:     for (int n : probabilitySizes) {
 136:         XorShift64Star rng(
 137:             0xE7037ED1A0B428DBULL ^
 138:             static_cast<uint64_t>(n)
 139:         );
 140: 
 141:         long double fixedTotal = 0.0L;
 142:         uint64_t noFixedTrials = 0;
 143: 
 144:         for (int trial = 0;
 145:              trial < probabilityTrials;
 146:              ++trial) {
 147: 
 148:             const HatTrial result =
 149:                 runHatTrial(n, rng);
 150: 
 151:             fixedTotal += result.fixedPoints;
 152: 
 153:             if (result.fixedPoints == 0)
 154:                 ++noFixedTrials;
 155:         }
```

For each n the program counts total fixed points and number of derangements
(no fixed points) across 100,000 shuffled permutations.

**Correctness / mathematical role:** Average fixed points estimates E[X]; derangement frequency estimates D_n/n!.

### Probability estimates and exact comparison — `problem04_hat_problem.cpp` L157–L190

```cpp
 157:         const long double averageFixed =
 158:             fixedTotal / probabilityTrials;
 159: 
 160:         const long double ownProbability =
 161:             fixedTotal /
 162:             (static_cast<long double>(probabilityTrials) * n);
 163: 
 164:         const long double experimentalNone =
 165:             static_cast<long double>(noFixedTrials) /
 166:             probabilityTrials;
 167: 
 168:         const long double exactNone =
 169:             exactNoFixedPointProbability(n);
 170: 
 171:         cout << left
 172:              << setw(8)  << n
 173:              << setw(16) << fixed << setprecision(6)
 174:              << static_cast<double>(averageFixed)
 175:              << setw(16)
 176:              << static_cast<double>(ownProbability * n)
 177:              << setw(16)
 178:              << static_cast<double>(experimentalNone)
 179:              << setw(16)
 180:              << static_cast<double>(exactNone)
 181:              << setw(16)
 182:              << static_cast<double>(
 183:                     abs(experimentalNone - exactNone)
 184:                 )
 185:              << setw(16)
 186:              << static_cast<double>(
 187:                     abs(exactNone - inverseE)
 188:                 )
 189:              << '\n';
 190:     }
```

The code computes average fixed count, normalized own-hat probability,
experimental derangement probability, exact derangement probability, experiment
error, and exact asymptotic error relative to 1/e.

**Correctness / mathematical role:** The columns test three distinct claims:

1. \(E[X]=1\),
2. \(P(X_i=1)=1/n\),
3. \(P(X=0)\to1/e\).

**C++ / implementation concepts:** `exp(-1.0L)`, `long double`, normalization.

### High-n complexity experiment columns — `problem04_hat_problem.cpp` L196–L212

```cpp
 196:     const vector<int> complexitySizes = {
 197:         1'000,
 198:         10'000,
 199:         100'000,
 200:         1'000'000
 201:     };
 202: 
 203:     cout << "\nB. High-n complexity experiment\n";
 204:     cout << left
 205:          << setw(12) << "n"
 206:          << setw(10) << "trials"
 207:          << setw(16) << "avg_fixed"
 208:          << setw(16) << "n*own_prob"
 209:          << setw(16) << "avg_ops"
 210:          << setw(14) << "ops/n"
 211:          << setw(14) << "ms/trial"
 212:          << '\n';
```

The separate second experiment grows n to one million and reports operation
count and wall-clock time without pretending that the relatively few high-n
trials are a precise probability estimate.

**Viva point:** This separation is the professor-quality experimental improvement over the first draft.

### High-n execution and timing — `problem04_hat_problem.cpp` L214–L251

```cpp
 214:     for (int n : complexitySizes) {
 215:         const int trials =
 216:             complexityTrialsFor(n);
 217: 
 218:         XorShift64Star rng(
 219:             0xA0761D6478BD642FULL ^
 220:             static_cast<uint64_t>(n)
 221:         );
 222: 
 223:         long double fixedTotal = 0.0L;
 224:         long double operationTotal = 0.0L;
 225: 
 226:         auto start = Clock::now();
 227: 
 228:         for (int trial = 0; trial < trials; ++trial) {
 229:             const HatTrial result =
 230:                 runHatTrial(n, rng);
 231: 
 232:             fixedTotal += result.fixedPoints;
 233:             operationTotal += result.operations;
 234:         }
 235: 
 236:         auto end = Clock::now();
 237: 
 238:         const long double averageFixed =
 239:             fixedTotal / trials;
 240: 
 241:         const long double ownProbability =
 242:             fixedTotal /
 243:             (static_cast<long double>(trials) * n);
 244: 
 245:         const long double averageOperations =
 246:             operationTotal / trials;
 247: 
 248:         const double msPerTrial =
 249:             chrono::duration<double, milli>(
 250:                 end - start
 251:             ).count() / trials;
```

Every trial performs a uniform shuffle and fixed-point scan. The code
accumulates operations and times the complete trial.

**Correctness / mathematical role:** For each n:

- initialization charges n,
- Fisher-Yates charges n-1,
- fixed-point scan charges n.

So this implementation records exactly

\[
3n-1
\]

abstract operations per trial.

**C++ / implementation concepts:** operation-count experiment, `chrono`.

### High-n normalization and conclusion — `problem04_hat_problem.cpp` L253–L277

```cpp
 253:         cout << left
 254:              << setw(12) << n
 255:              << setw(10) << trials
 256:              << setw(16) << fixed << setprecision(5)
 257:              << static_cast<double>(averageFixed)
 258:              << setw(16)
 259:              << static_cast<double>(ownProbability * n)
 260:              << setw(16) << setprecision(2)
 261:              << static_cast<double>(averageOperations)
 262:              << setw(14) << setprecision(5)
 263:              << static_cast<double>(averageOperations / n)
 264:              << setw(14) << setprecision(6)
 265:              << msPerTrial
 266:              << '\n';
 267:     }
 268: 
 269:     cout << "\nInterpretation:\n";
 270:     cout << "1. avg_fixed and n*own_prob stay near 1, matching E[X]=1.\n";
 271:     cout << "2. The many-trial table tracks exact derangement probability D_n/n!.\n";
 272:     cout << "3. The exact probability rapidly approaches 1/e.\n";
 273:     cout << "4. avg_ops/n approaches 3 for this implementation, proving the\n";
 274:     cout << "   measured operation count grows linearly: Theta(n) per trial.\n";
 275: 
 276:     return 0;
 277: }
```

The key terminal column is `avg_ops/n`. Since `(3n-1)/n -> 3`, the ratio
should approach three. This gives especially clean experimental confirmation of
linear work.

**Correctness / mathematical role:** \[
\frac{3n-1}{n}=3-\frac1n\to3.
\]

Hence the measured operation count is Θ(n).


## Hat asymptotic proofs

### Probability one particular student gets own hat

A uniformly random permutation has `n!` possible assignments.

Fix student `i`. Exactly `(n-1)!` permutations place hat `i` at student `i`.

Therefore:

\[
P(X_i=1)
=
\frac{(n-1)!}{n!}
=
\frac1n.
\]

### Expected number of correct hats

\[
X=\sum_{i=1}^{n}X_i.
\]

By linearity of expectation:

\[
E[X]
=
\sum_{i=1}^{n}\frac1n
=
1.
\]

No independence assumption is required.

### Probability nobody receives own hat

The number of derangements is `D_n`.

\[
P(X=0)=\frac{D_n}{n!}.
\]

Inclusion-exclusion gives:

\[
\frac{D_n}{n!}
=
\sum_{k=0}^{n}\frac{(-1)^k}{k!}.
\]

As \(n\to\infty\):

\[
P(X=0)\to e^{-1}.
\]

### Runtime

Fisher-Yates:

\[
n-1
\]

iterations.

Fixed-point scan:

\[
n
\]

iterations.

Initialization is also linear.

Therefore:

\[
\boxed{\Theta(n)}
\]

per trial.

---

# 9. Cross-file Initialization / Maintenance / Progress / Termination map

| Algorithm | Initialization | Maintenance | Progress | Termination |
|---|---|---|---|---|
| Weighted prefix build | P1 L89–L90 | L92–L102 | one weight/iteration | all weights consumed |
| Weighted pick | prefix already valid | `upper_bound` locates interval | binary search shrinks | one index returned |
| Circle rejection | P1 L145–L150 | each proposal tested L152–L162 | new proposal on rejection | first accepted point |
| Fibonacci table | P2 L63–L70 | recurrence L76–L80 | Fibonacci index grows | before integer overflow |
| Morris increment | state/table valid | probabilistic step L103–L107 | one stream event consumed | caller finishes n events |
| Reservoir sampling | P3 L51–L54 | uniformity preserved L56–L60 | i++ | n items processed |
| Fisher-Yates | P4 L47–L54 | random swap L55–L61 | i-- | i reaches 0 |
| Fixed-point scan | zero fixed points | inspect one student | student++ | n students scanned |
| Derangement series | probability=1 | add next ±1/k! | k++ | k=n |
| Hat high-n benchmark | counters=0 | accumulate each trial | trial++ | requested repetitions complete |

---

# 10. Precondition / postcondition map

## Weighted picker

**Preconditions**

- nonempty weight list,
- every weight positive,
- total weight fits `uint32_t`.

**Postcondition**

`pickIndex()` returns index `i` with exact intended probability
`weight[i]/totalWeight` under the PRNG-uniformity model.

## Circle sampler

**Precondition**

Positive radius is assumed by the LeetCode problem.

**Postcondition**

Returned point lies inside the specified circle and follows uniform-area
rejection sampling.

## Fibonacci counter

**Preconditions**

- represented sequence strictly increasing,
- state not at overflow,
- bounded RNG uniform.

**Postcondition of one increment**

Represented value increases by expected value exactly 1.

## Reservoir sampling

**Precondition**

Stream contains at least one item if a selected output is needed.

**Postcondition**

After n arrivals, every item has probability exactly 1/n of occupying the
reservoir.

## Hat trial

**Precondition**

`n >= 0`.

**Postcondition**

`fixedPoints` equals the fixed-point count of one uniformly generated random
permutation.

---

# 11. Expected vs worst-case complexity

## Weighted pick

After O(m) preprocessing:

\[
O(\log m)
\]

per pick due to binary search.

The bounded-RNG rejection has expected O(1) retries.

## Circle sampling

Each proposal is O(1).

Acceptance probability is \(\pi/4\), so:

\[
E[\text{attempts}]
=
4/\pi.
\]

Expected time is O(1), although the theoretical rejection loop has no fixed
finite worst-case attempt bound.

## Fibonacci Morris counting

One event computes one gap and one bounded random draw.

Expected per-event time is O(1).

Processing n events:

\[
\Theta(n).
\]

The compressed state magnitude grows only \(\Theta(\log n)\).

## Reservoir sampling

Every arrival is processed:

\[
\Theta(n)
\]

total time and O(1) auxiliary reservoir memory.

Expected number of replacements is only:

\[
H_n-1=\Theta(\log n).
\]

## Hat experiment

One trial:

\[
\Theta(n).
\]

Repeated T times:

\[
\Theta(Tn).
\]

---

# 12. Exact bounded random generation — why it matters

A common but subtle mistake is:

```cpp
rng() % bound
```

when the RNG range size is not divisible by `bound`.

Suppose the source has 10 equally likely values `0..9` and `bound=4`.

Modulo gives:

```text
0 <- 0,4,8   (3 ways)
1 <- 1,5,9   (3 ways)
2 <- 2,6     (2 ways)
3 <- 3,7     (2 ways)
```

which is biased.

The code rejects the small incomplete region so each residue has an equal number
of source values.

This matters directly for:

- weighted selection probabilities,
- reservoir uniformity,
- Fisher-Yates permutation uniformity.

---

# 13. Why operation/probability statistics matter more than raw timing

Timing alone cannot validate a randomized algorithm.

For this assignment the terminal experiments therefore report:

### Weighted picker

- expected probability,
- observed probability,
- max absolute error,
- chi-square.

### Circle

- mean x/y,
- \(E[r^2/R^2]\),
- acceptance rate,
- quadrant probabilities.

### Fibonacci counter

- mean estimate / n,
- relative bias,
- standard deviation / n,
- state growth,
- ns/event.

### Reservoir

- categorical uniformity,
- chi-square,
- average replacements / harmonic prediction,
- ns/element.

### Hat

- average fixed points,
- normalized own-hat probability,
- exact vs experimental derangement probability,
- `ops/n`,
- timing.

This is substantially stronger than printing one random example.

---

# 14. Statistical caution

A stochastic result should not be judged by whether one run exactly equals its
expectation.

For example, Fibonacci Morris counting is unbiased:

\[
E[\hat n]=n,
\]

but the standard deviation of one counter can still be large.

Similarly, a 1,000,000-student Hat trial can easily contain 0, 1, 2, or several
fixed points. The expected value 1 is a statement about repeated experiments,
not a guarantee that each permutation has exactly one fixed point.

This is why the Hat program uses:

- many moderate-size trials for probability convergence,
- separate high-n trials for runtime growth.

---

# 15. C++ advanced-concept index

| Concept | Best source location |
|---|---|
| `uint32_t` | P1 L18–L49 |
| `uint64_t` | all four files |
| `uint8_t` compact counter state | P2 L86–L89 |
| `static_cast` | P1 L35–L48, P2 L195+, P3 L121+, P4 L56+ |
| `numeric_limits` | P1 L98–L99; P2 L72–L75 |
| exceptions | P1 L86–L99; P2 L100–L101 |
| `explicit` constructor | RNG classes |
| initializer list | RNG/counter constructors |
| `upper_bound` | P1 L108–L114 |
| iterator subtraction | P1 L108–L114 |
| `array` | P1 quadrant counters |
| `long double` | P1/P2/P3/P4 statistical sums |
| `chrono` | all experiment files |
| `acos(-1.0)` | P1 L328–L329 |
| `exp(-1.0L)` | P4 L133 |
| `sqrt`, `log` | P2 L160–L161, L227–L230 |
| `iota` | P4 L47–L48 |
| digit separators | trial counts such as `2'000'000` |
| bit shifts | xorshift generators |
| hexadecimal integer literals | RNG seeds/constants |
| modulo/rejection | all bounded RNG helpers |
| `volatile` | not required in these stochastic loops because outputs/statistics are observably consumed |

---

# 16. Likely viva questions

## Why is the weighted picker O(log m), not O(1)?

The random number is O(1), but locating which prefix interval contains it uses
`upper_bound`, a binary search over m prefix sums.

## Can weighted picking be O(1)?

Yes, with an alias table after O(m) preprocessing and O(m) extra storage. The
submitted solution uses the simpler prefix-sum/binary-search method.

## Why use rejection for circle sampling instead of choosing radius uniformly?

If radius were uniform on `[0,R]`, points would be too dense near the center.
Uniform area requires radial CDF:

\[
P(r\le x)=x^2/R^2,
\]

so inverse-transform would use \(r=R\sqrt U\). Rejection sampling is another
correct method and matches the submitted solution.

## Why is `E[r^2/R^2]=1/2`?

For uniform disk area, \(U=r^2/R^2\) is uniform on `[0,1]`.

Therefore \(E[U]=1/2\).

## Why does Morris counting stay unbiased?

Because a rare jump of size \(\Delta_i\) occurs with probability \(1/\Delta_i\),
so conditional expected increment is exactly 1.

## Why is the Fibonacci state logarithmic?

Fibonacci values grow exponentially:

\[
F_i=\Theta(\phi^i).
\]

Inverting exponential growth gives \(i=\Theta(\log n)\).

## Why can the counter use only log-log bits?

The stored register contains the state index i, not the represented count n.
Since \(i=\Theta(\log n)\), binary encoding i needs \(\Theta(\log\log n)\) bits.

## Why is reservoir sampling uniform?

Induction:

- new item gets probability 1/i,
- old item probability becomes `(1/(i-1))((i-1)/i)=1/i`.

## Why are expected replacements logarithmic?

Replacement at arrival i has probability 1/i.

So:

\[
E[R]=\sum_{i=2}^{n}1/i=H_n-1=\Theta(\log n).
\]

## Why is runtime still linear?

Every stream item must be examined once even if no replacement occurs.

## Why is expected number of correct hats exactly 1?

Indicator variables and linearity of expectation.

Independence between students is unnecessary.

## Why does no-fixed-point probability approach 1/e?

The derangement ratio is the finite alternating partial sum of `e^-1`.

## Why Fisher-Yates instead of repeated random swaps?

Fisher-Yates has a simple proof that every permutation occurs with exactly
probability `1/n!`.

Repeated arbitrary swaps do not automatically produce a uniform permutation.

---

# 17. Makefile

Compile all programs:

```bash
make
```

Run all programs:

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

---

# 18. Final formulas to remember

## Weighted selection

\[
P(i)=\frac{w_i}{\sum_j w_j}
\]

## Circle rejection

\[
P(\text{accept})=\frac{\pi}{4}
\]

\[
E[\text{attempts}]=\frac4\pi
\]

## Morris counting

\[
P(i\to i+1)=\frac1{n_{i+1}-n_i}
\]

\[
E[\Delta]=1
\]

\[
E[\hat n]=n
\]

For Fibonacci representation:

\[
state=\Theta(\log n)
\]

## Reservoir sampling

\[
P(\text{item j selected after n})=\frac1n
\]

\[
E[\text{replacements}]=H_n-1=\Theta(\log n)
\]

## Hat problem

\[
P(\text{own hat})=\frac1n
\]

\[
E[\text{fixed points}]=1
\]

\[
P(\text{no fixed points})=\frac{D_n}{n!}\to\frac1e
\]

\[
T(n)=\Theta(n)\text{ per trial}
\]

---

# 19. Professor-level experimental conclusion

The experiments are designed to test **specific mathematical consequences**, not
merely produce timings.

A defensible conclusion is:

> The implementations were derived from exact probability laws. High-iteration
> Monte Carlo experiments agree with the predicted distributions, while
> operation-count/time normalization agrees with the theoretical asymptotic
> behavior over the tested ranges. These experiments validate the
> implementations; the mathematical derivations establish the general claims.

That is the distinction to preserve in a viva:
**theory proves; experiment corroborates the implementation.**
