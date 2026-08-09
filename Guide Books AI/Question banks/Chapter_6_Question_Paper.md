# MTCS 102 — Chapter 6 Question Paper

## Warehouse-Scale Architectures for Utility Computing

**Primary text:** John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 6  
**Format:** 4 weeks × 15 questions = 60 questions  
**Weekly split:** Q1–Q10 = GATE CSE previous-year questions; Q11–Q15 = complete textbook exercises for class discussion  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included

> **Question-counting rule:** One source exercise is one question even when it has multiple subparts. No textbook exercise has been split.
>
> **GATE coverage note:** GATE CSE rarely asks warehouse-scale TCO, SLA, tail latency, or data-center power directly. The PYQs below are selected only where the mechanism transfers cleanly to Chapter 6: virtualization/address translation, privileged execution, DMA/I/O, bulk data movement, TCP/IP, fragmentation, routing, and end-to-end service communication. Unrelated cache, pipeline, and GPU questions are not used merely to fill the quota.
>
> **Wording:** PYQs and textbook exercises are reformatted/paraphrased while preserving their assessed task and numerical assumptions. Where a textbook exercise requires a chapter figure, the figure number is retained.
>
> **Figures:** No external image asset is required for this Markdown paper.

---

# Week 1 — Virtualization, Address Translation, Protection, and Cloud Isolation

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2026 • Set 1 • CS Q44] — High

A system contains an MMU, a TLB, and a **physically addressed cache**. Whenever a physical page is evicted from main memory, all cache blocks belonging to that page are invalidated.

For one memory reference, which event sequences can **never** occur? **Select all that apply.**

1. TLB miss → page-table hit → cache hit  
2. TLB hit → page-table miss → cache hit  
3. TLB miss → page-table miss → cache hit  
4. TLB miss → page-table miss → cache miss

---

### Q2. [GATE CSE 2026 • Set 2 • CS Q44] — High

A system has:

- TLB reach = **1 MiB**
- page size = **4 KiB**
- virtual-address space = **64 GiB**
- physical-address space = **1 GiB**

Each TLB entry stores a **4-bit process identifier**, the virtual page number, the physical frame number, and **2 control bits**.

Determine the **total TLB storage capacity in bytes**.

---

### Q3. [GATE CSE 2024 • Set 2 • CS Q54] — High

A system uses 32-bit virtual addresses, 4 KiB pages, 4-byte page-table entries, and a **two-level page table**. The first-level directory occupies one page and is always allocated. A second-level page-table page is allocated only when it contains at least one valid PTE.

A process has exactly **2000 mapped virtual pages**.

Let `X` be the minimum and `Y` the maximum possible number of pages occupied by the complete page-table structure. Find **X + Y**.

---

### Q4. [GATE CSE 2024 • Set 1 • CS Q52] — Medium

A paged system uses **2 KiB pages**. The virtual-to-physical mapping is:

| Virtual page | Physical frame |
|---:|---:|
| 0 | 1 |
| 1 | 3 |
| 2 | 2 |
| 3 | 0 |

Find the **decimal physical address** corresponding to virtual address **2500**.

---

### Q5. [GATE CSE 2022 • Q28] — High

Which statement about address translation is **false**?

1. A TLB is normally searched associatively using the virtual page number.
2. A TLB hit followed by a cache miss does not by itself imply a page fault.
3. Every lookup in an inverted page table must necessarily take exactly the same time.
4. In a hashed page table, two colliding virtual addresses need not have identical translation time.

---

### Q6. [GATE CSE 2020 • Q53] — High

A system has a single-level page table in main memory and a TLB. Main-memory access is **100 ns**, TLB lookup is **20 ns**, TLB hit ratio is **95%**, page-fault rate is **10%**, and one page transfer between disk and memory takes **5000 ns**. For **20% of page faults**, a dirty victim page must first be written to disk. Ignore TLB-update time after the fault.

Calculate the **average memory-access time**, in ns, to one decimal place.

---

### Q7. [GATE CSE 2019 • Q33] — High

A machine has 64-bit virtual addresses, 48-bit physical addresses, word-addressable memory with **4 bytes/word**, page size **8 KiB**, and a TLB with **128 valid entries**.

What is the maximum number of **distinct virtual addresses** that can be translated without a TLB miss?

1. `16 × 2^10`  
2. `256 × 2^10`  
3. `4 × 2^20`  
4. `8 × 2^20`

---

### Q8. [GATE CSE 2015 • Set 2 • Q47] — High

A computer uses **8 KiB pages** and 32-bit physical addresses. Each PTE stores the physical-page translation plus one valid bit, one dirty bit, and three protection bits. The maximum page-table size for a process is **24 MiB**.

