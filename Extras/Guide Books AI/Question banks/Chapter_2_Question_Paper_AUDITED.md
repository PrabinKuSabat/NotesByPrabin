# MTCS 102 — Chapter 2 Question Paper

## Memory Hierarchy Design

**Primary text:** *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 2  
**Format:** 4 weeks × 15 questions = 60 questions  
**Weekly split:** Questions 1–10 = GATE CSE previous-year questions; Questions 11–15 = complete textbook exercises for class discussion  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included

> **Audit status (2026-08-09):** Content retained. Chapter 2 is the canonical placement for cache, DRAM, virtual-memory, and TLB PYQs; duplicates formerly present in Chapter 6 have been removed from Chapter 6.  

> **Question-counting rule:** One source question/exercise is treated as exactly one question even when it contains several subparts. No textbook exercise has been split.
>
> **Figures:** No external image assets are required for this Chapter 2 Markdown paper. GATE questions whose originals use simple cache/memory-hierarchy figures have been represented as equivalent Markdown text or tables without changing the numerical information. Textbook Exercises 2.5 and 2.6 refer to the benchmark code in textbook Figure 2.29; that source reference is preserved.
>
> **Source-numbering note:** For recent GATE papers, the official master-paper number and the CS-indexed/GateOverflow number can differ because the official paper includes General Aptitude questions in the master sequence. Both identifiers are recorded where relevant.

---

# Week 1 — Cache Mapping, Address Decomposition, Write Policies, and Memory Organization

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2026 • Set 2 • Master Q52 / CS Q42] — High

A computer has a **4 KiB direct-mapped cache** with a **16-byte block size** and **16 MiB of physical memory**. The cache is initially empty. One word is one byte.

The following byte addresses are accessed in the order `P, Q, R, S`, and this four-access sequence is repeated **10 times**, giving 40 memory references in total:

| Symbol | Physical address |
|---|---|
| P | `0x845B32` |
| Q | `0x845B26` |
| R | `0x845B36` |
| S | `0x846B32` |

Which of the following statements are true? **Select all that apply.**

1. Every reference to P is a cache miss.
2. Every reference to R is a cache hit.
3. Every reference to Q is a cache miss.
4. Except for the first reference to S, all subsequent references to S are cache hits.

---

### Q2. [GATE CSE 2025 • Set 2 • Master Q39 / CS Q29] — Medium

A direct-mapped cache uses the following address interpretation:

- Tag field: **4 bits**
- Index field: **12 bits**
- Cache block: **1 byte**
- The memory is byte-addressable.
- Ignore all cache metadata other than the tag.

Which option gives the correct pair **(main-memory capacity, cache data capacity)**?

1. 64 KiB, 4 KiB
2. 128 KiB, 16 KiB
3. 64 KiB, 8 KiB
4. 128 KiB, 6 KiB

---

### Q3. [GATE CSE 2024 • Set 1 • Master Q53 / CS Q43] — High

Consider two set-associative caches using LRU replacement:

- **WB:** write-back cache
- **WT:** write-through cache

Which of the following statements are correct? **Select all that apply.**

1. A read miss in WB never causes a dirty cache block to be written back to main memory.
2. A read miss in WT never requires a cache block to be written back to main memory because of a dirty victim.
3. A write hit in WB can change the dirty status of a cache block.
4. A write miss in WT always requires the victim cache block to be written to main memory before the requested block can be fetched.

---

### Q4. [GATE CSE 2023 • Master Q63 / CS Q54] — Medium

A computer uses:

- 32-bit memory addresses
- an **8-way set-associative cache**
- total cache data capacity of **64 KiB**
- a cache block size of **64 bytes**

An address is divided into `TAG | SET INDEX | BLOCK OFFSET`.

How many bits are required for the **TAG** field?

---

### Q5. [GATE CSE 2021 • Set 2 • Q19] — Medium

A computer has:

- a **2 KiB set-associative cache**
- **64-byte cache blocks**
- byte-addressable memory
- 32-bit memory addresses
- a **22-bit tag field**

What is the associativity of the cache?

---

### Q6. [GATE CSE 2018 • Q34] — High

The physical address space contains \(2^P\) bytes. A machine word contains \(2^W\) bytes. A cache has:

- capacity \(2^N\) bytes,
- block size \(2^M\) words,
- \(K\)-way set associativity.

Which expression gives the number of **tag bits** in a physical address?

1. \(P-N-\log_2 K\)
2. \(P-N+\log_2 K\)
3. \(P-N-M-W-\log_2 K\)
4. \(P-N-M-W+\log_2 K\)

---

### Q7. [GATE CSE 2017 • Set 2 • Q53] — Medium

A byte-addressable processor has a \(2^{32}\)-byte physical address space. It uses a **direct-mapped cache** with:

- 512 cache lines
- 32 bytes per cache block

How many bits are used for the **tag**?

---

### Q8. [GATE CSE 2016 • Set 2 • Q32] — High

A processor has a **40-bit physical address** and a **512 KiB, 8-way set-associative cache**.

Determine the width, in bits, of the **tag field**.

---

### Q9. [GATE CSE 2015 • Set 3 • Q14] — Medium

A byte-addressable computer has:

- \(2^{20}\) bytes of main memory,
- a direct-mapped cache with \(2^{12}\) cache lines,
- a cache block size of 16 bytes.

Two consecutive byte addresses are `0xE201F` and `0xE2020`.

For the address `0xE201F`, which option gives the correct **(tag, cache-line index)** pair?

1. (`E`, `201`)
2. (`F`, `201`)
3. (`E`, `E20`)
4. (`2`, `01F`)

---

### Q10. [GATE CSE 2007 • Q10] — High

A **4-way set-associative cache** has:

- 128 cache lines in total,
- 64 words per cache line,
- a processor with a **20-bit word address**.

If a memory address is divided into **TAG, SET/LINE, WORD-OFFSET**, which option gives the correct number of bits in these three fields?

1. 9, 6, 5
2. 7, 7, 6
3. 7, 5, 8
4. 9, 5, 6

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 2 • Exercise 2.5 • pp. 163–164] — CLASS DISCUSSION — High

Use and, where necessary, modify the memory-system benchmark provided in **Figure 2.29** of the textbook. By varying relevant parameters such as memory stride and the amount of memory touched, construct experiments that allow you to infer the following properties of the system on which the program is run:

**(a)** Page size  
**(b)** Number of TLB entries  
**(c)** TLB miss penalty  
**(d)** TLB associativity  

For each quantity, use the measured timing behavior to justify the value you infer.

> **Required textbook material:** Figure 2.29 benchmark code.

---

### Q12. [BOOK • Chapter 2 • Exercise 2.6 • p. 164] — CLASS DISCUSSION — High

Run multiple copies of the benchmark from **Figure 2.29** simultaneously on a multiprocessor or multicore machine and use the measured behavior to investigate the organization of the machine.

Design experiments that help distinguish:

**(a)** the number of actual processor cores from additional logical processors supplied through hardware multithreading, and  
**(b)** characteristics of the memory-system organization, including how many independent memory-controller resources can be exercised concurrently.

Explain what measurements would allow you to support your conclusions.

> **Required textbook material:** Figure 2.29 benchmark code.

---

### Q13. [BOOK • Chapter 2 • Exercise 2.7 • p. 164] — CLASS DISCUSSION — High

Design a benchmark that can be used to determine important characteristics of the **instruction cache** of a processor.

Your experiment should account for the fact that the compiler and instruction layout can make instruction-cache measurements harder to interpret. Use a controlled sequence of simple arithmetic instructions of known size, or another defensible method, and explain how the resulting timing behavior can be used to infer instruction-cache properties.

---

### Q14. [BOOK • Chapter 2 • Exercise 2.16 • p. 166] — CLASS DISCUSSION — High

In the default DRAM configuration considered by the chapter, one rank is built from **eight ×8, 2-Gb DRAM chips**. Alternative rank organizations can use:

- sixteen ×4 chips,
- eight ×8 chips,
- four ×16 chips,

and the DRAM-chip capacity can be selected from **1 Gb, 2 Gb, or 4 Gb**.

Using the Micron DDR3 power-calculation methodology referenced by the chapter, tabulate the **total power consumed by a rank** for the feasible organizations.

For a fixed rank capacity, determine which organization is the most power-efficient and explain the architectural reason for the result.

---

### Q15. [BOOK • Chapter 2 • Exercise 2.17 • p. 166] — CLASS DISCUSSION — High

Compare two DDR memory controllers:

- an **open-page** controller, which leaves a row buffer open when there is no pending request in the hope of a later row-buffer hit;
- a **close-page** controller, which precharges an idle bank so that it is ready for an access to another row.

For both controllers:

- pending row-buffer hits have priority;
- if there is no row-buffer hit, requests are handled first-come, first-served.

A sequence of reads to one bank arrives as follows:

| Request | Cycle enqueued |
|---|---:|
| Read row X | 20 |
| Read row X | 35 |
| Read row X | 90 |
| Read row Y | 95 |
| Read row X | 100 |
| Read row X | 160 |

Assume:

- PRECHARGE takes 10 cycles,
- ACTIVATE takes 10 cycles,
- COLUMN-READ takes 10 cycles,
- the bank is already precharged when the first request arrives at cycle 20.

For **both open-page and close-page policies**, specify the cycle at which each required DRAM command is issued. Compare the resulting schedules and explain which request patterns favor each policy.

---

# Week 2 — AMAT, Miss Penalties, Replacement, and Multilevel Cache Design

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2025 • Set 1 • Master Q53 / CS Q43] — High

A two-level memory hierarchy has the following behavior:

| Event | Probability / time |
|---|---:|
| L1 hit rate | 95% |
| L1 access time | 10 ns |
| L2 hit rate, among L1 misses | 85% |
| Time for an L2 hit, including the preceding L1 lookup | 20 ns |
| Main-memory service time, including the preceding cache lookups | 200 ns |

Compute the **average memory-access time**, in nanoseconds, rounded to **two decimal places**.

---

### Q2. [GATE CSE 2025 • Set 2 • Master Q55 / CS Q45] — High

A processor has the following cache hierarchy:

- L1 access time = **1 ns**
- L1 hit rate = **90%**
- L2 hit rate = **80%**
- An L2 hit following an L1 miss incurs **10 ns** of miss-service time back toward L1.
- If L2 also misses, fetching the required data from main memory toward L2 incurs **100 ns**.

Compute the **average memory-access time** in nanoseconds, rounded to **one decimal place**.

---

### Q3. [GATE CSE 2024 • Set 1 • Master Q56 / CS Q46] — High

A program has the following characteristics:

- 25% of its instructions are loads or stores.
- With an ideal memory system, its CPI is **2**.
- Instruction-cache miss rate = **2%**.
- Data-cache miss rate = **8%**.
- Each cache miss adds **100 processor cycles**.

Determine the **speedup** obtained by replacing the given caches with a perfect cache that never misses. Round the result to **two decimal places**.

---

### Q4. [GATE CSE 2020 • Q21] — High

A processor has a **1 MiB direct-mapped cache** with a **256-byte block size**.

- Cache lookup time = 3 ns
- Hit rate = 94%
- A cache block contains 64-bit words.
- On a miss, the **first word** arrives from memory after 20 ns.
- Each **additional word** in the same cache block requires 5 ns to transfer.

