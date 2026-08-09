# MTCS 102 — Chapter 3 Question Paper

## Instruction-Level Parallelism and Its Exploitation

**Primary text:** John L. Hennessy, David A. Patterson, Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 3  
**Format:** 4 weeks × 15 questions = 60 questions  
**Weekly split:** Questions 1–10 = GATE CSE previous-year questions; Questions 11–15 = complete textbook exercises for class discussion  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included

> **Audit status (2026-08-09):** The incomplete option set in Week 4 Q7 has been restored from the indexed GATE CSE 2003 source. Chapter-3 PYQs duplicated in the older Chapter-4 draft are retained here as their canonical placement.

> **Question-counting rule:** One source question/exercise is one question even if it contains multiple subparts. Book exercises are never split.
>
> **Chapter boundary:** The GATE portion is restricted to pipelining, hazards, forwarding, instruction dependencies, ILP, compiler scheduling/register pressure, branch handling, and related execution-unit scheduling. Cache-only questions are not used here.
>
> **Figures:** No external image package is required for this Markdown paper. Numerical pipeline diagrams and reservation tables have been transcribed into Markdown tables/text without changing their source data.
>
> **Book wording:** The textbook exercises are presented as complete, faithful problem statements but are paraphrased rather than reproduced verbatim. All numerical assumptions, code sequences, subparts, and exercise identifiers needed to solve them are retained.


# Week 1 — Pipeline Hazards, Forwarding, Branch Penalties, and Speedup

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2026 • Set 1 • Q50] — High

A processor has a 1 ns clock. Its EX stage performs both arithmetic/logic operations and the memory read required by a load. The EX stage is **not internally pipelined**, so an instruction occupying EX for multiple cycles blocks another instruction from using EX.

The instruction mix and EX latencies are:

| Instruction class | EX latency | Percentage |
|---|---:|---:|
| LOAD | 1.8 ns | 15% |
| Integer multiply | 1.5 ns | 10% |
| Integer divide | 2.5 ns | 5% |
| Floating-point add | 1.7 ns | 10% |
| Floating-point subtract | 1.7 ns | 5% |
| Floating-point multiply | 2.8 ns | 15% |
| Floating-point divide | 3.2 ns | 5% |
| All remaining instructions | less than 1 ns | 35% |

Determine the number of **stall clock cycles caused by EX-stage structural hazards** for every 100 instructions.

---

### Q2. [GATE CSE 2026 • Set 2 • Q47] — High

A non-pipelined processor operates at **1.6 GHz** and requires an average of **5 clock cycles per instruction**.

It is redesigned as a pipelined processor operating at **1.2 GHz**. In the ideal case the pipeline completes one instruction per clock cycle, but **30% of the instructions incur a 2-cycle stall**.

Determine the speedup of the pipelined processor over the non-pipelined processor, rounded to **two decimal places**.

---

### Q3. [GATE CSE 2024 • Set 1 • Q20] — Medium

Consider a five-stage pipeline:

```text
IF → ID → EX → MEM → WB
```

Which statements about **forwarding/bypassing** are correct? Select all that apply.

1. Forwarding can pass a result produced by a stage of an older instruction directly to a stage of a younger instruction that needs it.
2. Data available at the output of MEM can be forwarded to the EX input of the following instruction.
3. Forwarding cannot eliminate every possible pipeline stall.
4. Forwarding needs no additional hardware to retrieve and route intermediate pipeline results.

---

### Q4. [GATE CSE 2024 • Set 2 • Q21] — Medium

Instruction syntax is:

```text
opcode destination, source1, source2
```

Consider:

```text
I1: DIV R3, R1, R2
I2: SUB R5, R3, R4
I3: ADD R3, R5, R6
I4: MUL R7, R3, R8
```

Which statements are true? Select all that apply.

1. There is a RAW dependency on `R3` between I1 and I2.
2. There is a WAR dependency on `R3` between I1 and I3.
3. There is a RAW dependency on `R3` between I2 and I3.
4. There is a WAW dependency on `R3` between I3 and I4.

---

### Q5. [GATE CSE 2024 • Set 2 • Q48] — High

A non-pipelined instruction-execution unit operates at **2 GHz** and takes an average of **6 cycles per instruction**.

It is redesigned as a **five-stage pipeline**, also at 2 GHz, with ideal throughput of one instruction per cycle. While executing program \(P\):

- 20% of instructions incur an average **2-cycle data-hazard stall**;
- another 20% incur an average **3-cycle control-hazard stall**.

Find the speedup of the pipelined design over the non-pipelined design, rounded to **one decimal place**.

---

### Q6. [GATE CSE 2023 • Q23] — Medium

A processor uses a three-stage instruction pipeline. The stage delays are:

| Stage | Delay |
|---|---:|
| Stage 1 | 10 ns |
| Stage 2 | 20 ns |
| Stage 3 | 14 ns |

Assume negligible pipeline-register delay and no stalls or hazards. The processor fetches at most one new instruction per cycle.

How much time, in nanoseconds, is required to execute a sequence of **100 instructions**?

---

### Q7. [GATE CSE 2022 • Q51] — High

Processor \(X_1\) is a 2 GHz, standard five-stage RISC pipeline with base CPI 1. A program \(P\) has **30% branch instructions**. On \(X_1\), every branch causes a **2-cycle stall**.

Processor \(X_2\) has the same clock frequency and pipeline, but includes a branch predictor:

- a correct prediction eliminates the branch stall;
- an incorrect prediction gives neither a saving nor an additional penalty relative to \(X_1\);
- predictor accuracy is **80%**.

Ignore structural and data hazards.

Find the speedup \(T_{X_1}/T_{X_2}\), rounded to **two decimal places**.

---

### Q8. [GATE CSE 2021 • Set 1 • Q53] — Medium

A five-stage pipeline has stage delays:

```text
150 ns, 120 ns, 150 ns, 160 ns, 140 ns
```

Each pipeline register contributes an additional **5 ns** delay.

A program contains **100 independent instructions** and executes without stalls.

Determine the total execution time in nanoseconds.

---