Determine the length of the **virtual address**, in bits.

---

### Q9. [GATE CSE 2013 • Q52] — High

A system has **46-bit virtual addresses**, **32-bit physical addresses**, a **three-level page table**, and **32-bit PTEs**. Every page-table page is exactly one virtual-memory page.

Using the level sizes specified in the source problem, determine the requested **page size and address-field decomposition**. Show the offset and all page-table index fields.

---

### Q10. [GATE CSE 2008 • Q34] — Medium

For a general-purpose processor, consider an **RFE (Return From Exception)** instruction.

I. RFE must itself be a trap instruction.  
II. RFE must be a privileged instruction.  
III. No exception may be allowed during execution of RFE.

Which must be true?

1. I only  
2. II only  
3. I and II only  
4. I, II and III

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 6 • Exercise 6.1 • p. 558] — CLASS DISCUSSION — High

Using the chapter TCO model and Figures **6.32–6.33**, replace the baseline servers by servers that are **10% faster** at the same utilization but **20% more expensive**.

**(a)** Recalculate WSC **CAPEX**.  
**(b)** If the new servers also consume **15% more power**, recalculate **OPEX**.  
**(c)** Find the new-server purchase price at which the redesigned WSC has TCO comparable to the baseline, accounting for the changed server count and facility critical load.

> **Required textbook material:** Figures 6.32 and 6.33; Section 6.7 TCO assumptions.

---

### Q12. [BOOK • Chapter 6 • Exercise 6.2 • p. 558] — CLASS DISCUSSION — High

A low-power server provides the **same performance**, consumes **15% less power**, but costs **20% more** than the baseline.

**(a)** Determine whether it lowers TCO.  
**(b)** Find its break-even purchase price.  
**(c)** Recalculate the break-even price if electricity cost doubles.

Explain which TCO component drives the decision.

---

### Q13. [BOOK • Chapter 6 • Exercise 6.3 • p. 559] — CLASS DISCUSSION — High

Use the power/performance modes in **Figure 6.45**.

**(a)** If every server runs at medium performance, how many servers are required to match baseline throughput?  
**(b)** Compute CAPEX and OPEX.  
**(c)** A different server is **20% cheaper**, `x%` slower, and uses `y%` less power. Derive the `x–y` trade-off curve for TCO equal to the baseline.

> **Required textbook material:** Figure 6.45.

---

### Q14. [BOOK • Chapter 6 • Exercise 6.4 • p. 559] — CLASS DISCUSSION — Medium

For the alternatives in Exercise 6.3, assume a **constant workload**. Compare server count, utilization, energy, CAPEX, OPEX, reliability exposure, and performance headroom. Recommend a design and state the assumptions that make it preferable.

---

### Q15. [BOOK • Chapter 6 • Exercise 6.5 • p. 559] — CLASS DISCUSSION — High

Repeat the Exercise 6.4 comparison for a WSC whose demand varies substantially over the day. Analyze peak provisioning, idle power, power/performance modes, spare capacity, SLA risk, and TCO. Explain why the constant-load optimum may not remain optimal.

---

# Week 2 — High-Performance I/O, DMA, Bulk Data Movement, and Transport Efficiency

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2024 • Set 1 • Q5] — Medium

Which statement is **false**?

1. Cycle-stealing DMA can transfer one word between an I/O device and memory in a stolen cycle.
2. Burst-mode DMA is suitable for high-throughput block transfers.
3. Programmed I/O generally gives better useful CPU utilization than interrupt-driven I/O.
4. A vectored interrupt can reduce the work needed to identify the proper ISR compared with a non-vectored interrupt.

---

### Q2. [GATE CSE 2024 • Set 2 • Q1] — Medium

A **4 MHz** processor shares memory with a DMA controller. During one stolen processor cycle the DMA controller transfers **8 bytes**. Exactly **1% of processor cycles** are used for DMA transfers.

Calculate the device transfer rate in **bits/s**.

---

### Q3. [GATE CSE 2022 • Q7] — Medium

Which I/O method gives the highest throughput for transferring a **large block from hard disk to main memory**?

1. DMA-based transfer  
2. Interrupt-driven transfer  
3. Polling-based transfer  
4. Programmed I/O transfer

---

### Q4. [GATE CSE 2021 • Set 2 • Q20] — Medium

A DMA controller transfers **one 8-bit character per CPU cycle** through cycle stealing. The processor runs at **2 MHz**, and **0.5% of its cycles** are stolen by DMA.

Find the device data-transfer rate in **bits/s**.