Assuming a complete cache block is filled on a miss, calculate the **average memory-access time** in nanoseconds, rounded to **one decimal place**.

---

### Q5. [GATE CSE 2019 • Q45] — High

A single-level cache uses blocks of **8 words**, with **4 bytes per word**. Main memory operates at **60 MHz**.

On a cache miss:

1. one memory cycle is required to accept the block address,
2. three additional memory cycles are required before the complete requested block becomes available for transfer,
3. the eight words are then transmitted at a rate of **one word per memory cycle**.

What is the **maximum sustained memory bandwidth**, expressed as

\[
X \times 10^6 \text{ bytes/s}?
\]

Determine \(X\).

---

### Q6. [GATE CSE 2017 • Set 1 • Q51] — High

A **2-way set-associative cache** contains **256 cache blocks** and uses LRU replacement. It is initially empty.

The following block-reference sequence is repeated **10 times**:

```text
0, 128, 256, 128, 0, 128, 256, 128,
1, 129, 257, 129, 1, 129, 257, 129
```

How many **conflict misses** occur over the complete sequence?

---

### Q7. [GATE CSE 2010 • Q48] — Medium

Consider a two-level cache hierarchy:

- L1 block size = 4 words
- L2 block size = 16 words
- L1 access time = 2 ns
- L2 access time = 20 ns
- Main-memory access time = 200 ns

If an access **misses in L1 but hits in L2**, the required L1 block is transferred from L2 to L1.

Which option gives the time required to service this access?

1. 2 ns
2. 20 ns
3. 22 ns
4. 88 ns

---

### Q8. [GATE CSE 2010 • Q49] — High

Use the same hierarchy as in Question 7:

- L1 block size = 4 words
- L2 block size = 16 words
- L1 access time = 2 ns
- L2 access time = 20 ns
- Main-memory access time = 200 ns

If an access **misses in both L1 and L2**, the L2 block must first be obtained from main memory and the required L1 block must then be supplied from L2.

Which option gives the resulting access/service time?

1. 222 ns
2. 888 ns
3. 902 ns
4. 968 ns

---

### Q9. [GATE CSE 2009 • Q29] — High

A **4-way set-associative cache** is initially empty. The cache has:

- 16 cache blocks in total,
- LRU replacement,
- a main memory containing 256 blocks.

The following main-memory block numbers are referenced:

```text
0, 255, 1, 4, 3, 8, 133, 159, 216,
129, 63, 8, 48, 32, 73, 92, 155
```

Which one of the following blocks is **not present in the cache** after the final reference?

1. 3
2. 8
3. 129
4. 216

---

### Q10. [GATE CSE 2014 • Set 1 • Q44] — High

A memory-reference sequence contains **N references** to **n distinct cache blocks**. Between any two consecutive references to the same cache block, at most **k distinct other blocks** are referenced.

The sequence is executed on a cache that:

- uses LRU replacement,
- has associativity \(A \ge k\).

What is the miss ratio?

1. \(n/N\)
2. \(1/N\)
3. \(1/A\)
4. \(k/n\)

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 2 • Exercise 2.19 • pp. 167–168] — CLASS DISCUSSION — High

Compare two organizations for providing greater L1 cache bandwidth. Both are based on a **64 KiB, 2-way set-associative L1 cache with 64-byte blocks**:

- **Design A:** a pipelined 64 KiB cache whose access is divided into four pipeline stages;
- **Design B:** a banked cache constructed from two independent **32 KiB, 2-way set-associative banks**.

Use an SRAM/cache modeling tool such as CACTI or an equivalent current tool to compare the designs in terms of:

- access latency and achievable bandwidth,
- area,
- dynamic energy per read,
- architectural advantages and disadvantages of banking versus pipelining.

Conclude which organization is preferable under explicitly stated workload assumptions.

---

### Q12. [BOOK • Chapter 2 • Exercise 2.20 • p. 168] — CLASS DISCUSSION — High

Consider a **2 MiB L2 cache** with a **64-byte block size**. The refill path supplies **16 bytes at a time**.

Assume:

- the L2 data array can accept one 16-byte refill segment every **4 cycles**;
- after a miss is issued to memory, the first 16-byte segment arrives after **120 cycles**;
- each additional 16-byte segment arrives **16 cycles** after the previous segment;
- a segment arriving from memory can be bypassed directly toward the processor instead of waiting for the complete line to be installed.

Analyze the miss latency/service behavior:

**(a)** without critical-word-first / early restart, and  
**(b)** with critical-word-first / early restart.

Then explain why the usefulness of these techniques can differ between an L1 miss and an L2 miss.

---

### Q13. [BOOK • Chapter 2 • Exercise 2.21 • p. 168] — CLASS DISCUSSION — High

A processor has a **write-through L1 cache** and a **write-back L2 cache**. Between them is a **merging write buffer**. The path to L2 transfers **16 bytes**, and L2 can accept one independent cache-address write every **4 cycles**.

Analyze the design as follows:

**(a)** Determine what information and data capacity each write-buffer entry must hold so that multiple writes to the same cache block can be merged correctly.

**(b)** Consider a loop that clears a large memory region using consecutive **64-bit stores**. Compare the steady-state behavior/performance of a merging write buffer with a nonmerging write buffer.

**(c)** Extend the comparison to the case in which L1 misses also occur. Discuss how the conclusion changes for a blocking versus a nonblocking cache.

---

### Q14. [BOOK • Chapter 2 • Exercise 2.22 • p. 168] — CLASS DISCUSSION — High

The following table describes possible cache levels. **MPKI** is misses per thousand processor instructions.

