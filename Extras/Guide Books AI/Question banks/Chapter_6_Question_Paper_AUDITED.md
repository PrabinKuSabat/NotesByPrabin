# MTCS 102 — Chapter 6 Question Paper

## Warehouse-Scale Architectures for Utility Computing

**Primary text:** John L. Hennessy, David A. Patterson, and Christos Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 6  
**Format:** 4 weeks × 15 questions = 60 questions  
**Weekly split:** Q1–Q10 = GATE CSE previous-year questions; Q11–Q15 = complete textbook exercises for class discussion  
**Difficulty:** Medium or High only  
**Solutions / answer key:** Not included

> **Question-counting rule:** One source exercise is one question even when it has multiple subparts. No textbook exercise has been split.
>
> **GATE coverage note — audited:** GATE CSE rarely asks WSC TCO, SLA, tail latency, or data-center power directly. The revised forty-PYQ block therefore focuses on mechanisms that transfer cleanly to WSCs: DMA/high-throughput I/O, interrupt handling, bulk data movement, link utilization, TCP congestion control, web/IP service communication, routing, fragmentation, and network scaling. The virtual-memory/TLB questions formerly duplicated from Chapter 2 have been removed; Chapter 2 is their canonical placement.
>
> **Wording:** Every revised PYQ is self-contained: required alternatives, tables, event sequences, and numerical parameters are included in this file. Textbook figure dependencies remain explicitly identified.
>
> **Figures:** No external image asset is required for this Markdown paper.

---

# Week 1 — High-Performance I/O, DMA, Interrupts, and Protection

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2024 • Set 1 • Q5] — Medium

Which statement is **false**?

1. In cycle-stealing DMA, one word can be transferred between an I/O device and main memory during a stolen cycle.
2. Burst-mode DMA is suitable for high-throughput block transfer.
3. Programmed I/O generally provides better useful CPU utilization than interrupt-driven I/O.
4. A vectored interrupt can reduce the work required to identify the proper interrupt-service routine compared with a non-vectored interrupt.

### Q2. [GATE CSE 2024 • Set 2 • Q1] — Medium

A **4 MHz** processor shares memory with a DMA controller. During one stolen processor cycle, the DMA controller transfers **8 bytes**. Exactly **1% of processor cycles** are used by DMA.

Calculate the device transfer rate in **bits/s**.

### Q3. [GATE CSE 2022 • Q7] — Medium

Which I/O mechanism normally provides the highest throughput for transferring a **large block of data from a hard disk directly to main memory**?

1. DMA-based I/O
2. Interrupt-driven I/O
3. Polling-based I/O
4. Programmed I/O

### Q4. [GATE CSE 2021 • Set 2 • Q20] — Medium

A DMA controller transfers **one 8-bit character per stolen CPU cycle** from an I/O device to memory. The processor runs at **2 MHz**, and **0.5% of the processor cycles** are stolen by DMA.

Find the device data-transfer rate in **bits/s**.

### Q5. [GATE CSE 2011 • Q28] — High

A non-pipelined processor transfers **500 bytes** from an I/O device to memory using an interrupt-service routine.

For the programmed transfer:

- initialize the address register: 1 cycle;
- initialize the count register: 1 cycle;
- load one byte from the device: 2 cycles;
- store one byte to memory: 2 cycles;
- increment the address: 1 cycle;
- decrement the count: 1 cycle;
- test/branch for the loop: 1 cycle.

A DMA implementation requires **20 processor cycles of setup** and then **2 cycles per byte transferred**.

Approximately what speedup is obtained by using DMA?

1. 3.4
2. 4.4
3. 5.1
4. 6.7

### Q6. [GATE CSE 2005 • Q70] — High

A disk drive has:

- 16 surfaces,
- 512 tracks/surface,
- 512 sectors/track,
- 1 KiB/sector,
- rotational speed = 3000 rpm.

It operates in cycle-stealing DMA mode. Whenever one **4-byte word** is ready, one DMA memory cycle transfers that word. The memory-cycle time is **40 ns**.

What is the **maximum percentage of time** for which the CPU can be blocked by the DMA operation?