---

### Q5. [GATE CSE 2011 • Q28] — High

A non-pipelined processor executes an interrupt-service routine that transfers **500 bytes** from an I/O device to memory. The source question supplies the instruction sequence and cycle costs for the programmed transfer, and then gives the setup/transfer cost for DMA.

Using those source parameters, calculate the **speedup obtained by DMA** over the ISR-based transfer.

> **Required source material:** the instruction/cycle table in GATE CSE 2011 Q28.

---

### Q6. [GATE CSE 2025 • Set 2 • Q26] — Medium

Stop-and-Wait is used between two nodes.

- frame = **3000 bits**
- link rate = **2000 bit/s**
- one-way propagation delay = **100 ms**
- processing and ACK transmission times are negligible

What is channel utilization?

1. 88.23%  
2. 93.75%  
3. 85.44%  
4. 66.67%

---

### Q7. [GATE CSE 2022 • Q49] — High

A **100 Mb/s** link connects an earth station to a satellite at altitude **2100 km**. Using the propagation speed and remaining transfer parameters specified in the source problem, compute the requested **bandwidth-delay / outstanding-data quantity** needed for efficient transfer.

Show the propagation-delay and bandwidth-delay-product calculation.

---

### Q8. [GATE CSE 2024 • Set 2 • Q44] — High

A TCP connection has congestion window **12 MSS** when a **timeout** occurs. Apply the slow-start and congestion-avoidance rules given in the source question and determine the congestion-window size after the specified number of successful RTTs.

Show the window value after each RTT.

---

### Q9. [GATE CSE 2020 • Q55] — High

A TCP connection has **RTT = 6 ms**, with the receiver window, MSS, initial congestion window, and transfer size given in the source question.

Assuming no further losses, determine the requested **transfer time / effective throughput**, accounting for TCP congestion-window growth rather than assuming full-window transmission immediately.

---

### Q10. [GATE CSE 2014 • Set 1 • Q27] — High

A TCP connection has congestion window **32 KiB** when a timeout occurs. RTT is **100 ms** and the MSS is specified in the source problem.

Using TCP slow start followed by congestion avoidance, determine the requested **recovery time or congestion-window value**. Show the new slow-start threshold and the complete window evolution.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 6 • Exercise 6.7 • pp. 559–560] — CLASS DISCUSSION — High

Using Figures **6.32–6.33** and the TCO model, evaluate:

**(a)** a server rated at **200 W** and costing **$3000**;  
**(b)** a server rated at **300 W** and costing **$2000**;  
**(c)** the effect when actual average power is only **70% of nameplate power**.

Estimate **per-server TCO** and explain the difference between facility provisioning based on peak/nameplate power and energy cost based on actual consumption.

---

### Q12. [BOOK • Chapter 6 • Exercise 6.8 • p. 560] — CLASS DISCUSSION — High

A WSC is provisioned against fixed critical power.

**(a)** Assume a **300 W nameplate** server that averages **40% utilization** and actually consumes **225 W**. Calculate actual monthly critical power used, TCO, and unused facility capacity.  
**(b)** Repeat for a **500 W nameplate** server averaging **20% utilization** and consuming **300 W**.

Explain why nameplate provisioning can strand capital at WSC scale.

---

### Q13. [BOOK • Chapter 6 • Exercise 6.27 • p. 571] — CLASS DISCUSSION — High

Use the exercise's simplified WSC operational-power equation. Assume an **8 MW** data center at **80% power usage**, electricity at **$0.10/kWh**, and cooling-inefficiency multiplier **0.8**.

**(a)** Compare savings from a **20% cooling-efficiency improvement** with a **20% IT-equipment efficiency improvement**.  
**(b)** Find the IT-efficiency improvement required to match the cooling saving.  
**(c)** Interpret which class of optimization deserves priority under this model.

---

### Q14. [BOOK • Chapter 6 • Exercise 6.28 • pp. 571–572] — CLASS DISCUSSION — High

For a CRAC unit, `COP = heat removed / cooling work`. Remove **10 kW** of heat in two cases:

- return air **20°C**, COP **1.9**;
- return air **25°C**, COP **3.1**.

**(a)** Calculate cooling input power in both cases.  
**(b)** Calculate the saving enabled by temperature-aware workload placement.  
**(c)** Analyze interactions between independent optimizations such as workload consolidation and ACPI power-state control, and propose a coordination mechanism.

---

### Q15. [BOOK • Chapter 6 • Exercise 6.29 • pp. 572–573] — CLASS DISCUSSION — High

Use **Figure 6.50** to study energy proportionality.