### Q9. [GATE CSE 2021 • Set 2 • Q53] — High

Consider the five-stage pipeline:

```text
IF → ID → EX → MEM → WB
```

All stages take one cycle except EX. Register operands are read in EX; ID only decodes the instruction.

- `ADD` uses EX for 1 cycle.
- `MUL` uses EX for 2 cycles.

The instruction sequence is:

```text
ADD
MUL
ADD
MUL
ADD
MUL
ADD
MUL
```

Each `MUL` depends on the immediately preceding `ADD`, and every `ADD` except the first depends on the immediately preceding `MUL`.

Compute

\[
\frac{\text{execution time without forwarding}}
     {\text{execution time with forwarding}}
\]

and round to **two decimal places**.

---

### Q10. [GATE CSE 2020 • Q43] — High

A non-pipelined processor runs at **2.5 GHz** and has an average CPI of **5**.

It is redesigned as a five-stage pipeline running at **2 GHz**. The instruction mix is:

- 30% memory instructions,
- 60% ALU instructions,
- the remainder branches.

Additional behavior:

- 5% of the memory instructions suffer a cache miss, adding **50 cycles**;
- 50% of branch instructions incur a **2-cycle branch stall**;
- ALU instructions incur no extra stalls.

Calculate the speedup of the pipelined design over the non-pipelined design, rounded to **two decimal places**.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

For Exercises **3.1–3.10**, use this Newton–Raphson loop as the common base where referenced:

```asm
loop: fld    f2,0(x2)
      fmul.d f4,f2,f2
      fsub.d f4,f4,f0
      fmul.d f6,f2,f8
      fdiv.d f4,f4,f6
      fsub.d f2,f2,f4
      fsd    f2,0(x2)
      addi   x1,x1,-1
      addi   x2,x2,8
      bne    x1,zero,loop
```

`f0` holds \(s\), `f8` holds 2.0, `x1` is the iteration count, and `x2` points to the value being updated.

### Q11. [BOOK • Chapter 3 • Exercise 3.1 • p. 275] — CLASS DISCUSSION — High

Schedule the common loop on a **single-issue, in-order processor**.

Use these result latencies:

- load result: 4 cycles;
- floating-point arithmetic result: 5 cycles;
- integer arithmetic result: 2 cycles;
- branch resolution: 4 cycles;
- store: no produced register result.

Assume complete forwarding: an instruction may begin execution in the same cycle in which the last source value it needs becomes available. Treat an iteration as complete when the last instruction of that iteration commits/resolves.

Construct a cycle-by-cycle schedule and determine the number of cycles per iteration, identifying every required stall.

---

### Q12. [BOOK • Chapter 3 • Exercise 3.2 • p. 275] — CLASS DISCUSSION — Medium

Using the schedule obtained for Exercise 3.1, determine the **fraction and percentage of processor cycles in which a useful instruction is issued**.

Relate this utilization to the true data-dependence chain and the available ILP in the loop.

---

### Q13. [BOOK • Chapter 3 • Exercise 3.3 • p. 275] — CLASS DISCUSSION — High

Execute the same loop on an **out-of-order dynamically scheduled processor** with:

- at most one instruction dispatched per cycle;
- at most one instruction committed per cycle;
- automatic register renaming;
- the same functional latencies as Exercise 3.1.

When multiple instructions are ready, use program order to break ties unless a different rule is explicitly stated.

Create a schedule for the loop and determine the average cycles required per iteration. Show where out-of-order execution removes stalls and where true dependences still serialize execution.

---

### Q14. [BOOK • Chapter 3 • Exercise 3.4 • p. 275] — CLASS DISCUSSION — Medium

For the out-of-order execution schedule from Exercise 3.3, compute the **percentage of cycles in which an instruction is dispatched**.

Compare this with Exercise 3.2 and explain which bottlenecks remain despite dynamic scheduling.

---

### Q15. [BOOK • Chapter 3 • Exercise 3.5 • p. 276] — CLASS DISCUSSION — High

Extend the dynamically scheduled design so that branches are **speculated with 100% prediction accuracy**.

Instructions fetched after a branch may be dispatched before the branch resolves, but they may not commit before that branch resolves.

Schedule **two consecutive iterations** of the loop and determine the **average number of cycles per iteration**. Make clear which instructions from the second iteration can overlap the first and what ultimately limits the overlap.

---


# Week 2 — Variable-Latency Pipelines, Register Renaming, and Structural Scheduling

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2018 • Q50] — High

A RISC processor has five stages:

```text
IF → ID → OF → PO → WB
```

IF, ID, OF, and WB each take one cycle for every instruction. For a sequence of **100 instructions**, the PO stage requires:

- 3 cycles for 40 instructions,
- 2 cycles for 35 instructions,
- 1 cycle for the remaining 25 instructions.

Assume no data or control hazards.

How many clock cycles are required to complete the entire sequence?

---

### Q2. [GATE CSE 2017 • Set 1 • Q50] — High

A processor \(NP\) has five pipeline stages with combinational delays:

| Stage | Delay |
|---|---:|
| IF | 5 ns |
| ID | 4 ns |
| OF | 20 ns |
| EX | 10 ns |
| WB | 3 ns |

Each interstage buffer adds **2 ns**.

A redesigned processor \(EP\) splits the 20 ns OF stage into two stages of **12 ns** and **8 ns**; all other logic is unchanged.

For a sequence of **20 independent instructions** with no hazards, determine the speedup of \(EP\) over \(NP\).

---

### Q3. [GATE CSE 2016 • Set 1 • Q32] — Medium

A four-stage pipeline has stage delays:

```text
800 ps, 500 ps, 400 ps, 300 ps
```

The 800 ps stage is replaced by two stages having delays **600 ps** and **350 ps**. Ignore pipeline-register overhead.

What is the **percentage increase in maximum steady-state throughput** after the change?

---

### Q4. [GATE CSE 2016 • Set 2 • Q33] — Medium

A 3 GHz processor has a three-stage pipeline with stage delays \(\tau_1,\tau_2,\tau_3\) satisfying

