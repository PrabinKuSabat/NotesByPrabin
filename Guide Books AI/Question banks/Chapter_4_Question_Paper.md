# MTCS 102 — Chapter 4 Question Paper

## Data-Level Parallelism in Vector, SIMD, and GPU Architectures

**Primary text:** John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 4  
**Format:** 4 weeks × 15 questions = 60 questions  
**Weekly split:** Questions 1–10 = verified national-level previous-year questions; Questions 11–15 = class-discussion questions drawn primarily from the textbook  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included  

> **Question-counting rule:** One source question/exercise is treated as exactly one question even when it contains several subparts. No textbook exercise has been split.
>
> **Chapter-4 source rule:** GATE CSE contains too few genuine vector/SIMD/GPU questions to supply forty chapter-specific PYQs. For this chapter only, the PYQ pool is therefore broadened to verified national-level examinations such as UGC-NET CSE, ISRO CSE, UPSC/NCRB and closely related national examinations, while retaining GATE questions where the overlap is direct.
>
> **Wording note:** PYQs are faithfully reformatted/paraphrased from the cited source pages so that the paper is self-contained; numerical data, code, alternatives, and the assessed concept are preserved. No solution is included.
>
> **Figures:** No external image asset is required. The one precedence-graph PYQ is represented textually by its dependency levels.
>
> **Textbook-selection note:** Exercises 4.1 and 4.4 were not selected because they are below the target difficulty. Exercise 4.8 is also omitted because the available text extraction does not expose its full standalone task boundary reliably; no exercise has been reconstructed or invented.

---

# Week 1 — Vector/SIMD Foundations and Quantitative Parallel Performance

## National-Level Previous-Year Questions — Questions 1–10

### Q1. [UGC-NET CSE August 2024 • Part 2 • Q40] — Medium

A vector processor contains **16 lanes**. A vector operation must be applied to **1024 elements**, and one lane requires **5 clock cycles** for the operation assigned to an element group.

How many clock cycles are required to complete the complete vector operation?

1. 64  
2. 80  
3. 100  
4. 128

---

### Q2. [UGC-NET CSE August 2024 • Part 2 • Q38] — High

A computation has a serial portion requiring **200 cycles** and a parallelizable portion requiring **800 cycles** on one processor.

Assuming ideal distribution of the parallel portion and no communication/synchronization overhead, determine the total execution time when the computation is run on **16 processors**.

1. 250 cycles  
2. 300 cycles  
3. 400 cycles  
4. 450 cycles

---

### Q3. [ISRO CSE 2018 • Q71] — Medium

A parallel program requires **100 seconds** on one processor. Exactly **40%** of its work is inherently sequential and cannot benefit from additional processors.

What are the theoretically best execution times on **2 processors** and **4 processors**, respectively?

1. 20 s and 10 s  
2. 30 s and 15 s  
3. 50 s and 25 s  
4. 70 s and 55 s

---

### Q4. [UGC-NET CSE June 2012 • Part 3 • Q5] — Medium

An application is run on a **64-processor** machine. **70%** of the original execution can be parallelized perfectly; the remainder is serial.

Using Amdahl's law, what performance improvement should be expected?

1. 4.22  
2. 3.22  
3. 3.32  
4. 3.52

---

### Q5. [UGC-NET CSE November 2017 • Part 2 • Q50] — Medium

A program is **5% sequential** and **95% ideally parallelizable**. According to Amdahl's law, what is the limiting speedup as the number of processors tends to infinity?

1. Infinite  
2. 5  
3. 20  
4. 50

---

### Q6. [UGC-NET CSE December 2007 • Part 2 • Q46] — High

A parallel algorithm has computation time \(t\) and performs \(m\) computational operations. Assuming ideal scheduling except for the critical path represented by \(t\), which expression gives the execution-time bound when **\(p\) processors** are available?

1. \(t/p\)  
2. \(mt/p\)  
3. \(t + (m-t)/p\)  
4. \((m-t)/p\)

---

### Q7. [ISRO CSE 2011 • Q10] — High

Eight equal-duration tasks have the following precedence levels:

- Level 1: \(T_1\)
- Level 2: \(T_2\)
- Level 3: \(T_3,T_4,T_5\) can execute concurrently
- Level 4: \(T_6,T_7,T_8\) can execute concurrently

The parallel system has **5 processors**.

What is the processor efficiency for executing the complete task graph?

1. 25%  
2. 40%  
3. 50%  
4. 80%

---

### Q8. [ISRO CSE 2008 • Q42] — Medium

Which architecture is **not suitable** for directly realizing a SIMD organization?

1. Vector processor  
2. Array processor  
3. Conventional von Neumann processor  
4. All of the above

---

### Q9. [UGC-NET CSE October 2020 • Part 2 • Q75] — Medium

Arrange the following machine organizations in **descending order of architectural complexity**:

- SISD
- MIMD
- SIMD

Choose the correct ordering.

1. SISD, MIMD, SIMD  
2. SIMD, MIMD, SISD  
3. MIMD, SIMD, SISD  
4. SIMD, SISD, MIMD