**(a)** Build a linear power-versus-utilization model from peak and idle power and plot performance/W; repeat if idle power is halved and if idle power is zero.  
**(b)** Repeat using the measured power values in Figure 6.50 and compare with the linear model.  
**(c)** Apply the Figure 6.50 utilization mix to **1000 servers** and compute aggregate performance and energy.  
**(d)** Construct a sublinear power curve from 0–50% utilization whose efficiency peaks earlier, then recompute the aggregate result.

> **Required textbook material:** Figure 6.50.

---

# Week 3 — IP, Fragmentation, Web-Service Communication, and Data-Center Networking

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2025 • Set 2 • Q13] — High

Ethernet carries IPv4 datagrams with an MTU of **1500 bytes**. IPv4 has no options. A UDP segment contains **7488 bytes of application data**.

Determine the **number of IPv4 fragments** transmitted and the **total size of the last fragment including its IPv4 header**.

1. 5 fragments, 1488 B  
2. 6 fragments, 88 B  
3. 6 fragments, 108 B  
4. 6 fragments, 116 B

---

### Q2. [GATE CSE 2024 • Set 1 • Q6] — High

A user opens a webpage on a remote server. The browser uses **one TCP connection** to fetch the complete page and its embedded objects. Using the DNS/TCP/HTTP timing assumptions and object layout specified in the source problem, calculate the total **RTTs / elapsed communication time** before the page is fully available.

Separate connection establishment from application-data transfer.

---

### Q3. [GATE CSE 2024 • Set 1 • Q19] — Medium

TCP client `P` establishes a connection with TCP server `Q`. Let `N_P` and `N_Q` be their initial sequence numbers.

Using the SYN, SYN+ACK, and ACK segments of the **three-way handshake**, determine which source statements about sequence and acknowledgement numbers are correct.

---

### Q4. [GATE CSE 2024 • Set 2 • Q28] — Medium

Find the CIDR prefix that represents **exactly** the IPv4 range:

```text
10.12.2.0  through  10.12.3.255
```

Give the network address and prefix length.

---

### Q5. [GATE CSE 2023 • Q7] — Medium

Two hosts use **Stop-and-Wait** over a point-to-point link. Among the source alternatives, identify the combination of propagation delay/link length, transmission rate, and frame size that gives the **lowest link utilization**.

Justify using transmission time relative to round-trip propagation delay.

---

### Q6. [GATE CSE 2022 • Q25] — High

A resolver must resolve **`www.gate.org.in`** and no relevant DNS resource record is cached anywhere initially.

Using the DNS hierarchy and query behavior in the source question, determine the requested **number of DNS messages / RTTs** required to obtain the final address. Distinguish recursive from iterative work.

---

### Q7. [GATE CSE 2020 • Q25] — High

A browser requests a remote webpage with an initially empty cache. The source problem gives the DNS state, number of objects, and HTTP connection behavior.

Calculate the **minimum elapsed time in RTTs** to retrieve the complete page. Account separately for DNS resolution, TCP setup, the base document, and embedded objects.

---

### Q8. [GATE CSE 2020 • Q15] — Medium

Evaluate the source statements about an **IPv4 router**, including whether packet fields can change during forwarding, how router interfaces are addressed, and how forwarding uses the destination address and routing table.

Select the correct statement combination and identify which IPv4 fields can legitimately change hop by hop.

---

### Q9. [GATE CSE 2013 • Q37] — High

An IPv4 fragment has:

- `M = 0`
- `HLEN = 10`
- total length = **400 bytes**
- fragment offset = **300**

Determine the positions in the original IP payload of the **first and last data bytes** carried by this fragment. HLEN is in 32-bit words and fragment offset is in 8-byte units.

---

### Q10. [GATE CSE 2004 • Q57] — High

A host in network `A` sends messages containing **180 bytes of application data** to a host in network `C` through multiple IP networks with different MTUs. Using the transport/network headers and MTUs supplied by the source problem, determine the required **fragmentation and transmission overhead** across the path.

State where fragmentation occurs and where reassembly occurs.

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 6 • Exercise 6.14 • p. 562] — CLASS DISCUSSION — High

A MapReduce-style job processes **300 GB** with:

- network = **1 Gb/s**
- map rate = **10 s/GB**
- reduce rate = **20 s/GB**
- **30%** of input read remotely
- each output written to **two additional nodes** for redundancy
- other parameters from Figure **6.21**