\[
\tau_1=\frac{3\tau_2}{4}=2\tau_3.
\]

The longest stage is split into two stages of equal delay. Ignore pipeline-register delay.

What is the new maximum clock frequency, in GHz?

---

### Q5. [GATE CSE 2016 • Set 2 • Q30] — High

A system has two types of operations:

- \(F\), which takes **5 ns** on an \(F\)-unit;
- \(G\), which takes **3 ns** on a \(G\)-unit.

There are **two \(F\)-units and two \(G\)-units**. Ignore all communication and scheduling overheads.

The system must compute

\[
F(G(X_i)), \qquad i=1,\dots,10.
\]

Determine the **minimum total time** required to complete all ten results.

---

### Q6. [GATE CSE 2015 • Set 1 • Q38] — Medium

A non-pipelined processor operates at **2.5 GHz** with average CPI **4**.

It is replaced by a five-stage pipelined processor operating at **2 GHz**. Assume there are no pipeline stalls and the pipeline sustains one completed instruction per cycle after filling.

Determine the speedup of the pipelined processor.

---

### Q7. [GATE CSE 2015 • Set 2 • Q44] — High

A four-stage pipeline has:

```text
IF → OF → PO → WB
```

IF, OF, and WB each require one cycle. In PO:

- ADD/SUB require 1 cycle,
- MUL requires 3 cycles,
- DIV requires 5 cycles.

Forwarding is available from PO to a dependent instruction.

Execute:

```text
I1: MUL R5, R0, R1
I2: DIV R6, R2, R3
I3: ADD R7, R5, R6
I4: SUB R8, R7, R4
```

How many clock cycles are required to complete the sequence?

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

Evaluate the following statements.

- **S1:** I2 and I5 have an anti-dependence.
- **S2:** I2 and I4 have an anti-dependence.
- **S3:** An anti-dependence necessarily produces a pipeline stall even if register renaming is available.

Which statements are correct?

---

### Q9. [GATE CSE 2015 • Set 3 • Q51] — High

The reservation table for a pipelined functional unit is:

| Stage | t1 | t2 | t3 | t4 | t5 |
|---|:---:|:---:|:---:|:---:|:---:|
| S1 | X |  |  |  | X |
| S2 |  | X |  | X |  |
| S3 |  |  | X |  |  |

Determine the **minimum average latency (MAL)** between initiations that can be sustained without a forbidden collision.

---

### Q10. [GATE CSE 2014 • Set 1 • Q43] — Medium

A perfectly balanced **six-stage pipeline** has no pipeline-register cycle-time overhead.

For a given program, **25% of the instructions incur two stall cycles**.

What speedup does this pipeline achieve over the corresponding non-pipelined implementation?

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 3 • Exercise 3.6 • p. 276] — CLASS DISCUSSION — Medium

Using the two-iteration speculative schedule from Exercise 3.5, compute the **percentage of cycles in which the processor dispatches an instruction**.

Explain whether the dispatch bandwidth, true dependences, branch resolution, or commit serialization is the dominant reason that the utilization is below 100%.

---

### Q12. [BOOK • Chapter 3 • Exercise 3.7 • p. 276] — CLASS DISCUSSION — High

For the schedule developed in Exercise 3.5, regard a destination register as **live from the cycle in which its producing instruction commits until the last dependent instruction is dispatched**.

Determine the live-cycle ranges of:

```text
x1, x2, f2, f4, f6
```

Show how overlapping iterations alter register lifetimes and identify which values must coexist.

---

### Q13. [BOOK • Chapter 3 • Exercise 3.8 • p. 276] — CLASS DISCUSSION — High

Using the register lifetimes obtained in Exercise 3.7, determine whether the loop contains any **name dependences**—WAR or WAW—that can restrict the dynamically reordered execution.

For each dependence you identify, distinguish it from a true RAW dependence and explain whether register renaming can remove it.

---

### Q14. [BOOK • Chapter 3 • Exercise 3.9 • p. 276] — CLASS DISCUSSION — High

Modify the speculative out-of-order processor so that it supports:

- **dual issue**,
- **dual commit**,
- an effectively unlimited instruction window.

Schedule two iterations of the Newton–Raphson loop and determine the **average cycles per iteration**.

Your schedule must show which pairs of instructions can issue together and which dependencies or resources prevent a second issue.

---

### Q15. [BOOK • Chapter 3 • Exercise 3.10 • p. 276] — CLASS DISCUSSION — Medium

For the dual-issue schedule from Exercise 3.9, calculate the **percentage of available issue slots actually used**.

Compare this value with the single-issue utilization from the earlier exercises and explain why doubling issue width does not necessarily double performance.

---


# Week 3 — Deeper Pipelines, Compiler ILP, Register Pressure, and Dynamic Scheduling

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2014 • Set 3 • Q43] — High

An original five-stage pipeline has:

| Stage | Latency |
|---|---:|
| IF | 1 ns |
| ID/RF | 2.2 ns |
| EX | 2 ns |
| MEM | 1 ns |
| WB | 0.75 ns |

A redesign splits ID/RF into three stages, each of latency \(2.2/3\) ns, and splits EX into two 1 ns stages, giving an eight-stage pipeline.

A program contains **20% branches**.

- In the old pipeline, a branch supplies the next PC at the end of EX.
- In the new pipeline, it supplies the next PC at the end of EX2.
- After fetching a branch, IF stops until the next PC is available.
- Non-branch instructions have average CPI 1 in both designs.

If program execution times in the old and new designs are \(P\) and \(Q\), respectively, find \(P/Q\).

---

### Q2. [GATE CSE 2014 • Set 3 • Q9] — Medium

Assume pipeline registers have zero latency.

| Processor | Pipeline-stage latencies |
|---|---|
| P1 | 1 ns, 2 ns, 2 ns, 1 ns |
| P2 | 1 ns, 1.5 ns, 1.5 ns, 1.5 ns |
| P3 | 0.5 ns, 1 ns, 1 ns, 0.6 ns, 1 ns |
| P4 | 0.5 ns, 0.5 ns, 1 ns, 1 ns, 1.1 ns |