---

### Q10. [UGC-NET CSE June 2008 • Part 2 • Q46] — Medium

Let \(f\) be the fraction of a program that must execute sequentially and let \(p\) processors execute the remaining fraction in parallel.

Which expression gives the maximum speedup \(S\) according to Amdahl's law?

1. \(S \le f+(1-f)/p\)  
2. \(S \le f/p+(1-f)\)  
3. \(S \le 1/[f+(1-f)/p]\)  
4. \(S \le 1/[1-f+f/p]\)

---

## CLASS DISCUSSION — Questions 11–15

### Q11. [BOOK • Chapter 4 • Exercise 4.2 • p. 369] — CLASS DISCUSSION — High

Consider the chapter's float16 batch-normalization + ReLU loop:

```c
for (uint32_t i = 0; i < n_channels; i++) {
    temp1 = max(in[i], 0);
    temp2 = (temp1 - mean[i]) / stddev[i];
    out[i] = temp2 * scale[i] + offset[i];
}
```

Implement the **loop body in PTX** under these placement assumptions:

- `in` and `out` are in GPU global memory;
- `mean`, `stddev`, `scale`, and `offset` are in shared memory.

Use an instruction sequence that correctly expresses the arithmetic, addressing, and required loads/stores for one CUDA thread.

---

### Q12. [BOOK • Chapter 4 • Exercise 4.3 • p. 369] — CLASS DISCUSSION — Medium

For the same GPU kernel, assume:

- a load has latency **10 cycles**;
- every other floating-point instruction has latency **4 cycles**;
- the GPU core has enough registers and memory bandwidth that those resources do not limit occupancy;
- warp size = **32 threads**.

Determine how many warps must be resident/executing so that instruction latency can be hidden and the core can remain fully utilized. Also give the corresponding number of resident threads.

---

### Q13. [BOOK • Chapter 4 • Exercise 4.5 • pp. 369–370] — CLASS DISCUSSION — High

Translate the batch-normalization + ReLU loop into **RV64V vector assembly**. Assume support for 16-bit floating point and these initial registers:

- `x1`: base of `in`
- `x2`: base of `mean`
- `x3`: base of `stddev`
- `x4`: base of `scale`
- `x5`: base of `offset`
- `x6`: base of `out`
- `x7`: `n_channels`

Your implementation must handle vector length correctly and perform the complete computation for all channels.

---

### Q14. [BOOK • Chapter 4 • Exercise 4.6 • p. 370] — CLASS DISCUSSION — High

Use the RV64V sequence developed for Exercise 4.5.

Assume the vector machine contains:

- **2 load/store units**, and
- **5 vector floating-point functional units**.

Rearrange the vector instructions into legal **convoys** so that independent operations use the available functional units efficiently. Show the convoy grouping and justify every dependence/resource restriction that prevents instructions from sharing a convoy.

---

### Q15. [UGC-NET CSE December 2019 • Part 2 • Q75] — CLASS DISCUSSION — High

A multithreaded matrix-transpose procedure recursively partitions an \(N\times N\) input matrix and output matrix into four \((N/2)\times(N/2)\) submatrices and **spawns four recursive transpose calls**. The base case copies one element.

Determine the asymptotic parallelism \(T_1/T_\infty\).

1. \(\Theta(N^2/\log N)\)  
2. \(\Theta(N/\log N)\)  
3. \(\Theta(\log N/N^2)\)  
4. \(\Theta(\log N/N)\)

---

# Week 2 — Vector Pipelines, GPU Execution, Reductions, and Parallel Programming Models

## National-Level Previous-Year Questions — Questions 1–10

### Q1. [UPSC/NCRB 2022 • DPA • Q33] — Medium

When optimizing a parallel program, which profiling approach is the most appropriate strategy for identifying the regions where optimization effort can produce the greatest performance benefit?

Select the option in the source question that correctly prioritizes **measured execution hot spots/bottlenecks rather than unmeasured intuition**.

---

### Q2. [UPSC/NCRB 2022 • DPA • Q34] — Medium

Which of the following is **not** fundamentally a SIMD-style execution organization?

1. Vector processor  
2. Modern superscalar processor  
3. Processor array under one instruction stream  
4. GPU execution of lock-step lane groups

---

### Q3. [UPSC/NCRB 2022 • DPA • Q35] — Medium

A dual-core chip contains two CPUs that share a **single uniform path to main memory**. Which memory-architecture description best matches the organization?

1. Uniform Memory Access (UMA)  
2. Cache-coherent NUMA  
3. Non-Uniform Memory Access without coherence  
4. A cache-coherence protocol rather than a memory architecture

---

### Q4. [UPSC/NCRB 2022 • DPA • Q36] — Medium

For the **fork-join** parallel programming model, identify the correct statement about execution inside a parallel region, thread teams, and the relationship between the master thread and worker threads.

1. Parallel regions can execute work truly concurrently and a program may contain multiple such regions.  
2. Worker threads permanently replace the master between parallel regions.  
3. A thread team continues to execute concurrently outside all parallel regions.  
4. The number of active threads is required to change continuously inside one parallel region.

