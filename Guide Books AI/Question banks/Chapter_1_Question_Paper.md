# MTCS 102 — Chapter 1 Question Paper

## Fundamentals of Quantitative Design and Analysis

**Primary text:** *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 1  
**Format:** 4 weeks × 15 questions = 60 questions  
**Weekly split:** Questions 1–10 = GATE CSE previous-year questions; Questions 11–15 = complete textbook exercises for class discussion  
**Solutions:** Not included  

> **Question-counting rule:** One source question/exercise is treated as one question even when it contains several subparts. The textbook exercises below are therefore kept intact rather than splitting their subparts into separate questions.
>
> **Figures:** GATE figures needed for the selected questions are included in the `assets/` folder. A few textbook exercises explicitly depend on figures in the textbook (Figures 1.22, 1.26, 1.29, 1.30 and 1.31); those references are preserved exactly so the organizer can use the corresponding textbook page while solving the exercise.

---

# Week 1 — ISA Encoding, Performance, Fabrication Economics and Strong Scaling

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2026 • Set 2 • Master Q44 / CS Q34]

Consider a processor that has 16 general purpose registers and it uses 2-byte instruction format for all its instructions. Variable-sized opcodes are permitted. There are three different types of instructions: M-type, R-type, and C-type. Each M-type instruction has 2 register operands and a 6-bit immediate operand. Each R-type instruction has 3 register operands. Each C-type instruction has a register operand and a 6-bit offset value.

If there are 2 unique M-type opcodes and 7 unique R-type opcodes, which one of the following options gives the maximum number of unique opcodes possible for C-type instructions?

1. 8
2. 4
3. 64
4. 16

### Q2. [GATE CSE 2026 • Set 1 • Master Q14 / CS Q4]

Match each addressing mode in **List I** with a data element or an element of a data structure (in a high-level language) in **List II**.

| List I — Addressing mode | List II — Data element / structure element |
|---|---|
| P. Immediate | 1. Element of an array |
| Q. Indirect | 2. Pointer |
| R. Base with index | 3. Element of a record |
| S. Base with offset/displacement | 4. Constant |

1. P-4, Q-3, R-1, S-2
2. P-4, Q-2, R-1, S-3
3. P-1, Q-4, R-3, S-2
4. P-2, Q-3, R-1, S-4

### Q3. [GATE CSE 2026 • Set 1 • Master Q15 / CS Q5]

Consider a processor P whose instruction set architecture is the load-store architecture. The instruction format is such that the first operand of any instruction is the destination operand.

Which one of the following sequences of instructions corresponds to the high-level language statement `Z = X + Y`?

**Note:** X, Y, and Z are memory operands. R0, R1, and R2 are registers.

1. `ADD Z, X, Y`
2. `LOAD R0, X; ADD Z, R0, Y`
3. `ADD R0, X, Y; STORE Z, R0`
4. `LOAD R0, X; LOAD R1, Y; ADD R2, R0, R1; STORE Z, R2`

### Q4. [GATE CSE 2025 • Set 2 • Master Q61 / CS Q51]

An application executes $6.4 \times 10^8$ instructions in 6.3 seconds. There are four types of instructions, the details of which are given in the table. The duration of a clock cycle in nanoseconds is _________. *(Rounded off to one decimal place.)*

| Instruction type | CPI | Number of instructions executed |
|---|---:|---:|
| Branch | 2 | $2.25 \times 10^8$ |
| Load | 5 | $1.20 \times 10^8$ |
| Store | 4 | $1.65 \times 10^8$ |
| Arithmetic | 3 | $1.30 \times 10^8$ |

### Q5. [GATE CSE 2024 • Set 1 • Master Q55 / CS Q45]

The baseline execution time of a program on a 2 GHz single core machine is 100 nanoseconds (ns). The code corresponding to 90% of the execution time can be fully parallelized. The overhead for using an additional core is 10 ns when running on a multicore system. Assume that all cores in the multicore system run their share of the parallelized code for an equal amount of time.

The number of cores that minimize the execution time of the program is ______.

### Q6. [GATE CSE 2024 • Set 2 • Master Q57 / CS Q47]

A processor with 16 general purpose registers uses a 32-bit instruction format. The instruction format consists of an opcode field, an addressing mode field, two register operand fields, and a 16-bit scalar field. If 8 addressing modes are to be supported, the maximum number of unique opcodes possible for every addressing mode is _________.

### Q7. [GATE CSE 2024 • Set 2 • Master Q61 / CS Q51]

A processor uses a 32-bit instruction format and supports byte-addressable memory access. The ISA of the processor has 150 distinct instructions. The instructions are equally divided into two types, namely R-type and I-type, whose formats are shown below.

**R-type**

`OPCODE | UNUSED | DST Register | SRC Register1 | SRC Register2`

**I-type**

`OPCODE | DST Register | SRC Register | # Immediate value/address`

In the OPCODE, 1 bit is used to distinguish between I-type and R-type instructions and the remaining bits indicate the operation. The processor has 50 architectural registers, and all register fields in the instructions are of equal size.

Let X be the number of bits used to encode the UNUSED field, Y be the number of bits used to encode the OPCODE field, and Z be the number of bits used to encode the immediate value/address field. The value of $X + 2Y + Z$ is ______.

### Q8. [GATE CSE 2023 • Q31]

Consider the given C-code and its corresponding assembly code, with a few operands U1–U4 being unknown. Some useful information as well as the semantics of each unique assembly instruction is annotated as inline comments in the code. The memory is byte-addressable.

```c
// C-code
int a[10], b[10], i;
// int is 32 bit
for(i = 0; i < 10; i++)
    a[i] = b[i] * 8;
```

```text
; r1-r5 are 32-bit integer registers
; initialize r1=0, r2=10
; initialize r3, r4 with base address of a, b
L01: jeq r1, r2, end  ; if(r1==r2) goto end
L02: lw  r5, 0(r4)    ; r5 <- Memory[r4+0]
L03: shl r5, r5, U1   ; r5 <- r5 << U1
L04: sw  r5, 0(r3)    ; Memory[r3+0] <- r5
L05: add r3, r3, U2    ; r3 <- r3+U2
L06: add r4, r4, U3
L07: add r1, r1, 1
L08: jmp U4            ; goto U4
L09: end
```

