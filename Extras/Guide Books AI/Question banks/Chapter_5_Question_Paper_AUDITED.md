# MTCS 102 — Chapter 5 Question Paper

## Thread-Level Parallelism

**Primary text:** John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 5  
**Format:** 4 weeks × 15 questions = 60 questions  
**Weekly split:** Questions 1–10 = GATE CSE previous-year questions; Questions 11–15 = complete textbook exercises for class discussion  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included

> **Audit status (2026-08-09):** The duplicate 2024 core-count PYQ has been removed from Chapter 5 because Chapter 1 is frozen and remains its canonical placement. The unusual `m[(i+1) mod 4]` expression in GATE CSE 2000 Q1.21 has been rechecked and is retained because that is how the indexed source states the question.  

> **Question-counting rule:** One source question/exercise is treated as exactly one question even when it contains several subparts. No source exercise has been split.
>
> **Wording note:** GATE PYQs are reformatted for readability while preserving the source data, program fragments, alternatives, and assessed concept. Textbook exercises are presented as self-contained paraphrases where possible; when an exercise fundamentally depends on a textbook figure/setup, the exact required figure/setup is explicitly identified.
>
> **Figures:** No external image asset is required for this Markdown paper. Simple precedence graphs are represented as program text or edge lists. Textbook Exercises 5.1 and 5.2 require Figures 5.35 and 5.36 from the primary text; those references are preserved.
>
> **Synchronization notation:** `P(S)` / `wait(S)` denotes the semaphore wait operation and `V(S)` / `signal(S)` denotes the semaphore signal operation unless the source question defines otherwise.

---

# Week 1 — Mutual Exclusion, Atomic Operations, and Snooping Cache Coherence

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2024 • Set 2 • Q36] — High

Three shared variables are initialized as follows:

```text
s1 = 1
s2 = 0
x  = 0
```

`S1` and `S2` are semaphores. Two threads execute:

```text
T1:                         T2:
wait(s1);                   wait(s1);
x = x + 1;                  x = x + 1;
print(x);                   print(x);
wait(s2);                   signal(s2);
signal(s1);                 signal(s1);
```

Which of the following outcomes are possible? **Select all that apply.**

1. `T1` prints `1` first and `T2` subsequently prints `2`.
2. `T2` prints `1` first and `T1` subsequently prints `2`.
3. `T1` prints `1` first and `T2` never prints because the execution deadlocks.
4. `T2` prints `1` first and `T1` never prints because the execution deadlocks.

---

### Q2. [GATE CSE 2015 • Set 1 • Q9] — Medium

The shared variable `B` is initially `2`. Two processes execute concurrently:

```text
P1:                         P2:
C = B - 1;                  D = 2 * B;
B = 2 * C;                  B = D - 1;
```

Each assignment is atomic, but the two processes may interleave in any order consistent with the program order within each process.

How many **distinct final values** can `B` take?

---

### Q3. [GATE CSE 2013 • Q34] — High

A shared integer `x` is initially `0`. Four processes `W`, `X`, `Y`, and `Z` execute concurrently.

- `W` and `X` each perform a read-modify-write operation that increments `x` by `1`.
- `Y` and `Z` each perform a read-modify-write operation that decrements `x` by `2`.

Each process surrounds its read-modify-write operation by `P(S)` and `V(S)`, where `S` is a **counting semaphore initialized to 2**.

What is the **maximum possible final value** of `x`?

1. `-2`
2. `-1`
3. `1`
4. `2`

---

### Q4. [GATE CSE 2012 • Q32] — High

Assume `Fetch_And_Add(X, i)` is an atomic instruction that:

1. returns the old value of `X`, and
2. atomically replaces `X` by `X + i`.

Consider the following lock, where `L` is an unsigned integer initialized to `0` when the lock is free.

```c
AcquireLock(L)
{
    while (Fetch_And_Add(L, 1))
        L = 1;
}

ReleaseLock(L)
{
    L = 0;
}
```

Which one of the following correctly characterizes this implementation?

1. It fails because the unsigned lock variable can overflow.
2. It fails because `L` can be nonzero even when the lock should be available.
3. It implements mutual exclusion, but starvation is possible.
4. It implements mutual exclusion and guarantees starvation freedom.

---

### Q5. [GATE CSE 2010 • Q23] — High

`S1` and `S2` are shared Boolean variables with arbitrary initial values. Two processes execute:

```text
P1:                                  P2:
while (S1 == S2) ;                   while (S1 != S2) ;
/* critical section */               /* critical section */
S1 = S2;                             S2 = !S1;
```

Which statement correctly describes the synchronization properties of this program?

1. It guarantees both mutual exclusion and progress.
2. It guarantees mutual exclusion but can violate progress.
3. It can violate mutual exclusion but guarantees progress.
4. It guarantees neither mutual exclusion nor progress.

---

### Q6. [GATE CSE 2009 • Q33] — High

Assume `test-and-set(X)` atomically returns the old value of `X` and sets `X` to `1`.

```c
enter_CS(X)
{
    while (test_and_set(X))
        ;
}

leave_CS(X)
{
    X = 0;
}
```

`X` is initially `0`.

Consider the statements:

I. The implementation is deadlock-free.  
II. The implementation is starvation-free.  
III. Processes enter the critical section in FIFO order.  
IV. More than one process can be in the critical section simultaneously.

Which option is correct?

1. I only
2. I and II only
3. II and III only
4. IV only

---

### Q7. [GATE CSE 2006 • Q61] — High

An atomic primitive `fetch-and-set(x, y)` sets `*x` to `1` and returns the old value of `*x` in `y`.

Consider the following semaphore operation:

```c
P(s)
{
    unsigned y;
    unsigned *x = &(s->value);

    do {
        fetch_and_set(x, y);
    } while (y);
}

V(s)
{
    s->value = 0;
}
```

Which one of the following is the correct assessment of this implementation?

1. It is incorrect because context switching must be disabled around the entire `P` operation.
2. `fetch-and-set` can be replaced by ordinary independent load and store operations without changing correctness.
3. The `V` operation is fundamentally incorrect because it does not use `fetch-and-set`.
4. The implementation behaves as a binary lock rather than as a general counting semaphore.

---

### Q8. [GATE CSE 2004 • Q48] — High

Two shared variables `X` and `Y` are protected by binary semaphores `Sx` and `Sy`, both initialized to `1`.