---

### Q5. [UPSC/NCRB 2022 • DPA • Q37] — High

**Array padding** can improve parallel-program performance when different threads update logically separate data that happen to occupy the same cache line.

Which statement best describes the architectural purpose of padding?

1. Insert suitable spacing so independently updated objects map to different cache lines and reduce false sharing.  
2. Compress adjacent objects to increase sharing of writable cache lines.  
3. Force every thread to update the same physical cache block.  
4. Eliminate synchronization by converting all shared objects to registers.

---

### Q6. [UPSC/NCRB 2022 • DPA • Q38] — Medium

In a distributed-memory system, one processor cannot directly address another processor's local memory.

Which programming mechanism is therefore essential for exchanging data between processors?

1. Mutual exclusion only  
2. Multithreading only  
3. Message passing  
4. NUMA address translation only

---

### Q7. [UPSC/NCRB 2022 • DPA • Q39] — Medium

What operation does **MPI_Scatter** perform?

Choose the description that correctly states how data initially held by a root process is partitioned and distributed as chunks to the processes in a communicator.

---

### Q8. [UPSC/NCRB 2022 • DPA • Q40] — Medium

Which architecture is commonly described by the term **NORMA (No Remote Memory Access)**?

1. Shared-memory multiprocessor  
2. Distributed-memory multiprocessor  
3. Single-main-memory uniprocessor  
4. Segmented virtual-memory machine

---

### Q9. [UGC-NET CSE October 2020 • Part 2 • Q44] — Medium

Consider these statements about a multiprocessor system:

I. It is controlled by one operating system.  
II. It consists of independent computers connected only through communication lines.  
III. In Flynn's taxonomy it is normally a MIMD system.

Which statements are true?

1. I only  
2. I and II only  
3. I and III only  
4. II and III only

---

### Q10. [UGC-NET CSE December 2014 • Part 3 • Q75] — Medium

Which computing model is **not inherently a distributed-computing environment**?

1. Cloud computing  
2. Parallel computing  
3. Cluster computing  
4. Peer-to-peer computing

Explain the architectural distinction you use when selecting the answer.

---

## CLASS DISCUSSION — Questions 11–15

### Q11. [BOOK • Chapter 4 • Exercise 4.7 • p. 370] — CLASS DISCUSSION — Medium

For the convoy schedule from Exercise 4.6, assume a vector length of **32**.

Determine:

1. the number of **chimes** required by the vector sequence; and  
2. the achieved **cycles per floating-point operation**.

Account for the convoy organization rather than treating every vector instruction as an independent scalar instruction.

---

### Q12. [BOOK • Chapter 4 • Exercise 4.9 • pp. 370–371] — CLASS DISCUSSION — High

Consider complex-vector multiplication:

```c
for (i = 0; i < 300; i++) {
    c_re[i] = a_re[i] * b_re[i] - a_im[i] * b_im[i];
    c_im[i] = a_re[i] * b_im[i] + a_im[i] * b_re[i];
}
```

Assume:

- clock = **700 MHz**;
- maximum vector length = **64**;
- load/store startup = **15 cycles**;
- multiply startup = **8 cycles**;
- add/subtract startup = **5 cycles**.

Complete all source subparts:

**(a)** Determine arithmetic intensity.  
**(b)** Convert the loop to RV64V using strip mining.  
**(c)** With chaining and one memory pipeline, determine chimes and cycles per complex result including startup.  
**(d)** Determine cycles per complex result for the chained vector sequence under the exercise's specified organization.  
**(e)** Repeat with **three memory pipelines**, chaining, and no memory-bank conflicts.

---

### Q13. [BOOK • Chapter 4 • Exercise 4.10 • p. 371] — CLASS DISCUSSION — High

Compare a **vector computer** with a **host CPU + GPU hybrid** for an application containing a memory-bound vector kernel.

Assume:

- kernel arithmetic intensity = **0.5 FLOP per DRAM byte**;
- scalar pre/post work = **400 ms** on either scalar processor;
- kernel input = **200 MB**;
- kernel output = **100 MB**;
- vector-processor memory bandwidth = **30 GB/s**;
- GPU memory bandwidth = **150 GB/s**;
- host↔GPU DMA bandwidth = **10 GB/s**;
- DMA average latency = **10 ms**;
- both vector kernel implementations are limited by memory bandwidth.

Compute the complete execution time of the application on both systems, including the hybrid's transfer overhead.

---

### Q14. [BOOK • Chapter 4 • Exercise 4.11 • pp. 371–372] — CLASS DISCUSSION — High

A dot product is initially written as a scalar reduction over **64 elements**. After scalar expansion, the elementwise products can be vectorized but the final reduction remains.

Complete every source subpart:

**(a)** Rewrite the reduction using **recurrence doubling**.  
**(b)** On an RV64V machine whose vector-register elements are individually addressable, reduce `V1` to **eight partial sums**. Assume total vector-add latency including operand read and result write is **8 cycles**.  
**(c)** Rewrite the GPU shared-memory reduction so active threads remain contiguous, the expensive modulo operation is removed, and shared-memory bank conflicts are avoided. Assume **32 threads per warp** and a conflict when two active threads in a warp address indices having the same value modulo 32.

---

### Q15. [UGC-NET CSE June 2023 • Part 2 • Q67] — CLASS DISCUSSION — High

Match the algorithms with their asymptotic complexities:

| List I | List II |
|---|---|
| A. Parallel FFT | I. \(\Theta(n^2)\) |
| B. Iterative FFT | II. \(\Theta(n)\) |
| C. Evaluate a polynomial at \(n\) points using Horner's method | III. \(\Theta(\log n)\) |
| D. Multiply two polynomials already represented in point-value form | IV. \(\Theta(n\log n)\) |

Choose the correct matching from the source alternatives.

---

# Week 3 — Loop-Level Parallelism, Dependence Analysis, Roofline, and GPU Throughput

## National-Level Previous-Year Questions — Questions 1–10

### Q1. [GATE CSE 2006 • Q60] — High

Consider:

```c
for (i = 0; i < n; i++) {
    for (j = 0; j < n; j++) {
        if (i % 2) {
            x += (4*j + 5*i);
            y += (7 + 4*j);
        }
    }
}
```

Which optimization is **not applicable** to this code?

1. Loop-invariant code motion  
2. Common-subexpression elimination  
3. Strength reduction  
4. Dead-code elimination

---

### Q2. [GATE CSE 2006 • Q55] — High

Consider the two functions:

```c
int work1(int *a, int i, int j) {
    int x = a[i+2];
    a[j] = x + 1;
    return a[i+2] - 3;
}

int work2(int *a, int i, int j) {
    int t1 = i + 2;
    int t2 = a[t1];
    a[j] = t2 + 1;
    return t2 - 3;
}
```

Let:

- **S1:** the transformation from `work1` to `work2` is valid for every possible initial memory state and every `i,j`;
- **S2:** every transformation of this form necessarily reduces execution time.

Choose the correct truth values for S1 and S2, paying particular attention to possible **memory aliasing**.

---

### Q3. [GATE CSE 2014 • Set 1 • Q17] — Medium

Which statement about compiler optimization is false?

1. A basic block has one entry and one exit.  
2. Available-expression analysis can support common-subexpression elimination.  
3. Live-variable analysis can support dead-code elimination.  
4. Replacing `x = 4 * 5` by `x = 20` is an example of common-subexpression elimination.

---

### Q4. [GATE CSE 2008 • Q12] — Medium

Why is machine-independent optimization commonly performed on an **intermediate representation (IR)** rather than only on final machine code?

Choose the source alternative that correctly captures the principal compiler-design reason.

---

### Q5. [GATE CSE 2013 • Q48] — High

A target processor has only **two registers** available for the following code. The compiler may perform **code motion**, but no other semantic change.

```c
c = a + b;
d = c * a;
e = c + a;
x = c * c;

if (x > a)
    y = a * a;
else {
    d = d * d;
    e = e * e;
}
```

Determine the minimum number of values that must be spilled to memory.

1. 0  
2. 1  
3. 2  
4. 3

---

### Q6. [GATE CSE 2012 • Q20] — Medium

Register renaming is principally used to:

1. choose alternative registers only at compile time for parameter passing;
2. reduce storage for local variables;
3. eliminate certain **name-dependence hazards** that would otherwise constrain reordering;
4. translate virtual register addresses into memory addresses.

---

### Q7. [UGC-NET CSE July 2016 • Part 2 • Q33] — Medium

Two loops traverse the same iteration range. Their bodies do not contain cross-loop dependences, so the two loop bodies can be merged into one loop.

Which loop transformation is being described?

1. Loop unrolling  
2. Strength reduction  
3. Loop concatenation only in the sense of sequential append  
4. Loop jamming / loop fusion

---

### Q8. [UGC-NET CSE December 2015 • Part 2 • Q41] — Medium

Which description correctly characterizes **loop unrolling** as a performance optimization?

1. It reduces repeated loop-control tests/branches by replicating loop-body work.  
2. It necessarily decreases the number of instructions in the basic block.  
3. It exchanges an inner and outer loop.  
4. It is identical to software pipelining.

---

### Q9. [UGC-NET CSE August 2016 • Part 2 • Q33] — Medium

Which transformation is an example of **strength reduction**?

1. Replace `P + P` with an unrelated constant-folding step.  
2. Replace `P * 32` with `P << 5`.  
3. Replace `P * 0` with a table lookup.  
4. Replace `(P << 4) - P` with a more expensive multiply solely to increase instruction count.

---

### Q10. [ISRO CSE 2020 • Q42] — Medium

A compiler rearranges loop computations so that independent operations from different iterations overlap, allowing long-latency operations to be hidden.

Which optimization is this?

1. Loop unrolling only  
2. Dead-code elimination  
3. Strength reduction  
4. Software pipelining

---

## CLASS DISCUSSION — Questions 11–15