Which one option is a **CORRECT** replacement for `(U1, U2, U3, U4)`?

1. `(8, 4, 1, L02)`
2. `(3, 4, 4, L01)`
3. `(8, 1, 1, L02)`
4. `(3, 1, 1, L01)`

### Q9. [GATE CSE 2021 • Set 1 • Q55]

Consider the following instruction sequence where registers R1, R2 and R3 are general purpose and `MEMORY[X]` denotes the content at the memory location X.

| Instruction | Semantics | Instruction size (bytes) |
|---|---|---:|
| `MOV R1,(5000)` | $R1 \leftarrow MEMORY[5000]$ | 4 |
| `MOV R2,(R3)` | $R2 \leftarrow MEMORY[R3]$ | 4 |
| `ADD R2,R1` | $R2 \leftarrow R1 + R2$ | 2 |
| `MOV (R3),R2` | $MEMORY[R3] \leftarrow R2$ | 4 |
| `INC R3` | $R3 \leftarrow R3 + 1$ | 2 |
| `DEC R1` | $R1 \leftarrow R1 - 1$ | 2 |
| `BNZ 1004` | Branch if not zero to the given absolute address | 2 |
| `HALT` | Stop | 1 |

Assume that the content of memory location 5000 is 10, and the content of register R3 is 3000. The content of each of the memory locations from 3000 to 3020 is 50. The instruction sequence starts from memory location 1000. All the numbers are in decimal format. Assume that the memory is byte addressable.

After the execution of the program, the content of memory location 3010 is ____________.

### Q10. [GATE CSE 2020 • Q44]

A processor has 64 registers and uses 16-bit instruction format. It has two types of instructions: I-type and R-type. Each I-type instruction contains an opcode, a register name, and a 4-bit immediate value. Each R-type instruction contains an opcode and two register names. If there are 8 distinct I-type opcodes, then the maximum number of distinct R-type opcodes is _______.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 1 • Exercise 1.1] — CLASS DISCUSSION

Figure 1.28 gives hypothetical relevant data that influence the cost of GPU and CPU chips. In the next few exercises you will be exploring the effect of different possible design decisions for these chips.

**Figure 1.28 data, reconstructed as a table:**

| Chip | Die size (mm²) | Estimated defect rate (per cm²) | N | Manufacturing size (nm) | Transistors (billions) | Cores or SMs |
|---|---:|---:|---:|---:|---:|---:|
| GPU A | 800 | 0.011 | 36 | 4 | 80 | 132 |
| GPU B | 2 × 850 | 0.011 | 37 | 4 | 210 | 160 |
| CPU A | 270 | 0.010 | 36 | 8 | 16 | 8 |
| CPU B compute | 12 × 75 | 0.010 | 35 | 5 | 90 | 96 |
| CPU B I/O | 1 × 400 | 0.010 | 34 | 6 | — | — |

**(a)** What is the yield for the GPU A chip per good wafer?

**(b)** What is the yield for the CPU A chip per good wafer?

**(c)** GPU B is made by combining two large chiplets into a single packaged system. What is the yield for each GPU B chiplet per good wafer?

**(d)** CPU B is made by combining a number of different chiplets into a packaged system. In this case there are 12 chiplet copies for compute tasks fabricated using a smaller manufacturing size and one chiplet for I/O tasks using a larger manufacturing size. What is the yield for each of the compute chiplets as well as the yield for the I/O chiplet that comprises CPU B, assuming good wafers?

**(e)** Consider the possibility of manufacturing a single, larger chip for GPU B instead of using chiplets. How would the yield compare in this case? Can you think of other reasons that using chiplets would be a better option?

### Q12. [BOOK • Chapter 1 • Exercise 1.2] — CLASS DISCUSSION

Consider a manufacturing facility for each of the types of chips just discussed. We would like to evaluate how much capacity to dedicate to each type of chip. Imagine that GPU A will make a profit of $1000 per defect-free chip, and CPU A will make a profit of $100 per defect-free chip. For each type of chip, assume that each wafer has a 300 mm diameter. Use the Figure 1.28 data given in Question 11.

**(a)** How much profit do you make on each wafer of GPU A chips?

**(b)** How much profit do you make on each wafer of CPU A chips?

**(c)** If your demand is up to 5000 GPU A chips per month and 3000 CPU A chips per month, and your facility can fabricate 90 wafers a month, how many wafers should you make of each chip?

**(d)** If we assume that each GPU B will make a profit of $2500 per defect-free product (requiring two chiplets), how much profit do you make on each wafer of GPU B chips?

**(e)** If we assume that each CPU B will make a profit of $75,000 per defect-free product (requiring 12 of the chiplets for compute along with one of the chiplets for I/O functions), how many CPU B products can be sold for each wafer of CPU B compute chiplets? How many wafers of CPU B I/O chiplets are needed for each wafer of CPU B compute chiplets? If your facility can fabricate 30 wafers of these chiplets, how many wafers should be for compute chiplets and how many for I/O chiplets to maximize profit? What is this profit?

### Q13. [BOOK • Chapter 1 • Exercise 1.3] — CLASS DISCUSSION

Consider the possibility of increasing the economic viability of large processor chips with poor yields by selling partially functional chips for less instead of discarding them. For some products, different versions of chips (also known as stock keeping units, or SKUs) can be sold that include different numbers of cores. For example, you could sell the CPU A chip with versions that contain 8, 4, 2, and 1 cores on each chip. If all eight cores are defect-free, then it is sold as CPU A₈. Chips with four to seven defect-free cores are sold as CPU A₄, and those with two or three defect-free cores are sold as CPU A₂. For simplification, calculate the yield for a single core as the yield for a chip that is 1/8 the area of the original CPU A chip. Then view that yield as an independent probability of a single core being defect-free. Calculate the yield for each configuration as the probability of the corresponding number of cores being defect-free.