```text
P1:                                  P2:
L1;                                  L3;
L2;                                  L4;
X = X + 1;                           Y = Y + 1;
Y = Y - 1;                           X = Y - 1;
V(Sx);                               V(Sy);
V(Sy);                               V(Sx);
```

`L1`–`L4` are to be replaced by `P` operations.

Which assignment of semaphore operations makes the synchronization correct?

1. `L1=P(Sy), L2=P(Sx), L3=P(Sx), L4=P(Sy)`
2. `L1=P(Sx), L2=P(Sy), L3=P(Sy), L4=P(Sx)`
3. `L1=P(Sx), L2=P(Sx), L3=P(Sy), L4=P(Sy)`
4. `L1=P(Sx), L2=P(Sy), L3=P(Sx), L4=P(Sy)`

---

### Q9. [GATE CSE 2002 • Q18(b)] — High

Consider the following **non-atomic** implementation of `TEST-AND-SET`:

```c
int TEST_AND_SET(int *x)
{
    int y;

A1: y = *x;
A2: *x = 1;
A3: return y;
}
```

Complete the entire source exercise:

**(a)** Fill the blanks in the critical-section protocol below using `TEST_AND_SET`:

```c
int mutex = 0;

void enter_cs()
{
    while ( ____________________ )
        ;
}

void leave_cs()
{
    ____________________;
}
```

**(b)** For the intended atomic version of `TEST-AND-SET`, determine whether the resulting protocol is deadlock-free and starvation-free. Justify both conclusions.

**(c)** Because `A1`, `A2`, and `A3` above are separate instructions rather than one atomic instruction, give an interleaving of two processes that violates mutual exclusion.

---

### Q10. [GATE CSE 2001 • Q2.22] — High

Peterson's two-process mutual-exclusion algorithm contains the following code for process `Pi`, where `j` denotes the other process:

```c
flag[i] = true;
turn = j;

while ( P )
    ;

/* critical section */

flag[i] = false;
```

Which predicate should replace `P`?

1. `flag[j] && (turn == i)`
2. `flag[j] && (turn == j)`
3. `flag[i] && (turn == j)`
4. `flag[i] && (turn == i)`

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 5 • Exercise 5.1 • pp. 453–454] — CLASS DISCUSSION — High

Consider the four-processor SMP and the initial cache contents/states shown in **Figure 5.35** of the textbook.

Use the case-study assumptions:

- each processor has a private L1 cache;
- all processors share an L2 cache;
- L1 and L2 are direct-mapped, write-back, write-allocate;
- L2 is inclusive of the L1 caches;
- all cache levels use the same block size;
- coherence is maintained with a write-invalidate **MSI** snooping protocol.

Trace the following ordered sequence:

```text
<P0,r,M0 || P3,w,M1 || P1,r,M5 || P2,r,M5 ||
 P0,r,M0 || P3,r,M1 || P2,w,M5>
```

For **every memory operation**, describe the state transitions of every affected cache line, the bus/snoop request generated, any invalidation or write-back, the source from which the requested block is supplied, and the resulting cache state after the transaction completes.

> **Required textbook material:** Figure 5.35 — initial states and data of the L1/L2 cache lines.

---

### Q12. [BOOK • Chapter 5 • Exercise 5.2 • p. 454] — CLASS DISCUSSION — High

Use the transaction sequence from Exercise 5.1 and the snooping-protocol timing model given in **Figure 5.36**.

The timing model distinguishes processor/L1 tag and data access, L2 tag and data access, coherence-logic access, snoop lookup, write-back, and cache refill.

Calculate the **total number of processor delay cycles** required by the entire sequence from Exercise 5.1. Show the delay contribution of each individual read/write transaction before obtaining the total.

> **Required textbook material:** Figure 5.36 — snooping-protocol action latencies. Figure 5.37 may be used as the worked timing example supplied by the text.

---

### Q13. [BOOK • Chapter 5 • Exercise 5.3 • p. 455] — CLASS DISCUSSION — Medium

Use the array-update loop immediately preceding Exercise 5.3 in the textbook.

Assume one cache line holds exactly one element of array `A`, all of array `A` is already resident in the shared L2 cache, and the relevant elements are initially absent from processor `P0`'s L1 cache.

Complete both parts:

**(a)** Under the **MSI** protocol, determine the number of cycles required by the two memory accesses in each loop iteration.

**(b)** Recalculate the memory-access cost when the coherence protocol is changed from **MSI to MESI**.

Explain the state-transition difference responsible for the timing change.

> **Required textbook material:** the array-update loop printed immediately before Exercise 5.3 and the timing assumptions from the preceding case study.

---

### Q14. [BOOK • Chapter 5 • Exercise 5.4 • p. 455] — CLASS DISCUSSION — High

Assume that array `A` is entirely resident in L2 but its elements are not initially present in the processor's L1 cache.

Compare:

```c
for (...) A[i] = A[i-1] + 1;
```

and

```c
for (...) A[i] = A[i] + 1;
```

**(a)** Explain why the memory accesses of the two loops require different numbers of cycles when a **MSI** coherence protocol is used.

**(b)** Explain why the difference should become insignificant, or substantially smaller, when the protocol is changed to **MESI**.

Identify the relevant cache-line states and bus/coherence transactions.

---

### Q15. [BOOK • Chapter 5 • Exercise 5.5 • pp. 455–456] — CLASS DISCUSSION — High

Consider the memory-operation sequence:

```text
<P0,r,M0 || P3,w,M1 || P1,r,M5 || P2,r,M5 ||
 P2,r,M0 || P0,r,M1 || P2,w,M1>
```

Assume initially none of the referenced blocks is in an L1 cache, all referenced blocks are present in L2, and the case-study's write-invalidate snooping protocol and timing assumptions apply.

Complete the full exercise:

**(a)** Calculate the total delay cycles for the sequence under the baseline organization.

**(b)** Recalculate the sequence when the implementation supports **direct cache-to-cache transfer** where applicable rather than forcing every intervention/refill through the baseline path.

Compare the two totals and identify which transactions benefit from the optimization.

---

# Week 2 — Semaphores, Barriers, Directory Coherence, and Memory Consistency

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2022 • Q9] — Medium

Three processes repeatedly execute:

```text
T1:                         T2:                         T3:
wait(S3);                   wait(S1);                   wait(S2);
print("C");                 print("B");                 print("A");
signal(S2);                 signal(S3);                 signal(S1);
```