### Q11. [BOOK • Chapter 4 • Exercise 4.12 • pp. 372–373] — CLASS DISCUSSION — High

Consider the FDTD kernel:

```c
for (int x = 0; x < NX-1; x++)
for (int y = 0; y < NY-1; y++)
for (int z = 0; z < NZ-1; z++) {
    int index = x*NY*NZ + y*NZ + z;
    if (y > 0 && x > 0) {
        material = IDx[index];
        dH1 = (Hz[index] - Hz[index-incrementY]) / dy[y];
        dH2 = (Hy[index] - Hy[index-incrementZ]) / dz[z];
        Ex[index] = Ca[material]*Ex[index]
                  + Cb[material]*(dH2-dH1);
    }
}
```

`dH1`, `dH2`, `Hy`, `Hz`, `dy`, `dz`, `Ca`, `Cb`, and `Ex` are single-precision floating-point arrays; `IDx` contains unsigned integers.

Complete all parts:

**(a)** Determine the arithmetic intensity.  
**(b)** Decide whether the kernel is amenable to vector/SIMD execution and justify.  
**(c)** With **30 GB/s** memory bandwidth, determine whether the kernel is memory- or compute-bound.  
**(d)** Construct the Roofline model assuming peak compute throughput is **85 GFLOP/s**.

---

### Q12. [BOOK • Chapter 4 • Exercise 4.13 • pp. 373–374] — CLASS DISCUSSION — High

A GPU contains **10 SIMD processors**. Each SIMD instruction is **32-wide**, and each SIMD processor has **8 single-precision arithmetic/load-store lanes**, so a nondiverged 32-wide operation is completed over four lane cycles.

For a kernel:

- average active-thread fraction due to divergence = **80%**;
- 70% of issued SIMD instructions are single-precision arithmetic;
- 20% are load/store;
- average SIMD-instruction issue rate = **0.85**;
- clock = **1.5 GHz**.

Compute the kernel's effective floating-point throughput in **GFLOP/s**.

---

### Q13. [BOOK • Chapter 4 • Exercise 4.14 • p. 374] — CLASS DISCUSSION — High

Using the GPU and kernel from Exercise 4.13, calculate the throughput speedup for each independent architectural change:

1. increase single-precision lanes per SIMD processor from **8 to 16**;
2. increase SIMD processors from **10 to 15**, assuming perfect scaling and no secondary effects;
3. add a cache that reduces relevant memory latency by **40%** and raises the average SIMD issue rate from **0.85 to 0.95**.

Quantify each speedup rather than merely ranking the changes.

---

### Q14. [BOOK • Chapter 4 • Exercise 4.15 • p. 374] — CLASS DISCUSSION — High

Analyze loop-carried and name dependences.

**(a)** Determine whether the following loop has a loop-carried dependence:

```c
for (i = 0; i < 100; i++) {
    A[i] = B[2*i + 4];
    B[4*i + 5] = A[i];
}
```

**(b)** For:

```c
for (i = 0; i < 100; i++) {
    A[i] = A[i] * B[i];   /* S1 */
    B[i] = A[i] + c;      /* S2 */
    A[i] = C[i] * c;      /* S3 */
    C[i] = D[i] * A[i];   /* S4 */
}
```

identify every **true dependence, output dependence, and antidependence**, then eliminate output/anti-dependences by renaming.

**(c)** For:

```c
for (i = 0; i < 100; i++) {
    A[i] = A[i] + B[i];       /* S1 */
    B[i+1] = C[i] + D[i];     /* S2 */
}
```

determine the dependence between S1 and S2, decide whether the loop is parallel, and transform it if necessary to expose parallelism.

---

### Q15. [UGC-NET CSE January 2025 • Part 2 • Q68] — CLASS DISCUSSION — Medium

The following optimization activities are listed:

I. Algebraic simplification  
II. Use of machine idioms  
III. Redundant-instruction elimination  
IV. Flow-of-control optimization  
V. Improved target code

Choose the source alternative that places these steps in the correct optimization sequence.

---

# Week 4 — GPU Utilization, Parallel Execution Costs, and Throughput-Oriented Scheduling

## National-Level Previous-Year Questions — Questions 1–10

### Q1. [ISRO CSE 2011 • Q3] — Medium

Which statement best describes **strength reduction**?

1. Moving a runtime computation entirely to compile time regardless of operands  
2. Removing only loop-invariant statements  
3. Performing common-subexpression elimination  
4. Replacing an expensive operation by an equivalent cheaper operation

---

### Q2. [UGC-NET CSE October 2020 • Part 2 • Q7] — High

A non-pipelined system takes **50 ns per task**. The same task stream is executed by a **six-stage pipeline** with a **10 ns clock**.

For **500 tasks**, what is the approximate speedup?

1. 6  
2. 4.95  
3. 5.7  
4. 5.5

---

### Q3. [ISRO CSE 2016 • Q19] — Medium

A non-pipelined processor runs at **2.5 GHz** with average CPI = **4**. It is replaced by a five-stage pipelined implementation running at **2 GHz**. Assume no stalls and steady-state CPI = 1 for the pipeline.