**(a)** What is the yield for a single core being defect-free as well as the yield for CPU A₄, CPU A₂, and CPU A₁?

**(b)** Using your results from (a), determine which chips you think would be worthwhile to package and sell, and explain why.

### Q14. [BOOK • Chapter 1 • Exercise 1.4] — CLASS DISCUSSION

Now we consider a similar approach to improving yields for GPUs by using redundancy. In the case of GPU B assume the product is comprised of two chiplets, each of which contains 85 symmetric multiprocessors (SMs), the equivalent of cores for GPUs. Further assume that to ship products with 160 SMs, 2 functional GPU chiplets are required, each of which can have up to 5 of their SMs with defects. As in the case of problem 1.3 and CPU A, calculate the yield for a single SM as the yield for a chiplet that is 1/85 the area of the original GPU B chiplet. Once again, view that yield as an independent probability of a single SM being defect-free. Calculate the yield for the chiplet as the probability of at least 80 SMs being defect-free.

**(a)** What is the yield for a single SM being defect-free as well as the yield for each GPU B chiplet?

**(b)** Using your results from (a), what is the profit from each wafer of GPU B chiplets? How does this compare to the results from 1.2(d)?

**(c)** A similar analysis could be done for each GPU A chip, assuming there are 144 SMs on each die, and that chips with at least 132 functional SMs could be sold. How would this redundancy affect the profit for each wafer? How would this affect the answer for 1.2(c)?

### Q15. [BOOK • Chapter 1 • Exercise 1.5] — CLASS DISCUSSION

When parallelizing an application, the ideal speedup would be speeding the application up by the number of processors. In practice, this is limited by two things: the percentage of the application that can be parallelized and the impact of overheads such as the cost of communication. Amdahl’s Law takes into account the former but not the latter.

**(a)** What is the speedup with N processors if [80%, 90%, 95%, 99%] of the application is parallelizable, ignoring the cost of communication?

**(b)** What is the speedup with 32 processors if, for every processor added, the communication overhead is [0.5%, 1%, 2%, 5%] of the original execution time?

**(c)** What is the speedup with 32 processors if, for every time the number of processors is doubled, the communication overhead is increased by [0.5%, 1%, 2%, 5%] of the original execution time?

**(d)** What is the speedup with N processors if, for every time the number of processors is doubled, the communication overhead is increased by [0.5%, 1%, 2%, 5%] of the original execution time?

**(e)** Write the general equation that solves this question: What is the number of processors with the highest speedup in an application in which P% of the original execution time is parallelizable, and, for every time the number of processors is doubled, the communication is increased by [0.5%, 1%, 2%, 5%] of the original execution time?

---

# Week 2 — Instruction Formats, Addressing Modes, Scaling and Availability

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2018 • Q51]

A processor has 16 integer registers `(R0, R1, ..., R15)` and 64 floating point registers `(F0, F1, ..., F63)`. It uses a 2-byte instruction format. There are four categories of instructions: Type-1, Type-2, Type-3, and Type-4.

- Type-1 category consists of four instructions, each with 3 integer register operands (3Rs).
- Type-2 category consists of eight instructions, each with 2 floating point register operands (2Fs).
- Type-3 category consists of fourteen instructions, each with one integer register operand and one floating point register operand (1R + 1F).
- Type-4 category consists of N instructions, each with a floating point register operand (1F).

The maximum value of N is _________.

### Q2. [GATE CSE 2017 • Set 1 • Q49]

Consider a RISC machine where each instruction is exactly 4 bytes long. Conditional and unconditional branch instructions use PC-relative addressing mode with Offset specified in bytes to the target location of the branch instruction. Further, the Offset is always with respect to the address of the next instruction in the program sequence.

```text
i:   add R2,R3,R4
i+1: sub R5,R6,R7
i+2: cmp R1,R9,R10
i+3: beq R1,Offset
```

If the target of the branch instruction is instruction `i`, the decimal value of Offset is ______.

### Q3. [GATE CSE 2017 • Set 1 • Q11]

Consider the C struct defined below:

```c
struct data {
    int marks[100];
    char grade;
    int cnumber;
};
struct data student;
```

The base address of `student` is available in register R1. The field `student.grade` can be accessed efficiently using:

1. Post-increment addressing mode, `(R1)+`
2. Pre-decrement addressing mode, `−(R1)`
3. Register direct addressing mode, `R1`
4. Index addressing mode, `X(R1)`, where X is an offset represented in 2’s complement 16-bit representation

### Q4. [GATE CSE 2016 • Set 2 • Q31]

Consider a processor with 64 registers and an instruction set of size twelve. Each instruction has five distinct fields, namely, opcode, two source register identifiers, one destination register identifier, and twelve-bit immediate value. Each instruction must be stored in memory in a byte-aligned fashion. If a program has 100 instructions, the amount of memory (in bytes) consumed by the program text is _________.

### Q5. [GATE CSE 2014 • Set 1 • Q9]

A machine has a 32-bit architecture, with 1-word long instructions. It has 64 registers, each of which is 32 bits long. It needs to support 45 instructions, which have an immediate operand in addition to two register operands. Assuming that the immediate operand is an unsigned integer, the maximum value of the immediate operand is ____________.

### Q6. [GATE CSE 2014 • Set 1 • Q55]

Consider two processors P₁ and P₂ executing the same instruction set. Assume that under identical conditions, for the same input, a program running on P₂ takes 25% less time but incurs 20% more CPI (clock cycles per instruction) as compared to the program running on P₁. If the clock frequency of P₁ is 1 GHz, then the clock frequency of P₂ (in GHz) is ______.

### Q7. [GATE CSE 2008 • Q33]

Which of the following is/are true of the auto-increment addressing mode?

I. It is useful in creating self-relocating code.  
II. If it is included in an ISA, an additional ALU is required for effective address calculation.  
III. The amount of increment depends on the size of the data item accessed.

1. I only
2. II only
3. III only
4. II and III only

### Q8. [GATE CSE 2008 • Q34]

Which of the following must be true for the RFE (Return From Exception) instruction on a general purpose processor?