Which processor has the **highest peak clock frequency**?

1. P1
2. P2
3. P3
4. P4

---

### Q3. [GATE CSE 2013 • Q45] — High

A five-stage pipeline has:

```text
FI → DI → FO → EI → WO
```

The corresponding combinational delays are:

```text
5 ns, 7 ns, 10 ns, 8 ns, 6 ns
```

Each intermediate storage buffer adds **1 ns**.

A program consists of `I1, I2, ..., I12`. `I4` is the only branch and its target is `I9`. There is **no branch prediction**, and the branch is taken.

How much time is needed to complete the program?

1. 132 ns
2. 165 ns
3. 176 ns
4. 328 ns

---

### Q4. [GATE CSE 2011 • Q41] — Medium

A four-stage instruction pipeline has combinational stage delays:

```text
S1 = 5 ns
S2 = 6 ns
S3 = 11 ns
S4 = 8 ns
```

A pipeline register of **1 ns** is required after every stage, including after S4.

Under ideal steady-state operation, what is the approximate speedup over the corresponding non-pipelined implementation?

1. 4.0
2. 2.5
3. 1.1
4. 3.0

---

### Q5. [GATE CSE 2010 • Q33] — High

A five-stage pipeline has:

```text
IF → ID → OF → PO → WO
```

IF, ID, OF, and WO each require one cycle. PO requires:

- 1 cycle for ADD/SUB,
- 3 cycles for MUL,
- 6 cycles for DIV.

Operand forwarding is used.

Execute:

```text
t0: MUL R2, R0, R1
t1: DIV R5, R3, R4
t2: ADD R2, R5, R2
t3: SUB R5, R2, R6
```

How many clock cycles are required?

1. 13
2. 15
3. 17
4. 19

---

### Q6. [GATE CSE 2009 • Q28] — High

A four-stage pipeline has stages \(S_1,S_2,S_3,S_4\). The number of cycles required by each instruction in each stage is:

| Instruction | S1 | S2 | S3 | S4 |
|---|---:|---:|---:|---:|
| I1 | 2 | 1 | 1 | 1 |
| I2 | 1 | 3 | 2 | 2 |
| I3 | 2 | 1 | 1 | 3 |
| I4 | 1 | 2 | 2 | 2 |

The following loop executes twice:

```text
for i = 1 to 2:
    I1
    I2
    I3
    I4
```

Assuming the normal interstage ordering constraints of an instruction pipeline, determine the total number of cycles required.

1. 16
2. 23
3. 28
4. 30

---

### Q7. [GATE CSE 2017 • Set 1 • Q52] — Medium

Consider the expression

\[
(a-1)\times\left(\frac{b+c}{3}+d\right).
\]

The target is a load-store architecture:

- arithmetic instructions operate only on registers and immediate constants;
- only load/store instructions access memory.

Assuming optimal code generation and **no spilling**, what is the **minimum number of registers** needed to evaluate the expression?

---

### Q8. [GATE CSE 2013 • Q48] — High

Consider:

```c
c = a + b;
d = c * a;
e = c + a;
x = c * c;

if (x > a) {
    y = a * a;
} else {
    d = d * d;
    e = e * e;
}
```

The target processor permits arithmetic only on registers, with at most two source registers and one destination register per instruction. All variables are dead after this fragment.

The ISA exposes only **two registers**. The compiler may use **code motion** provided program semantics are preserved.

What is the **minimum number of register spills** required?

1. 0
2. 1
3. 2
4. 3

---

### Q9. [GATE CSE 2013 • Q49] — High

Use the same program fragment as in Question 8. Assume optimal register allocation but no transformations other than those permitted in the question.

What is the **minimum number of registers** required to compile the fragment **without any spilling**?

1. 3
2. 4
3. 5
4. 6

---

### Q10. [GATE CSE 2008 • Q36] — Medium

Consider the following statements about pipeline-hazard techniques:

I. Bypassing can handle all RAW hazards.  
II. Register renaming can eliminate all register-carried WAR hazards.  
III. Dynamic branch prediction can eliminate every control-hazard penalty.

Which statements are **not true**?

1. I and II only
2. I and III only
3. II and III only
4. I, II, and III

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 3 • Exercise 3.12 • pp. 276–278] — CLASS DISCUSSION — High

A parallel list-search application uses **four threads**, each scanning an equal contiguous part of the search range. Its hot loop is:

```asm
loop: lw     x1,0(x16)
      lw     x2,8(x16)
      lw     x3,16(x16)
      lw     x4,24(x16)
      lw     x5,32(x16)
      lw     x6,40(x16)
      lw     x7,48(x16)
      lw     x8,56(x16)
      beq    x9,x1,match0
      beq    x9,x2,match1
      beq    x9,x3,match2
      beq    x9,x4,match3
      beq    x9,x5,match4
      beq    x9,x6,match5
      beq    x9,x7,match6
      beq    x9,x8,match7
      daddiu x16,x16,#64
      blt    x16,x17,loop
```

Assume:

- all four threads start together after a barrier;
- all processors are superscalar and in-order;
- a load or branch introduces a fixed **3-cycle stall** in its thread;
- the first L1 miss occurs only after the first two loop iterations;
- none of the equality branches is taken;
- the final `blt` is always taken for the iterations studied;
- thread selection is round-robin.

Compare:

- **Processor A:** simultaneous multithreading, up to 2 instructions/cycle drawn from two threads;
- **Processor B:** fine-grained multithreading, up to 4 instructions/cycle from one thread, switching threads whenever that thread stalls;
- **Processor C:** coarse-grained multithreading, up to 8 instructions/cycle from one thread, switching only on an L1 miss.

Determine how many cycles each processor needs to complete the **first two iterations** of the loop for all required threads, and explain why the best architecture differs by stall type.

---

### Q12. [BOOK • Chapter 3 • Exercise 3.13 • pp. 278–280] — CLASS DISCUSSION — High

The following DAXPY loop computes \(Y=aX+Y\) for 100 double-precision elements:

```asm
addi   x4,x1,#800
foo:   fld    F2,0(x1)
       fmul.d F4,F2,F0
       fld    F6,0(x2)
       fadd.d F6,F4,F6
       fsd    F6,0(x2)
       addi   x1,x1,#8
       addi   x2,x2,#8
       sltu   x3,x1,x4
       bnez   x3,foo
```

Use these producer-to-consumer latencies:

| Producer | Consumer | Latency |
|---|---|---:|
| FP multiply | FP ALU operation | 6 cycles |
| FP add | FP ALU operation | 4 cycles |
| FP multiply | FP store | 5 cycles |
| FP add | FP store | 4 cycles |
| Integer op or load | Any dependent instruction | 2 cycles |

Assume complete bypassing and a one-cycle delayed branch resolved in ID.

Perform **all parts of the exercise**:

**(a)** For a single-issue processor, show the loop before and after compiler scheduling. Mark all stalls/idle slots, compute cycles per result element in both cases, and determine how much faster the unscheduled processor's clock would need to be to match the compiler-scheduled code.

**(b)** Still assuming single issue, unroll the loop sufficiently to eliminate pipeline stalls through scheduling, collapsing redundant loop-overhead operations. Show the unrolled schedule, the minimum useful unroll factor, and cycles per result element.

**(c)** Complete the exercise's multiple-issue/static-scheduling extension using the same dependencies and latencies: schedule the unrolled work for the specified issue capability, report achieved issue-slot utilization/cycles per element, and identify the resource/dependence limit that prevents ideal issue rate.

---

### Q13. [BOOK • Chapter 3 • Exercise 3.14 • pp. 279–280] — CLASS DISCUSSION — High

Use the DAXPY code and functional-unit assumptions from Exercise 3.13, but now analyze **dynamic scheduling**.

The processor has:

| Functional resource | Units | Reservation stations | Execution latency |
|---|---:|---:|---:|
| Integer unit | 1 | 5 | 1 cycle |
| FP adder | 1 | 3 | 10 cycles |
| FP multiplier | 1 | 2 | 15 cycles |

Additional assumptions:

- stages are IF, ID, DIS, EX, WB;
- loads take one EX cycle;
- dispatch and writeback each take one cycle;
- there are five load buffers and five store buffers;
- results are broadcast after execution completes;
- dependent instructions may use a result according to the timing rules of the exercise.

**(a)** For a **single-issue** processor, construct a table for **three iterations** showing, for every instruction, dispatch cycle, execution cycle(s), memory-access cycle where applicable, completion/result-availability cycle, and the cause of any waiting/stall. The initial setup instruction may be ignored.

**(b)** Repeat for a **two-issue processor with a fully pipelined floating-point unit** and compare the bottleneck with part (a).

---

### Q14. [BOOK • Chapter 3 • Exercise 3.15 • p. 280] — CLASS DISCUSSION — High

Assume a dynamically scheduled processor using the functional-unit configuration and latencies of Exercise 3.14, but the result network can **broadcast only one functional-unit result per cycle** to waiting instructions.

Construct an instruction sequence of **no more than 10 instructions** that forces the processor to stall because two or more results become ready for broadcast in the same cycle.

Show the intended execution timing and identify the exact cycle and instructions involved in the structural hazard.

---

### Q15. [BOOK • Chapter 3 • Exercise 3.16 • pp. 280–281] — CLASS DISCUSSION — High

Compare two branch predictors with equal storage:

- a **(1,2) correlating predictor** capable of tracking four branches;
- a **local two-level predictor** with the same total storage but capable of tracking only two branches at once.

Use the initial predictor states specified by Exercise 3.16 and the branch stream:

| Branch PC | Outcome |
|---:|:---:|
| 454 | T |
| 543 | NT |
| 777 | NT |
| 543 | NT |
| 777 | NT |
| 454 | T |
| 777 | NT |
| 454 | T |
| 543 | T |

For **each predictor**, show for every dynamic branch:

- history/state used for lookup,
- table entry selected,
- prediction,
- actual result,
- counter/history update.

Then calculate the final misprediction rate of each predictor and explain the aliasing-versus-history-capacity trade-off exposed by the sequence.

> **Required textbook material:** the initial correlating/local predictor state tables printed with Exercise 3.16 on pp. 280–281.

---


# Week 4 — Control Hazards, Delay Slots, Memory Disambiguation, and Advanced ILP

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2008 • Q76] — Medium

Delayed branching is used to reduce control-hazard cost.

For a delayed **conditional** branch, regardless of whether the branch is ultimately taken, which statement is always true?

1. The instruction immediately following the branch in memory is executed.
2. The first instruction on the ordinary fall-through path is always executed.
3. The first instruction at the taken target is always executed.
4. A branch necessarily takes longer to execute than every non-branch instruction.

---

### Q2. [GATE CSE 2008 • Q77] — High

A processor has **one branch-delay slot**. Consider:

```text
I1: ADD   R2 ← R7 + R8
I2: SUB   R4 ← R5 - R6
I3: ADD   R1 ← R2 + R3
I4: STORE Memory[R4] ← R1
    BRANCH to Label if R1 == 0
```

Without otherwise modifying the program, which one of `I1`, `I2`, `I3`, or `I4` can be moved into the branch-delay slot while preserving program correctness?

1. I1
2. I2
3. I3
4. I4

---

### Q3. [GATE CSE 2007 • Q37] — High

A processor has four pipeline stages:

```text
IF → ID/operand fetch → EX → WB
```

IF, ID, and WB each take one clock cycle. EX takes:

- 1 cycle for ADD/SUB,
- 3 cycles for MUL.

Operand forwarding is available.

Execute:

```text
ADD R2, R1, R0    ; R2 ← R1 + R0
MUL R4, R3, R2    ; R4 ← R3 × R2
SUB R6, R5, R4    ; R6 ← R5 - R4
```

How many clock cycles are required?

1. 7
2. 8
3. 10
4. 14

---

### Q4. [GATE CSE 2006 • Q42] — Medium

A CPU has a **five-stage pipeline** and runs at **1 GHz**.