What speedup is obtained?

1. 3.2  
2. 3.0  
3. 2.2  
4. 2.0

---

### Q4. [UGC-NET CSE December 2004 • Part 2 • Q48] — Medium

The cost of a parallel-processing implementation is primarily determined by which of the following?

1. Time complexity  
2. Switching complexity  
3. Circuit complexity  
4. None of the above

For class use, justify the selected metric in terms of the amount of work and available concurrency rather than selecting by terminology alone.

---

### Q5. [UGC-NET CSE October 2022 • Part 1 • Q59] — Medium

Match the concepts:

| List I | List II |
|---|---|
| A. Least Frequently Used | I. Memory distributed among processors |
| B. Critical Section | II. A cache/page replacement policy |
| C. Loosely coupled multiprocessor | III. Region requiring mutually exclusive access to a shared resource |
| D. Distributed operating-system organization | IV. OS routines/resources distributed across the system |

Choose the correct matching from the source alternatives.

---

### Q6. [GATE CSE 2015 • Set 3 • Q51] — High

A pipelined functional unit has the reservation table:

| Stage | t1 | t2 | t3 | t4 | t5 |
|---|:---:|:---:|:---:|:---:|:---:|
| S1 | X |  |  |  | X |
| S2 |  | X |  | X |  |
| S3 |  |  | X |  |  |

Determine the **minimum average latency (MAL)** between successive initiations that can be sustained without a forbidden collision.

---

### Q7. [GATE CSE 2016 • Set 2 • Q30] — High

Two operation types are available:

- \(F\): 5 ns on an \(F\)-unit;
- \(G\): 3 ns on a \(G\)-unit.

There are **two F-units** and **two G-units**. Ignore communication and scheduling overhead.

The system must compute:

\[
F(G(X_i)),\qquad i=1,\ldots,10.
\]

Determine the **minimum total completion time**.

---

### Q8. [GATE CSE 2015 • Set 3 • Q47] — High

Consider:

```text
I1: ADD R1, R2, R3
I2: MUL R7, R1, R3
I3: SUB R4, R1, R5
I4: ADD R3, R2, R4
I5: MUL R7, R8, R9
```

Evaluate:

- **S1:** I2 and I5 have an antidependence.
- **S2:** I2 and I4 have an antidependence.
- **S3:** An antidependence necessarily causes a pipeline stall even when register renaming is available.

Determine which statements are correct.

---

### Q9. [UGC-NET CSE June 2015 • Part 2 • Q46] — Medium

If fraction \(P\) of a program is parallelizable, fraction \(1-P\) remains serial, and \(N\) processors are available, which expression gives Amdahl's maximum speedup?

1. \(\frac{1}{(1-P)+NP}\)  
2. \(\frac{1}{(N-1)P+P}\)  
3. \(\frac{1}{(1-P)+P/N}\)  
4. \(\frac{1}{P+(1-P)/N}\)

---

### Q10. [UGC-NET CSE July 2016 • Part 2 • Q48] — Medium

Pipelining improves processor performance primarily by:

1. decreasing the latency of every individual instruction;
2. eliminating all data hazards;
3. exploiting overlap/parallelism so instruction **throughput** rises;
4. decreasing the cache-miss rate.

Relate the selected principle to vector functional-unit pipelines and SIMD throughput.

---

## CLASS DISCUSSION — Questions 11–15

### Q11. [BOOK • Chapter 4 • Exercise 4.16 • p. 374] — CLASS DISCUSSION — Medium

Identify and explain **at least four runtime behaviors caused by GPU kernel code** that can reduce hardware-resource utilization and lower kernel performance.

Your answer must connect each behavior to the affected GPU resource or execution mechanism—for example lane utilization, warp scheduling, memory-system efficiency, or occupancy—rather than merely listing programming guidelines.

---

### Q12. [BOOK • Chapter 4 • Exercise 4.17 • p. 374] — CLASS DISCUSSION — High

A hypothetical GPU has:

- clock rate = **1.5 GHz**;
- **16 SIMD processors**;
- **16 single-precision floating-point units per SIMD processor**;
- off-chip memory bandwidth = **100 GB/s**.

**(a)** Ignoring memory bandwidth and assuming memory latency can be fully hidden, compute peak single-precision throughput in GFLOP/s.  
**(b)** Determine whether that peak can be sustained with the stated off-chip bandwidth, stating the arithmetic-intensity/bandwidth condition required for your conclusion.

---

### Q13. [BOOK • Chapter 4 • Exercise 4.18 • pp. 374–375] — CLASS DISCUSSION — High

Implement and characterize a CUDA kernel for **Conway's Game of Life**:

- board size = **256 × 256**;
- execute **100 generations**;
- the host initializes the board;
- associate one CUDA thread with each cell;
- synchronize correctly between generations.

Game rules:

- live cell with fewer than 2 live neighbors dies;
- live cell with 2 or 3 live neighbors survives;
- live cell with more than 3 live neighbors dies;
- dead cell with exactly 3 live neighbors becomes live.