1. 10%
2. 25%
3. 40%
4. 50%

### Q7. [GATE CSE 2004 • Q68] — High

A hard disk transfers data continuously to memory at **10 MB/s** using DMA. The processor runs at **600 MHz**. It takes **300 processor cycles** to initiate a DMA transfer and **900 cycles** to complete the DMA handling. Each DMA request transfers **20 KiB**.

What percentage of CPU time is consumed by DMA-related processing?

1. 5%
2. 1%
3. 0.5%
4. 0.1%

### Q8. [GATE CSE 2008 • Q64] — Medium

Which statement about synchronous and asynchronous I/O is **not true**?

1. In synchronous I/O, the invoking process waits for completion; in asynchronous I/O it may continue execution.
2. In both synchronous and asynchronous I/O, completion may cause an interrupt to invoke an ISR.
3. An asynchronous-I/O call need not block the calling process until the transfer completes.
4. In synchronous I/O, a blocked process may be awakened after the ISR handles completion.

### Q9. [GATE CSE 2005 • Q20] — Medium

User programs are normally prevented from performing unrestricted I/O. Explicit I/O instructions can be made privileged. In a **memory-mapped I/O** system, however, device registers are accessed through ordinary memory instructions.

Which statement is correct?

1. Protection can still be enforced using the memory-protection/address-translation mechanism controlled by the operating system.
2. Memory-mapped I/O necessarily makes all I/O registers directly accessible to user programs.
3. Protection is possible only during system configuration and not while programs run.
4. Memory-mapped I/O makes hardware protection impossible.

### Q10. [GATE CSE 2026 • Set 2 • Master Q18 / CS Q8] — Medium

Consider two statements about interrupt handling:

- **S1:** In a non-vectored interrupt mechanism, it usually takes more time to begin the ISR than in a vectored mechanism.
- **S2:** In a daisy-chain interrupt mechanism, the CPU individually polls every input device to determine the interrupt source.

Which option is correct?

1. Both S1 and S2 are true.
2. Both S1 and S2 are false.
3. S1 is true and S2 is false.
4. S1 is false and S2 is true.

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

# Week 2 — Transport, Link Utilization, and Bulk Data Movement

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2025 • Set 2 • Q26] — Medium

Stop-and-Wait is used between two nodes.

- frame size = **3000 bits**
- link rate = **2000 bit/s**
- one-way propagation delay = **100 ms**
- processing and ACK transmission times are negligible

What is the channel utilization?

1. 88.23%
2. 93.75%
3. 85.44%
4. 66.67%

### Q2. [GATE CSE 2022 • Q49] — High

A **100 Mb/s** link connects an earth station to a satellite at altitude **2100 km**. A packet of size **1000 bytes** is transmitted from the earth station. Assume propagation speed is \(3\times10^8\) m/s and ignore all delays other than packet transmission and propagation.

How many milliseconds after the first bit starts transmission does the satellite completely receive the packet? Round to two decimal places.

### Q3. [GATE CSE 2024 • Set 2 • Q44] — High

A TCP connection has congestion window **12 MSS** when a timeout occurs. Assume the usual TCP behavior in which the congestion window is reset after the timeout and the slow-start threshold is set from the pre-timeout window. During the next **two RTTs**, all transmitted segments are acknowledged successfully.

What is the congestion-window size, in MSS, **during the third RTT after the timeout**?

### Q4. [GATE CSE 2020 • Q55] — High

A TCP connection has:

- RTT = **6 ms**,
- receiver-advertised window = **50 KiB**,
- slow-start threshold = **32 KiB**,
- MSS = **2 KiB**.

The connection is established at time \(t=0\), the sender always has data to transmit, and there is no packet loss.

What is the congestion-window size, in KiB, at \(t+60\) ms after all acknowledgements arriving up to that time have been processed?

### Q5. [GATE CSE 2014 • Set 1 • Q27] — High

A TCP connection has congestion window **32 KiB** when a timeout occurs. RTT is **100 ms** and MSS is **2 KiB**. Assume the standard timeout response: the slow-start threshold is set to half the previous congestion window and congestion-window growth restarts from one MSS.