| Cache size | Access time | MPKI |
|---:|---:|---:|
| 32 KiB | 1 cycle | 100 |
| 128 KiB | 2 cycles | 80 |
| 512 KiB | 4 cycles | 50 |
| 2 MiB | 8 cycles | 40 |
| 8 MiB | 16 cycles | 10 |

The average off-chip main-memory access takes **200 cycles**.

Compute and compare the average memory-system cost for each of the following hierarchies:

**(a)** 32 KiB L1 → 8 MiB L2 → main memory  
**(b)** 32 KiB L1 → 512 KiB L2 → 8 MiB L3 → main memory  
**(c)** 32 KiB L1 → 128 KiB L2 → 2 MiB L3 → 8 MiB L4 → main memory  

State the assumptions needed to convert the supplied MPKI data into traffic at each lower level. Then discuss why making a hierarchy either too shallow or too deep can reduce performance.

---

### Q15. [BOOK • Chapter 2 • Exercise 2.28 • p. 169] — CLASS DISCUSSION — High

The chapter describes a large DRAM-based **L4 “Alloy Cache”** organized as a **direct-mapped cache** in order to reduce tag-access cost.

Analyze the design choices:

1. Is the usual concern about direct-mapped **conflict misses** likely to be important for such a large cache? Under what access patterns?
2. If conflict misses are significant, what mechanisms could reduce them without losing the main latency advantage of the design?
3. Discuss the performance, capacity, bandwidth, metadata, and pollution trade-offs of using a **large cache block**.
4. In particular, analyze the consequences of choosing a **4 KiB block size**.

---

# Week 3 — Virtual Memory, TLBs, Address Translation, and Shared/NUCA Caches

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2026 • Set 1 • Master Q54 / CS Q44] — High

A processor uses:

- virtual memory,
- a TLB,
- a **physically addressed cache**.

Whenever a physical page is evicted from main memory, all cache blocks belonging to that page are invalidated.

Which of the following event sequences can **never** occur for one memory reference? **Select all that apply.**

1. TLB miss → page-table hit → cache hit
2. TLB hit → page-table miss → cache hit
3. TLB miss → page-table miss → cache hit
4. TLB miss → page-table miss → cache miss

---

### Q2. [GATE CSE 2026 • Set 2 • Master Q54 / CS Q44] — High

A system has:

- TLB reach = **1 MiB**
- page size = **4 KiB**
- virtual-address space = **64 GiB**
- physical-address space = **1 GiB**

Each TLB entry contains:

- a 4-bit process identifier,
- the virtual page number,
- the physical frame number,
- 2 additional control bits.

Assume \(1\text{ KiB}=2^{10}\) bytes, \(1\text{ MiB}=2^{20}\) bytes, and \(1\text{ GiB}=2^{30}\) bytes.

What is the total **TLB storage capacity in bytes**?

---

### Q3. [GATE CSE 2024 • Set 1 • Master Q62 / CS Q52] — Medium

A paged virtual-memory system uses a **2 KiB page size**.

Virtual pages are mapped to physical frames as follows:

| Virtual page | Physical frame |
|---:|---:|
| 0 | 1 |
| 1 | 3 |
| 2 | 2 |
| 3 | 0 |

What is the **decimal physical address** corresponding to virtual address **2500**?

---

### Q4. [GATE CSE 2024 • Set 2 • Master Q64 / CS Q54] — High

A system uses:

- 32-bit virtual addresses,
- 4 KiB pages,
- 4-byte page-table entries,
- a **two-level page table**.

The first-level page directory occupies exactly one page and is always allocated. A second-level page-table page is allocated only if it contains at least one valid PTE.

A process has exactly **2000 distinct virtual pages** resident/mapped, and none is swapped out.

Let:

- \(X\) = minimum possible total number of pages occupied by the first- and second-level page tables,
- \(Y\) = maximum possible total number of pages occupied by those page tables.

Find \(X+Y\).

---

### Q5. [GATE CSE 2022 • Master Q38 / CS Q28] — High

Which one of the following statements about address translation is **false**?

1. A TLB is normally searched associatively using the virtual page number.
2. If a TLB lookup hits but the subsequent cache lookup misses, the requested word must still have a valid copy in main memory.
3. With a particular inverted-page-table organization, the time required to translate every possible virtual address is necessarily identical.
4. In a hashed page table, two different virtual addresses that collide in the hash structure need not have identical translation time.

---

### Q6. [GATE CSE 2020 • Q53] — High

A system has:

- a single-level page table stored in main memory,
- a TLB,
- main-memory access time = **100 ns**,
- TLB lookup time = **20 ns**,
- TLB hit ratio = **95%**,
- page-fault rate = **10%**,
- transfer time between disk and memory for one page = **5000 ns**.

Of all page faults, **20%** require a dirty victim page to be written to disk before the demanded page is read. Assume the time to update the TLB after the fault is negligible.

Calculate the **average memory-access time**, in nanoseconds, rounded to **one decimal place**.

> **Organizer note:** This is an official GATE question. Its indexed discussion has historically noted interpretation sensitivity in how the page-fault timing assumptions are read; no interpretation or answer is supplied here.

---

### Q7. [GATE CSE 2019 • Q33] — High

A system has:

- 64-bit virtual addresses,
- 48-bit physical addresses,
- word-addressable memory,
- 4 bytes per word,
- page size = 8 KiB,
- a TLB with 128 valid entries.

What is the maximum number of **distinct virtual addresses** that can be translated without causing a TLB miss?

1. \(16 \times 2^{10}\)
2. \(256 \times 2^{10}\)
3. \(4 \times 2^{20}\)
4. \(8 \times 2^{20}\)

---

### Q8. [GATE CSE 2015 • Set 2 • Q47] — High

A computer uses:

- 8 KiB pages,
- 32-bit physical addresses.

Each page-table entry contains:

- the physical-page translation,
- 1 valid bit,
- 1 dirty bit,
- 3 permission/protection bits.

The maximum page-table size for one process is **24 MiB**.

What is the length, in bits, of the **virtual address**?

---

### Q9. [GATE CSE 2013 • Q52] — High

A system has:

- 46-bit virtual addresses,
- 32-bit physical addresses,
- a **three-level page table**,
- 32-bit page-table entries.

The first-level page table \(T_1\) occupies exactly one page. Each entry in \(T_1\) points to a page containing a second-level table \(T_2\). Each \(T_2\) entry points to a page containing a third-level table \(T_3\), whose entries are the final PTEs.

The processor also has a **1 MiB, 16-way set-associative virtually indexed, physically tagged cache** with 64-byte blocks.

Which page size is compatible with the page-table organization?

1. 2 KiB
2. 4 KiB
3. 8 KiB
4. 16 KiB

---

### Q10. [GATE CSE 2013 • Q53] — High

Use the same system described in Question 9:

- 46-bit virtual addresses,
- 32-bit physical addresses,
- three-level page tables,
- 1 MiB 16-way set-associative **virtually indexed, physically tagged** cache,
- 64-byte cache blocks.

What is the **minimum number of page colors** required to ensure that two virtual-address synonyms of the same physical block cannot occupy different cache sets?

1. 2
2. 4
3. 8
4. 16

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 2 • Exercise 2.29 • pp. 169–170] — CLASS DISCUSSION — High

Consider a baseline **Alloy Cache** design:

- tag access = 30 cycles,
- after a tag hit, data requires 4 additional cycles,
- if the tag lookup reveals a miss, an average memory access of 100 cycles is initiated.

A proposed design adds a **1-cycle hit/miss predictor** operating in parallel with the Alloy Cache lookup.

- If the predictor predicts a hit, the access proceeds as in the baseline design.
- If it predicts a miss, the main-memory access begins in parallel with the Alloy Cache lookup.

The predictor behaves as follows:

| Outcome | Fraction of all accesses |
|---|---:|
| Correctly predicts hit | 80% |
| Correctly predicts miss | 5% |
| Incorrectly predicts hit | 5% |
| Incorrectly predicts miss | 10% |

Calculate the **average access latency at the Alloy Cache and below** for:

**(a)** the baseline design, and  
**(b)** the design with the hit/miss predictor.

Then identify which predictor mistakes are most expensive and explain why.

---

### Q12. [BOOK • Chapter 2 • Exercise 2.30 • p. 170] — CLASS DISCUSSION — High

View the ways of one set in a set-associative cache as a priority list from highest to lowest replacement priority. Cache management can then be decomposed into:

- **Insertion:** where a newly fetched block enters the priority list,
- **Promotion:** how a block moves in the list after a hit,
- **Victim Selection:** which block is evicted on a miss.

**(a)** Express conventional **LRU** replacement using these three subpolicies.

**(b)** Propose alternative insertion and promotion policies that could outperform LRU for some workloads. For each policy, identify the access pattern for which it may help and the pattern for which it may hurt.

---

### Q13. [BOOK • Chapter 2 • Exercise 2.31 • p. 170] — CLASS DISCUSSION — High

A multicore processor shares its **last-level cache (LLC)** among multiple programs. One program can therefore affect the space, latency, and replacement behavior seen by another.

This creates two architectural concerns:

- **quality of service (QoS):** one program may receive less cache capacity or lower performance than expected;
- **privacy/security:** a program may infer another program's memory-access behavior through cache interference.

Design LLC-management policies that make one program's observable cache behavior as independent as practical from the behavior of other programs sharing the LLC.

Compare the performance, utilization, fairness, and security trade-offs of the policies you propose.

---

### Q14. [BOOK • Chapter 2 • Exercise 2.32 • p. 170] — CLASS DISCUSSION — High

A conventional **16 MiB L3 cache** takes approximately **20 cycles** per access. An alternative organization divides the cache into banks at different physical distances from the processor, creating a **Non-Uniform Cache Access (NUCA)** design.

For example:

- nearest 2 MiB bank: 8 cycles,
- next 2 MiB: 10 cycles,
- continuing in 2 MiB increments,
- farthest 2 MiB bank: 22 cycles.

Propose policies for maximizing performance in this NUCA cache.

Your discussion should consider at least:

- initial block placement,
- block migration or replication,
- replacement,
- bank contention,
- access locality,
- whether frequently used data should be moved toward the processor.

Explain the costs introduced by each policy.

---

### Q15. [BOOK • Chapter 2 • Exercise 2.33 • pp. 170–171] — CLASS DISCUSSION — High

A desktop system contains **2 GiB of DRAM with ECC**. There is one memory channel of width **72 bits**:

- 64 bits carry data,
- 8 bits carry ECC.

Assume **1-Gb DRAM chips** and that one DRAM chip connects to each required group of DIMM data pins.

**(a)** How many DRAM chips are required on the DIMM, and how many data I/O pins must each DRAM chip provide?

**(b)** What DRAM burst length is required to transfer a **32-byte L2 cache block**?

**(c)** Ignoring ECC traffic when reporting useful data bandwidth, calculate the peak useful bandwidth of **DDR2-667** and **DDR2-533** for reads from an already active row.

---

# Week 4 — Cache Policy, Metadata, Bandwidth, Prefetching, and Energy

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2021 • Set 2 • Q27] — High

A processor has an **inclusive two-level cache hierarchy** in which L2 is larger than L1.

Consider the following statements:

**S1.** In a write-through L1 cache, a read miss does not require a dirty L1 victim to be written back to L2.