Complete all source tasks:

**(a)** Compile to PTX and inspect the kernel: report PTX instruction count and determine whether conditional regions compile to branches, predicated instructions, or a mixture.  
**(b)** Using the simulator/profiler required by the exercise, report dynamic instruction count, IPC/issue rate, control/ALU/memory instruction mix, shared-memory bank conflicts, and effective off-chip bandwidth.  
**(c)** Improve the kernel so off-chip references are coalesced and compare the resulting runtime/performance measurements with the baseline.

---

### Q14. [GATE CSE 2018 • Q50] — CLASS DISCUSSION — High

A RISC processor has stages:

```text
IF → ID → OF → PO → WB
```

IF, ID, OF, and WB take one cycle. For a stream of **100 instructions**, the PO stage requires:

- 3 cycles for 40 instructions;
- 2 cycles for 35 instructions;
- 1 cycle for 25 instructions.

Assume no data or control hazards.

Determine the total number of clock cycles required. Then state how the same structural-throughput reasoning applies to a vector/SIMD functional pipeline whose initiation rate is lower than its operation latency.

---

### Q15. [GATE CSE 2017 • Set 1 • Q50] — CLASS DISCUSSION — High

Processor \(NP\) has stage delays:

| Stage | Delay |
|---|---:|
| IF | 5 ns |
| ID | 4 ns |
| OF | 20 ns |
| EX | 10 ns |
| WB | 3 ns |

Every interstage register adds **2 ns**.

Processor \(EP\) splits the 20 ns OF stage into **12 ns** and **8 ns** stages, leaving the other logic unchanged.

For **20 independent instructions** with no hazards, determine the speedup of \(EP\) over \(NP\). Relate the result to the design trade-off between deeper pipelining and lane throughput in data-parallel processors.

---

# Organizer Source Ledger

## Textbook source

John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 4, **Data-Level Parallelism in Vector, SIMD, and GPU Architectures**, Case Study and Exercises by Jason D. Bakos, pp. 369–375.

### Selected textbook exercises

| Week-Q | Exercise | Page(s) | Principal topic |
|---|---:|---:|---|
| W1-Q11 | 4.2 | 369 | PTX implementation |
| W1-Q12 | 4.3 | 369 | Latency hiding and warp count |
| W1-Q13 | 4.5 | 369–370 | RV64V implementation |
| W1-Q14 | 4.6 | 370 | Vector convoys |
| W2-Q11 | 4.7 | 370 | Chimes and cycles/FLOP |
| W2-Q12 | 4.9 | 370–371 | Complex-vector strip mining and vector timing |
| W2-Q13 | 4.10 | 371 | Vector vs host+GPU execution time |
| W2-Q14 | 4.11 | 371–372 | Parallel reductions |
| W3-Q11 | 4.12 | 372–373 | Arithmetic intensity and Roofline |
| W3-Q12 | 4.13 | 373–374 | Effective GPU throughput |
| W3-Q13 | 4.14 | 374 | GPU architectural speedups |
| W3-Q14 | 4.15 | 374 | Loop-carried/name dependences |
| W4-Q11 | 4.16 | 374 | GPU utilization limits |
| W4-Q12 | 4.17 | 374 | Peak throughput and bandwidth |
| W4-Q13 | 4.18 | 374–375 | CUDA profiling and coalescing |

## National-level PYQ source links

The links below are retained for organizer verification. They point to indexed copies/discussions of the source examination questions; the paper above does **not** include their solutions.