I. It must be a trap instruction.  
II. It must be a privileged instruction.  
III. An exception cannot be allowed to occur during execution of an RFE instruction.

1. I only
2. II only
3. I and II only
4. I, II and III only

### Q9. [GATE CSE 2007 • Q54]

In a simplified computer the instructions are:

- `OP Rj,Ri`: Perform `Rj OP Ri` and store the result in `Rj`.
- `OP m,Ri`: Perform `val OP Ri` and store the result in `Ri`, where `val` is the content of memory location `m`.
- `MOV m,Ri`: Move `memory[m]` to `Ri`.
- `MOV Ri,m`: Move `Ri` to `memory[m]`.

Only two registers are available, and `OP` is `ADD` or `SUB`.

For the basic block:

```text
t1 = a + b
t2 = c + d
t3 = e - t2
t4 = t1 - t3
```

All operands are initially in memory and the final value must be stored in memory. The minimum number of `MOV` instructions is:

1. 2
2. 3
3. 5
4. 6

### Q10. [GATE CSE 2007 • Q71]

Consider the following program segment. Here R1, R2 and R3 are the general purpose registers.

|  | Instruction | Operation | Instruction size (no. of words) |
|---|---|---|---:|
|  | `MOV R1,(3000)` | $R1 \leftarrow M[3000]$ | 2 |
| LOOP: | `MOV R2,(R3)` | $R2 \leftarrow M[R3]$ | 1 |
|  | `ADD R2,R1` | $R2 \leftarrow R1 + R2$ | 1 |
|  | `MOV (R3),R2` | $M[R3] \leftarrow R2$ | 1 |
|  | `INC R3` | $R3 \leftarrow R3 + 1$ | 1 |
|  | `DEC R1` | $R1 \leftarrow R1 - 1$ | 1 |
|  | `BNZ LOOP` | Branch on not zero | 2 |
|  | `HALT` | Stop | 1 |

Assume that the content of memory location 3000 is 10 and the content of register R3 is 2000. The content of each of the memory locations from 2000 to 2010 is 100. The program is loaded from memory location 1000. All the numbers are in decimal.

Assume that the memory is word addressable. The number of memory references for accessing the data in executing the program completely is:

1. 10
2. 11
3. 20
4. 21

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 1 • Exercise 1.6] — CLASS DISCUSSION

In problem 1.5, the problem size being run in parallel was the same in each case, known as strong scaling. In weak scaling (sometimes referred to as Gustafson’s Law) the problem size increases with the number of processors. Consider the performance, or scaled speedup, of a weakly scaled application considering the percentage of the serial application (baseline) that is parallelized and the cost of communication.

**(a)** What is the scaled speedup with N processors if [80%, 90%, 95%, 99%] of the serial application is parallelizable, ignoring the cost of communication and otherwise assuming perfect scaling with additional processors?

**(b)** What is the scaled speedup with 32 processors if, for every processor added, the communication overhead is [0.5%, 1%, 2%, 5%] of the original execution time?

**(c)** What is the scaled speedup with 32 processors if, for every time the number of processors is doubled, the communication overhead is increased by [0.5%, 1%, 2%, 5%] of the original execution time?

**(d)** What is the scaled speedup with N processors if, for every time the number of processors is doubled, the communication overhead is increased by [0.5%, 1%, 2%, 5%] of the original execution time?

### Q12. [BOOK • Chapter 1 • Exercise 1.7] — CLASS DISCUSSION

As a benchmark for the largest supercomputers in the world, the Top 500 list (`top500.org`) employs High Performance LINPACK (HPL), a dense linear algebra code, to report the obtained floating-point operations per second (FLOPS) achieved along with theoretical peak FLOPS for the system. Similarly, they also use the High Performance Conjugate Gradient (HPCG), a sparse linear algebra code, to report the obtained FLOPS as well. From the June 2024 lists, the top machines averaged about 70% of the peak FLOPS performance for HPL, but less than 2% of the peak FLOPS for HPCG.

**(a)** Can you translate the Top 500 HPL or HPCG results into speedup or scaled speedup? Explain.

**(b)** Compare these Top 500 results to those reported for scientific codes on supercomputers as shown in Figure 1.26.

**(c)** In a 2024 study exploring machine learning training on Frontier, then the top machine on the Top 500 list, researchers reported obtaining 32% to 38% of theoretical operations per second. They also reported scaled speedups (weak scaling) of nearly 100% and speedups (strong scaling) of 87% to 89% for the training codes. How do these results compare to the cases from (a) and (b)?

> **Required textbook material:** Figure 1.26.

### Q13. [BOOK • Chapter 1 • Exercise 1.8] — CLASS DISCUSSION

You have been asked to optimize the performance of a mix of applications on a new processor that includes 64 cores. You will run four applications on this processor, but the resource requirements are not equal. Assume the system and application characteristics listed in Figure 1.29. This problem can be solved for any or all of the independent scenarios A to D in the figure. The percentage of resources in the figure indicates what proportion of the total runtime would be associated with each application (1–4), assuming they are all run serially. Assume that when you parallelize a portion of the program by X, the speedup for that portion is X.

**(a)** How much speedup would result from running application 1 on the entire processor, as compared to running it serially?

**(b)** How much speedup would result from running application 4 on the entire processor, as compared to running it serially?

**(c)** Given the percentage of resources that application 1 requires, if we statically assign it that percentage of the cores, what is the overall speedup if application 1 is run parallelized but everything else is run serially?

**(d)** What is the overall speedup if all four applications are statically assigned some of the cores, proportional to their percentage of resource needs, and all run parallelized?

> **Required textbook material:** Figure 1.29.

### Q14. [BOOK • Chapter 1 • Exercise 1.9] — CLASS DISCUSSION

Availability is the most important consideration for designing servers, followed closely by scalability and throughput.

**(a)** We have a single processor with a failure in time (FIT) of 100. What is the mean time to failure (MTTF) for this system?

**(b)** If it takes one day to get the system running again, what is the availability of the system?