Which initial semaphore values cause the output to repeat as

```text
BCABCABCA...
```

1. `S1=1, S2=1, S3=1`
2. `S1=1, S2=1, S3=0`
3. `S1=1, S2=0, S3=0`
4. `S1=0, S2=1, S3=1`

---

### Q2. [GATE CSE 2021 • Set 1 • Q46] — High

A counting semaphore `S` is initialized to `5`, and a shared integer `counter` is initialized to `0`.

Five threads execute the following code once:

```c
wait(S);
wait(S);

counter++;       /* this increment is not atomic */

signal(S);
signal(S);
```

Which of the following are possible? **Select all that apply.**

1. All five threads complete and the final value of `counter` is `5`.
2. All five threads complete and the final value of `counter` is `1`.
3. All five threads complete and the final value of `counter` is `0`.
4. The execution deadlocks with one or more threads permanently blocked.

---

### Q3. [GATE CSE 2018 • Q40] — High

A bounded buffer contains `N` slots.

The semaphores are initialized as:

```text
empty = 0
full  = N
mutex = 1
```

The producer is written in the form:

```text
produce_item();
wait(P);
wait(mutex);
add_item_to_buffer();
signal(mutex);
signal(Q);
```

and the consumer is:

```text
wait(R);
wait(mutex);
remove_item_from_buffer();
signal(mutex);
signal(S);
consume_item();
```

Which choice correctly assigns `P`, `Q`, `R`, and `S` to the semaphores `empty` and `full`?

1. `P=full, Q=full, R=empty, S=empty`
2. `P=empty, Q=empty, R=full, S=full`
3. `P=full, Q=empty, R=empty, S=full`
4. `P=empty, Q=full, R=full, S=empty`

---

### Q4. [GATE CSE 2016 • Set 2 • Q48] — High

Two processes use a shared variable `turn`, initially `0`:

```text
P0:                                  P1:
while (turn == 1) ;                  while (turn == 0) ;
/* critical section */               /* critical section */
turn = 1;                            turn = 0;
```

Which statement is correct?

1. The algorithm correctly satisfies all critical-section requirements.
2. The algorithm violates mutual exclusion.
3. The algorithm can violate the progress requirement.
4. The algorithm violates bounded waiting but satisfies progress.

---

### Q5. [GATE CSE 2014 • Set 2 • Q31] — High

Two semaphores are initialized as:

```text
n = 0
s = 1
```

Producer:

```text
produce();
wait(s);
add_to_buffer();
signal(s);
signal(n);
```

Consumer:

```text
wait(s);
wait(n);
remove_from_buffer();
signal(s);
consume();
```

Which statement is correct?

1. A producer can add an item that can never be consumed.
2. A consumer can consume at most one item in the entire execution.
3. A deadlock can occur if a consumer acquires `s` while the buffer is empty.
4. `n` must be initialized to `1` for the scheme to be correct.

---

### Q6. [GATE CSE 2010 • Q45] — High

Three semaphores are initialized as:

```text
S0 = 1
S1 = 0
S2 = 0
```

The three processes execute forever:

```text
P0:                         P1:                         P2:
wait(S0);                   wait(S1);                   wait(S2);
print(0);                   signal(S0);                 signal(S0);
signal(S1);
signal(S2);
```

How many times can `P0` print `0`?

1. At least twice
2. Exactly twice
3. Exactly three times
4. Exactly once

---

### Q7. [GATE CSE 2008 • Q63] — High

A counting semaphore is implemented using two binary semaphores `xb` and `yb`:

```c
P(s)
{
    P_b(xb);
    s--;

    if (s < 0) {
        V_b(xb);
        P_b(yb);
    }
    else
        V_b(xb);
}

V(s)
{
    P_b(xb);
    s++;

    if (s <= 0)
        V_b(yb);

    V_b(xb);
}
```

What should the initial values of `(xb, yb)` be?

1. `(0,0)`
2. `(0,1)`
3. `(1,0)`
4. `(1,1)`

---

### Q8. [GATE CSE 2007 • Q58] — High

Two processes use Boolean variables `wants1` and `wants2`, initially `false`.

```text
P1:                                  P2:
wants1 = true;                       wants2 = true;
while (wants2 == true) ;             while (wants1 == true) ;
/* critical section */               /* critical section */
wants1 = false;                      wants2 = false;
```

Which statement is correct?

1. Mutual exclusion can be violated.
2. Bounded waiting is the only property violated.
3. The scheme enforces strict alternation.
4. The scheme preserves mutual exclusion but can deadlock.

---

### Q9. [GATE CSE 2006 • Q79] — High

Three processes repeatedly execute a barrier implemented with a binary semaphore `S` and shared counters `arrived` and `left`, initially `0`.

```text
1  P(S);
2  arrived++;
3  V(S);
4  while (arrived != 3) ;
5  P(S);
6  left++;
7  if (left == 3) {
8      arrived = 0;
9      left = 0;
10 }
11 V(S);
```

Which modification correctly repairs the barrier for repeated use?

1. Replace lines 6–10 by `arrived--`.
2. Before a process starts the next barrier round, require it to wait until the previous round has reset `arrived` to `0`.
3. Disable context switching for the entire barrier.
4. Make `left` a private variable of each process.

---

### Q10. [GATE CSE 2006 • Q78] — High

Use the barrier code from Question 9.

Which statement correctly characterizes the original program?

1. It is incorrect because `S` must be a counting semaphore rather than a binary semaphore.
2. It can fail or deadlock when processes immediately enter a subsequent barrier round before the previous round has been completely reset.
3. Lines 6–10 need not be protected by a critical section.
4. The same code is correct if only two processes participate, without any other change.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 5 • Exercise 5.14 • pp. 459–460] — CLASS DISCUSSION — High

Use the distributed shared-memory directory organization and the transaction sequence defined in **Exercise 5.14** of the textbook.

Complete **all subparts** of the source exercise. Your analysis must include the directory/coherence actions for the specified sequence of memory transactions, the directory information maintained at each node, the coherence messages exchanged, and the trade-off between directory granularity and communication traffic.

For the final comparison, complete the source table by determining both **directory size per node (bits)** and **number of coherence messages exchanged** as these parameters vary:

| Number of nodes in coherence group | Memory block size managed per directory entry |
|---:|---:|
| 1 | 128 B |
| 1 | 4 KiB |
| 4 | 128 B |
| 4 | 4 KiB |