How many milliseconds are required for the congestion window to reach **32 KiB** again?

### Q6. [GATE CSE 2022 • Q50] — High

TCP data is transmitted continuously over a **1 Gb/s** link. The Maximum Segment Lifetime is **60 s**.

What is the minimum number of bits required in the TCP sequence-number field so that the byte-oriented sequence-number space cannot wrap around within one MSL?

### Q7. [GATE CSE 2026 • Set 2 • Master Q58 / CS Q48] — High

A new TCP connection has:

- receiver-advertised window = **48 KiB**,
- MSS = **2 KiB**,
- slow-start threshold = **16 KiB**,
- no timeouts or duplicate acknowledgements.

How many **rounds of transmission** are required for TCP congestion control to reach the congestion-avoidance phase?

### Q8. [GATE CSE 2012 • Q45] — High

TCP begins slow start with congestion window **2 MSS** and slow-start threshold **8 MSS**. A timeout occurs during the **fifth transmission round**. Apply standard slow-start/AIMD behavior.

What is the congestion-window size at the end of the **tenth transmission round**?

1. 8 MSS
2. 14 MSS
3. 7 MSS
4. 12 MSS

### Q9. [GATE CSE 2026 • Set 2 • Master Q21 / CS Q11] — Medium

A file of size **4 million bytes** is transferred between two hosts over three consecutive links having bandwidths **2 Mb/s**, **500 kb/s**, and **1 Mb/s**, respectively. Propagation and processing delays are negligible; there is no background traffic and no additional protocol overhead.

What is the total file-transfer time?

1. 731 s
2. 64 s
3. 8 s
4. 16 s

Use \(1\text{ M}=10^6\) and \(1\text{ k}=10^3\).

### Q10. [GATE CSE 2024 • Set 2 • Q45] — High

An Ethernet segment has:

- transmission rate = \(10^8\) bit/s,
- maximum segment length = **500 m**,
- propagation speed = \(2\times10^8\) m/s.

What is the **minimum Ethernet frame size, in bits**, required for collision detection?

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

# Week 3 — Web/IP Service Communication and Packet Handling

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2025 • Set 2 • Q13] — High

Ethernet carries IPv4 datagrams with an MTU of **1500 bytes**. IPv4 has no options. A UDP segment contains **7488 bytes of application data**.

Determine the number of IPv4 fragments transmitted and the total size of the final fragment including its IPv4 header.

1. 5 fragments, 1488 B
2. 6 fragments, 88 B
3. 6 fragments, 108 B
4. 6 fragments, 116 B

### Q2. [GATE CSE 2024 • Set 1 • Q6] — Medium

When a browser accesses a previously uncached remote web resource, consider these events:

I. HTTP `GET` for the base page  
II. DNS request  
III. HTTP `GET` for an embedded image  
IV. TCP `SYN`

Which ordering is possible for a normal first-time access?

1. IV, II, III, I
2. II, IV, III, I
3. II, IV, I, III
4. IV, II, I, III

### Q3. [GATE CSE 2024 • Set 1 • Q19] — Medium

TCP client \(P\) establishes a connection with server \(Q\). Let \(N_P\) be the sequence number carried by the SYN from \(P\), and let \(N_Q\) be the acknowledgement number carried by the SYN+ACK from \(Q\).

Which statements are correct? **Select all that apply.**

1. \(N_P\) is chosen independently as an initial sequence number rather than being required to be zero.
2. \(N_P\) must always be zero.
3. \(N_Q=N_P\).
4. \(N_Q=N_P+1\).

### Q4. [GATE CSE 2024 • Set 2 • Q28] — Medium

Which CIDR prefix represents exactly the IPv4 address range

```text
10.12.2.0 through 10.12.3.255
```

1. `10.12.2.0/23`
2. `10.12.2.0/24`
3. `10.12.0.0/22`
4. `10.12.2.0/22`

### Q5. [GATE CSE 2024 • Set 1 • Q26] — High