**S2.** Write-allocate must be paired with write-through, whereas no-write-allocate must be paired with write-back.

Which option is correct?

1. S1 is true and S2 is false.
2. S1 is false and S2 is true.
3. Both S1 and S2 are true.
4. Both S1 and S2 are false.

---

### Q2. [GATE CSE 2019 • Q1] — Medium

A computer has a **16 KiB fully associative cache** with:

- 16-byte cache blocks,
- 32-bit byte addresses.

How many bits of an address are used as the **tag** and the **cache index**, respectively?

1. 24, 0
2. 28, 4
3. 24, 4
4. 28, 0

---

### Q3. [GATE CSE 2017 • Set 1 • Q54] — High

A cache has capacity **N words** and a block size of **B words**.

- In its original **direct-mapped** organization, the tag field is 10 bits.
- It is redesigned to have the **same total capacity and same block size**, but to be **16-way set associative**.

What is the tag-field width, in bits, in the redesigned cache?

---

### Q4. [GATE CSE 2014 • Set 2 • Q43] — Medium

For a cache with fixed total capacity, which statement is correct when the **cache block size is decreased**?

1. A smaller block improves spatial locality.
2. A smaller block requires a smaller tag and therefore lowers tag-storage overhead.
3. A smaller block makes the tag larger and necessarily lowers hit time.
4. A smaller block can reduce the miss penalty.

---

### Q5. [GATE CSE 2014 • Set 2 • Q44] — Medium

A cache is redesigned by **doubling its associativity** while keeping total cache capacity and block size unchanged.

Which of the following is guaranteed **not** to be affected by this change?

1. Width of the tag comparators
2. Set-index decoding
3. Way-selection multiplexing
4. Width of the data path between the processor and main memory

---

### Q6. [GATE CSE 2013 • Q20] — Medium

A \(k\)-way set-associative cache has \(v\) sets and \(k\) cache lines in each set. Assume the \(k\) cache lines belonging to one set are numbered contiguously.

A main-memory block numbered \(j\) can be placed in which range of cache-line numbers?

1. \((j \bmod v)k\) through \((j \bmod v)k+(k-1)\)
2. \((j \bmod v)\) through \((j \bmod v)+(k-1)\)
3. \((j \bmod k)\) through \((j \bmod k)+(v-1)\)
4. \((j \bmod k)v\) through \((j \bmod k)v+(v-1)\)

---

### Q7. [GATE CSE 2012 • Q54] — Medium

A processor has:

- 32-bit byte addresses,
- a **256 KiB, 4-way set-associative write-back cache**,
- a 32-byte block size.

For each cache line, the tag directory stores:

- the address tag,
- two validity/status bits,
- one modified/dirty bit,
- one replacement-policy bit.

How many **address-tag bits** are required per cache line?

1. 11
2. 14
3. 16
4. 27

---

### Q8. [GATE CSE 2012 • Q55] — High

Use the same cache as in Question 7:

- 32-bit byte addresses,
- 256 KiB capacity,
- 4-way set associativity,
- 32-byte blocks,
- write-back,
- per-line metadata consisting of the address tag plus two validity/status bits, one modified bit, and one replacement bit.

What is the total size of the **tag directory and the specified per-line metadata**?

1. 160 Kibits
2. 136 Kibits
3. 40 Kibits
4. 32 Kibits

---

### Q9. [GATE CSE 2011 • Q43] — High

A processor uses:

- 32-bit byte addresses,
- an **8 KiB direct-mapped write-back cache**,
- 32-byte cache blocks.

Each cache line stores:

- one valid bit,
- one modified bit,
- the minimum number of tag bits required to identify the corresponding main-memory block.

What is the total number of bits required for this **tag/status storage**?

1. 4864 bits
2. 6144 bits
3. 6656 bits
4. 5376 bits

---

### Q10. [GATE CSE 2008 • Q73] — High

A processor has a **64 KiB, 2-way set-associative data cache** with **16-byte blocks**. It uses:

- 32-bit virtual addresses,
- 4 KiB virtual-memory pages,
- no data prefetching.

Consider:

```c
double ARR[1024][1024];
int i, j;

/* ARR begins at virtual page 0xFF000.
   The array is stored in row-major order.
   A double occupies 8 bytes.
   The cache is initially empty.
   Ignore data references other than accesses to ARR. */

for (i = 0; i < 1024; i++)
    for (j = 0; j < 1024; j++)
        ARR[i][j] = 0.0;
```

What is the cache hit ratio for the data references to `ARR`?

1. 0%
2. 25%
3. 50%
4. 75%

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 2 • Exercise 2.34 • p. 171] — CLASS DISCUSSION — High

A large last-level cache may have many sets and high associativity.

Identify and analyze the major implementation challenges in maintaining **exact LRU replacement information** in such a cache.

Your discussion should consider the amount of replacement metadata, the logic and state updates required on every access, timing/critical-path implications, energy, concurrency, and why practical processors often use approximations rather than true LRU.

---

### Q12. [BOOK • Chapter 2 • Exercise 2.35 • p. 171] — CLASS DISCUSSION — High

A large fraction of a modern processor's transistor area can be occupied by caches, making cache leakage energy significant.

Assume:

- a cache block consumes \(X\) units of leakage energy per cycle while powered,
- power-gating a block eliminates this leakage but destroys the data stored in the block,
- refetching that block from memory later costs approximately \(1000X\) units of dynamic energy.

Design a last-level-cache mechanism that uses power gating to **substantially reduce total system energy** while causing as little performance loss as possible.

Explain:

- when a block should be powered down,
- how the design predicts whether the block is likely to be reused,
- the energy break-even condition,
- the performance penalty of a wrong decision,
- what metadata or counters the mechanism requires.