> **Required textbook material:** the complete DSM organization, address assumptions, and transaction sequence printed with Exercise 5.14.

---

### Q12. [BOOK • Chapter 5 • Exercise 5.16 • pp. 460–461] — CLASS DISCUSSION — High

Use the two-process **Peterson mutual-exclusion algorithm** printed in Exercise 5.16. Initially neither process wishes to enter the critical section.

Complete every source subpart:

**(a)** Assuming both processors obey **Sequential Consistency (SC)**, show that if both processes try to enter the critical section, no more than one process can be in the critical section at a time.

**(b)** Give an SC ordering of the reads and writes that permits `P0` to enter before `P1`.

**(c)** Give an SC ordering that permits `P1` to enter before `P0`.

For each ordering, preserve the program order required by SC and identify the values seen by the relevant reads.

> **Required textbook material:** the Peterson code printed with Exercise 5.16.

---

### Q13. [BOOK • Chapter 5 • Exercise 5.17 • p. 461] — CLASS DISCUSSION — High

Using the Peterson algorithm from Exercise 5.16, show that the algorithm is **not guaranteed to operate correctly under a Release Consistency (RC) memory model**.

Construct a legal RC ordering in which both processes can enter the critical section at the same time.

Identify which memory operations are allowed to appear out of the SC order and why the RC model permits the failure.

---

### Q14. [BOOK • Chapter 5 • Exercise 5.18 • p. 461] — CLASS DISCUSSION — Medium

Assume shared variables `a` and `b` are initially `0`.

```text
P0:                                  P1:
a = 1;                               b = 1;
while (b == 0) ;                     while (a == 0) ;
print("p0 done\n");                  print("p1 done\n");
```

Complete all parts:

**(a)** List the possible output behavior when the machine implements **Sequential Consistency (SC)**.

**(b)** Explain how, under a **Release Consistency (RC)** model, it is possible for both processes to remain in their `while` loops indefinitely even though each has executed its own store.

**(c)** Show a compiler reordering that can create the same observable problem even on hardware that otherwise implements SC.

---

### Q15. [BOOK • Chapter 5 • Exercise 5.19 • p. 461] — CLASS DISCUSSION — Medium

Use the program from Exercise 5.18.

Suppose `P0` **prefetches `b` into its cache before `P1` writes `b = 1`**.

On a system that obeys Sequential Consistency and has a functioning cache-coherence mechanism, determine whether the prefetch can cause an unexpected result or change the legal program behavior.

Justify the answer in terms of coherence and the visibility of the later write.

---

# Week 3 — Race Interleavings, Producer–Consumer Synchronization, False Sharing, and Directory Scalability

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2019 • Q23] — Medium

A shared variable `D` is initially `100`.

Three processes execute one non-atomic read-modify-write statement each:

```text
P1: D = D + 20;
P2: D = D - 50;
P3: D = D + 10;
```

Each statement may be decomposed into a read, arithmetic operation, and write, and the operations of the three processes may interleave.

Let `X` be the minimum possible final value of `D` and `Y` be the maximum possible final value of `D`.

Determine `Y - X`.

---

### Q2. [GATE CSE 2015 • Set 3 • Q10] — High

Two processes `X` and `Y` use Boolean variables `varP` and `varQ`, both initially `false`.

```text
Process X:                           Process Y:
varP = true;                         varQ = true;

while (varQ == true) {               while (varP == true) {
    /* critical section */               /* critical section */
    varP = false;                        varQ = false;
}                                      }
```

Which statement is correct?

1. The program is deadlock-free but does not guarantee mutual exclusion.
2. The program guarantees mutual exclusion but deadlock is possible.
3. The program guarantees both mutual exclusion and deadlock freedom.
4. The program guarantees neither property.

---

### Q3. [GATE CSE 2013 • Q39] — High

Process `X` computes array `a`, and process `Y` must use the completed values of `a` to compute array `b`.

```text
Process X:                       Process Y:

for (...)                        EntryY(R,S);
    a[i] = f(i);                 for (...)
ExitX(R,S);                          b[i] = g(a[i]);
```

`R` and `S` are binary semaphores initialized to `0`.

Which pair of routines correctly enforces the required ordering?

1. `ExitX: P(R); V(S)` and `EntryY: P(S); V(R)`
2. `ExitX: V(R); V(S)` and `EntryY: P(R); P(S)`
3. `ExitX: P(S); V(R)` and `EntryY: V(S); P(R)`
4. `ExitX: V(R); P(S)` and `EntryY: V(S); P(R)`

---

### Q4. [GATE CSE 2003 • Q80] — High

Two processes repeatedly execute:

```text
Process P:                       Process Q:
W;                               Y;
print(0);                        print(1);
print(0);                        print(1);
X;                               Z;
```

Binary semaphores `S` and `T` are used to force the output to be

```text
001100110011...
```

Which replacement is correct?

1. `W=P(S), X=V(S), Y=P(T), Z=V(T)`, with `S=T=1`
2. `W=P(S), X=V(T), Y=P(T), Z=V(S)`, with `S=1, T=0`
3. `W=P(S), X=V(T), Y=P(T), Z=V(S)`, with `S=T=1`
4. `W=P(S), X=V(S), Y=P(T), Z=V(T)`, with `S=1, T=0`

---

### Q5. [GATE CSE 2002 • Q20] — High

A bounded buffer of size `BUFFSIZE = 100` is shared by producer and consumer processes.

```c
item buf[BUFFSIZE];
int first = 0, last = 0;

semaphore b_full  = 0;
semaphore b_empty = BUFFSIZE;
```

Producer:

```c
produce_item(&item);

p1: ______________________;

buf[first] = item;
first = (first + 1) % BUFFSIZE;

p2: ______________________;
```

Consumer:

```c
c1: ______________________;

item = buf[last];
last = (last + 1) % BUFFSIZE;

c2: ______________________;

consume_item(item);
```

Complete the whole source exercise:

**(a)** Fill `p1`, `p2`, `c1`, and `c2` with semaphore operations so that one producer and one consumer synchronize correctly.

**(b)** Now permit **multiple producers and multiple consumers**. Introduce one additional semaphore and specify one synchronization line at each of the four indicated locations necessary to prevent races on the shared buffer indices.

---

### Q6. [GATE CSE 2000 • Q20] — High