A path \(P\rightarrow Q\rightarrow R\) has two links. A file of \(10^6\) bytes is split into chunks of \(10^3\) bytes. Each link operates at \(10^6\) bit/s. Header overhead, propagation, processing, and queueing delays are negligible. Node \(Q\) is full-duplex and forwards a chunk to \(R\) as soon as that complete chunk has arrived.

If transmission starts at \(t=0\), when does the complete file reach \(R\)?

1. 8.000 s
2. 8.008 s
3. 15.992 s
4. 16.000 s

### Q6. [GATE CSE 2023 • Q42] — High

A browser cache is empty and no relevant DNS records are initially cached. Name resolution uses a **three-tier iterative DNS hierarchy**. The base HTML page is negligibly small and contains **10 equally small embedded objects on the same server**. Treat every relevant network round trip as one RTT.

Which statements are correct? **Select all that apply.**

1. Non-persistent HTTP with at most five parallel TCP connections can complete in 7 RTTs.
2. Persistent HTTP with pipelining can complete in 5 RTTs.
3. Non-persistent HTTP with at most five parallel TCP connections requires 9 RTTs.
4. Persistent HTTP with pipelining requires 6 RTTs.

### Q7. [GATE CSE 2024 • Set 1 • Q21] — Medium

Which IPv4 header fields are modified by a Network Address Translation (NAT) device when a packet travels from an internal network to an external network? **Select all that apply.**

1. Source IP address
2. Destination IP address
3. Header checksum
4. Total Length

### Q8. [GATE CSE 2024 • Set 2 • Q13] — Medium

Node \(X\) has a TCP connection to node \(Y\). Packets travel through an IP router \(R\). Ethernet switch \(S\) is the first switch on the path from \(X\) to \(R\).

When an IP packet leaves \(X\), which statements are correct? **Select all that apply.**

1. The destination IP address is the IP address of \(R\).
2. The destination IP address is the IP address of \(Y\).
3. The destination MAC address is the MAC address of switch \(S\).
4. The destination MAC address is the MAC address of \(Y\).

### Q9. [GATE CSE 2013 • Q37] — High

An IPv4 fragment has:

- `M = 0`,
- `HLEN = 10`,
- total length = **400 bytes**,
- fragment offset = **300**.

Determine the positions in the original IP payload of the **first and last data bytes** carried by this fragment. HLEN is measured in 32-bit words and fragment offset in 8-byte units.

### Q10. [GATE CSE 2024 • Set 2 • Q18] — Medium

Which statements about IPv4 fragmentation are true? **Select all that apply.**

1. Fragmentation of an IP datagram is performed only at the source.
2. An IP router may fragment a datagram when the datagram is larger than the MTU of its outgoing link.
3. Reassembly of fragments is performed only at the destination.
4. Reassembly is performed at every intermediate router along the source-to-destination path.

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

# Week 4 — Routing, Reliability, and Network Scaling

## GATE CSE Questions — Questions 1–10

### Q1. [GATE CSE 2023 • Q15] — Medium

Which statements about **OSPF** are incorrect? **Select all that apply.**

1. OSPF implements the Bellman-Ford distance-vector algorithm.
2. OSPF uses Dijkstra's shortest-path algorithm.
3. OSPF is an inter-domain routing protocol used between autonomous systems.
4. OSPF supports hierarchical routing through areas.

### Q2. [GATE CSE 2023 • Q55] — High

A router uses longest-prefix matching with this forwarding table:

| Destination prefix | Interface |
|---|---:|
| `200.150.0.0/16` | 1 |
| `200.150.64.0/19` | 2 |
| `200.150.68.0/24` | 3 |
| `200.150.68.64/27` | 4 |
| default | 0 |

Through which interface is a packet for destination `200.150.68.118` forwarded?

### Q3. [GATE CSE 2024 • Set 1 • Q48] — High

A router has these forwarding entries:

| Prefix | Next hop |
|---|---|
| `10.1.1.0/24` | R1 |
| `10.1.1.128/25` | R2 |
| `10.1.1.64/26` | R3 |
| `10.1.1.192/26` | R4 |