| Source | Link |
|---|---|
| UGC-NET Aug 2024 P2 Q40 | https://gateoverflow.in/485411/ugc-net-cse-august-2024-part-2-question-40 |
| UGC-NET Aug 2024 P2 Q38 | https://gateoverflow.in/485413/ugc-net-cse-august-2024-part-2-question-38 |
| ISRO CSE 2018 Q71 | https://gateoverflow.in/213517/isro-cse-2018-question-71 |
| UGC-NET Jun 2012 P3 Q5 | https://gateoverflow.in/44498/ugc-net-cse-june-2012-part-3-question-5 |
| UGC-NET Nov 2017 P2 Q50 | https://gateoverflow.in/166284/ugc-net-cse-november-2017-part-2-question-50 |
| UGC-NET Dec 2007 P2 Q46 | https://gateoverflow.in/questions?sort=hot&start=22520 |
| ISRO CSE 2011 Q10 | https://gateoverflow.in/52258/isro-cse-2011-question-10 |
| ISRO CSE 2008 Q42 | https://gateoverflow.in/49969/isro-cse-2008-question-42 |
| UGC-NET Oct 2020 P2 Q75 | https://gateoverflow.in/349598/ugc-net-cse-october-2020-part-2-question-75 |
| UGC-NET Jun 2008 P2 Q46 | https://gateoverflow.in/418291/ugc-net-cse-june-2008-part-2-question-46 |
| UPSC/NCRB 2022 DPA Q33 | https://gateoverflow.in/492860/upsc-ncrb-2022-dpa-question-33 |
| UPSC/NCRB 2022 DPA Q34 | https://gateoverflow.in/492859/upsc-ncrb-2022-dpa-question-34 |
| UPSC/NCRB 2022 DPA Q35 | https://gateoverflow.in/492858/upsc-ncrb-2022-dpa-question-35 |
| UPSC/NCRB 2022 DPA Q36 | https://gateoverflow.in/492857/upsc-ncrb-2022-dpa-question-36 |
| UPSC/NCRB 2022 DPA Q37 | https://gateoverflow.in/492856/upsc-ncrb-2022-dpa-question-37 |
| UPSC/NCRB 2022 DPA Q38 | https://gateoverflow.in/492855/upsc-ncrb-2022-dpa-question-38 |
| UPSC/NCRB 2022 DPA Q39 | https://gateoverflow.in/492854/upsc-ncrb-2022-dpa-question-39 |
| UPSC/NCRB 2022 DPA Q40 | https://gateoverflow.in/492853/upsc-ncrb-2022-dpa-question-40 |
| UGC-NET Oct 2020 P2 Q44 | https://gateoverflow.in/349629/ugc-net-cse-october-2020-part-2-question-44 |
| UGC-NET Dec 2014 P3 Q75 | https://gateoverflow.in/61536/ugc-net-cse-december-2014-part-3-question-75 |
| UGC-NET Dec 2019 P2 Q75 | https://gateoverflow.in/360758/ugc-net-cse-december-2019-part-2-question-75 |
| UGC-NET Jun 2023 P2 Q67 | https://gateoverflow.in/407901/ugc-net-cse-june-2023-part-2-67 |
| GATE CSE 2006 Q60 | https://gateoverflow.in/1838/gate-cse-2006-question-60 |
| GATE CSE 2006 Q55 | https://gateoverflow.in/1833/gate-cse-2006-question-55 |
| GATE CSE 2014 Set 1 Q17 | https://gateoverflow.in/1784/gate-cse-2014-set-1-question-17 |
| GATE CSE 2008 Q12 | https://gateoverflow.in/410/gate-cse-2008-question-12 |
| GATE CSE 2013 Q48 | https://gateoverflow.in/1556/gate-cse-2013-question-48 |
| GATE CSE 2012 Q20 | https://gateoverflow.in/52/gate-cse-2012-question-20-isro2016-23 |
| UGC-NET Jul 2016 P2 Q33 | https://gateoverflow.in/63445/ugc-net-cse-july-2016-part-2-question-33 |
| UGC-NET Dec 2015 P2 Q41 | https://gateoverflow.in/62317/ugc-net-cse-december-2015-part-2-question-41 |
| UGC-NET Aug 2016 P2 Q33 | https://gateoverflow.in/70296/ugc-net-cse-august-2016-part-2-question-33 |
| ISRO CSE 2020 Q42 | https://gateoverflow.in/331453/isro-cse-2020-question-42 |
| UGC-NET Jan 2025 P2 Q68 | https://gateoverflow.in/485921/ugc-net-cse-january-2025-part-2-question-68 |
| ISRO CSE 2011 Q3 | https://gateoverflow.in/50567/isro-cse-2011-question-3 |
| UGC-NET Oct 2020 P2 Q7 | https://gateoverflow.in/349666/ugc-net-cse-october-2020-part-2-question-7 |
| ISRO CSE 2016 Q19 | https://gateoverflow.in/55465/isro-cse-2016-question-19 |
| UGC-NET Dec 2004 P2 Q48 | https://gateoverflow.in/tag/ugcnetcse-dec2004-paper2 |
| UGC-NET Oct 2022 P1 Q59 | https://gateoverflow.in/386098/ugc-net-cse-october-2022-part-1-question-59 |
| GATE CSE 2015 Set 3 Q51 | https://gateoverflow.in/questions?search=GATE+CSE+2015+Set+3+Q51 |
| GATE CSE 2016 Set 2 Q30 | https://gateoverflow.in/questions?search=GATE+CSE+2016+Set+2+Q30 |
| GATE CSE 2015 Set 3 Q47 | https://gateoverflow.in/questions?search=GATE+CSE+2015+Set+3+Q47 |
| UGC-NET Jun 2015 P2 Q46 | https://gateoverflow.in/61088/ugc-net-cse-june-2015-part-2-question-46 |
| UGC-NET Jul 2016 P2 Q48 | https://gateoverflow.in/63507/ugc-net-cse-july-2016-part-2-question-48 |
| GATE CSE 2018 Q50 | https://gateoverflow.in/questions?search=GATE+CSE+2018+Q50 |
| GATE CSE 2017 Set 1 Q50 | https://gateoverflow.in/questions?search=GATE+CSE+2017+Set+1+Q50 |

---

## Organizer verification summary

- Total questions: **60**
- Weeks: **4**
- Questions per week: **15**
- Textbook exercises selected: **15**
- One textbook exercise number is never split into multiple paper questions.
- No answer key or solution is included.
- No external image asset is required.