**(a)** If all nodes are in one rack, estimate runtime for **5, 10, 100, and 1000 nodes** and identify the bottleneck at each scale.  
**(b)** With **40 nodes/rack** and remote accesses equally likely to target any node, estimate runtime for **100 and 1000 nodes**.  
**(c)** For 1000 nodes, recalculate if **20%, 50%, and 80%** of remote accesses stay within the same rack.

> **Required textbook material:** Figure 6.21.

---

### Q12. [BOOK • Chapter 6 • Exercise 6.15 • pp. 562–563] — CLASS DISCUSSION — High

HDFS uses three-way replication: local, another copy in-rack, and another copy across racks.

**(a)** From Figure **6.1**, estimate availability of a **10-node** Hadoop cluster with one-, two-, and three-way replication.  
**(b)** Repeat for a **1000-node** cluster and interpret the scaling effect.  
**(c)** For a **1 PB** sort on 1000 nodes with shuffle intermediates written to HDFS, calculate extra disk I/O, intra-rack traffic, and inter-rack traffic.  
**(d)** Using Figure **6.21**, estimate time overhead of two-/three-way replication and compare expected completion time after incorporating failures.  
**(e)** Analyze replicated database logs when each transaction makes one disk access and writes **1 KiB** of log; repeat for an in-memory **10 ms** transaction.  
**(f)** Add the cost of **two network round trips** for two-phase commit.

> **Required textbook material:** Figures 6.1 and 6.21.

---

### Q13. [BOOK • Chapter 6 • Exercise 6.16 • p. 563] — CLASS DISCUSSION — High

A WSC receives **30,000 queries/s** and has the SLA: **95% of queries must finish within 0.5 s**. Figure **6.46** shows response-time distributions for a baseline server and a slower “small” server.

**(a)** Find the number of baseline and small servers required; determine how much cheaper a small server must be to lower server cost.  
**(b)** If flaky-machine and bad-memory event rates for the small server rise by **30%**, recompute server count and break-even price.  
**(c)** In a batch environment, assume a small server gives only **30%** of baseline performance; analyze the resource/cost result.  
**(d)** Explain the danger of partitioning request work too finely.

> **Required textbook material:** Figures 6.1 and 6.46.

---

### Q14. [BOOK • Chapter 6 • Exercise 6.22 • pp. 567–568] — CLASS DISCUSSION — High

Act as infrastructure architect for a web service historically hosted on a server with roughly two quad-core 2.5 GHz Xeons, 8 GB DRAM, three 15K-RPM SAS disks in RAID1, heavy caching, and CPU demand around 0.5–2.5 cores.

**(a)** Select cloud VM configurations that meet or exceed its needs.  
**(b)** Find a cost-efficient deployment and monthly compute cost.  
**(c)** Add networking/IP cost for **100 GB/day inbound and outbound**.  
**(d)** Evaluate whether it fits the free-tier assumptions in the exercise or a clearly stated current equivalent.  
**(e)** For a Netflix-scale streaming/encoding service, map suitable cloud services to storage, encoding, CDN, scaling, monitoring, and compute.  
**(f)** Compare equivalent offerings from at least two other cloud providers.  
**(g)** State when serverless/FaaS is preferable and when it is not.

---

### Q15. [BOOK • Chapter 6 • Exercise 6.23 • p. 568] — CLASS DISCUSSION — High

Figure **6.36** motivates the economic importance of low user-visible latency.

**(a)** For web search, identify architectural techniques for lowering end-to-end query latency.  
**(b)** Design a monitoring system that can determine where query time is spent; specify queue, network, storage, CPU, and distributed-trace measurements.  
**(c)** Disk accesses per query are normally distributed with mean **2** and standard deviation **3**. Determine the disk-access latency needed to satisfy a **0.1 s** latency SLA for **95%** of queries under the exercise assumptions.  
**(d)** Analyze how in-memory caching changes long-latency-event frequency and the query tail.  
**(e)** Compare extra capacity for lower average utilization with software techniques aimed specifically at tail latency.

> **Required textbook material:** Figure 6.36.

---

# Week 4 — Routing, Congestion, Reliability, and WSC-Scale Tail Effects

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2021 • Set 1 • Q49] — High

A full-duplex, error-free point-to-point link is used continuously by a sender that always has frames waiting to transmit.

Assume:

- data-frame size = **2000 bits**;
- acknowledgement size = **10 bits**;
- link rate in each direction = **1 Mb/s**;
- one-way propagation delay = **100 ms**;
- receiver frame-processing time is negligible;
- sender acknowledgement-processing time is negligible.

A sliding-window protocol is used.

What is the **minimum sender-window size, in frames**, required to achieve **50% utilization** of the forward link? Give the nearest integer.

---

### Q2. [GATE CSE 2021 • Set 1 • Q44] — High