Exactly **20 packets** are sent to each of these destinations:

`10.1.1.16`, `10.1.1.72`, `10.1.1.132`, `10.1.1.191`, `10.1.1.205`.

How many of the 100 packets are forwarded through **R2**?

### Q4. [GATE CSE 2020 • Q38] — High

An organization needs an IPv4 subnet for **1500 computers**. Its ISP owns the address block `202.61.0.0/17`.

Consider these candidate subnets:

I. `202.61.84.0/21`  
II. `202.61.104.0/21`  
III. `202.61.64.0/21`  
IV. `202.61.144.0/21`

Which pair consists of valid subnets contained in the ISP's block and large enough for the organization?

1. I and II
2. II and III
3. III and IV
4. I and IV

### Q5. [GATE CSE 2026 • Set 1 • Master Q44 / CS Q34] — High

A TCP sender successfully establishes a connection. The slow-start threshold is **10,000 segments**, RTT is fixed at **1 ms**, the sender always has data, segments are numbered starting at 1, and no segment is lost.

Let \(t\) be the time in milliseconds at which transmission of segment **2000** begins. Which interval contains \(t\)?

1. \(9\le t<10\)
2. \(10\le t<11\)
3. \(11\le t<12\)
4. \(12\le t<13\)

### Q6. [GATE CSE 2026 • Set 1 • Master Q45 / CS Q35] — High

A sliding-window protocol operates over a lossless link. Each frame is **1000 bits**, link bandwidth is **100 kb/s**, and one-way propagation delay is **100 ms**. Sender/receiver processing times and ACK transmission time are zero.

What minimum sender-window size \(W\), in frames, is required for **100% link utilization**?

1. 10
2. 21
3. 20
4. 11

### Q7. [GATE CSE 2026 • Set 2 • Master Q65 / CS Q55] — High

Two hosts are directly connected by a lossless link of length **3000 km**. Link bandwidth is \(10^8\) bit/s and propagation delay is **5 ns/m**. Every transmitted **data byte** is assigned a unique sequence number.

Let \(N\) be the minimum sequence-number-field width such that:

1. sequence numbers do not wrap around before **60 s**, and
2. maximum link utilization can be achieved.

Find \(N\).

### Q8. [GATE CSE 2021 • Set 1 • Q44] — High

A TCP client is connected to a server application listening on port \(P\) of host \(S\). While the connection is active, host \(S\) crashes and later reboots. The client does not use TCP keepalive.

Which behaviors are possible? **Select all that apply.**

1. A client blocked in `receive()` may continue waiting if it otherwise does not detect the failure.
2. After reboot, the server application can again bind/listen on port \(P\).
3. If the client sends data after reboot and no matching connection state exists at \(S\), it may receive a TCP `RST`.
4. Under the same condition, it must receive a TCP `FIN`.

### Q9. [GATE CSE 2021 • Set 1 • Q45] — High

Hosts \(P\) and \(Q\) communicate through router \(R\).

- MTU of link \(P-R\) = **1500 bytes**
- MTU of link \(R-Q\) = **820 bytes**
- \(P\) sends a TCP segment containing **1400 bytes** through \(R\)
- IPv4 header = **20 bytes**
- IPv4 Identification = `0x1234`
- DF is not set

Which statements are correct? **Select all that apply.**

1. \(R\) fragments the IP datagram into two fragments and the total length of the second fragment is 620 bytes.
2. If the second fragment is lost, router \(R\) retransmits it with Identification `0x1234`.
3. If the second fragment is lost, reliability is recovered end-to-end and \(P\) may have to retransmit the TCP data.
4. The TCP destination port can be determined by examining only the second IP fragment.

### Q10. [GATE CSE 2005 • Q73] — High

In a packet-switched network, a message of **24 bytes** must cross a path containing **two intermediate nodes**. Every packet has a **3-byte header**. Assume equal link rates, store-and-forward operation, and ignore propagation/processing delays.

Which packet size minimizes the total end-to-end transmission time?

1. 4 bytes
2. 6 bytes
3. 7 bytes
4. 9 bytes

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