- Instruction fetch is stage 1.
- A conditional branch computes its target and condition in stage 3.
- Once a conditional branch is fetched, the processor stops fetching new instructions until the branch outcome is known.

A program executes \(10^9\) instructions, **20% of which are conditional branches**. Apart from the branch-induced fetch stalls, assume an average of one cycle per instruction.

What is the program execution time?

1. 1.0 s
2. 1.2 s
3. 1.4 s
4. 1.6 s

---

### Q5. [GATE CSE 2005 • Q68] — High

A processor uses:

```text
IF → RD → EX → MA → WB
```

Each stage takes one cycle. Consider:

```text
I1: L  R0, loc1
I2: A  R0, R0
I3: S  R2, R0
```

where the instructions have their conventional load/arithmetic/store data dependencies.

Determine the number of cycles from the fetch of I1 until the sequence completes.

1. 8
2. 10
3. 12
4. 15

---

### Q6. [GATE CSE 2004 • Q69] — Medium

A four-stage pipeline has combinational delays:

```text
150 ns, 120 ns, 160 ns, 140 ns
```

Every interstage pipeline register contributes **5 ns**. The processor uses a constant clock period determined by the slowest pipeline stage plus register overhead.

How much time is required to process **1000 data items**?

1. 120.4 μs
2. 160.5 μs
3. 165.5 μs
4. 590 μs

---

### Q7. [GATE CSE 2003 • Q10] — Medium

For a pipelined CPU with a **single ALU**, consider the following situations:

I. Instruction \(j+1\) uses the result produced by instruction \(j\) as an operand.  
II. A conditional jump instruction is executed.  
III. Instructions \(j\) and \(j+1\) require the ALU at the same time.

Which of the above can cause a pipeline hazard?

1. I and II only
2. II and III only
3. III only
4. I, II and III

### Q8. [GATE CSE 2001 • Q12] — High

A five-stage processor uses:

```text
IF → ID → EX → MEM → WB
```

Register-file writes occur in the first half of a clock cycle and reads occur in the second half.

Consider:

```text
I1: sub r2,r3,r4       ; r2 = r3 - r4
I2: sub r4,r2,r3       ; r4 = r2 - r3
I3: sw  r2,100(r1)     ; M[r1+100] = r2
I4: sub r3,r4,r2       ; r3 = r4 - r2
```

For the instruction sequence above:

1. List all data dependencies among the instructions.
2. Identify which of those dependencies create pipeline hazards in this five-stage pipeline.
3. Determine whether forwarding can eliminate all of the resulting stalls; justify any case that still requires a stall.

---

### Q9. [GATE CSE 2000 • Q12] — High

A five-stage pipeline has **2 ns per stage**, and every instruction passes through all five stages.

Branches are initially handled conservatively: the processor does not fetch the next instruction until the branch instruction completes.

**(a)** If **20% of all instructions are branches**, find the average instruction execution time.

**(b)** Now suppose **80% of branch instructions are conditional**, and **50% of those conditional branches are taken**. If a not-taken conditional branch permits useful fall-through instructions to overlap instead of waiting for branch completion, determine the new average instruction execution time.

Keep the pipeline-fill/steady-state assumptions consistent across both parts.

---

### Q10. [GATE CSE 1999 • Q13] — High

A four-stage pipeline has stages `F, D, E, W`. Different instructions occupy stages for different numbers of cycles:

| Instruction | F | D | E | W |
|---|---:|---:|---:|---:|
| I1 | 1 | 2 | 1 | 1 |
| I2 | 1 | 2 | 2 | 1 |
| I3 | 2 | 1 | 3 | 2 |
| I4 | 1 | 3 | 2 | 1 |
| I5 | 1 | 2 | 1 | 2 |

Assuming normal pipeline ordering and exclusive occupancy of a stage by one instruction at a time, determine the total number of clock cycles required to execute `I1` through `I5`.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 3 • Exercise 3.17 • p. 281] — CLASS DISCUSSION — High

A deeply pipelined processor adds a **branch-target buffer (BTB)** for conditional branches.

Assume:

- branch misprediction penalty = 4 cycles;
- BTB miss penalty = 3 cycles;
- BTB hit rate = 90%;
- branch-prediction accuracy = 90%;
- conditional branches = 15% of instructions;
- base CPI without branch stalls = 1.

Compare this processor with a design that instead incurs a **fixed 2-cycle penalty on every branch**.

Calculate the speedup from the BTB/predictor design and identify which of hit rate, accuracy, or penalty has the greatest leverage on performance under the given assumptions.

---

### Q12. [BOOK • Chapter 3 • Exercise 3.18 • pp. 281–282] — CLASS DISCUSSION — High

A BTB distinguishes conditional and unconditional branches.

For a conditional branch, its entry stores the branch target. For an unconditional branch, assume the BTB can store/use the **target instruction** in a way that supports branch folding.

The penalties are:

- correct prediction: 0 cycles;
- incorrect prediction: 2 cycles;
- BTB miss: 2 cycles.

**(a)** Determine the penalty when an unconditional branch is found in the BTB and explain why its treatment differs from that of an ordinary predicted-taken conditional branch.

**(b)** Suppose unconditional branches account for **5% of instructions** and the BTB hit rate is **90%**. Quantify the performance improvement due to branch folding, then determine the **minimum BTB hit rate** for which the folding mechanism still provides a net performance gain under the exercise assumptions.

---

### Q13. [BOOK • Chapter 3 • Exercise 3.19 • p. 282] — CLASS DISCUSSION — High

The following loop performs part of a sparse matrix-vector computation using CSR-like arrays:

```asm
loop: fld    f0,0(x1)       # val[i]
      lw     x3,0(x2)       # col[i]
      slli   x3,x3,3
      add    x4,x10,x3
      fld    f2,0(x4)       # x[col[i]]
      fmac.d f4,f0,f2
      addi   x1,x1,8
      addi   x2,x2,8
      addi   x5,x5,1
      bne    x5,x11,skip
      fsd    f4,0(x6)
      addi   x7,x7,8
      beq    x7,x12,done
      addi   x6,x6,8
      lw     x11,0(x7)
      fli    f4,0
skip: b      loop
```