A TCP client is connected to a TCP server application listening on port `P` of host `S`. While the TCP connection is active, host `S` crashes and later reboots. Assume that the client does **not** use TCP keepalive.

Which of the following behaviors are possible? **Select all that apply.**

1. A client blocked in `receive()` may continue waiting indefinitely if it does not otherwise detect the failure.
2. After reboot, the server application can again bind/listen on port `P`.
3. If the client sends data after the reboot and no matching connection state exists at `S`, it may receive a TCP `RST`.
4. If the client sends data after the reboot and no matching connection state exists at `S`, it must receive a TCP `FIN`.

For class use, relate the answer to the difference between **application/service restart** and **transport-connection state recovery**.

---

### Q3. [GATE CSE 2021 • Set 1 • Q45] — High

Hosts `P` and `Q` communicate through router `R`.

- MTU of link `P–R` = **1500 bytes**;
- MTU of link `R–Q` = **820 bytes**;
- `P` sends a TCP segment of **1400 bytes** to `Q` through `R`;
- the IPv4 header is **20 bytes**;
- IPv4 identification field = `0x1234`;
- the **DF** bit is not set.

Which statements are correct? **Select all that apply.**

1. `R` fragments the datagram into two fragments, and the total length of the second fragment is **620 bytes**.
2. If the second fragment is lost, router `R` retransmits that fragment with identification `0x1234`.
3. If the second fragment is lost, reliability is recovered end-to-end and `P` may be required to retransmit the TCP data rather than `R` retransmitting an IP fragment.
4. The TCP destination port can be determined by examining only the second IP fragment.

---

### Q4. [GATE CSE 2026 • Set 2 • Master Q58 / CS Q48] — High

Consider a new TCP connection between a sender and a receiver.

- receiver-advertised window = **48 KiB**;
- MSS = **2 KiB**;
- slow-start threshold = **16 KiB**;
- there are no timeouts or duplicate acknowledgements.

How many **rounds of transmission** are required for TCP congestion control to reach the **congestion-avoidance phase**?

---

### Q5. [GATE CSE 2022 • Q45] — High

A router has the subnet/prefix table given in the source question. For each destination address listed in that problem, determine the selected next hop using **longest-prefix matching**.

For every address, show all matching entries and explain why the most specific prefix wins.

> **Required source material:** the routing table in GATE CSE 2022 Q45.

---

### Q6. [GATE CSE 2020 • Q38] — High

A TCP sender starts with the congestion-window and slow-start-threshold values specified in the source question. Follow the stated sequence of acknowledgements and loss events and calculate the requested congestion-window value.

Show explicitly when the sender is in **slow start**, **congestion avoidance**, and **multiplicative decrease**.

> **Required source material:** the initial TCP state and event sequence in GATE CSE 2020 Q38.

---

### Q7. [GATE CSE 2026 • Set 1 • Q34] — High

A TCP sender has successfully established a connection. The source problem specifies its initial congestion window, slow-start threshold, receiver window, and a sequence of loss/acknowledgement events.

Determine the requested final congestion-window value or number of transmitted segments, showing each slow-start and congestion-avoidance transition.

> **Required source material:** the TCP parameters/event sequence in the source paper.

---

### Q8. [GATE CSE 2012 • Q45] — High

In TCP AIMD, the congestion window at the beginning of slow start is **2 MSS**. Using the slow-start threshold and loss/acknowledgement sequence stated in the source question, compute the requested congestion-window evolution.

Do not treat slow-start growth as linear.

> **Required source material:** the threshold and event sequence in GATE CSE 2012 Q45.

---

### Q9. [GATE CSE 2022 • Q50] — High

TCP data is transferred over a **1 Gb/s** link. The **Maximum Segment Lifetime (MSL)** is **60 s**.

What is the **minimum number of bits required in the TCP sequence-number field** so that the sequence-number space cannot wrap around within one MSL?

Remember that TCP sequence numbers count **bytes**, not bits.

---

### Q10. [GATE CSE 2026 • Set 2 • Master Q65 / CS Q55] — High

A link-layer protocol is to be designed between two hosts directly connected by a **lossless 3000 km link**.

Assume:

- link bandwidth = \(10^8\) bit/s;
- propagation delay = **5 ns/m**;
- every transmitted **data byte** is assigned a unique sequence number.

Let \(N\) be the minimum number of bits in the sequence-number field such that:

1. sequence numbers do **not wrap around before 60 s**, and
2. the protocol can achieve the **maximum utilization of the link**.

Determine \(N\).

---