**(c)** Imagine that the government, to cut costs, is going to build a supercomputer out of inexpensive computers rather than expensive, reliable computers. What is the MTTF for a system with 1000 processors? Assume that if one fails, they all fail.

**(d)** The largest supercomputers can have 10,000 or more nodes, each with multiple processors or accelerators. What is the MTTF for a system with 50,000 processors with the same reliability characteristics as above? In this case what is the longest runtime for a job that I can expect to complete before a failure?

### Q15. [BOOK • Chapter 1 • Exercise 1.10] — CLASS DISCUSSION

In a server farm such as those used by cloud vendors, a single failure does not cause the entire system to crash. Instead, it will reduce the number of requests that can be satisfied at any one time.

**(a)** If a company has 10,000 computers, each with an MTTF of 35 days, and it experiences catastrophic failure only if 1/3 of the computers fail, what is the MTTF for the system?

**(b)** Assume that downtimes cost $100,000 an hour and it takes 1 day for the computers to recover after the system crashes. What would the annual losses from downtime cost?

**(c)** Assume that downtimes cost $100,000 an hour and it takes 1 day for the computers to recover after the system crashes. If it costs an extra $1000 per computer to double the MTTF, would this be a good business decision? Assume the computers have a life span of 3 years.

---

# Week 3 — Datapaths, Machine Instructions, Power and Acceleration

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2007 • Q72]

Use the following program and initial conditions:

|  | Instruction | Operation | Instruction size (no. of words) |
|---|---|---|---:|
|  | `MOV R1,(3000)` | $R1 \leftarrow M[3000]$ | 2 |
| LOOP: | `MOV R2,(R3)` | $R2 \leftarrow M[R3]$ | 1 |
|  | `ADD R2,R1` | $R2 \leftarrow R1 + R2$ | 1 |
|  | `MOV (R3),R2` | $M[R3] \leftarrow R2$ | 1 |
|  | `INC R3` | $R3 \leftarrow R3 + 1$ | 1 |
|  | `DEC R1` | $R1 \leftarrow R1 - 1$ | 1 |
|  | `BNZ LOOP` | Branch on not zero | 2 |
|  | `HALT` | Stop | 1 |

Assume that the content of memory location 3000 is 10 and the content of register R3 is 2000. The content of each of the memory locations from 2000 to 2010 is 100. The program is loaded from memory location 1000. All the numbers are in decimal.

Assume that the memory is word addressable. After the execution of this program, the content of memory location 2010 is:

1. 100
2. 101
3. 102
4. 110

### Q2. [GATE CSE 2007 • Q73]

Use the same program and initial conditions as in Question 1.

Assume that the memory is byte addressable and the word size is 32 bits. If an interrupt occurs during the execution of the instruction `INC R3`, what return address will be pushed on to the stack?

1. 1005
2. 1020
3. 1024
4. 1040

### Q3. [GATE CSE 2005 • Q65]

Consider a three-word machine instruction:

`ADD A[R0], @B`

The first operand (destination) `A[R0]` uses indexed addressing mode with R0 as the index register. The second operand (source) `@B` uses indirect addressing mode. A and B are memory addresses residing at the second and third words, respectively. The first word of the instruction specifies the opcode, the index register designation and the source and destination addressing modes. During execution of the `ADD` instruction, the two operands are added and stored in the destination (first operand).

The number of memory cycles needed during the **execution cycle** of the instruction is:

1. 3
2. 4
3. 5
4. 6

### Q4. [GATE CSE 2005 • Q79]

Consider the following data path of a CPU. The ALU, the bus and all the registers in the data path are of identical size. All operations including incrementation of the PC and the GPRs are to be carried out in the ALU. Two clock cycles are needed for memory read operation — the first one for loading address in the MAR and the next one for loading data from the memory bus into the MDR.

![GATE CSE 2005 Q79/Q80 datapath](assets/gate_2005_q79_q80_datapath.png)

The instruction `add R0, R1` has the register transfer interpretation:

$R0 \leftarrow R0 + R1$

The minimum number of clock cycles needed for execution cycle of this instruction is:

1. 2
2. 3
3. 4
4. 5

### Q5. [GATE CSE 2005 • Q80]

Consider the same CPU datapath and assumptions as in Question 4.

![GATE CSE 2005 Q79/Q80 datapath](assets/gate_2005_q79_q80_datapath.png)

The instruction `call Rn, sub` is a two-word instruction. Assuming that PC is incremented during the fetch cycle of the first word of the instruction, its register transfer interpretation is:

$Rn \leftarrow PC + 1$

$PC \leftarrow M[PC]$

The minimum number of CPU clock cycles needed during the execution cycle of this instruction is:

1. 2
2. 3
3. 4
4. 5

### Q6. [GATE CSE 2004 • Q63]

Consider the following program segment.

| Instruction | Operation | Size (words) |
|---|---|---:|
| `MOV R1,5000` | $R1 \leftarrow Memory[5000]$ | 2 |
| `MOV R2,(R1)` | $R2 \leftarrow Memory[(R1)]$ | 1 |
| `ADD R2,R3` | $R2 \leftarrow R2 + R3$ | 1 |
| `MOV 6000,R2` | $Memory[6000] \leftarrow R2$ | 2 |
| `HALT` | Stop | 1 |

Consider memory byte-addressable with word size 32 bits and assume that the program is loaded from location 1000 (decimal). If an interrupt occurs while the CPU has been halted after executing `HALT`, the return address saved in stack is:

1. 1007
2. 1020
3. 1024
4. 1028

### Q7. [GATE CSE 2004 • Q64]

Use the program from Question 6. Assume the following cycle counts:

- Register-to/from-memory transfer: 3 clock cycles
- `ADD` with both operands in registers: 1 clock cycle
- Instruction fetch and decode: 2 clock cycles

The total number of clock cycles required to execute the program is:

1. 29
2. 24
3. 23
4. 20

### Q8. [GATE CSE 2003 • Q48]

Consider the following assembly program:

```text
MOV B,#0       ; B <- 0
MOV C,#8       ; C <- 8
Z: CMP C,#0    ; compare C with 0
   JZ X        ; jump to X if zero flag is set
   SUB C,#1    ; C <- C-1
   RRC A,#1    ; right rotate A through carry by one bit
               ; if initial A and carry are a7...a0 and c0,
               ; after instruction they are c0a7...a1 and a0
   JC Y        ; jump to Y if carry flag is set
   JMP Z
Y: ADD B,#1    ; B <- B+1
   JMP Z
X:
```

If the initial value of register A is A₀, the value of register B after the program execution will be:

1. Number of 0 bits in A₀
2. Number of 1 bits in A₀
3. A₀
4. 8

### Q9. [GATE CSE 2003 • Q49]

Use the program from Question 8. Which instruction inserted at `X` ensures that register A after execution is the same as its initial value?

1. `RRC A,#1`
2. `NOP`
3. `LRC A,#1` — left rotate A through carry by one bit
4. `ADD A,#1`

### Q10. [GATE CSE 2001 • Q2.13]

Consider the following data path of a simple non-pipelined CPU. Registers A, B, A1, A2, MDR, the bus and ALU are 8-bit wide. SP and MAR are 16-bit registers. MUX is $8 \times (2:1)$ and DEMUX is $8 \times (1:2)$. Each memory operation takes 2 CPU clock cycles and uses MAR and MDR. SP can be decremented locally.

![GATE CSE 2001 Q2.13 datapath](assets/gate_2001_q2_13_datapath.png)

The CPU instruction `push r`, where `r = A` or `B`, performs:

$M[SP] \leftarrow r$

$SP \leftarrow SP - 1$

How many CPU clock cycles are required?

1. 2
2. 3
3. 4
4. 5

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 1 • Exercise 1.11] — CLASS DISCUSSION

Cell phones perform very different tasks, including streaming music or video, reading email, and increasingly performing machine learning tasks such as image or speech recognition. Users demand long battery life for cell phones, so reductions in power and energy consumption are critical. In this problem we consider an unrealistic scenario in which the cell phone has no specialized processing units. Instead, it has a quad-core, general-purpose processing unit. Each core uses 0.5 W at full use. For its tasks, the quad-core is 8× as fast as necessary.

**(a)** How much dynamic energy and power are required compared to running at full power? First, suppose that the quad-core operates for 1/8 of the time and is idle for the rest of the time. That is, the clock is disabled for 7/8 of the time, with no leakage occurring during that time. Compare total dynamic energy as well as dynamic power while the core is running.

**(b)** How much dynamic energy and power are required using frequency and voltage scaling? Assume frequency and voltage are both reduced to 1/8 the entire time.

**(c)** Now assume the voltage may not decrease below 50% of the original voltage. This voltage is referred to as the voltage floor, and any voltage lower than that will lose the state. Therefore, while the frequency can keep decreasing, the voltage cannot. What are the dynamic energy and power savings in this case?

**(d)** How much energy is used with a dark silicon approach? This involves creating specialized ASIC hardware for each major task and power gating those elements when not in use. Only one general-purpose core would be provided, and the rest of the chip would be filled with specialized units. For tasks such as email, the one core would operate for 25% of the time and be turned completely off with power gating for the other 75% of the time. During the other 75% of the time, a specialized ASIC unit that requires 20% of the energy of a core would be running for tasks such as speech recognition.

### Q12. [BOOK • Chapter 1 • Exercise 1.12] — CLASS DISCUSSION

As mentioned in Exercise 1.11, cell phones run a wide variety of applications. We’ll make the same assumptions as before, that it is 0.5 W per core.

**(a)** Imagine that 80% of the code is parallelizable. By how much would the frequency and voltage on a single core need to be increased in order to execute at the same speed as the four-way parallelized code?

**(b)** What is the reduction in dynamic energy from using frequency and voltage scaling in (a)?

**(c)** How much energy is used with a dark silicon approach? In this approach all hardware units are power gated, allowing them to turn off entirely (causing no leakage). Specialized ASICs are provided that perform the same computation for 20% of the power as the general-purpose processor. Imagine that each core is power gated. Assume a task such as image recognition requires two ASICs and two cores. How much dynamic energy is required compared to the baseline of running in parallel on four cores?

### Q13. [BOOK • Chapter 1 • Exercise 1.13] — CLASS DISCUSSION

General-purpose processors are optimized for general-purpose computing, so they perform well across a large number of applications. However, once the domain is restricted somewhat, optimized functional units or specialized processors can perform more efficiently. Many machine language operations can be performed concurrently, using specialized hardware like ASICs or graphical processing units (GPUs). This problem explores the trade-offs between a general-purpose processor and a GPU, in terms of performance and cooling. If heat is not removed from the computer efficiently, the fans will blow hot air back onto the computer, not cold air. Note: The differences involve more than just a processor-on-chip — memory and DRAM also come into play. Therefore statistics are at a system level, not a chip level.

**(a)** If a data center spends 70% of its time on workload A and 30% of its time on workload B when running CPUs, what is the speedup of the GPU system over the CPU system?

**(b)** If a data center spends 70% of its time on workload A and 30% of its time on workload B when running CPUs, what percentage of Max IPS does it achieve for each of the three systems?

**(c)** Building on (b), assuming that the power scales linearly from idle to busy power as IPS grows from 0% to 100%, what is the performance per watt of the CPU system compared to the GPU system?

**(d)** If another data center spends 40% of its time on workload A, 10% of its time on workload B, and 50% of its time on workload C, what is the speedup of the GPU system over the general-purpose system?

**(e)** Assume a cooling door for a rack costs $4000 and dissipates 14 kW (into the room; additional cost is required to get it out of the room). How many CPU- or GPU-based servers can you cool with one cooling door, assuming TDP in Figures 1.30 and 1.31?

**(f)** Typical server farms can dissipate a maximum of 200 W per square foot. Given that a server rack requires 11 square feet (including front and back clearance), how many servers from (e) can be placed on a single rack, and how many cooling doors are required?

> **Required textbook material:** Figures 1.30 and 1.31.

### Q14. [BOOK • Chapter 1 • Exercise 1.14] — CLASS DISCUSSION