Answer both parts:

**(a)** Identify the loads for which **speculative memory disambiguation** may be needed. Determine whether a RAW dependence through memory can occur and explain what addresses/older stores must be compared.

**(b)** Analyze the likely predictability of the `bne` near the row-end test. Relate predictor behavior to CSR row-length patterns and explain why a simple predictor may or may not perform well.

---

### Q14. [BOOK • Chapter 3 • Exercise 3.20 • p. 282] — CLASS DISCUSSION — High

The loop below uses Horner's method to evaluate a polynomial simultaneously for two input values in `f6` and `f8`:

```asm
loop: fld    f0,0(x1)
      fmul.d f2,f6,f10
      fadd.d f10,f2,f0
      fmul.d f2,f8,f12
      fadd.d f12,f2,f0
      addi   x1,x1,8
      bne    x1,x2,loop
```

Unroll the loop by a factor of **2**.

Rename floating-point registers as necessary so that artificial name dependences introduced by register reuse do not reduce performance. Present:

1. the complete unrolled instruction sequence;
2. the renamed registers used for each iteration;
3. the **final architectural-to-physical/logical register map** after the unrolled body.

---

### Q15. [BOOK • Chapter 3 • Exercise 3.21 • pp. 282–283] — CLASS DISCUSSION — High

Consider:

```asm
add x8,x10,x11
add x6,x8,x9
add x3,x6,x7
sw  x2,0(x3)
lw  x4,0(x5)
```

Assume:

- single-issue out-of-order execution;
- one integer functional unit with **3-cycle latency**;
- one load/store unit with **4-cycle latency**;
- an instruction may issue in the same cycle in which its last dependency completes;
- the store and load addresses are known not to refer to the same memory location.

Determine the execution time in cycles for:

1. a processor **without load bypassing and without speculative memory disambiguation**;
2. a processor **with load bypassing and speculative memory disambiguation**.

State any additional timing assumptions required, construct both schedules, and quantify the benefit of the more aggressive memory-dependence machinery.

---

# Organizer Source Ledger

## Textbook source

Hennessy, Patterson, and Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 3, **Instruction-Level Parallelism and Its Exploitation**, Case Studies and Exercises by Jason D. Bakos, pp. 275–283.

### Selected textbook exercises

| Week-Q | Exercise | Page(s) | Principal topic |
|---|---:|---:|---|
| W1-Q11 | 3.1 | 275 | In-order scheduling and data-hazard stalls |
| W1-Q12 | 3.2 | 275 | Issue utilization |
| W1-Q13 | 3.3 | 275 | Out-of-order dynamic scheduling |
| W1-Q14 | 3.4 | 275 | Dispatch utilization |
| W1-Q15 | 3.5 | 276 | Accurate branch speculation across iterations |
| W2-Q11 | 3.6 | 276 | Dispatch utilization with speculation |
| W2-Q12 | 3.7 | 276 | Register lifetimes |
| W2-Q13 | 3.8 | 276 | WAR/WAW name dependences |
| W2-Q14 | 3.9 | 276 | Dual issue and dual commit |
| W2-Q15 | 3.10 | 276 | Issue-slot utilization |
| W3-Q11 | 3.12 | 276–278 | SMT vs fine/coarse-grained multithreading |
| W3-Q12 | 3.13 | 278–280 | DAXPY compiler scheduling and loop unrolling |
| W3-Q13 | 3.14 | 279–280 | Dynamic scheduling and functional units |
| W3-Q14 | 3.15 | 280 | Result-broadcast structural hazard |
| W3-Q15 | 3.16 | 280–281 | Correlating vs local branch predictors |
| W4-Q11 | 3.17 | 281 | BTB performance |
| W4-Q12 | 3.18 | 281–282 | Branch folding |
| W4-Q13 | 3.19 | 282 | Memory disambiguation and branch predictability |
| W4-Q14 | 3.20 | 282 | Loop unrolling and register renaming |
| W4-Q15 | 3.21 | 282–283 | Load bypassing and speculative disambiguation |

## GATE source ledger

**Current official GATE archive:** https://gate2026.iitg.ac.in/download.html

For 2020–2026 entries below, a direct official paper URL is retained. For 2007–2019, the current official IIT Guwahati archive is used together with a GateOverflow indexed-question cross-check. Pre-2007 papers are not exposed through that current official archive, so those entries remain explicitly marked for later official-scan verification.