## CLASS DISCUSSION — Complete Book Exercises — Questions 11–15

### Q11. [BOOK • Chapter 6 • Exercise 6.26 • pp. 570–571] — CLASS DISCUSSION — High

Compare a **facility-wide UPS** with **92% efficiency** against per-server batteries with **99.99% efficiency**. Other power-path efficiencies are:

- substation switching = **99.7%**
- PDU = **98%**
- step-down stage = **98%**
- other breakers = **99%**

**(a)** Calculate the overall power-infrastructure efficiency improvement of the per-server-battery design.  
**(b)** If the facility UPS costs **10% of IT-equipment cost**, use the case-study TCO model to find the break-even battery cost as a fraction of one server's price.  
**(c)** Compare manageability, failure domains, serviceability, replacement, monitoring, and fault isolation.

---

### Q12. [BOOK • Chapter 6 • Exercise 6.30 • pp. 573–574] — CLASS DISCUSSION — High

Use Figures **6.50–6.52**. Server A is the baseline; Server B is less energy-proportional but more energy-efficient over part of its operating range.

**(a)** For the 1000-server utilization mix, calculate total performance and energy for A and B.  
**(b)** Recalculate after consolidation changes the utilization distribution to case C. Compare with an ideal linear energy-proportional server having **0 W idle** and **662 W peak**.  
**(c)** Repeat the consolidation comparison for Server B and explain when better energy proportionality does not imply lower total energy.

> **Required textbook material:** Figures 6.50, 6.51, and 6.52.

---

### Q13. [BOOK • Chapter 6 • Exercise 6.31 • p. 574] — CLASS DISCUSSION — High

Consider two server power breakdowns:

| Component | Case 1 | Case 2 |
|---|---:|---:|
| CPU | 50% | 33% |
| Memory | 23% | 30% |
| Disks | 11% | 10% |
| Networking/other | 16% | 27% |

Peak-to-idle dynamic ranges are CPU **3.0×**, memory **2.0×**, disks **1.3×**, and networking/other **1.2×**.

**(a)** Compute the **overall system dynamic range** in both cases.  
**(b)** Explain why CPU-only optimization cannot make the entire server energy-proportional.  
**(c)** Prioritize component-level improvements for each case.

---

### Q14. [BOOK • Chapter 6 • Exercise 6.38 • p. 577] — CLASS DISCUSSION — High

A cluster uses servers costing **$2000 each**, with:

- annual failure rate = **5% per server**
- average repair/service time = **1 hour/failure**
- replacement parts = **10% of server cost/failure**
- technician labor = **$100/hour**

**(a)** Calculate expected annual maintenance cost per server.  
**(b)** Compare this WSC manageability model with a traditional enterprise data center containing many dedicated application infrastructures.  
**(c)** Discuss the advantages and operational costs of heterogeneous server generations/configurations in one WSC.

---

### Q15. [BOOK • Chapter 6 • Exercise 6.39 • p. 577] — CLASS DISCUSSION — High

A service responds in **100 ms for 99%** of requests but takes **1000 ms for 1%**.

**(a)** A request fans out to **100 servers** and waits for all required replies. Find the probability that the overall request sees at least one slow response.  
**(b)** Find the single-server good-latency probability (“number of nines”) required so that the 100-server request is slow at most **10%** of the time.  
**(c)** Repeat for **2000 servers**.  
**(d)** Propose tail-tolerant mechanisms such as hedged/backup requests, selective replication, cancellation, latency-aware placement, or partitioning changes, and discuss their resource costs.

---

# Organizer Source Ledger

## Textbook source

John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 6, **Warehouse-Scale Architectures for Utility Computing**, Case Studies and Exercises by Parthasarathy Ranganathan, pp. 558–579.

### Selected textbook exercises

| Week-Q | Exercise | Page(s) | Principal topic |
|---|---:|---:|---|
| W1-Q11 | 6.1 | 558 | Faster servers, CAPEX/OPEX/TCO |
| W1-Q12 | 6.2 | 558 | Low-power server break-even |
| W1-Q13 | 6.3 | 559 | Server power/performance modes |
| W1-Q14 | 6.4 | 559 | Constant-load provisioning |
| W1-Q15 | 6.5 | 559 | Variable-load provisioning |
| W2-Q11 | 6.7 | 559–560 | Nameplate power and TCO |
| W2-Q12 | 6.8 | 560 | Critical-load overprovisioning |
| W2-Q13 | 6.27 | 571 | IT vs cooling efficiency |
| W2-Q14 | 6.28 | 571–572 | CRAC COP / temperature-aware placement |
| W2-Q15 | 6.29 | 572–573 | Energy proportionality |
| W3-Q11 | 6.14 | 562 | MapReduce scaling/locality |
| W3-Q12 | 6.15 | 562–563 | HDFS replication/availability |
| W3-Q13 | 6.16 | 563 | SLA provisioning |
| W3-Q14 | 6.22 | 567–568 | Cloud migration/serverless |
| W3-Q15 | 6.23 | 568 | Query latency/monitoring |
| W4-Q11 | 6.26 | 570–571 | UPS vs per-server batteries |
| W4-Q12 | 6.30 | 573–574 | Consolidation/energy proportionality |
| W4-Q13 | 6.31 | 574 | System dynamic power range |
| W4-Q14 | 6.38 | 577 | Maintenance/manageability |
| W4-Q15 | 6.39 | 577 | Tail latency at scale |