# Organizer Source Ledger — Audited

## Textbook source

Hennessy, Patterson, and Kozyrakis, *Computer Architecture: A Quantitative Approach*, 7th Edition, Chapter 6, **Warehouse-Scale Architectures for Utility Computing**, pp. 558–579.

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
| W2-Q13 | 6.27 | 571 | IT versus cooling efficiency |
| W2-Q14 | 6.28 | 571–572 | CRAC COP / temperature-aware placement |
| W2-Q15 | 6.29 | 572–573 | Energy proportionality |
| W3-Q11 | 6.14 | 562 | MapReduce scaling/locality |
| W3-Q12 | 6.15 | 562–563 | HDFS replication/availability |
| W3-Q13 | 6.16 | 563 | SLA provisioning |
| W3-Q14 | 6.22 | 567–568 | Cloud migration/serverless |
| W3-Q15 | 6.23 | 568 | Query latency/monitoring |
| W4-Q11 | 6.26 | 570–571 | UPS versus per-server batteries |
| W4-Q12 | 6.30 | 573–574 | Consolidation/energy proportionality |
| W4-Q13 | 6.31 | 574 | System dynamic power range |
| W4-Q14 | 6.38 | 577 | Maintenance/manageability |
| W4-Q15 | 6.39 | 577 | Tail latency at scale |

## GATE CSE verification links used for revised questions

- 2024 Set 2 Q45 Ethernet: https://gateoverflow.in/422852/gate-cse-2024-set-2-question-45
- 2024 Set 2 Q18 IPv4 fragmentation: https://gateoverflow.in/422879/gate-cse-2024-set-2-question-18
- 2005 Q70 DMA: https://gateoverflow.in/1393/gate-cse-2005-question-70
- 2004 Q68 DMA: https://gateoverflow.in/1062/gate-cse-2004-question-68
- 2011 Q28 DMA: https://gateoverflow.in/2130/gate-cse-2011-question-28
- 2022 Q49 satellite link: https://gateoverflow.in/371887/gate-cse-2022-question-49
- 2024 Set 2 Q44 TCP: https://gateoverflow.in/422853/gate-cse-2024-set-2-question-44
- 2020 Q55 TCP: https://gateoverflow.in/333176/gate-cse-2020-question-55
- 2014 Set 1 Q27 TCP: https://gateoverflow.in/1794/gate-cse-2014-set-1-question-27
- 2012 Q45 TCP: https://gateoverflow.in/2156/gate-cse-2012-question-45
- 2024 Set 1 Q6: https://gateoverflow.in/422836/gate-cse-2024-set-1-question-6
- 2024 Set 1 Q19: https://gateoverflow.in/422823/gate-cse-2024-set-1-question-19
- 2024 Set 2 Q28: https://gateoverflow.in/422869/gate-cse-2024-set-2-question-28
- 2024 Set 1 Q26: https://gateoverflow.in/422816/gate-cse-2024-set-1-question-26
- 2023 Q42: https://gateoverflow.in/399269/gate-cse-2023-question-42
- 2024 Set 1 Q21: https://gateoverflow.in/422821/gate-cse-2024-set-1-question-21
- 2024 Set 2 Q13: https://gateoverflow.in/422884/gate-cse-2024-set-2-question-13
- 2023 Q15: https://gateoverflow.in/399297/gate-cse-2023-question-15
- 2023 Q55: https://gateoverflow.in/399256/gate-cse-2023-question-55
- 2024 Set 1 Q48: https://gateoverflow.in/422794/gate-cse-2024-set-1-question-48
- 2005 Q73: https://gateoverflow.in/1396/gate-cse-2005-question-73

For 2026 entries, the official IIT Guwahati CS1/CS2 master papers are the authoritative source.

## Audit notes

- All Chapter-2 virtual-memory/TLB duplicates were removed from Chapter 6.
- All GATE questions that previously depended on omitted source tables/parameters were replaced or made self-contained.
- The revised GATE portion is intentionally centered on high-throughput I/O and data-center networking mechanisms rather than generic VM questions.
