# Intro

Address translation is the hardware+OS mechanism that maps the addresses a program uses (virtual/effective addresses) to the actual DRAM addresses (physical/real addresses) while enforcing protection and supporting features like per-process isolation, sharing, and virtualization.

In “Computer Architecture: A Quantitative Approach” terms, address translation is part of the memory-system _critical path_—it sits between “generate an address” and “access caches/DRAM,” so its latency, caching (TLBs), and interaction with caches strongly influence performance (especially on misses).

## What translation does

Conceptually, the Memory Management Unit (MMU) takes a virtual address (VA) and produces a physical address (PA) **and** checks whether the access is allowed (e.g., load/store/execute, user vs supervisor).  
If translation is enabled, the OS defines the mapping in page tables stored in memory, and the MMU consults those tables to find the physical page frame that backs a given virtual page.​

Key properties architects care about:

- Isolation: different processes can use the same VA range but map to different PAs.
- Sharing: multiple VAs (possibly from different processes) can map to the same PA (shared libraries, shared memory).
- Controlled access: permissions are checked as part of translation (faults/traps on violations).

## Core hardware pieces (MMU, pages, TLB)

Most systems use paging: the VA is split into a virtual page number (VPN) and a page offset; translation replaces the VPN with a physical page number (PPN) and keeps the offset unchanged.
Because consulting page tables in memory would add extra memory accesses, CPUs use a Translation Lookaside Buffer (TLB), which is a small cache of recent translations inside the CPU.​

A typical load/store flow:

- TLB hit: permissions are checked, PPN is returned, PA is formed by concatenating PPN+offset, then caches/DRAM are accessed.​
- TLB miss: hardware “walks” page tables (multi-level lookup) or traps to software (ISA/implementation dependent), then fills the TLB and retries.

## Quantitative-approach performance topics (the “why” behind designs)

The quantitative view is to treat translation as another cache hierarchy problem: TLB hit time, miss rate, and miss penalty dominate average cost when working sets are large or access patterns are random.
A major design point is _TLB and cache concurrency_: because the page offset is identical in VA and PA, a cache can be indexed using offset bits while translation is in progress, then the physical tag is checked after translation completes (a common “virtually indexed, physically tagged” style).​

Architectural issues you should know cold (these drive real microarchitecture choices):

- TLB reach: \text{Reach} = \text{#TLB entries} \times \text{page size}; larger pages increase reach but can increase internal fragmentation.
- Multi-level page tables: reduce page-table memory footprint for sparse address spaces, but increase TLB miss penalty (more memory references per walk).
- Synonyms/aliasing: multiple VAs mapping to one PA can confuse virtually tagged caches; many designs avoid this by physically tagging caches.​

## Protection, virtualization, and I/O translation

Modern systems extend the same idea to devices: an IOMMU sits between DMA-capable devices and memory and translates device-issued I/O virtual addresses (IOVAs) to system physical addresses (SPAs), enforcing protection for DMA.
RISC-V’s IOMMU spec explicitly describes using a two-stage translation model (useful for virtualization), where either stage can be effectively disabled by selecting “Bare” mode for that stage (i.e., no translation/protection at that stage).​

On the CPU side, RISC-V exposes control of supervisor-mode translation via the `satp` CSR (“Supervisor Address Translation and Protection”), and the spec notes that implementations may perform _implicit_ reads of CSRs—specifically, “all S-mode instruction fetches implicitly read the `satp` CSR,” which highlights how directly translation state impacts fetch/execute behavior.​

## Concrete ISA anchors (RISC-V and POWER)

RISC-V: address translation behavior, the role of `satp`, and the broader privileged software stack model (U/S/M modes, traps on illegal accesses) are defined in the RISC-V Privileged Architecture specification.
POWER/OpenPOWER: the Power ISA is published as a multi-book specification by the OpenPOWER Foundation, and it is the authoritative reference set you’d use when comparing address-translation structures and behaviors across architectures.​

If you tell me which “Quantitative Approach” edition you’re reading (4th/5th/6th) and which ISA you want to anchor on (RISC-V Sv39 vs POWER radix/hash, etc.), I can tailor the explanation to the exact chapter/section topics and use the matching spec terminology.