A reader-writer synchronization program uses the shared variables:

```text
mutex = 1
R = 0
W = 0
```

with `mutex` a semaphore.

Reader:

```text
L1:
wait(mutex);

if (W == 0) {
    R++;
    ______________________;        /* blank 1 */
}
else {
    ______________________;        /* blank 2 */
    goto L1;
}

/* read */

wait(mutex);
R--;
signal(mutex);
```

Writer:

```text
L2:
wait(mutex);

if ( ______________________ ) {    /* blank 3 */
    signal(mutex);
    goto L2;
}

W = 1;
signal(mutex);

/* write */

wait(mutex);
W = 0;
signal(mutex);
```

Complete the source exercise:

**(a)** Fill the three blanks to implement the intended reader-writer synchronization.

**(b)** Determine whether writers can starve. Justify the conclusion from the admission rule used by readers.

---

### Q7. [GATE CSE 2000 • Q1.21] — High

Five processes `P0` through `P4` share mutex semaphores `m0` through `m4`.

Each process repeatedly executes the pattern

```text
P(mi);
P(m(i+1) mod 4);

/* critical work */

V(m(i+1) mod 4);
V(mi);
```

> **Source-transcription note:** the `mod 4` indexing above is intentional; it matches the indexed GATE source.

Which of the following phenomena can the program exhibit?

1. Thrashing
2. Deadlock
3. Starvation but not deadlock
4. None of the above

---

### Q8. [GATE CSE 1999 • Q20(b)] — High

A one-slot producer-consumer buffer is implemented with a shared variable `count`, initially `0`.

The operations shown below are ordinary program operations; the check and the subsequent sleep are **not atomic**.

```text
Producer:                           Consumer:
produce_item();                     if (count == 0)
if (count == 1)                         sleep();
    sleep();                        remove_item();
put_item();                         count = 0;
count = 1;                          wakeup(Producer);
wakeup(Consumer);                   consume_item();
```

Construct an execution sequence in which the producer and consumer both end up **sleeping simultaneously**, even though such a state should be impossible in a correct producer-consumer implementation.

Identify the lost-wakeup race that causes the failure.

---

### Q9. [GATE CSE 1997 • Q6.8] — High

Processes `P1` through `P9` repeatedly execute:

```text
P(mutex);
/* critical section */
V(mutex);
```

Process `P10`, however, repeatedly executes:

```text
V(mutex);
/* critical section */
V(mutex);
```

Assume `mutex` is initially `1`.

What is the largest possible number of processes that may be simultaneously inside the critical section?

1. 1
2. 2
3. 3
4. None of the above

---

### Q10. [GATE CSE 1996 • Q21] — High

The concurrent-programming primitives are:

```text
Fork L    /* create a new process beginning at label L */
Join V    /* decrement V; continue only when its required join condition is met */
```

Consider the following program skeleton:

```text
N = 2;
M = 2;

Fork L3;
Fork L4;

S1;

L1: Join N;
    S3;

L2: Join M;
    S5;

L3: S2;
    goto L1;

L4: S4;
    goto L2;

Next:
```

Construct the **precedence graph** among statements `S1` through `S5`.

Represent the result either graphically or as a complete set of directed precedence edges.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 5 • Exercise 5.27 • p. 464] — CLASS DISCUSSION — Medium

**False sharing** can cause a cache-coherent multiprocessor to exchange coherence messages even when different processors update logically independent variables.

Complete the source exercise:

**(a)** Explain how application code or data layout can be changed to avoid false sharing.

**(b)** Distinguish remedies that can reasonably be performed automatically by a compiler from remedies that require information or directives from the programmer.

**(c)** Discuss the cost of padding/alignment in terms of memory footprint and locality.

---

### Q12. [BOOK • Chapter 5 • Exercise 5.28 • p. 464] — CLASS DISCUSSION — High

A parallel word-count reduction uses four processors:

```c
for (int p = 0; p <= 3; p++) {      /* each p assigned to a separate processor */
    sum[p] = 0;

    for (int i = 0; i < n/4; i++)
        sum[p] = sum[p] + word_count[p + 4*i];
}

/* executed by one processor */
total_sum = sum[0] + sum[1] + sum[2] + sum[3];
```

Assume the L1 cache line size is **32 bytes**.

Complete all parts:

**(a)** Identify the cache lines that experience sharing and classify each case as **true sharing** or **false sharing**.

**(b)** Rewrite or reorganize the computation so that accesses to `word_count` produce fewer cache misses.

**(c)** Modify the layout or code manually to eliminate/reduce false sharing involving the partial sums.

Explain why each modification changes coherence traffic.

---

### Q13. [BOOK • Chapter 5 • Exercise 5.29 • p. 464] — CLASS DISCUSSION — High

In a directory-based coherence system, the directory indicates that processor `P1` has a particular block in an **exclusive/modified ownership state**.

The directory then receives another request for that same block that appears to originate from `P1`.

Analyze this apparently contradictory situation:

**(a)** Give plausible protocol/race scenarios that can make the request legitimate.

**(b)** State how the directory controller should respond without violating coherence.

**(c)** Explain why treating the directory entry as an instantaneous, perfectly synchronized description of all private-cache state can be unsafe in a real implementation with in-flight messages.

---

### Q14. [BOOK • Chapter 5 • Exercise 5.30 • pp. 464–465] — CLASS DISCUSSION — High

A directory protocol normally learns about a private cache replacement only when a later coherence transaction exposes the fact that the directory's sharer information has become stale.

Modify the directory protocol so that **private-cache replacement information is communicated to the directory proactively**.

Your design must specify what replacement/eviction message is sent, how the directory updates owner/sharer information, how races with simultaneous reads/writes are handled, what acknowledgement (if any) is required, and how this change can reduce unnecessary future invalidation or forwarding traffic.

Discuss the extra message and implementation cost.

---

### Q15. [BOOK • Chapter 5 • Exercise 5.31 • p. 465] — CLASS DISCUSSION — High

A straightforward directory maintains a full sharer bit vector for every memory block. If both the number of processors and the amount of memory increase, this metadata can grow poorly.

Complete the source exercise on **scalable directory representations**.

**(a)** Quantify how the storage of a straightforward full-map directory scales as processor count and memory capacity increase.

**(b)** Analyze directory designs that reduce metadata by grouping processors and/or allowing one directory entry to cover more memory.