| Week-Q | GATE CSE source | Official source | Indexed cross-check |
|---|---|---|---|
| W1-Q1 | 2026 Set 1 Q50 | https://gate2026.iitg.ac.in/doc/download/2026/QPs/CS1.pdf | https://gateoverflow.in/523030/gate-cse-2026-set-1-question-50 |
| W1-Q2 | 2026 Set 2 Q47 | https://gate2026.iitg.ac.in/doc/download/2026/QPs/CS2.pdf | https://gateoverflow.in/523099/gate-cse-2026-set-2-question-47 |
| W1-Q3 | 2024 Set 1 Q20 | https://gate2026.iitg.ac.in/doc/download/2024/CS124S5.pdf | https://gateoverflow.in/422822/gate-cse-2024-set-1-question-20 |
| W1-Q4 | 2024 Set 2 Q21 | https://gate2026.iitg.ac.in/doc/download/2024/CS224S6.pdf | https://gateoverflow.in/422876/gate-cse-2024-set-2-question-21 |
| W1-Q5 | 2024 Set 2 Q48 | https://gate2026.iitg.ac.in/doc/download/2024/CS224S6.pdf | https://gateoverflow.in/422849/gate-cse-2024-set-2-question-48 |
| W1-Q6 | 2023 Q23 | https://gate2026.iitg.ac.in/doc/download/2023/cs_2023.pdf | https://gateoverflow.in/399288/gate-cse-2023-question-23 |
| W1-Q7 | 2022 Q51 | https://gate2026.iitg.ac.in/doc/download/2022/cs_2022.pdf | https://gateoverflow.in/371885/gate-cse-2022-question-51 |
| W1-Q8 | 2021 Set 1 Q53 | https://gate2026.iitg.ac.in/doc/download/2021/cs_2021.pdf | https://gateoverflow.in/357398/gate-cse-2021-set-1-question-53 |
| W1-Q9 | 2021 Set 2 Q53 | https://gate2026.iitg.ac.in/doc/download/2021/cs_2021.pdf | https://gateoverflow.in/357484/gate-cse-2021-set-2-question-53 |
| W1-Q10 | 2020 Q43 | https://gate2026.iitg.ac.in/doc/download/2020/cs_2020.pdf | https://gateoverflow.in/333188/gate-cse-2020-question-43 |
| W2-Q1 | 2018 Q50 | Official archive above | https://gateoverflow.in/204125/gate-cse-2018-question-50 |
| W2-Q2 | 2017 Set 1 Q50 | Official archive above | https://gateoverflow.in/118719/gate-cse-2017-set-1-question-50 |
| W2-Q3 | 2016 Set 1 Q32 | Official archive above | https://gateoverflow.in/39691/gate-cse-2016-set-1-question-32 |
| W2-Q4 | 2016 Set 2 Q33 | Official archive above | https://gateoverflow.in/39580/gate-cse-2016-set-2-question-33 |
| W2-Q5 | 2016 Set 2 Q30 | Official archive above | https://gateoverflow.in/39627/gate-cse-2016-set-2-question-30 |
| W2-Q6 | 2015 Set 1 Q38 | Official archive above | https://gateoverflow.in/8288/gate-cse-2015-set-1-question-38 |
| W2-Q7 | 2015 Set 2 Q44 | Official archive above | https://gateoverflow.in/8218/gate-cse-2015-set-2-question-44 |
| W2-Q8 | 2015 Set 3 Q47 | Official archive above | https://gateoverflow.in/8556/gate-cse-2015-set-3-question-47 |
| W2-Q9 | 2015 Set 3 Q51 | Official archive above | https://gateoverflow.in/8560/gate-cse-2015-set-3-question-51 |
| W2-Q10 | 2014 Set 1 Q43 | Official archive above | https://gateoverflow.in/1921/gate-cse-2014-set-1-question-43 |
| W3-Q1 | 2014 Set 3 Q43 | Official archive above | https://gateoverflow.in/2077/gate-cse-2014-set-3-question-43 |
| W3-Q2 | 2014 Set 3 Q9 | Official archive above | https://gateoverflow.in/2043/gate-cse-2014-set-3-question-9 |
| W3-Q3 | 2013 Q45 | Official archive above | https://gateoverflow.in/330/gate-cse-2013-question-45 |
| W3-Q4 | 2011 Q41 | Official archive above | https://gateoverflow.in/2143/gate-cse-2011-question-41 |
| W3-Q5 | 2010 Q33 | Official archive above | https://gateoverflow.in/2207/gate-cse-2010-question-33 |
| W3-Q6 | 2009 Q28 | Official archive above | https://gateoverflow.in/1314/gate-cse-2009-question-28 |
| W3-Q7 | 2017 Set 1 Q52 | Official archive above | https://gateoverflow.in/118746/gate-cse-2017-set-1-question-52 |
| W3-Q8 | 2013 Q48 | Official archive above | https://gateoverflow.in/1556/gate-cse-2013-question-48 |
| W3-Q9 | 2013 Q49 | Official archive above | https://gateoverflow.in/43293/gate-cse-2013-question-49 |
| W3-Q10 | 2008 Q36 | Official archive above | https://gateoverflow.in/447/gate-cse-2008-question-36 |
| W4-Q1 | 2008 Q76 | Official archive above | https://gateoverflow.in/496/gate-cse-2008-question-76 |
| W4-Q2 | 2008 Q77 | Official archive above | https://gateoverflow.in/43487/gate-cse-2008-question-77 |
| W4-Q3 | 2007 Q37 | Official archive above | https://gateoverflow.in/1235/gate-cse-2007-question-37-isro2009-37 |
| W4-Q4 | 2006 Q42 | **Official paper not exposed in current IITG archive** | https://gateoverflow.in/1818/gate-cse-2006-question-42 |
| W4-Q5 | 2005 Q68 | **Official paper not exposed in current IITG archive** | https://gateoverflow.in/1391/gate-cse-2005-question-68 |
| W4-Q6 | 2004 Q69 | **Official paper not exposed in current IITG archive** | https://gateoverflow.in/1063/gate-cse-2004-question-69 |
| W4-Q7 | 2003 Q10 | **Official paper not exposed in current IITG archive** | https://gateoverflow.in/901/gate-cse-2003-question-10-isro-dec2017-41 |
| W4-Q8 | 2001 Q12 | **Official paper not exposed in current IITG archive** | https://gateoverflow.in/753/gate-cse-2001-question-12 |
| W4-Q9 | 2000 Q12 | **Official paper not exposed in current IITG archive** | https://gateoverflow.in/683/gate-cse-2000-question-12 |
| W4-Q10 | 1999 Q13 | **Official paper not exposed in current IITG archive** | https://gateoverflow.in/1512/gate-cse-1999-question-13 |

# Validation Summary

| Check | Result |
|---|---:|
| Total questions | **60** |
| GATE CSE PYQs | **40** |
| Complete textbook exercises | **20** |
| Q1–Q10 are GATE in every week | **Yes** |
| Q11–Q15 are `CLASS DISCUSSION` in every week | **Yes** |
| Easy / purely definitional selections | **0** |
| External image assets required | **0** |
| Direct official-paper URLs | **10 / 40** |
| Additional GATE entries covered by current official 2007–2019 archive | **23 / 40** |
| Current official-source coverage | **33 / 40** |
| GateOverflow indexed cross-checks | **40 / 40** |
| Pre-2007 entries requiring later official-scan verification | **7** |
| Book exercises used | **3.1–3.10, 3.12–3.21** |
| Book exercises split into separate questions | **0** |

**No solutions, hints, or answer key are included.**