## GATE CSE verification ledger

The following are genuine GATE CSE PYQs selected for mechanisms directly transferable to Chapter 6. For final typesetting, the official GATE paper remains authoritative; GateOverflow is used as an indexed cross-check.

**Official archive:** `https://gate2026.iitg.ac.in/download.html`

| Week | PYQ identifiers |
|---|---|
| Week 1 | 2026 S1 Q44; 2026 S2 Q44; 2024 S2 Q54; 2024 S1 Q52; 2022 Q28; 2020 Q53; 2019 Q33; 2015 S2 Q47; 2013 Q52; 2008 Q34 |
| Week 2 | 2024 S1 Q5; 2024 S2 Q1; 2022 Q7; 2021 S2 Q20; 2011 Q28; 2025 S2 Q26; 2022 Q49; 2024 S2 Q44; 2020 Q55; 2014 S1 Q27 |
| Week 3 | 2025 S2 Q13; 2024 S1 Q6; 2024 S1 Q19; 2024 S2 Q28; 2023 Q7; 2022 Q25; 2020 Q25; 2020 Q15; 2013 Q37; 2004 Q57 |
| Week 4 | 2021 S1 Q49; 2021 S1 Q44; 2021 S1 Q45; 2026 S2 Master Q58 / CS Q48; 2022 Q45; 2020 Q38; 2026 S1 Q34; 2012 Q45; 2022 Q50; 2026 S2 Master Q65 / CS Q55 |

### Indexed source pages used during verification

- `https://gateoverflow.in/523036/gate-cse-2026-set-1-question-44`
- `https://gateoverflow.in/523102/gate-cse-2026-set-2-question-44`
- `https://gateoverflow.in/422837/gate-cse-2024-set-1-question-5`
- `https://gateoverflow.in/422896/gate-cse-2024-set-2-question-1`
- `https://gateoverflow.in/371929/gate-cse-2022-question-7`
- `https://gateoverflow.in/357520/gate-cse-2021-set-2-question-20`
- `https://gateoverflow.in/2130/gate-cse-2011-question-28`
- `https://gateoverflow.in/460809/gate-cse-2025-set-2-question-26`
- `https://gateoverflow.in/422853/gate-cse-2024-set-2-question-44`
- `https://gateoverflow.in/333176/gate-cse-2020-question-55`
- `https://gateoverflow.in/1794/gate-cse-2014-set-1-question-27`
- `https://gateoverflow.in/460822/gate-cse-2025-set-2-question-13`
- `https://gateoverflow.in/422836/gate-cse-2024-set-1-question-6`
- `https://gateoverflow.in/371911/gate-cse-2022-question-25`
- `https://gateoverflow.in/333193/gate-cse-2020-question-38`
- `https://gateoverflow.in/371886/gate-cse-2022-question-50`
- `https://gateoverflow.in/357402/gate-cse-2021-set-1-question-49`
- `https://gateoverflow.in/357407/gate-cse-2021-set-1-question-44`
- `https://gateoverflow.in/357406/gate-cse-2021-set-1-question-45`
- Official GATE 2026 CS2 paper: Set 2 Master Q58 and Q65

## Verification notes

- **60 questions total:** 40 GATE CSE PYQs + 20 Chapter 6 textbook exercises.
- **Every week has exactly 15 questions.**
- **Every weekly Q1–Q10 is a GATE CSE PYQ.**
- **Every weekly Q11–Q15 is marked `CLASS DISCUSSION`.**
- **20 distinct Chapter 6 book exercises are used.**
- No textbook exercise has been split into separate numbered questions.
- No solutions or answer key are included.
- No external image asset is required; necessary textbook figures are referenced by figure number.
- Where an old/long PYQ contains a source table or parameter block, the question explicitly identifies the source material required rather than inventing missing values.