**(c)** Consider a **64-processor DSM** divided into **eight groups of eight processors**. Replace a dense full sharer vector by a **9-bit directory field**:

- if a block is cached by only one remote node, the field can identify that node;
- if it is cached by more than one remote node, interpret the field as a coarse vector in which each bit denotes a group of eight processors and indicates that at least one processor in the group may hold the block.

Illustrate how the encoding works and analyze the resulting storage/message trade-off.

**(d)** Take the optimization to the extreme: eliminate persistent per-block directory state while retaining enough directory/routing logic to forward requests. Assuming deterministic in-order routes between nodes, determine whether such directory logic still provides value compared with having no directory mechanism at all.

---

# Week 4 — Parallel Speedup, Classical Synchronization, LL/SC, and Multicore Scaling

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2026 • Set 1 • Master Q29 / CS Q19] — Medium

With respect to deadlocks in an operating system, which of the following statements are **FALSE**? **Select all that apply.**

1. Banker's algorithm is used to prevent deadlocks.
2. Deadlock formation can be prevented by disallowing the hold-and-wait condition.
3. In a resource-allocation graph, an assignment edge is directed from a process to a resource.
4. A safe state guarantees that all processes can finish without formation of a deadlock.

### Q2. [GATE CSE 2016 • Set 2 • Q49] — Medium

`S` is a nonnegative counting semaphore. A `P(S)` operation decrements `S` when possible and otherwise blocks the caller; `V(S)` increments `S` and may wake a blocked process.

Across a concurrent execution, exactly:

- 20 `P(S)` operations, and
- 12 `V(S)` operations

are issued in some order.

What is the **largest possible initial value of `S`** for which at least one `P(S)` operation can remain blocked after all 32 operations have been issued?

---

### Q3. [GATE CSE 1999 • Q20(a)] — High

A machine supplies the atomic instruction

```text
TSET register, flag
```

which atomically:

1. copies the current value of the shared variable `flag` into `register`, and
2. sets `flag` to `1`.

Using `TSET`, write pseudocode for:

**(a)** entry into a critical region, and  
**(b)** exit from the critical region,

so that multiple processes can use the same shared `flag` for mutual exclusion.

---

### Q4. [GATE CSE 1996 • Q2.19] — High

Five philosophers sit around a table. Each philosopher needs both adjacent forks to eat.

Which strategy avoids the circular-wait deadlock of the standard Dining Philosophers problem?

1. Every philosopher always picks up the left fork and then the right fork.
2. Every philosopher always picks up the right fork and then the left fork.
3. One designated philosopher picks up the forks in the opposite order from the other philosophers.
4. None of the above.

---

### Q5. [GATE CSE 1995 • Q19] — High

Semaphores `a` through `k` are used to impose ordering on statements `S1` through `S9`.

```text
begin
  cobegin

    begin
      S1;
      V(a);
      V(b);
    end;

    begin
      P(a);
      S2;
      V(c);
      V(d);
    end;

    begin
      P(c);
      S4;
      V(e);
    end;

    begin
      P(d);
      S5;
      V(f);
    end;

    begin
      P(e);
      P(f);
      S7;
      V(k);
    end;

    begin
      P(b);
      S3;
      V(g);
      V(h);
    end;

    begin
      P(g);
      S6;
      V(i);
    end;

    begin
      P(h);
      P(i);
      S8;
      V(j);
    end;

    begin
      P(j);
      P(k);
      S9;
    end;

  coend
end
```

Draw the **precedence graph** for statements `S1` through `S9`.

A textual answer may list all directed precedence edges instead of drawing the graph.

---

### Q6. [GATE CSE 1994 • Q27] — High

Consider the following sequential program:

```text
S1: read n;
S2: i := 1;

S3: if i > n goto NEXT;
S4: a(i) := i + 1;
S5: i := i + 1;
    goto S3;

NEXT:
S6: write a(i);
```

Complete the source exercise:

**(a)** Construct the precedence/dependence graph implied by the program.

**(b)** Determine whether the program's available concurrency can be expressed using only nested `parbegin-parend` (`cobegin-coend`) constructs without additional synchronization. Justify the answer from the graph structure.

---

### Q7. [GATE CSE 1993 • Q22] — High

A precedence graph contains statements `S1` through `S6` with these directed edges:

```text
S1 -> S2
S1 -> S3
S2 -> S4
S2 -> S5
S3 -> S5
S6 -> S4
S6 -> S5
```

Write an equivalent concurrent program using:

- `parbegin-parend` / `cobegin-coend`, and
- semaphores where necessary,

so that **all and only** the required precedence constraints are enforced.

---

### Q8. [GATE CSE 1992 • Q12(a)] — High

Consider the concurrent program:

```text
S1;

parbegin

    begin
        S2;
        S4;
    end;

    begin
        S3;

        parbegin
            S5;

            begin
                S6;
                S8;
            end;
        parend
    end;

    S7;

parend;

S9;
```

Draw the complete **precedence graph** for statements `S1` through `S9`.

A textual answer may provide all directed precedence edges instead of a drawing.

---

### Q9. [GATE CSE 1988 • Q10(ii)(b)] — High

Shared variables are:

```text
flag[0] = false
flag[1] = false
turn ∈ {0,1}
```

Process `Pi`, where `j` is the other process, executes:

```text
repeat

    flag[i] := true;

    while turn != i do
    begin
        while flag[j] do
            skip;

        turn := i;
    end;

    /* critical section */

    flag[i] := false;

until false;
```

Determine whether the algorithm is a correct solution to the two-process critical-section problem.

If it is incorrect, demonstrate an execution that violates one of the required critical-section properties.

---

### Q10. [GATE CSE 1987 • Q8(a)] — High

A readers-writers solution maintains these shared counters:

```text
aw  /* active/waiting writers as used by the source algorithm */
ar  /* active/arrived readers */
rw  /* writers released to write */
rr  /* readers released to read */
```

and semaphores:

```text
mutex   = 1
reading = 0
writing = 0
```

The algorithm uses:

```text
grantread:
    if aw == 0 then
        while rr < ar do
        begin
            rr := rr + 1;
            V(reading);
        end

grantwrite:
    if rr == 0 then
        while rw < aw do
        begin
            rw := rw + 1;
            V(writing);
        end
```

A reader follows:

```text
P(mutex);
ar := ar + 1;
grantread;
V(mutex);

P(reading);
read;

P(mutex);
rr := rr - 1;
ar := ar - 1;
grantwrite;
V(mutex);

other_work;
```

A writer follows:

```text
P(mutex);
aw := aw + 1;
grantwrite;
V(mutex);

P(writing);
write;

P(mutex);
rw := rw - 1;
aw := aw - 1;
grantread;
V(mutex);

other_work;
```

Complete the source analysis:

**(a)** Give the values/state of the shared variables and relevant semaphores in a state where **12 readers are reading and 31 writers are waiting**.

**(b)** Determine whether a continuing stream of readers can starve waiting writers, and whether a continuing stream of writers can starve waiting readers.

**(c)** Identify the correctness defect in the scheme and explain concisely why it invalidates the intended readers-writers synchronization.

> **Transcription note:** obvious typographical/OCR inconsistencies in the old source scan have been normalized in the variable declarations and writer decrement so that the algorithm is readable; the synchronization structure and requested analysis are unchanged.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 5 • Exercise 5.32 • p. 465] — CLASS DISCUSSION — Medium

Assume the architecture provides **load-linked/store-conditional (LL/SC)**.

Construct an implementation of the atomic **compare-and-swap (CAS)** operation using LL/SC.

The implementation must compare the loaded value with an expected value, update memory only if the comparison succeeds and no conflicting write invalidates the reservation, retry correctly when `store-conditional` fails, and return enough information for the caller to determine whether the CAS succeeded.

Explain the role of the linked reservation in preserving atomicity.

---

### Q12. [BOOK • Chapter 5 • Exercise 5.33 • p. 465] — CLASS DISCUSSION — High

Padding synchronization variables can reduce false sharing but increases memory consumption.

Construct a concrete shared-memory program in which **placing independent synchronization variables on separate cache lines produces a large performance improvement** under a snooping write-invalidate coherence protocol.

For your example:

- specify the cache-line size;
- show the unpadded and padded data layout;
- identify which processors repeatedly access each synchronization variable;
- explain the invalidation/ping-pong traffic in the unpadded case;
- explain why padding removes most of that traffic;
- discuss the memory-footprint trade-off.

---

### Q13. [BOOK • Chapter 5 • Exercise 5.34 • pp. 465–466] — CLASS DISCUSSION — High

Design a hardware **LL/SC reservation monitor** for a four-core SMP.

Requirements:

- a load-linked/store-conditional pair may target any memory location;
- normal loads/stores may be interleaved with LL/SC accesses to the same region;
- requests may have data sizes of **4, 8, 16, or 32 bytes**;
- a conflicting access by another core must cause a later store-conditional to fail;
- a failing store-conditional must not modify memory;
- the design may observe interconnect/coherence signals;
- monitor complexity must be **independent of total memory size**.

Specify the state stored by the monitor, how overlapping address ranges are detected, when reservations are created/cleared, and how a store-conditional is accepted or rejected.

---

### Q14. [BOOK • Chapter 5 • Exercise 5.35 • p. 466] — CLASS DISCUSSION — High

Consider a two-level cache hierarchy in which:

- L1 is closer to the processor;
- L2 has at least as much associativity as L1;
- both levels use the same cache-block size;
- both levels use true LRU replacement.

Prove that **cache inclusion is maintained without any extra inclusion-enforcement action** under these conditions.

Your proof must consider the possible access/replacement sequence that could otherwise cause a line still present in L1 to be evicted from L2.

---

### Q15. [BOOK • Chapter 5 • Exercise 5.36 • pp. 466–467] — CLASS DISCUSSION — High

Multiprocessor programs normally aim for increasing performance as processor count rises.

Design a deliberately biased benchmark whose performance instead becomes **worse as processors are added**:

```text
1 processor  -> fastest
2 processors -> slower
4 processors -> slower than 2
...
```

Analyze separately what workload/system characteristics can create approximately **inverse linear speedup** on:

**(a)** a shared-memory multiprocessor, and  
**(b)** a cluster/distributed-memory machine.

Connect the benchmark behavior to synchronization, coherence, contention, communication, memory-system interference, and useful work per processor rather than merely inserting artificial delays.

---

# Organizer Source Ledger

## Textbook source

John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 5, **Thread-Level Parallelism**, Case Studies and Exercises by Amr Zaky, pp. 453–467.

### Selected textbook exercises

| Week-Q | Exercise | Page(s) | Principal topic |
|---|---:|---:|---|
| W1-Q11 | 5.1 | 453–454 | MSI snooping state/action trace |
| W1-Q12 | 5.2 | 454 | Coherence-transaction delay cycles |
| W1-Q13 | 5.3 | 455 | MSI versus MESI transaction cost |
| W1-Q14 | 5.4 | 455 | MSI/MESI behavior for array-update traffic |
| W1-Q15 | 5.5 | 455–456 | Cache-to-cache transfer optimization |
| W2-Q11 | 5.14 | 459–460 | Directory storage and message trade-off |
| W2-Q12 | 5.16 | 460–461 | Peterson algorithm under SC |
| W2-Q13 | 5.17 | 461 | Peterson algorithm under RC |
| W2-Q14 | 5.18 | 461 | SC/RC ordering and compiler reordering |
| W2-Q15 | 5.19 | 461 | Prefetch/coherence interaction |
| W3-Q11 | 5.27 | 464 | False-sharing avoidance |
| W3-Q12 | 5.28 | 464 | True/false sharing in parallel reduction |
| W3-Q13 | 5.29 | 464 | Directory race/controller response |
| W3-Q14 | 5.30 | 464–465 | Replacement information in directories |
| W3-Q15 | 5.31 | 465 | Scalable directory representations |
| W4-Q11 | 5.32 | 465 | Compare-and-swap using LL/SC |
| W4-Q12 | 5.33 | 465 | Synchronization-variable padding |
| W4-Q13 | 5.34 | 465–466 | LL/SC reservation monitor |
| W4-Q14 | 5.35 | 466 | Inclusion proof for LRU hierarchy |
| W4-Q15 | 5.36 | 466–467 | Inverse scaling benchmark |

## GATE CSE source ledger

The links below are organizer references to GateOverflow's indexed copies/discussions. Recent official GATE papers should remain the authoritative final-paper source during the later master-source audit.

### Week 1