---

### Q13. [BOOK • Chapter 2 • Exercise 2.36 • p. 171] — CLASS DISCUSSION — High

A server processor has:

- 16 cores,
- a 3 GHz clock,
- CPI = 2 when L2 refills are never delayed by memory bandwidth,
- 32-byte L2 cache blocks,
- an L2 miss rate of **6.67 misses per 1000 instructions**,
- DDR4-2666 memory.

Assume that short-term memory-bandwidth demand can reach **twice the average demand**.

Determine how many **independent memory channels** are required so that memory bandwidth does not become the bottleneck under the stated peak-demand condition.

State any assumptions you make about useful DDR bandwidth and channel width.

---

### Q14. [BOOK • Chapter 2 • Exercise 2.37 • p. 171] — CLASS DISCUSSION — High

A processor has **four independent memory channels**, each containing multiple DRAM banks.

When mapping consecutive memory/cache blocks into the memory system, compare the following policies:

1. consecutive blocks go to the same bank,
2. consecutive blocks go to different banks within one channel,
3. consecutive blocks are distributed across different memory channels.

For each mapping, analyze:

- row-buffer locality,
- bank-level parallelism,
- channel-level parallelism,
- sequential-stream bandwidth,
- interference between independent request streams.

Recommend a mapping policy for at least two different workload types and justify the choice.

---

### Q15. [BOOK • Chapter 2 • Exercise 2.39 • pp. 171–172] — CLASS DISCUSSION — High

Modern high-performance processors can use aggressive hardware prefetchers to bring data into the cache before the processor explicitly requests it.

Analyze the possible disadvantages of making such a prefetcher **too aggressive**.

Your discussion should cover architectural failure modes such as:

- cache pollution,
- unnecessary memory traffic,
- memory-channel or interconnect contention,
- displacement of useful data,
- wasted energy,
- reduced performance of other cores or programs sharing memory resources.

Propose mechanisms that could dynamically regulate prefetch aggressiveness and identify the measurements that should drive those mechanisms.

---

# Organizer Source Ledger

## Textbook source

John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, **7th Edition**, Chapter 2, **Memory Hierarchy Design**, Case Studies and Exercises.

### Selected textbook exercises

| Week-Q | Exercise | Page(s) | Principal topic |
|---|---:|---:|---|
| W1-Q11 | 2.5 | 163–164 | Experimental TLB/page characterization |
| W1-Q12 | 2.6 | 164 | Multicore/SMT and memory-system characterization |
| W1-Q13 | 2.7 | 164 | Instruction-cache benchmark design |
| W1-Q14 | 2.16 | 166 | DRAM rank organization and power |
| W1-Q15 | 2.17 | 166 | Open-page vs close-page DRAM scheduling |
| W2-Q11 | 2.19 | 167–168 | Banked vs pipelined L1 cache |
| W2-Q12 | 2.20 | 168 | Critical-word-first and early restart |
| W2-Q13 | 2.21 | 168 | Merging write buffers |
| W2-Q14 | 2.22 | 168 | Multilevel-cache filtering and hierarchy depth |
| W2-Q15 | 2.28 | 169 | Direct-mapped Alloy Cache and large blocks |
| W3-Q11 | 2.29 | 169–170 | Alloy Cache hit/miss prediction |
| W3-Q12 | 2.30 | 170 | Insertion/promotion/victim replacement policies |
| W3-Q13 | 2.31 | 170 | Shared LLC QoS and side-channel isolation |
| W3-Q14 | 2.32 | 170 | NUCA placement and migration |
| W3-Q15 | 2.33 | 170–171 | DRAM ECC organization, burst length, bandwidth |
| W4-Q11 | 2.34 | 171 | Practical difficulty of exact LLC LRU |
| W4-Q12 | 2.35 | 171 | Cache leakage and power gating |
| W4-Q13 | 2.36 | 171 | Memory bandwidth and channel provisioning |
| W4-Q14 | 2.37 | 171 | DRAM address mapping across banks/channels |
| W4-Q15 | 2.39 | 171–172 | Aggressive prefetching trade-offs |

## GATE source ledger

For recent papers, a direct official-paper link is recorded. For older papers, the official GATE archive link is recorded together with the indexed GateOverflow question page.