Assume that we make an enhancement to a computer that improves some mode of execution by a factor of 10. Enhanced mode is used 50% of the time, measured as a percentage of the execution time when the enhanced mode is in use. Recall that Amdahl’s Law depends on the fraction of the original, unenhanced execution time that could make use of enhanced mode. Thus we cannot directly use this 50% measurement to compute speedup with Amdahl’s Law.

**(a)** What is the speedup we have obtained from fast mode?

**(b)** What percentage of the original execution time has been converted to fast mode?

### Q15. [BOOK • Chapter 1 • Exercise 1.15] — CLASS DISCUSSION

In this exercise assume that we are considering enhancing a chiplet-based multicore processor by adding encryption hardware to it as a substitute chiplet for a computing core chiplet. When computing encryption operations, it is 20 times faster than the normal mode of execution. We will define the percentage of encryption as the percentage of time in the original execution that is spent performing encryption operations.

**(a)** Draw a graph that plots the speedup as a percentage of the computation spent performing encryption. Label the y-axis **Net speedup** and label the x-axis **Percent encryption**.

**(b)** With what percentage of encryption will adding encryption hardware result in a speedup of 2?

**(c)** What percentage of time in the new execution will be spent on encryption operations if a speedup of 2 is achieved?

**(d)** Consider the case that the loss of the computing core chiplet reduces the performance of other computations by 10%. How often must the encryption unit be used to make it worthwhile?

---

# Week 4 — Privilege, Instruction Encoding, Amdahl Trade-offs, Cloud Power and Roadmaps

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2001 • Q1.13]

A CPU has two modes — privileged and non-privileged. In order to change the mode from privileged to non-privileged:

1. A hardware interrupt is needed
2. A software interrupt is needed
3. A privileged instruction (which does not generate an interrupt) is needed
4. A non-privileged instruction (which does not generate an interrupt) is needed

### Q2. [GATE CSE 1999 • Q17]

Consider the following program segment:

```text
X: CMP R1,0      ; compare R1 and 0
   JZ Z
   MOV R2,R1
   SHR R1
   SHL R1
   CMP R2,R1
   JZ Y
   INC R3
Y: SHR R1
   JMP X
Z: ...
```

**(a)** Initially R1, R2 and R3 contain 5, 0 and 0, respectively. What are the final values of R1 and R3 when control reaches Z?

**(b)** In general, if the initial values of R1, R2 and R3 are n, 0 and 0, respectively, what is the final value of R3 when control reaches Z?

### Q3. [GATE CSE 1999 • Q2.22]

The main difference(s) between a CISC and a RISC processor is/are that a RISC processor typically:

1. Has fewer instructions
2. Has fewer addressing modes
3. Has more registers
4. Is easier to implement using hard-wired logic

*Select all statements that apply.*

### Q4. [GATE CSE 1999 • Q2.23]

A certain processor supports only the immediate and the direct addressing modes. Which of the following programming language features cannot be implemented on this processor?

1. Pointers
2. Arrays
3. Records
4. Recursive procedures with local variables

*Select all that apply.*

### Q5. [GATE CSE 2020 • Q4]

Consider the following data path diagram.

![GATE CSE 2020 Q4 datapath](assets/gate_2020_q4_datapath.png)

Consider the instruction:

$R0 \leftarrow R1 + R2$

The following micro-operations are given:

1. `R2_r, TEMP1_r, ALU_add, TEMP2_w`
2. `R1_r, TEMP1_w`
3. `PC_r, MAR_w, MEM_r`
4. `TEMP2_r, R0_w`
5. `MDR_r, IR_w`

Assume that PC is incremented appropriately. The subscripts `r` and `w` indicate read and write, respectively. The correct order of execution is:

1. 2, 1, 4, 5, 3
2. 1, 2, 4, 3, 5
3. 3, 5, 2, 1, 4
4. 3, 5, 1, 2, 4

### Q6. [GATE CSE 1993 • Q10]

The instruction format shown below contains fields `Mode` and `RegR`, which together specify the operand. `RegR` specifies a CPU register and `Mode` specifies an addressing mode. `Mode = 2` means that register `RegR` contains the address of the operand; after fetching the operand, `RegR` is incremented by 1.

![GATE CSE 1993 Q10 instruction format](assets/gate_1993_q10_format.png)

An instruction at memory location 2000 specifies `Mode = 2` and `RegR` refers to the PC.

**(a)** What is the address of the operand?

**(b)** Assuming that it is a non-jump instruction, what are the contents of the PC after execution?

### Q7. [GATE CSE 1992 • Q01(vi)]

In an 11-bit computer instruction format, the address field size is 4 bits. Using an expanding opcode, 5 two-address instructions and 32 one-address instructions are to be supported. The number of zero-address instructions that can be supported is ______.

### Q8. [GATE CSE 1991 • Q01(xi)]

The arithmetic expression

$(a+b)\times c - d/e^l$

is to be evaluated on a two-address machine, where each operand is either a register or a memory location. With a minimum number of memory accesses of operands:

- The number of registers required = ______
- The number of memory accesses of operands = ______

### Q9. [GATE CSE 1988 • Q9(iii)]

In the program scheme below, indicate the instructions containing any operand needing relocation for position-independent behaviour. Justify your answer.

```text
Y = 10
MOV X(R0), R1
MOV X, R0
MOV 2(R0), R1
MOV Y(R0), R5
...
X: WORD 0,0,0
```

### Q10. [GATE CSE 1988 • Q2(ii)]

Using an expanding opcode encoding for instructions, is it possible to encode all of the following in the instruction format shown? Justify your answer.

- 14 double-address instructions
- 127 single-address instructions
- 60 zero-address instructions

| Opcode | Operand 1 address | Operand 2 address |
|---:|---:|---:|
| 4 bits | 6 bits | 6 bits |

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 1 • Exercise 1.16] — CLASS DISCUSSION

When making changes to optimize part of a processor, it is often the case that speeding up one type of instruction comes at the cost of slowing down something else. For example, if we put in a complicated fast floating-point unit, that takes space, and something might have to be moved farther away from the middle to accommodate it, adding an extra cycle in delay to reach that unit. The basic Amdahl’s Law equation does not take into account this trade-off.