| Question | Source | Indexed source |
|---|---|---|
| W1-Q1 | GATE CSE 2024 Set 2 Q36 | https://gateoverflow.in/422861/gate-cse-2024-set-2-question-36 |
| W1-Q2 | GATE CSE 2015 Set 1 Q9 | https://gateoverflow.in/8121/gate-cse-2015-set-1-question-9 |
| W1-Q3 | GATE CSE 2013 Q34 | https://gateoverflow.in/1545/gate-cse-2013-question-34 |
| W1-Q4 | GATE CSE 2012 Q32 | https://gateoverflow.in/1750/gate-cse-2012-question-32 |
| W1-Q5 | GATE CSE 2010 Q23 | https://gateoverflow.in/2202/gate-cse-2010-question-23 |
| W1-Q6 | GATE CSE 2009 Q33 | https://gateoverflow.in/1319/gate-cse-2009-question-33 |
| W1-Q7 | GATE CSE 2006 Q61 | https://gateoverflow.in/1839/gate-cse-2006-question-61 |
| W1-Q8 | GATE CSE 2004 Q48 | https://gateoverflow.in/1044/gate-cse-2004-question-48 |
| W1-Q9 | GATE CSE 2002 Q18(b) | https://gateoverflow.in/205818/gate-cse-2002-question-18-b |
| W1-Q10 | GATE CSE 2001 Q2.22 | https://gateoverflow.in/740/gate-cse-2001-question-2-22 |

### Week 2

| Question | Source | Indexed source |
|---|---|---|
| W2-Q1 | GATE CSE 2022 Q9 | https://gateoverflow.in/371927/gate-cse-2022-question-9 |
| W2-Q2 | GATE CSE 2021 Set 1 Q46 | https://gateoverflow.in/357405/gate-cse-2021-set-1-question-46 |
| W2-Q3 | GATE CSE 2018 Q40 | https://gateoverflow.in/204114/gate-cse-2018-question-40 |
| W2-Q4 | GATE CSE 2016 Set 2 Q48 | https://gateoverflow.in/39600/gate-cse-2016-set-2-question-48 |
| W2-Q5 | GATE CSE 2014 Set 2 Q31 | https://gateoverflow.in/1990/gate-cse-2014-set-2-question-31 |
| W2-Q6 | GATE CSE 2010 Q45 | https://gateoverflow.in/2347/gate-cse-2010-question-45 |
| W2-Q7 | GATE CSE 2008 Q63 | https://gateoverflow.in/486/gate-cse-2008-question-63 |
| W2-Q8 | GATE CSE 2007 Q58 | https://gateoverflow.in/1256/gate-cse-2007-question-58 |
| W2-Q9 | GATE CSE 2006 Q79 | https://gateoverflow.in/43564/gate-cse-2006-question-79 |
| W2-Q10 | GATE CSE 2006 Q78 | https://gateoverflow.in/1853/gate-cse-2006-question-78 |

### Week 3

| Question | Source | Indexed source |
|---|---|---|
| W3-Q1 | GATE CSE 2019 Q23 | https://gateoverflow.in/302825/gate-cse-2019-question-23 |
| W3-Q2 | GATE CSE 2015 Set 3 Q10 | https://gateoverflow.in/8405/gate-cse-2015-set-3-question-10 |
| W3-Q3 | GATE CSE 2013 Q39 | https://gateoverflow.in/1550/gate-cse-2013-question-39 |
| W3-Q4 | GATE CSE 2003 Q80 | https://gateoverflow.in/964/gate-cse-2003-question-80 |
| W3-Q5 | GATE CSE 2002 Q20 | https://gateoverflow.in/873/gate-cse-2002-question-20 |
| W3-Q6 | GATE CSE 2000 Q20 | https://gateoverflow.in/691/gate-cse-2000-question-20 |
| W3-Q7 | GATE CSE 2000 Q1.21 | https://gateoverflow.in/645/gate-cse-2000-question-1-21 |
| W3-Q8 | GATE CSE 1999 Q20(b) | https://gateoverflow.in/205817/gate-cse-1999-question-20-b |
| W3-Q9 | GATE CSE 1997 Q6.8 | https://gateoverflow.in/2264/gate-cse-1997-question-6-8 |
| W3-Q10 | GATE CSE 1996 Q21 | https://gateoverflow.in/2773/gate-cse-1996-question-21 |

### Week 4

| Question | Source | Indexed source |
|---|---|---|
| W4-Q1 | GATE CSE 2026 Set 1 Master Q29 / CS Q19 | https://gateoverflow.in/523061/gate-cse-2026-set-1-question-19 |
| W4-Q2 | GATE CSE 2016 Set 2 Q49 | https://gateoverflow.in/39576/gate-cse-2016-set-2-question-49 |
| W4-Q3 | GATE CSE 1999 Q20(a) | https://gateoverflow.in/1519/gate-cse-1999-question-20-a |
| W4-Q4 | GATE CSE 1996 Q2.19 | https://gateoverflow.in/2748/gate-cse-1996-question-2-19 |
| W4-Q5 | GATE CSE 1995 Q19 | https://gateoverflow.in/2656/gate-cse-1995-question-19 |
| W4-Q6 | GATE CSE 1994 Q27 | https://gateoverflow.in/2523/gate-cse-1994-question-27 |
| W4-Q7 | GATE CSE 1993 Q22 | https://gateoverflow.in/2319/gate-cse-1993-question-22 |
| W4-Q8 | GATE CSE 1992 Q12(a) | Direct indexed URL to be re-locked during final source audit |
| W4-Q9 | GATE CSE 1988 Q10(ii)(b) | https://gateoverflow.in/94393/gate-cse-1988-question-10iib |
| W4-Q10 | GATE CSE 1987 Q8(a) | https://gateoverflow.in/82433/gate-cse-1987-question-8a |

## Verification notes

- **60 questions total:** 40 GATE CSE PYQs + 20 textbook exercises.
- **Every weekly Q1–Q10 is a GATE CSE PYQ.**
- **Every weekly Q11–Q15 is marked CLASS DISCUSSION.**
- The previously considered `GATE CSE 1997 Q73` was **not used** because it is not a valid Chapter 5 synchronization/TLP question; it has been replaced with `GATE CSE 1996 Q2.19`.
- `GATE CSE 1992 Q12(a)` is retained because its precedence-graph task fits the chapter; its direct indexed URL should be re-locked during the final all-chapter source audit.
- No solutions, hints, or answer key are included.