| Week-Q | GATE CSE source | Official source | Indexed cross-check |
|---|---|---|---|
| W1-Q1 | 2026 Set 2, Master Q52 / CS Q42 | https://gate2026.iitg.ac.in/doc/download/2026/QPs/CS2.pdf | https://gateoverflow.in/523104/gate-cse-2026-set-2-question-42 |
| W1-Q2 | 2025 Set 2, Master Q39 / CS Q29 | https://gate2026.iitg.ac.in/doc/download/2025/CS22025.pdf | https://gateoverflow.in/460806/gate-cse-2025-set-2-question-29 |
| W1-Q3 | 2024 Set 1, Master Q53 / CS Q43 | https://gate2026.iitg.ac.in/doc/download/2024/CS124S5.pdf | https://gateoverflow.in/422799/gate-cse-2024-set-1-question-43 |
| W1-Q4 | 2023, Master Q63 / CS Q54 | https://gate2026.iitg.ac.in/doc/download/2023/cs_2023.pdf | https://gateoverflow.in/399257/gate-cse-2023-question-54 |
| W1-Q5 | 2021 Set 2, Q19 | https://gate2026.iitg.ac.in/doc/download/2021/cs_2021.pdf | https://gateoverflow.in/357521/gate-cse-2021-set-2-question-19 |
| W1-Q6 | 2018, Q34 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/204108/gate-cse-2018-question-34 |
| W1-Q7 | 2017 Set 2, Q53 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/118613/gate-cse-2017-set-2-question-53 |
| W1-Q8 | 2016 Set 2, Q32 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/39622/gate-cse-2016-set-2-question-32 |
| W1-Q9 | 2015 Set 3, Q14 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/8410/gate-cse-2015-set-3-question-14 |
| W1-Q10 | 2007, Q10 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/1208/gate-cse-2007-question-10 |
| W2-Q1 | 2025 Set 1, Master Q53 / CS Q43 | https://gate2026.iitg.ac.in/doc/download/2025/CS12025.pdf | https://gateoverflow.in/460037/gate-cse-2025-set-1-question-43 |
| W2-Q2 | 2025 Set 2, Master Q55 / CS Q45 | https://gate2026.iitg.ac.in/doc/download/2025/CS22025.pdf | https://gateoverflow.in/460848/gate-cse-2025-set-2-question-45 |
| W2-Q3 | 2024 Set 1, Master Q56 / CS Q46 | https://gate2026.iitg.ac.in/doc/download/2024/CS124S5.pdf | https://gateoverflow.in/422796/gate-cse-2024-set-1-question-46 |
| W2-Q4 | 2020, Q21 | https://gate2026.iitg.ac.in/doc/download/2020/cs_2020.pdf | https://gateoverflow.in/333210/gate-cse-2020-question-21 |
| W2-Q5 | 2019, Q45 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/302803/gate-cse-2019-question-45 |
| W2-Q6 | 2017 Set 1, Q51 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/118745/gate-cse-2017-set-1-question-51 |
| W2-Q7 | 2010, Q48 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/2352/gate-cse-2010-question-48 |
| W2-Q8 | 2010, Q49 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/43329/gate-cse-2010-question-49 |
| W2-Q9 | 2009, Q29 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/1315/gate-cse-2009-question-29 |
| W2-Q10 | 2014 Set 1, Q44 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/1922/gate-cse-2014-set-1-question-44 |
| W3-Q1 | 2026 Set 1, Master Q54 / CS Q44 | https://gate2026.iitg.ac.in/doc/download/2026/QPs/CS1.pdf | https://gateoverflow.in/523036/gate-cse-2026-set-1-question-44 |
| W3-Q2 | 2026 Set 2, Master Q54 / CS Q44 | https://gate2026.iitg.ac.in/doc/download/2026/QPs/CS2.pdf | https://gateoverflow.in/523102/gate-cse-2026-set-2-question-44 |
| W3-Q3 | 2024 Set 1, Master Q62 / CS Q52 | https://gate2026.iitg.ac.in/doc/download/2024/CS124S5.pdf | https://gateoverflow.in/422790/gate-cse-2024-set-1-question-52 |
| W3-Q4 | 2024 Set 2, Master Q64 / CS Q54 | https://gate2026.iitg.ac.in/doc/download/2024/CS224S6.pdf | https://gateoverflow.in/422843/gate-cse-2024-set-2-question-54 |
| W3-Q5 | 2022, Master Q38 / CS Q28 | https://gate2026.iitg.ac.in/doc/download/2022/cs_2022.pdf | https://gateoverflow.in/371908/gate-cse-2022-question-28 |
| W3-Q6 | 2020, Q53 | https://gate2026.iitg.ac.in/doc/download/2020/cs_2020.pdf | https://gateoverflow.in/333178/gate-cse-2020-question-53 |
| W3-Q7 | 2019, Q33 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/302815/gate-cse-2019-question-33 |
| W3-Q8 | 2015 Set 2, Q47 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/8247/gate-cse-2015-set-2-question-47 |
| W3-Q9 | 2013, Q52 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/379/gate-cse-2013-question-52 |
| W3-Q10 | 2013, Q53 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/43294/gate-cse-2013-question-53 |
| W4-Q1 | 2021 Set 2, Q27 | https://gate2026.iitg.ac.in/doc/download/2021/cs_2021.pdf | https://gateoverflow.in/357513/gate-cse-2021-set-2-question-27 |
| W4-Q2 | 2019, Q1 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/302847/gate-cse-2019-question-1 |
| W4-Q3 | 2017 Set 1, Q54 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/118748/gate-cse-2017-set-1-question-54 |
| W4-Q4 | 2014 Set 2, Q43 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/2009/gate-cse-2014-set-2-question-43 |
| W4-Q5 | 2014 Set 2, Q44 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/2010/gate-cse-2014-set-2-question-44 |
| W4-Q6 | 2013, Q20 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/1442/gate-cse-2013-question-20 |
| W4-Q7 | 2012, Q54 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/2192/gate-cse-2012-question-54 |
| W4-Q8 | 2012, Q55 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/43311/gate-cse-2012-question-55 |
| W4-Q9 | 2011, Q43 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/2145/gate-cse-2011-question-43 |
| W4-Q10 | 2008, Q73 | https://gate2026.iitg.ac.in/download.html | https://gateoverflow.in/43491/gate-cse-2008-question-73 |

---

# Validation Summary

| Check | Result |
|---|---:|
| Total questions | **60** |
| GATE CSE PYQs | **40** |
| Complete textbook exercises | **20** |
| Q1–Q10 are GATE in every week | **Yes** |
| Q11–Q15 are marked `CLASS DISCUSSION` in every week | **Yes** |
| Easy / purely definitional questions | **0** |
| External image assets required | **0** |
| Direct official-paper URLs recorded | **16 / 40** |
| Older PYQs linked through the official 2007–2025 GATE archive | **24 / 40** |
| GateOverflow indexed cross-checks recorded | **40 / 40** |
| Entries requiring content/source-number re-selection | **0** |
| Special source note | **GATE 2020 Q53 retained with interpretation-sensitivity note** |

**No solutions, hints, or answer key are included.**