**(a)** If the new fast floating-point unit speeds up floating-point operations by, on average, 2×, and floating-point operations take 20% of the original program’s execution time, what is the overall speedup (ignoring the penalty to any other instructions)?

**(b)** Now assume that speeding up the floating-point unit slowed down data cache accesses, resulting in a 1.5× slowdown (or 2/3 speedup). Data cache accesses consume 10% of the execution time. What is the overall speedup now?

**(c)** After implementing the new floating-point operations, what percentage of execution time is spent on floating-point operations? What percentage is spent on data cache accesses?

### Q12. [BOOK • Chapter 1 • Exercise 1.17] — CLASS DISCUSSION

Server farms such as those of cloud computing providers include enough compute capacity for the highest request rate of the day. Imagine that most of the time these servers operate at only 60% capacity. Assume further that the power does not scale linearly with the load; that is, when the servers are operating at 60% capacity, they consume 90% of maximum power. The servers could be turned off, but they would take too long to restart in response to more load. A new system has been proposed that allows for a quick restart but requires 20% of the maximum power while in this “barely alive” state.

**(a)** How much power savings would be achieved by turning off 40% of the servers?

**(b)** How much power savings would be achieved by placing 40% of the servers in the “barely alive” state?

**(c)** How much power savings would be achieved by reducing the voltage by 20% and frequency by 20%?

**(d)** How much power savings would be achieved by placing 20% of the servers in the “barely alive” state and 20% off?

### Q13. [BOOK • Chapter 1 • Exercise 1.18] — CLASS DISCUSSION

When designing systems for real-time applications in which specific deadlines must be met, finishing the computation faster gains nothing. Consider the case of an IoT system that can execute the necessary code, in the worst case, twice as fast as necessary.

**(a)** How much energy do you save if you execute at the current speed and turn off the system when the computation is complete?

**(b)** How much energy do you save if you set the voltage and frequency to be half as much?

**(c)** Consider the case in which a different IoT system has 50% more cores and requires 30% more power. How does the energy compare to (a) if you execute at the current speed and turn off the system when the computation is complete?

**(d)** Once again considering the case of an IoT system with 50% more cores and requires 30% more power. How does the energy compare to (c) if you set the voltage and frequency to be one-third as much?

**(e)** Is it worthwhile to use the IoT system with additional cores? Why?

### Q14. [BOOK • Chapter 1 • Exercise 1.19] — CLASS DISCUSSION

One challenge for architects is that the design created today will require several years of implementation, verification, and testing before appearing on the market. This delay means that the architect must project what the technology will be like several years in advance. Sometimes, this is difficult to do.

**(a)** According to the trend in device scaling historically observed by Moore’s Law, the number of transistors on a chip in 2030 should be how many times the number in 2020?

**(b)** The increase in performance once mirrored this trend. Had performance continued to climb at the same rate as in the 1990s, approximately what performance would chips have over the VAX-11/780 in 2030?

**(c)** At the current rate of increase of the late 2010s to early 2020s, what is a more updated projection of performance in 2030?

**(d)** What has limited the rate of growth of the clock rate, and what are architects doing with the extra transistors now to increase performance?

**(e)** The rate of growth for DRAM capacity has also slowed down. For 20 years, DRAM capacity improved by 60% each year. If the 8-gigabit DRAM was first available in 2014, and 16 gigabit is not available until 2019, what is the current DRAM growth rate?

### Q15. [BOOK • Chapter 1 • Exercise 1.20] — CLASS DISCUSSION

This problem explores the total cost of ownership (TCO) associated with computer systems by exploring two similar server configurations as shown in Figure 1.22.

**(a)** For each of the servers, assuming each has a workload that is 70% integer and 30% floating point, using the SPEC integer and floating-point rates given in Figure 1.22 for each server, which provides better performance?

**(b)** Using the results from (a), how do the servers compare in their price/performance for this workload?

**(c)** Assume each of the servers is busy 60% of the time and idle the rest of the time. When each is busy, assume it uses 90% of the power supply limit and only 30% when it is idle. How much power will each server consume in a year?

**(d)** Assuming that 1 W of power per year costs $1, how much will the power usage for each server cost per year using the results from (c)?

**(e)** Assuming the servers have a lifetime of 5 years, how much do they cost per year, taking into account their initial cost and annual power costs? How does this change for 6- or 7-year lifetimes? How much does each cost in total lifetime costs?

> **Required textbook material:** Figure 1.22.

---

# Organizer Source Ledger

## Textbook source

Hennessy, John L.; Patterson, David A.; Kozyrakis, Christos. *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 1, Case Studies and Exercises.

## GATE source identifiers used

| Week | Paper questions |
|---|---|
| Week 1 | GATE CSE 2026 Set 2 Q44; 2026 Set 1 Q14, Q15; 2025 Set 2 Q61; 2024 Set 1 Q55; 2024 Set 2 Q57, Q61; 2023 Q31; 2021 Set 1 Q55; 2020 Q44 |
| Week 2 | GATE CSE 2018 Q51; 2017 Set 1 Q49, Q11; 2016 Set 2 Q31; 2014 Set 1 Q9, Q55; 2008 Q33, Q34; 2007 Q54, Q71 |
| Week 3 | GATE CSE 2007 Q72, Q73; 2005 Q65, Q79, Q80; 2004 Q63, Q64; 2003 Q48, Q49; 2001 Q2.13 |
| Week 4 | GATE CSE 2001 Q1.13; 1999 Q17, Q2.22, Q2.23; 2020 Q4; 1993 Q10; 1992 Q01(vi); 1991 Q01(xi); 1988 Q9(iii), Q2(ii) |

## Included image assets

- `assets/gate_2005_q79_q80_datapath.png`
- `assets/gate_2001_q2_13_datapath.png`
- `assets/gate_2020_q4_datapath.png`
- `assets/gate_1993_q10_format.png`

**No answer key or solutions are included in this file.**
