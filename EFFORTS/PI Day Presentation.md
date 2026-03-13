<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# <system>

You are an expert computer architect, embedded systems engineer, and semiconductor
industry analyst with deep knowledge of instruction set architectures (ISAs),
processor design, open-hardware ecosystems, and India's semiconductor policy landscape.
You are assisting an MTech student and academic professors who are new to RISC-V but
have strong backgrounds in computer engineering. Your goal is to produce a
comprehensive, technically precise, and pedagogically rich reference document.
Use Markdown with rich headers, tables, code blocks, and bullet lists. Be exhaustive —
do not truncate or summarize prematurely. Prioritize depth over brevity. This response
should function as a standalone textbook chapter.
</system>

<continuation_protocol>
CRITICAL INSTRUCTION — READ BEFORE GENERATING:

This prompt is intentionally long and requires more content than a single message can
hold. You MUST follow this protocol strictly:

1. Begin generating from Section 1.
2. After completing each full Section (1 through 8), check your remaining output
capacity.
3. If you are approaching your output limit BEFORE finishing all 8 sections, STOP
cleanly at the end of the last fully completed section.
4. At the stopping point, write exactly this line:
---
⏸️ PAUSED — Completed up to Section [X]. Reply with "continue" to receive
Section [X+1] onwards. Do NOT restart from the beginning.
---
5. When the user replies "continue", resume EXACTLY from where you stopped —
do not repeat any previously generated content.
6. Repeat this process until ALL 8 sections and the Quick Reference Card are
fully delivered.
7. Never skip, abbreviate, or summarize any section to fit within one message.
Each section must be complete before moving to the next.
8. Track internally which sections have been delivered and which remain pending.

PRIORITY ORDER if any single section must be trimmed:
Priority 1 (Never trim): Section 2 (ISA Deep Dive), Section 3 (ARM Comparison),
Section 6 (Hardware Ecosystem)
Priority 2 (Trim last): Section 1, Section 4, Section 7, Section 8
Priority 3 (Can shorten intro prose): Section 5 (India landscape)
</continuation_protocol>

<task>
Produce a detailed, long-form technical reference document on **RISC-V** — covering
its ISA fundamentals, India's strategic developments, hardware ecosystem, ISA deep-dive
from the official unprivileged specification, and a rigorous side-by-side comparison
with ARM. Follow the exact structure below. Never skip a section. Use real product
names, real specification numbers, official extension names, and verified facts.
</task>

---

## SECTION 1 — WHAT IS RISC-V? FOUNDATIONS OF THE ISA

Begin with a complete conceptual foundation:

1.1 Define what an Instruction Set Architecture (ISA) is — its role as the abstract
interface between hardware and software, and why it matters for portability,
toolchains, binary compatibility, and performance.

1.2 Explain the RISC philosophy (Reduced Instruction Set Computing) — its origin from
the 1980s research at Berkeley and Stanford, key principles: load-store
architecture, fixed-length instructions, large register files, pipeline friendliness.

1.3 Define RISC-V specifically:
- Pronounced "RISC Five" (the fifth RISC design from UC Berkeley, 2010)
- Creators: Krste Asanović, David Patterson, and the Berkeley Architecture Research
group (Yunsup Lee, Andrew Waterman)
- Key philosophy: clean-slate design with no legacy baggage
- Open, royalty-free, governed by RISC-V International (non-profit, Switzerland)

1.4 Explain the modular design philosophy:
- Base ISAs: RV32I, RV32E, RV64I, RV64E, RV128I
- Standard extensions: M, A, F, D, Q, L, C, B, J, T, P, V, N, H, S, Zicsr, Zifencei
- The meaning of IMAFD (the "G" general-purpose shorthand)
- What "ratified" vs "frozen" vs "draft" extensions mean
- How privileged and unprivileged ISAs are separated

1.5 Why was RISC-V created when MIPS/ARM/SPARC already existed?
- MIPS: complex licensing, Imagination Technologies ownership uncertainty
- ARM: royalty costs, design constraints, no custom extension freedom
- x86: CISC legacy, Intel/AMD duopoly, binary complexity
- SPARC/PowerPC: declining ecosystems, vendor lock-in
- RISC-V's answer: open standard, extensible, no royalties, academic + commercial
freedom

---

## SECTION 2 — RISC-V ISA: DEEP TECHNICAL DIVE

(Based on the official RISC-V Unprivileged ISA Specification)

Provide a chapter-by-chapter deep-dive as if teaching from the official spec:

2.1 **Base Integer ISA — RV32I**
- 32 general-purpose integer registers (x0–x31), with x0 hardwired to zero
- Register ABI names: zero, ra, sp, gp, tp, t0–t6, s0–s11, a0–a7
- XLEN: definition and its meaning for address/data width
- Instruction length and encoding: 32-bit fixed-length for base ISA
- Instruction formats: R-type, I-type, S-type, B-type, U-type, J-type
→ Draw each format showing bit fields (opcode, funct3, funct7, rs1, rs2, rd, imm)
→ Show example binary/hex encodings for: ADD, ADDI, LW, SW, BEQ, JAL
- Categories of instructions: arithmetic, logical, shift, compare, load, store,
control flow (branches, jumps), system/CSR access
- Notable design choices: no dedicated flag register, no condition codes,
branch-on-compare instructions, PC-relative addressing
- Integer division behavior: divide-by-zero produces defined results (no trap)

2.2 **RV64I — 64-bit Base ISA**
- Extends RV32I with 64-bit registers and addressing
- Additional instructions: LWU, LD, SD, ADDIW, SLLIW, SRLIW, SRAIW, ADDW, etc.
- Explain the W-suffix instructions for 32-bit operations in 64-bit mode
- Sign extension rules and memory model implications

2.3 **Standard Extensions — Detailed Coverage**

| Extension | Letter | Description | Status |
|-----------|--------|-------------|--------|
| Integer Multiply/Divide | M | MUL, MULH, DIV, REM | Ratified |
| Atomic Memory Ops | A | LR/SC, AMO (add, swap, and, or, xor, min, max) | Ratified |
| Single-Precision Float | F | 32-bit IEEE 754 FP, 32 FP registers | Ratified |
| Double-Precision Float | D | 64-bit IEEE 754 FP, extends F | Ratified |
| Quad-Precision Float | Q | 128-bit FP, extends D | Ratified |
| Compressed Instructions | C | 16-bit instruction encoding | Ratified |
| Bit Manipulation | B | Zba, Zbb, Zbc, Zbs sub-extensions | Ratified |
| Vector | V | SIMD/vector ops, VLEN configurable | Ratified (1.0) |
| Hypervisor | H | Virtualization support | Ratified |
| Crypto Scalar | K | AES, SHA2, SM3, SM4, entropy source | Ratified |
    
For each extension above:
- Explain the motivation (why it exists)
- List key instructions or operations
- Explain hardware implications (added registers, state)
- Give concrete use cases (embedded, HPC, security, AI)
2.4 **CSR (Control and Status Registers)**
- Role of CSRs: machine mode control, performance counters, FP control
- Key CSRs: mstatus, misa, medeleg, mideleg, mip, mie, mtvec, mepc, mcause,
mtval, mcycle, minstret, mhpmcounterN
- Zicsr extension as prerequisite for CSR instructions
- CSR access instructions: CSRRW, CSRRS, CSRRC, CSRRWI, CSRRSI, CSRRCI
- Show the misa CSR bit layout and how to decode implemented extensions at runtime

2.5 **Instruction Encoding Philosophy**
- Why immediates are scattered across bit fields (sign-extension efficiency)
- Why opcode space is partitioned to support 16-bit, 32-bit, and 48/64-bit
future encodings
- Variable-length encoding compatibility (C extension, future 48-bit)
- How RISC-V avoids instruction decode complexity compared to x86
- Show the full opcode map (major opcodes table)

2.6 **Memory Model — RVWMO (RISC-V Weak Memory Ordering)**
- Definition of RVWMO and why it matters for multicore correctness
- FENCE instruction: memory ordering, I/O ordering
- How LR/SC (Load-Reserved/Store-Conditional) implements lock-free atomics
- AMO (Atomic Memory Operations): how they differ from LR/SC
- Comparison: TSO (x86) vs RVWMO vs ARM's memory model

2.7 **Exception and Interrupt Handling**
- Privilege levels: Machine (M), Supervisor (S), User (U)
- Trap causes: illegal instruction, misaligned access, page fault, ecall, ebreak
- How mtvec, mepc, mcause are used in trap handling
- ECALL: system call mechanism from U-mode to S-mode to M-mode
- mcause values: complete table of synchronous exceptions and asynchronous interrupts

2.8 **Floating-Point Architecture**
- Separate FP register file: f0–f31
- NaN boxing for narrower FP types stored in wider registers
- Dynamic rounding modes: RNE, RTZ, RDN, RUP, RMM
- FP exception flags in fcsr: NX, UF, OF, DZ, NV
- How F, D, Q extensions are layered (each superset of previous)

2.9 **Vector Extension (RVV 1.0)**
- Key concepts: VLEN (register width), ELEN (max element width), LMUL
- vsetvli/vsetivli: how vector length is configured dynamically
- Vector register groups: v0–v31, with LMUL fractional grouping
- Categories: integer, float, mask, reduction, permutation, load/store
- vl (vector length) and vtype (vector configuration) CSRs
- Why RVV is architecturally superior to fixed-width SIMD (SSE/NEON)
- Show a practical example: vector dot product in RVV assembly

---

## SECTION 3 — RISC-V vs ARM: RIGOROUS SIDE-BY-SIDE COMPARISON

Provide the most detailed comparison possible across all dimensions:

3.1 **Licensing and Openness**

| Attribute | RISC-V | ARM |
|-----------|--------|-----|
| License type | Open ISA, royalty-free | Proprietary, licensing fees |
| Who owns it | RISC-V International (non-profit) | ARM Holdings (SoftBank/Nasdaq: ARM) |
| Chip design cost | Free to implement | Licensing: $1M–$10M+ per core |
| Custom extensions | Yes, defined opcode space | No, only ARM-defined extensions |
| HDL cores available | Yes (Rocket, BOOM, CVA6, etc.) | No public RTL |
| Formal spec | Public PDF + Sail formal model | Partially public (ARM ARM doc) |
| Export control risk | ISA itself not export-controlled | ARM Holdings subject to UK/US rules |
3.2 **Instruction Set Architecture Design**

| Attribute | RISC-V | ARM (AArch64 / ARMv9) |
|-----------|--------|----------------------|
| ISA age / revision | 2010, spec v20241101 | 1985 / ARMv9 (2021) |
| Registers | 32 × 64-bit GPR + 32 FP | 31 × 64-bit GPR + 32 FP/SIMD |
| Instruction length | 16/32-bit (C ext) | 32-bit (A64), 16/32 (Thumb-2) |
| Condition codes | None (branch-on-compare) | NZCV flags register |
| Predication | No (branch-based) | Conditional select (CSEL) |
| Zero register | x0 (hardwired) | xzr (hardwired) |
| Link register | ra / x1 (ABI convention) | x30 (architectural) |
| SIMD / Vector | V extension (VLEN flexible) | NEON (128-bit fixed) + SVE2 (flex) |
| Atomics | LR/SC + AMO | LDAXR/STLXR + SWP/CAS |
| Memory model | RVWMO (weak) | TSO-like (stronger than RVWMO) |
| TrustZone equiv | PMP + Keystone TEE | TrustZone (hardware-enforced) |
| Virtualization | H extension (HS/VS/VU modes) | EL2 hardware hypervisor |
| Pointer Auth (PAC) | Zicfilp / Zicfiss (CFI) | PAC (hardware signed pointers) |
| Memory Tagging | Pointer Masking (draft) | MTE (hardware tag per 16 bytes) |
3.3 **Performance and Pipeline Design**
- ARM Cortex-A series (A55 → A720): mature high-IPC pipelines, 10+ year advantage
- RISC-V high-performance cores: SiFive P550, P670, P870, XuanTie C910/C920
- Discuss BOOM (Berkeley Out-of-Order Machine) as the research flagship
- Current IPC gap: ARM leads by roughly 30–50% in single-thread at same process node
- Where gap is closing: SpacemIT X100, SiFive P870, UltraRISC UR-DP1000
- Performance table: CoreMark/MHz, SPECint2006/GHz across key cores from both sides

3.4 **Ecosystem and Software Support**

| Attribute | RISC-V | ARM |
|-----------|--------|-----|
| Linux kernel | Fully mainlined (2017, v4.15) | Fully mainlined |
| GCC / LLVM | Full support | Full support |
| Android | Experimental (AOSP 2023+) | Primary platform |
| Windows | Not supported | Windows 11 on ARM |
| Fedora / Debian / Ubuntu | Yes (ports archives) | Yes (primary arch) |
| JVM / .NET | Limited but improving | Full support |
| QEMU emulation | Full system + user-mode | Full system + user-mode |
| Binary repositories | Growing but sparse | Dense, mature |
3.5 **Security Architecture Deep Comparison**
- ARM TrustZone vs RISC-V Physical Memory Protection (PMP)
- ARM MTE (Memory Tagging Extension) vs RISC-V Pointer Masking
- ARM PAC (Pointer Authentication Codes) vs RISC-V Zicfilp (landing pads)
- CFI (Control Flow Integrity): Zicfilp + Zicfiss (shadow stack) vs ARM BTI+PAC
- Secure boot comparison: OpenTitan (Ibex/RISC-V) vs ARM TrustZone CCA

3.6 **Power Efficiency**
- C extension: 20–30% code size reduction → less I-cache pressure → lower power
- ARM big.LITTLE / DynamIQ vs RISC-V heterogeneous multi-core options
- Embedded comparison table: ARM Cortex-M4 vs RISC-V RV32IMC at same MHz
- mW/CoreMark across representative chips at 28nm, 12nm, 5nm nodes

3.7 **Unique Advantages of RISC-V Over ARM (Professor-Ready Arguments)**
- No licensing cost → university chip tape-outs possible at wafer cost only
- Open extensibility → AI accelerator vendors add custom matrix-multiply ops in
reserved opcode space without ISA fragmentation
- No ISA fragmentation: one ISA with optional declared extensions (misa CSR)
- Full formal specification in public domain → academic formal verification possible
- Sail formal model: machine-checkable semantics published on GitHub
- Sovereign chip design: India/China/EU can build processors without ARM approval
- Any engineer can read the full spec for free and start implementing today

---

## SECTION 4 — WHY RISC-V WHEN MIPS IS ALREADY A RISC ISA?

Address the precise confusion students commonly have:

4.1 History of MIPS: Stanford MIPS (1981), MIPS32/MIPS64, commercial trajectory
4.2 MIPS licensing saga:
MIPS Technologies → Silicon Graphics (SGI) → Imagination Technologies →
Wave Computing → MIPS Tech LLC → near-death and revival confusion
4.3 MIPS architectural technical debt:
- Branch delay slots (mandatory, breaks clean pipeline abstraction)
- MIPS16 vs microMIPS ISA fragmentation
- No clean modular extension mechanism
- No formal open governance body
4.4 ARM vs MIPS in the market: why ARM won embedded (better tools, support, licensing
flexibility vs full royalty lock-in of MIPS)
4.5 Why RISC-V beats MIPS definitively:
- Zero licensing ambiguity, governed by non-profit
- No branch delay slots (clean ISA design)
- First-class modular extensions with formal ratification process
- GCC/LLVM fully support RISC-V (MIPS support is declining)
- Active global community vs near-dormant MIPS ecosystem

4.6 Full 4-way comparison table:

| Attribute | RISC-V | MIPS | ARM | x86 |
|-----------|--------|------|-----|-----|
| License | Open/free | Proprietary | Proprietary | Proprietary |
| Branch delay slot | No | Yes | No | No |
| Register count | 32 | 32 | 31+xzr | 16 (x86-64: partially) |
| Vector support | RVV 1.0 (flexible) | MSA (128-bit fixed) | SVE2 (flexible) | AVX-512 |
| Atomic ops | LR/SC + AMO | LL/SC | LDAXR/STLXR | LOCK prefix |
| Governance | RISC-V International | Defunct/private | ARM Holdings | Intel/AMD |
| Ecosystem health 2026 | Rapidly growing | Near-dormant | Very mature | Dominant |
| Formal open spec | Yes (Sail model) | No | Partial | No |

---

## SECTION 5 — INDIA's RISC-V STRATEGIC LANDSCAPE

5.1 **Policy and Government Programs**
- DIR-V (Digital India RISC-V) Program: launched April 27, 2022 by MeitY
→ Targets: indigenous RISC-V SoCs for mobile, server, IoT, automotive by 2025
→ Partners: C-DAC, IIT Madras, Sony India, ISRO, BEL, Wipro, Tata Elxsi
- Semicon India Program: ₹76,000 crore (\$10B) semiconductor incentive scheme
- Chips-to-Startups (C2S) Program: semiconductor design education across 120 institutes
- DLI (Design Linked Incentive) Scheme: staged cash incentives for chip design milestones
- 2nd DIR-V Symposium: IIT Madras, March 2–3, 2025

5.2 **C-DAC VEGA and TEJAS Processors**
- VEGA: RISC-V based processor family, MeitY-funded, range from MCU to HPC
- Tejas 32: first ASIC tape-out, available on C-DAC Aries development boards
- Arduino-compatible form factor for university curriculum integration
- Plans for open-source licensing for wider academic deployment

5.3 **IIT Madras — SHAKTI Processor Family**
- India's most prominent RISC-V family from IIT Madras RISE group
- Core classes:
→ E-class: ultra-low power IoT (RV32IM)
→ C-class: embedded Linux (RV64GC)
→ I-class: server-grade (RV64GC with OOO pipeline)
→ M-class, S-class, H-class: progressively higher performance
- Taped out at ISRO's SCL (Semi-Conductor Laboratory), Chandigarh (180nm, 22nm)
- Used in academic research, drone avionics, and satellite computing

5.4 **DHRUV64 — India's Milestone 64-bit Processor**
- Announced December 2024 under the DIR-V national program
- Specifications: 1.0 GHz, 64-bit, dual-core RISC-V
- First indigenously designed 64-bit chip delivered under government program
- Signals India's readiness to move from design to production

5.5 **Mindgrove Technologies** (IIT Madras spinoff, Chennai)
- Secure IoT RISC-V chip: meets 35 of 36 STQC (Standardization Testing Quality
Certification) compliance criteria — highest by any Indian startup
- Vision chip product line in prototyping stage
- Backed by DLI scheme; positioning as end-to-end problem-solver
- Focus: edge AI, secure IoT, automotive MCUs

5.6 **Vervesemi Microelectronics**
- First company to receive DLI scheme approval milestone (February 2026)
- RISC-V based chip family for embedded/IoT sector
- Demonstrates DLI scheme delivering real commercial outcomes

5.7 **Karnataka Government — India's First RISC-V AI PC (November 2025)**
- India's first AI-powered affordable PC runs RISC-V processor + Linux
- Features: 4G connectivity, Wi-Fi, AI acceleration
- Designed for mass government/education deployment
- Signals state-level commitment to RISC-V beyond central policy

5.8 **India as Global RISC-V Talent and Supply Hub**
- DIR-V's strategic goal: position India as exporter of RISC-V SoCs globally
- Target partnerships: HP, VVDN, Apple supply chain integration discussions
- ISRO: using SHAKTI-class cores for space-grade processor needs
- C2S program: 100,000+ engineers trained in chip design by 2026 target

---

## SECTION 6 — RISC-V HARDWARE ECOSYSTEM: BOARDS, CPUs, AND SUPPORT

6.1 **Hardware Category Definitions**
Clarify the three buyer categories and what maps to each:
- SBC (Single Board Computer): complete computer on one PCB, Raspberry Pi-style
- Development Board: larger, expansion-focused, PCIe/DDR, for serious development
- Server/Workstation Board: full desktop/server class, multi-slot, ECC memory
- Embedded Module: SoM (System-on-Module) for product integration

6.2 **Most Powerful and Notable RISC-V Boards (2025–2026)**

    | Board | CPU | Cores/Freq | Max RAM | RAM Type | GPU | PCIe | Buy in India |
    |-------|-----|-----------|---------|----------|-----|------|--------------|
    | Milk-V Pioneer | SOPHGO SG2042 (T-Head C920) | 64 @ 2.0GHz | 128GB ECC | DDR4-3200 4ch | External via PCIe x16 Gen4 (AMD/Intel verified) | x16 Gen4 + x8 Gen4 + x4 Gen4 | Mouser India / direct import |
    | SiFive HiFive Premier P550 | ESWIN EIC7700X (SiFive P550 core) | 4 OOO @ 1.4GHz | 32GB | DDR5 | IMG AXM-8-256 onboard + PCIe x4 slot | PCIe 3.0 x4 | sifive.com (ships to India) |
    | Milk-V Jupiter | SpacemIT M1 | 8 @ 1.8GHz | 16GB | LPDDR4X | IMG BXE-2-32 (Vulkan 1.2) | PCIe 2.0 x4 | milkv.io direct |
    | Banana Pi BPI-F3 | SpacemIT K1 | 8 @ 1.6GHz | 16GB | LPDDR4 | IMG BXE-2-32 (OpenGL ES 3.2) | PCIe 2.1 x5 | Robu.in / Amazon India |
    | VisionFive 2 | StarFive JH7110 | 4 @ 1.5GHz | 8GB | LPDDR4 | IMG BXE-4-32 (Vulkan 1.2, OpenCL 3.0) | PCIe 2.0 x1 | Robu.in / Hubtronics.in |
    | LicheePi 4A | T-Head TH1520 | 4 @ 2.0GHz | 16GB | LPDDR4X | IMG BXE-2-32 | PCIe 2.0 | Mouser India |
    
    For each board provide:
    - Primary use case (desktop experimentation, server dev, embedded Linux, AI edge)
    - Which Linux distros are officially supported with kernel version
    - Connectivity: USB 3.x, HDMI version, Ethernet speed, WiFi/BT
    - Expansion: 40-pin GPIO, M.2 slots, mPCIe
    - Approximate INR price and procurement path in India
    6.3 **Most Powerful RISC-V CPUs/SoCs (Silicon Level, 2025–2026)**

    | CPU/SoC | Core Arch | Cores | Process | Key Feature | SPECint/GHz |
    |---------|-----------|-------|---------|-------------|-------------|
    | SOPHGO SG2042 | T-Head C920 (RVV 0.71) | 64 | 6nm TSMC | 64-core server, 4ch DDR4 | ~3.8 est |
    | SiFive P870 | OOO RVA23 | up to 16 | 12nm | Highest single-thread RISC-V | ~18 |
    | SiFive P550 (EIC7700X) | OOO RVA22 | 4 | 12nm | Best available dev board CPU | ~9 |
    | SpacemIT X100 | RVA23 + RVV 1.0 | 8 | 12nm | 2.5 TOPS NPU, 256-bit vector | ~9 |
    | UltraRISC UR-DP1000 | RV64GCBHX OOO | 8 | TBA | H-extension, full RVA22 | ~10.4 |
    | XuanTie C910 (TH1520) | OOO RV64GCV | 4 | 12nm | Open RTL on GitHub | ~7.1 |
    | Esperanto ET-SoC-1 | 1,000+ ET-Minion RV64 | 1,088 | 7nm TSMC | Massively parallel AI inference | N/A (throughput) |
    6.4 **DDR Memory Support on RISC-V**
- **DDR5**: SiFive HiFive Premier P550 (16/32GB DDR5) — currently the only
commercially available RISC-V dev board with DDR5
- **DDR4 ECC**: Milk-V Pioneer — 4-channel DDR4-3200, up to 128GB ECC — server grade
- **LPDDR4/4X**: Banana Pi BPI-F3, VisionFive 2, Milk-V Jupiter, LicheePi 4A
- **LPDDR5**: Expected in next-gen SpacemIT X100 and future SiFive P670 boards
- Note for professors: DDR5 on RISC-V is at the frontier — important research gap

6.5 **GPU Support — Detailed**

    A. **Discrete GPU via PCIe (Milk-V Pioneer)**
       - PCIe x16 Gen4 slot accepts full-length graphics cards
       - Verified working: AMD Radeon RX 580, RX 6600 (amdgpu driver, Mesa 23+)
       - Verified working: Intel Arc A380 (i915 driver)
       - NVIDIA: driver support NOT available (proprietary driver incompatibility)
       - Display output: fully functional for desktop workloads
       - GPU compute: OpenCL via Mesa Rusticl; Vulkan via RADV (AMD open driver)
    
    B. **Integrated GPU (PowerVR/Imagination Technologies)**
       - IMG BXE-2-32 (BPI-F3, Jupiter): Vulkan 1.2, OpenGL ES 3.2, OpenCL 3.0
       - IMG BXE-4-32 (VisionFive 2): Vulkan 1.2, OpenGL ES 3.2, OpenCL 3.0
       - IMG AXM-8-256 (HiFive P550): 8 ALU clusters, driver under active development
       - These GPUs enable: desktop compositing, light 3D, video decode, basic ML inference
    
    C. **GPU Compute for AI/ML**
       - SpacemIT K1: 2.0 TOPS onboard NPU for edge AI
       - Esperanto ET-SoC-1: 1,088 RISC-V cores for pure software-defined AI (no GPU needed)
    6.6 **Complete Peripheral / Interface Support Table**

    | Interface | Best Board | Notes |
    |-----------|-----------|-------|
    | NVMe SSD (PCIe 4.0) | Milk-V Pioneer | M.2 NVMe via PCIe 4.0 |
    | NVMe SSD (PCIe 3.0) | HiFive P550 | M.2 Key M via PCIe 3.0 x4 |
    | USB 3.2 Gen1 | HiFive P550, BPI-F3 | 5Gbps USB Type-A/C |
    | HDMI 2.0 (4K@60) | HiFive P550, VisionFive 2 | Display output |
    | 10GbE Ethernet | Milk-V Pioneer | Via PCIe NIC (Intel X540 verified) |
    | 1GbE Ethernet | All boards | Onboard standard |
    | WiFi 6 / BT 5 | BPI-F3 (M.2 Key E) | Via M.2 WiFi module |
    | MIPI CSI Camera | BPI-F3, VisionFive 2 | 2-lane/4-lane CSI-2 |
    | MIPI DSI Display | BPI-F3, VisionFive 2 | 4-lane DSI panels |
    | 40-pin GPIO | VisionFive 2, BPI-F3 | RPi-compatible pinout |
    | eMMC 5.1 | BPI-F3, VisionFive 2 | Onboard flash storage |
    | SATA III | Milk-V Pioneer via PCIe | SATA controller add-in card |
    | CAN Bus | SpacemIT K1 (BPI-F3) | 2× CAN FD — automotive use |
    | Audio I2S | VisionFive 2, BPI-F3 | 3.5mm + I2S headers |
    | Hardware Video Decode | All (H.264/H.265) | VisionFive 2: H.265@4K30 |
    6.7 **Buying RISC-V Hardware in India — Practical Guide**

    | Board | Indian Retailer | Approx Price (INR) | Link |
    |-------|----------------|-------------------|------|
    | VisionFive 2 (4GB) | Robu.in, Hubtronics.in | ₹5,500–₹8,000 | robu.in |
    | VisionFive 2 (8GB) | Hubtronics.in | ₹8,000–₹11,000 | hubtronics.in |
    | Banana Pi BPI-F3 (4GB) | Robu.in, Amazon India | ₹7,000–₹10,000 | amazon.in |
    | Banana Pi BPI-F3 (16GB) | Robu.in | ₹11,000–₹14,000 | robu.in |
    | Milk-V Pioneer | Mouser India / import | ₹1,00,000–₹1,70,000 | mouser.in |
    | SiFive HiFive P550 | sifive.com (international) | ~₹42,000–₹58,000 | sifive.com |
    | C-DAC Aries (Tejas 32) | C-DAC directly (academic) | Contact C-DAC | cdac.in |
    | LicheePi 4A | AliExpress / Mouser India | ₹12,000–₹18,000 | mouser.in |
    
---

## SECTION 7 — CRITICAL TECHNICAL KNOWLEDGE FOR MTECH/PROFESSORS

7.1 **ISA Naming Convention — Decode Any RISC-V String**
- Format: RV[XLEN][base-ISA][extensions]
- Examples:
→ RV32IMC = 32-bit, integer base, multiply, compressed
→ RV64GC = 64-bit, G (shorthand for IMAFD), compressed — typical Linux SBC
→ RV64GCBHX = 64-bit, G + bit-manip + hypervisor + custom
- RVA22U64: Application Profile, 2022, Unprivileged, 64-bit
- RVA23S64: Application Profile, 2023, Supervisor-mode, 64-bit
- How to read misa CSR bits at runtime to discover chip capabilities

7.2 **RISC-V Profiles System**
- Profiles define minimum mandatory ISA guarantees for software portability
- RVA22 mandatory: Zicsr, Zifencei, Zicntr, M, A, F, D, C, Zba, Zbb, Zbs,
Zic64b, Zcmp, Zcb, Zihintpause, Svnapot, Svpbmt, Svinval
- RVA23 adds: V (vector), Zvfhmin, Zfa, Zawrs, Zicond, and more
- Why profiles matter: OS distributions compile against profiles, not specific chips

7.3 **Full Toolchain Stack**
- GCC: riscv64-unknown-linux-gnu-gcc (riscv-gnu-toolchain GitHub)
- LLVM/Clang: riscv64 target in Clang 7+ (--target=riscv64-linux-gnu)
- Binutils: GNU assembler (as) with full RISC-V support
- OpenOCD: JTAG/SWD hardware debug (JTAG tap for RISC-V via debug spec 0.13/1.0)
- Spike: official RISC-V ISA reference simulator (models any RV32/RV64 config)
- QEMU: qemu-system-riscv64 + qemu-riscv64 (user mode)
- GDB: riscv64-linux-gnu-gdb with hardware watchpoints via JTAG
- Verilator: RTL simulation (used with Chipyard)
- Chipyard: UC Berkeley RISC-V SoC design framework (Rocket, BOOM, CVA6)

7.4 **ABI and Calling Convention**
- Standard ABI: LP64D (pointers=64-bit, longs=64-bit, doubles in FP registers)
- Argument registers: a0–a7 (x10–x17) → first 8 integer args
- Return values: a0 (primary), a1 (secondary for 128-bit return)
- Callee-saved (preserved across calls): s0–s11 (x8–x9, x18–x27), sp, fp
- Caller-saved (may be clobbered): t0–t6, a0–a7, ra
- Stack pointer: sp (x2) must be 16-byte aligned at function entry
- Global pointer: gp (x3) used for linker relaxation ±2KB from .sdata
- Thread pointer: tp (x4) used for TLS (Thread Local Storage)

7.5 **RISC-V in AI and Machine Learning**
- RVV 1.0 for inference: SIMD over float16, int8, int4 data types
- Custom matrix extensions: T-Head XuanTie XTHEADMATRIX (non-standard, vendor-specific)
- RISC-V IME (Integrated Matrix Extension): proposed standard for ML, under discussion
- Esperanto ET-SoC-1: 1,088 ET-Minion RV64 cores + 2 ET-Maxion OOO cores, 7nm, 25 TOPS
- SpacemIT K1: 2.0 TOPS NPU onboard, RVV 1.0 capable
- SiFive P670/P870: AI extensions in roadmap (INT8 MAC, FP16 throughput)
- Compiler support: ONNX Runtime, TVM, MicroTVM all have RISC-V backends

7.6 **Open-Source RTL Cores for Research Use**
- Rocket Core (Berkeley): RV64GC, 5-stage in-order, reference implementation in Chisel
- BOOM v3 (Berkeley): Superscalar OOO, 3–5 wide issue, Chisel HDL
- CVA6 / Ariane (ETH Zurich): RV64GC, Linux-capable, synthesizable to 28nm ASIC
- PicoRV32: ultra-minimal RV32IMC, ~750 LUTs on iCE40, FPGA-optimized
- VexRiscv: SpinalHDL, highly configurable, widely used in soft-SoC designs
- SHAKTI family (IIT Madras): E/C/I/M/S/H classes, open-source, India-origin
- Ibex (lowRISC/Google): RV32IMC for OpenTitan Root of Trust, formally verified

7.7 **FPGA Prototyping Recommendations**
- Budget FPGA (student):
→ Digilent Arty A7-35T with PicoRV32 or VexRiscv → runs bare-metal RV32
- Mid-range (MTech lab):
→ Digilent Nexys A7-100T with Rocket Core → boots Linux
- High-end (research group):
→ Xilinx ZCU104 with BOOM v3 via Chipyard → out-of-order Linux capable
- Cloud FPGA: AWS F1 instances with open RISC-V SoC builds
- Recommended tools: Chipyard (SoC framework), FireSim (FPGA simulation platform),
Verilator (fast simulation), GTKWave (waveform viewer), Yosys (synthesis)

7.8 **Physical Memory Protection (PMP)**
- Hardware memory access control for M/S/U mode isolation
- Up to 16 PMP entries standard; Smepmp adds 64 + execute-never defaults
- PMP configuration registers: pmpcfgN (lock, execute, write, read, mode bits)
- PMP address registers: pmpaddrN (TOR, NA4, NAPOT modes)
- Essential for bare-metal OS kernels and secure boot chains
- Smepmp extension: fixes security hole where M-mode could always access U-mode

7.9 **Hypervisor Extension (H-extension) — In Depth**
- Adds two new modes: HS-mode (Host Supervisor) and VS-mode/VU-mode (Guest)
- Two-stage address translation: GVA→GPA (via vsatp), GPA→HPA (via hgatp)
- New CSRs: hstatus, hedeleg, hideleg, hvip, hip, hie, hgatp, htval, htinst
- Enables KVM-RISC-V in Linux kernel (merged upstream)
- Enables QEMU full-system virtualization of guest RISC-V OSes
- Critical for cloud/data center deployment of RISC-V servers

7.10 **RISC-V Debug Specification**
- Separate from main ISA spec; defines JTAG-based external debug
- Debug Transport Module (DTM): JTAG to Debug Module Interface (DMI)
- Debug Module (DM): controls harts (hardware threads), memory access, breakpoints
- Hardware breakpoints: up to 16 trigger registers (tselect, tdata1/2/3)
- Software breakpoint: EBREAK instruction → jumps to debug ROM
- Tools: OpenOCD + GDB via JTAG for all RISC-V boards

---

## SECTION 8 — WHY RISC-V IS UNIQUELY POSITIONED: THE COMPLETE ARGUMENT

8.1 **No Legacy Constraint**
- Designed in 2010: post-smartphone, post-GPU, post-multicore era
- No 32-bit compatibility burden (unlike x86's real-mode relics)
- No Thumb/Thumb-2 encoding complexity (unlike ARM's AArch32)
- Every instruction encoding bit was chosen for modern pipeline efficiency

8.2 **Formal Specification and Verification**
- Sail formal model: machine-readable, executable formal semantics of entire ISA
- Published on GitHub (riscv/sail-riscv): runnable as reference interpreter
- Enables: theorem-prover formal verification of chip implementations
- Industry significance: first mainstream ISA with full formal model

8.3 **Composable Modular Design**
- Pick exactly what you need: RV32E (6 registers, 16KB MCU) to RV64GC (full Linux)
- Custom opcode space: four major opcodes (0x0B, 0x2B, 0x5B, 0x7B) permanently
reserved for vendor custom extensions — no ISA conflict possible
- Enables: AI companies adding custom matrix ops, cryptography accelerators,
DSP instructions — all without forking the ISA

8.4 **Geopolitical and Strategic Significance**
- ISA itself is not subject to US export controls (unlike ARM Holdings dealings)
- India, China, EU, Brazil can all build RISC-V chips with zero external approval
- This is why DRDO, ISRO, and MeitY fund SHAKTI/VEGA — true strategic autonomy
- China's RISC-V push: Alibaba XuanTie C910/C920, SOPHGO SG2042 — fully indigenous

8.5 **Academic to Production Continuity**
- Same ISA from: RV32E 6-register MCU → 64-core SOPHGO SG2042 server
- A student who learns RISC-V assembly today works on chips that will ship in 2030
- University tape-outs: IIT Madras SHAKTI, ETH Zurich CVA6, MIT CSAIL — all
using same ISA as commercial SiFive/SiPearl/Alibaba products

8.6 **Transparency and Community Governance**
- All spec proposals public on GitHub (riscv/riscv-isa-manual)
- Issue tracking, extension ratification votes, technical committee minutes — open
- Any organization can join RISC-V International and vote on spec direction
- Technical Working Groups open to all: Vector, Crypto, Debug, Hypervisor, AI/ML

---

## QUICK REFERENCE CARD

### Base ISA Naming

| Symbol | Meaning |
| :-- | :-- |
| RV32I | 32-bit, base integer |
| RV64I | 64-bit, base integer |
| RV32E | 32-bit, embedded (16 regs) |
| G | IMAFD combined shorthand |
| C | Compressed (16-bit instrs) |
| V | Vector (RVV 1.0) |
| H | Hypervisor |
| B | Bit manipulation (Zba+Zbb+Zbs) |

### Register ABI Quick Reference

| Register | ABI Name | Role | Saved by |
| :-- | :-- | :-- | :-- |
| x0 | zero | Hardwired 0 | N/A |
| x1 | ra | Return address | Caller |
| x2 | sp | Stack pointer | Callee |
| x3 | gp | Global pointer | N/A |
| x4 | tp | Thread pointer | N/A |
| x5–x7 | t0–t2 | Temporaries | Caller |
| x8–x9 | s0–s1 (fp) | Saved / Frame ptr | Callee |
| x10–x17 | a0–a7 | Fn args / return | Caller |
| x18–x27 | s2–s11 | Saved registers | Callee |
| x28–x31 | t3–t6 | Temporaries | Caller |

### Privilege Modes

| Mode | Level | Purpose |
| :-- | :-- | :-- |
| M (Machine) | 3 | Firmware, boot, direct hw |
| S (Supervisor) | 1 | OS kernel, MMU, page tables |
| U (User) | 0 | Applications |
| HS/VS/VU | H-ext | Hypervisor + guest OS |

### Top RISC-V Boards at a Glance

| Board | Best For | India Price |
| :-- | :-- | :-- |
| VisionFive 2 | Entry-level Linux, GPIO projects | ₹5,500–₹8,000 |
| Banana Pi BPI-F3 | Desktop Linux, AI edge, CAN bus | ₹7,000–₹14,000 |
| LicheePi 4A | High-clock Linux desktop | ₹12,000–₹18,000 |
| HiFive Premier P550 | Best per-core performance, DDR5 | ~₹42,000–₹58,000 |
| Milk-V Pioneer | Server workloads, discrete GPU | ₹1,00,000–₹1,70,000 |


---

END OF PROMPT.

After delivering the Quick Reference Card, write:
✅ COMPLETE — All 8 sections and Quick Reference Card delivered.
If this was delivered across multiple messages, compile a master index
listing which message contained which sections.

RISC-V is an open, modular instruction set architecture (ISA) designed as a clean, modern RISC ISA that scales from tiny microcontrollers to large servers, and it is now a central pillar of India’s semiconductor strategy. This chapter builds a rigorous foundation in the ISA itself, then connects it to hardware, ARM comparisons, and the Indian ecosystem.[^1][^2][^3]

***

## SECTION 1 — WHAT IS RISC-V? FOUNDATIONS OF THE ISA

### 1.1 What is an Instruction Set Architecture (ISA)?

An Instruction Set Architecture (ISA) is the abstract contract between software and processor hardware: it defines the instructions, registers, memory model, and visible state that compiled binaries can rely on. Compilers, operating systems, and applications target the ISA, not any particular microarchitecture, so any CPU that correctly implements the ISA can run the same binaries (modulo OS/ABI differences).[^2][^1]

Key roles of an ISA:

- **Portability:** A program compiled for RV64GC can run on any conforming RV64GC processor implementation (Rocket, SiFive, SHAKTI, etc.) without recompilation.[^1][^2]
- **Toolchain and ecosystem focus:** Compilers (GCC/LLVM), assemblers, linkers, debuggers, and profilers all target the ISA specification; this amortizes effort across many hardware designs.[^2][^1]
- **Binary compatibility and longevity:** An ISA defines a long-lived binary interface; x86-64 binaries from 2005 run on 2026 CPUs because Intel/AMD preserved the ISA contract.[^2]
- **Performance envelope:** While an ISA doesn’t fix microarchitectural details like pipeline depth or cache size, it strongly influences achievable IPC, pipeline complexity, out-of-order execution design, and energy efficiency.[^1][^2]

In practice, the ISA specification is written text plus formal models that define, for each instruction, how architectural state (registers, memory, CSRs) changes step by step.[^4][^2]

***

### 1.2 RISC Philosophy in Context

RISC (Reduced Instruction Set Computing) emerged in the 1980s from research at UC Berkeley and Stanford as a reaction against increasingly complex CISC ISAs such as VAX. Key empirical findings of that era:[^4][^2]

- Most compiled code uses relatively simple operations (loads/stores, adds, branches) more frequently than complex instructions.
- Simple, fixed-length instructions are easier to pipeline and can yield higher clock rates and IPC.
- A large register file reduces memory traffic and simplifies compiler optimization.[^4]

Canonical RISC principles that RISC-V follows:

- **Load–store architecture:** Only explicit load and store instructions access memory; arithmetic/logical operations work only on registers (no “add memory, register” instruction).[^1][^4]
- **Fixed-length base encodings:** The base instructions are 32 bits wide (with an optional compressed 16-bit subset) which simplifies instruction fetch, alignment, and decode.[^4][^1]
- **Large, uniform register file:** 32 general-purpose integer registers in RV32I/RV64I; when F/D/Q are present, 32 floating-point registers as well.[^2][^1]
- **Pipeline friendliness:** Simple, regular encodings and absence of obscure multi-step side effects reduce hazard and forwarding complexity and support deep, high-frequency pipelines.[^1][^2]
- **Simple addressing modes:** PC-relative, base+immediate, and simple displacement addressing instead of many complex modes.[^5][^1]

Many commercial RISC architectures—MIPS, SPARC, early ARM—took similar ideas to silicon; RISC-V can be seen as the fifth-generation refinement in this lineage.[^2][^4]

***

### 1.3 What is RISC-V Specifically?

RISC-V (“RISC Five”) is a modern RISC ISA created at UC Berkeley around 2010, led by Krste Asanović and David Patterson with key contributors Yunsup Lee, Andrew Waterman, and the Berkeley Architecture Research group. It is explicitly designed as the *fifth* major RISC from Berkeley after earlier experimental RISC projects, hence the “V”.[^4][^2]

Core characteristics:

- **Clean-slate, post-2010 design:** RISC-V was designed after decades of industry experience with x86, ARM, MIPS, and SPARC, allowing the designers to deliberately avoid legacy baggage such as condition code flags, branch delay slots, and convoluted encodings.[^1][^2]
- **Open, royalty-free ISA:** The specification is published openly; anyone can implement a RISC-V compatible core without paying ISA license fees or royalties.[^3][^2]
- **Governance by RISC-V International:** The ISA is standardized and evolved by RISC-V International, a non-profit organization headquartered in Switzerland with global membership from industry and academia.[^3][^2]
- **Modular and extensible:** A small base ISA plus a large library of standardized extensions (M, A, F, D, C, V, K, etc.) and reserved opcode space for vendor-defined custom extensions.[^2][^4]
- **Multiple word sizes:** Families RV32, RV64, and RV128 allow RISC-V to scale from microcontrollers to large memory servers within one architectural framework.[^2]

Unlike ARM or x86, RISC-V’s *ISA* is open even though specific core implementations may be proprietary; this is analogous to how TCP/IP is open but individual NICs can be proprietary.[^3][^2]

***

### 1.4 Modular Design Philosophy

RISC-V is explicitly modular. Software sees a base ISA plus an ordered list of extensions encoded both textually (e.g., RV64IMAFDCV) and in the hardware-reported `misa` CSR.[^1][^2]

#### Base ISAs

- **RV32I:** 32-bit base integer ISA with 32 general-purpose registers and 32-bit addresses.[^1]
- **RV32E:** 32-bit embedded subset with only 16 integer registers (x0–x15) for very small microcontrollers.[^2]
- **RV64I:** 64-bit base integer ISA with 64-bit registers and addresses; adds “W” instructions for 32-bit subword operations.[^1][^2]
- **RV64E:** Embedded 64-bit variant with 16 integer registers (less common in practice).[^2]
- **RV128I:** Architected but not yet commercially implemented; 128-bit registers and addresses for extreme-scale systems.[^2]


#### Standard Extensions (selection)

Common extension letters (each letter may bundle several “Z*” sub-extensions):[^4][^2]

- **M – Integer Multiply/Divide:** Hardware integer multiplication and division (e.g., `MUL`, `DIV`, `REM`).[^2]
- **A – Atomic Memory Operations:** LR/SC and AMO instructions for lock-free synchronization.[^2]
- **F – Single-precision Floating Point (32-bit, IEEE 754).**[^2]
- **D – Double-precision Floating Point (64-bit, IEEE 754), requires F.**[^2]
- **Q – Quad-precision Floating Point (128-bit), requires D.**[^2]
- **C – Compressed Instructions:** 16-bit encodings for common instructions, reducing code size by roughly 20–30%.[^2]
- **B – Bit Manipulation:** A family of “Zb*” sub-extensions (Zba, Zbb, Zbc, Zbs) for rotates, bit deposits/extracts, population counts, etc.[^2]
- **V – Vector Extension:** RVV 1.0 scalable vector extension with configurable vector length, element width, and grouping.[^2]
- **H – Hypervisor:** Adds hardware support for virtualization (HS/VS/VU modes and related CSRs).[^2]
- **K – Scalar Crypto:** AES, SHA2, SM3, SM4, and entropy-source extensions grouped under Zk*.[^2]

Other important “Z*” extensions include Zicsr (CSR instructions) and Zifencei (instruction-fetch fence), which are foundational for privileged code and self-modifying code support.[^2]

#### The “G” Shorthand

Historically, **G** was used as a shorthand for the set `{I, M, A, F, D}`, i.e., a “general-purpose” profile capable of running full OSes like Linux: “RV64GC” meant RV64IMAFD with compressed. Newer profile specs move away from G in favor of explicit profile names (RVA22, RVA23), but the shorthand remains widely used in documentation and toolchains.[^4][^2]

#### Ratified vs. Frozen vs. Draft

RISC-V Intl lifecycle for extensions:[^2]

- **Draft:** Under active development; semantics can still change. Not for production silicon meant to be long-lived.
- **Frozen:** Semantics are believed complete; changes are restricted to minor clarifications and editorial fixes, enabling early hardware and toolchain work.
- **Ratified:** Finalized and officially part of the standard; changes require a full deprecation process.

Many widely used extensions—M, A, F, D, C, V, K, B—are now ratified; some newer security and AI-related “Z*” extensions are frozen or draft.[^2]

#### Privileged vs. Unprivileged ISA

The RISC-V spec is split into:

- **Unprivileged ISA:** Base instructions, extensions, and user-visible state such as integer/FP registers and CSRs accessible from U-mode (e.g., `mcycle`, `fcsr`).[^4][^2]
- **Privileged Architecture:** Defines privilege modes (M/S/U), virtual memory, traps, interrupts, and system-level CSRs (e.g., `mstatus`, `satp`, `mtvec`).[^2]

This separation allows microcontrollers to implement only the unprivileged spec plus minimal M-mode, while Linux-capable SoCs implement the full privileged architecture with S-mode and (optionally) H-extension.[^4][^2]

***

### 1.5 Why RISC-V When MIPS/ARM/SPARC Already Exist?

By 2010, several RISC ISAs existed, but each had obstacles that made them unsuitable as *open, long-term academic and industrial standards*:[^3][^2]

- **MIPS:** Originally an elegant RISC from Stanford, it later became encumbered by a complex licensing history and was controlled by various companies (SGI, Imagination, Wave Computing, etc.). License terms, fragmentation between MIPS32/MIPS64 and microMIPS/MIPS16, and uncertainties about future governance discouraged new adopters.[^3]
- **ARM:** Ubiquitous in embedded and mobile, but the ISA is proprietary and licensed by ARM Holdings; implementers pay up-front license fees and per-core or per-chip royalties, and cannot legally define arbitrary custom opcode extensions in the ARM ISA space.[^3]
- **x86/x86‑64:** Architecturally complex CISC ISA with massive legacy baggage (real mode, segmentation, a large and irregular instruction set) and tightly controlled by an Intel/AMD duopoly.[^3][^2]
- **SPARC/PowerPC:** Once significant, but their ecosystems have declined; governance is tied to specific vendors (Oracle/Fujitsu for SPARC, IBM for Power) and the cost/benefit of adopting them for new designs is poor.[^3]

RISC-V’s answer:

- **Open standard, no royalties:** Anyone can implement it, from students doing small FPGA cores to national programs building server-grade CPUs, with no licensing payments.[^3][^2]
- **Extensibility without fragmentation:** A well-defined extension mechanism and reserved opcode ranges for custom vendor extensions allow innovation while keeping a coherent base ISA.[^2]
- **Academic + commercial freedom:** Universities can use the ISA in teaching and tape-outs without NDAs; startups can build proprietary cores or open ones (Rocket, BOOM, CVA6, SHAKTI) on equal footing.[^4][^2]
- **Modern, clean design:** RISC-V removes legacy artifacts like branch delay slots or condition code flags, making it attractive both for research (formal verification, microarchitectural exploration) and commercial high-performance designs.[^1][^2]

***

## SECTION 2 — RISC-V ISA: DEEP TECHNICAL DIVE (UNPRIVILEGED ISA)

This section is structured to roughly mirror the official Unprivileged ISA Manual and give you a spec-level understanding of the core ISA.[^4][^2]

***

### 2.1 Base Integer ISA — RV32I

#### Register File and ABI

RV32I defines 32 general-purpose integer registers $x0$–$x31$, each 32 bits wide (XLEN = 32). Register x0 is hardwired to constant zero; writes to x0 are discarded, and reads always return 0.[^1][^2]

The standard ABI assigns conventional roles and names:[^1][^2]


| Register | ABI name | Purpose (typical) |
| :-- | :-- | :-- |
| x0 | zero | Constant 0 |
| x1 | ra | Return address |
| x2 | sp | Stack pointer |
| x3 | gp | Global pointer |
| x4 | tp | Thread pointer |
| x5–x7 | t0–t2 | Temporaries |
| x8–x9 | s0/fp,s1 | Saved / frame pointer |
| x10–x17 | a0–a7 | Function args / returns |
| x18–x27 | s2–s11 | Saved registers |
| x28–x31 | t3–t6 | Temporaries |

This ABI is used by GCC/LLVM and the standard C libraries for RISC-V.[^4][^2]

#### XLEN

XLEN is the native integer register width and address width of the ISA variant:

- RV32: XLEN = 32
- RV64: XLEN = 64
- RV128: XLEN = 128

All integer registers, PC, and integer immediates are XLEN bits, and the unprivileged spec is parameterized by XLEN.[^1][^2]

#### Instruction Length and Formats

In the base ISA, every instruction word is 32 bits, aligned on 32-bit boundaries (addresses divisible by 4). Instruction formats pack opcode, register indices, function subcodes, and immediates in fixed bit positions to simplify decode.[^5][^1]

The main 32‑bit formats are:

- R-type: register–register operations (ADD, SUB, AND, OR, shifts, etc.)
- I-type: immediate arithmetic, loads, JALR, system instructions
- S-type: stores
- B-type: conditional branches
- U-type: upper-immediate instructions (LUI, AUIPC)
- J-type: JAL (jump and link)


##### R-type

```text
31          25 24   20 19   15 14  12 11    7 6      0
|  funct7     | rs2  | rs1  |funct3|   rd   | opcode |
```

Example: `ADD x3, x1, x2` (x3 = x1 + x2) has fields:[^5][^1]

- opcode = 0110011₂ (0x33)
- funct3 = 000₂
- funct7 = 0000000₂
- rd = 3, rs1 = 1, rs2 = 2

Encoded as 0x002081B3 (little-endian word in memory).[^5][^1]

##### I-type

```text
31             20 19   15 14  12 11    7 6      0
|   imm[11:0]    | rs1  |funct3|  rd   | opcode |
```

Example: `ADDI x5, x6, 10` (x5 = x6 + 10):

- opcode = 0010011₂ (0x13)
- funct3 = 000₂
- rs1 = 6, rd = 5, imm = 10 (0x00A)

Encoding: 0x00A30313.[^5][^1]

Loads (`LB`, `LH`, `LW`), JALR, and system instructions (`ECALL`, `EBREAK`, CSR ops when Zicsr is present) also use I-type.[^1]

##### S-type (Stores)

```text
31      25 24   20 19   15 14  12 11      7 6      0
|imm[11:5]| rs2  | rs1  |funct3|imm[4:0] | opcode |
```

Stores split the 12-bit signed immediate between two fields that are concatenated and sign-extended. Example: `SW x5, 8(x6)` (store word):[^5]

- opcode = 0100011₂ (0x23)
- funct3 = 010₂ (word)
- rs1 = 6 (base), rs2 = 5 (value)
- imm = 8 (binary 000000001000₂)
    - imm[11:5] = 0000000₂
    - imm[4:0]  = 01000₂

Encoding: 0x00532323.[^5]

##### B-type (Branches)

```text
31      25 24   20 19   15 14  12 11      7 6      0
|imm[12|10:5]| rs2 | rs1 |funct3|imm[4:1|11]|opcode|
```

The 13-bit branch offset (multiple of 2 bytes) is spread across bits for efficient sign-extension, with bit 0 implicitly 0. Example: `BEQ x1, x2, offset` uses opcode 1100011₂ and funct3 000₂.[^5]

##### U-type (LUI, AUIPC)

```text
31                     12 11    7 6      0
|         imm[31:12]      |  rd  | opcode |
```

The 20-bit immediate is placed in bits [31:12] and represents bits [31:12] of a 32-bit value with lower 12 bits zeroed. Instructions:[^5]

- `LUI rd, imm20`: rd = imm20 << 12
- `AUIPC rd, imm20`: rd = PC + (imm20 << 12)[^6]


##### J-type (JAL)

```text
31        12 11    7 6      0
|   imm[20|10:1|11|19:12] | rd | opcode |
```

The 21-bit signed offset (multiple of 2 bytes) is reassembled from scattered bits, with bit 0 implicit 0.[^5]

Example: `JAL x1, offset` saves return address in x1 and jumps PC-relative.[^1]

#### Example Encodings Summary

- `ADD x3, x1, x2` → opcode 0x33, encoding 0x002081B3.[^5][^1]
- `ADDI x5, x6, 10` → opcode 0x13, encoding 0x00A30313.[^5][^1]
- `LW x5, 8(x6)` → opcode 0x03, funct3=010₂, encoding 0x00832303.[^5][^1]
- `SW x5, 8(x6)` → opcode 0x23, encoding 0x00532323.[^5]
- `BEQ x1, x2, offset` → opcode 0x63; offset encoding follows B-type layout.[^5]
- `JAL x1, offset` → opcode 0x6F; immediate per J-type format.[^5]


#### Instruction Categories

RV32I defines instructions in several functional groups:[^1][^2]

- **Arithmetic/Logical:** `ADD`, `SUB`, `SLT`, `SLTU`, `AND`, `OR`, `XOR`, `SLL`, `SRL`, `SRA` and their immediate forms (`ADDI`, `SLTI`, etc.).
- **Shifts:** Logical and arithmetic shifts (register and immediate forms).
- **Loads/Stores:** Byte, halfword, word loads with sign/zero extension; stores for byte/halfword/word.
- **Control Flow:** Conditional branches (`BEQ`, `BNE`, `BLT`, `BGE`, etc.), `JAL`, `JALR`.
- **System:** `ECALL`, `EBREAK`, plus CSR instructions via Zicsr (`CSRRW`, `CSRRS`, etc.).[^2]
- **No dedicated flags register:** Instead of condition codes, branches compare register values directly (e.g., `BEQ x1, x2, label`) or rely on `SLT`/`SLTU`.[^1][^2]


#### Notable Design Choices

- **No condition code flags:** Eliminates global status flags (NZCV) and associated hazards; comparisons produce boolean values in registers or are embedded within branch instructions.[^1][^2]
- **PC-relative addressing:** `AUIPC` and branches make PC-relative code easy, improving position-independent code and linker relaxation.[^6]
- **Defined division behavior:** For `DIV`/`DIVU` (in M extension), division by zero returns a defined result (−1 or all 1s) instead of trapping, simplifying low-level code.[^2]
- **x0 as zero:** Frequent use of zero constants costs no register and helps encode moves and clears cheaply (e.g., `ADD x5, x0, x6` is a move).[^1][^2]

***

### 2.2 RV64I — 64-bit Base ISA

RV64I generalizes RV32I to 64-bit integer registers and addresses. Key implications:[^4][^2]

- **Registers:** 32 general-purpose registers, each 64 bits (XLEN = 64).[^2]
- **Address space:** 64-bit virtual and physical addresses (exact virtual address scheme governed by the privileged spec and `satp`/paging mode).[^2]


#### Additional Instructions

RV64I adds instructions that operate on 32-bit subwords but store sign-extended results in 64-bit registers:[^4][^2]

- **Loads/stores:**
    - `LWU rd, offset(rs1)`: Load 32-bit word and zero-extend to 64 bits.
    - `LD rd, offset(rs1)`: Load 64-bit doubleword.
    - `SD rs2, offset(rs1)`: Store 64-bit doubleword.
- **W-suffix arithmetic/logical:**
    - `ADDIW rd, rs1, imm`: 32-bit add, then sign-extend result to 64 bits.
    - `ADDW`, `SUBW`, `SLLW`, `SRLW`, `SRAW`, and immediate variants (`SLLIW`, `SRLIW`, `SRAIW`).[^2]

The W-forms treat operands as 32-bit values (lower 32 bits of register), perform the operation in 32 bits, then sign-extend to 64 bits; this matches C’s int32 arithmetic semantics on a 64-bit platform.[^4][^2]

#### Sign Extension and Memory Model Implications

- **32→64-bit promotion:** Many instructions implicitly sign-extend their 32-bit results (e.g., `ADDW`); compilers must be aware when mixing 32-bit and 64-bit operations to avoid redundant `SEXT` or masking.[^2]
- **Loads:**
    - `LW`: sign-extends 32-bit word.
    - `LWU`: zero-extends.
    - `LD`: does not extend (already 64 bits).[^2]
- **Pointer size:** Pointers and `size_t` are typically 64-bit under the LP64D ABI (Linux RISC-V), impacting stack frame layout and struct alignment.[^4][^2]

The semantics are chosen so that compiled 32-bit code ported to 64-bit RISC-V behaves naturally, similar to x86-64’s 32-bit register write semantics (zero-extend to 64 bits) but using sign-extension for W operations in line with C integer rules.[^2]

***

### 2.3 Standard Extensions — Detailed Coverage

The table below summarizes selected major extensions and their status:[^2]


| Extension | Letter | Description | Status |
| :-- | :-- | :-- | :-- |
| Integer Multiply/Divide | M | Integer MUL/DIV/REM | Ratified |
| Atomic Memory Ops | A | LR/SC, AMO add/swap/and/or/xor/min/max | Ratified |
| Single-Precision Float | F | IEEE-754 binary32 FP, 32 FP regs | Ratified |
| Double-Precision Float | D | IEEE-754 binary64 FP, extends F | Ratified |
| Quad-Precision Float | Q | IEEE-754 binary128 FP, extends D | Ratified |
| Compressed | C | 16-bit encodings for common ops | Ratified |
| Bit Manipulation | B | Zba, Zbb, Zbc, Zbs sub-extensions | Ratified |
| Vector | V | Scalable vector extension (RVV 1.0) | Ratified |
| Hypervisor | H | Virtualization support (HS/VS/VU) | Ratified |
| Crypto Scalar | K | AES, SHA, SM3/4, entropy (Zk*) | Ratified |

Below we give motivation, key operations, hardware implications, and use cases.

#### M — Integer Multiply/Divide

- **Motivation:** Many workloads (DSP, graphics, cryptography, general integer code) require fast multiplication and division; doing them in software on RV32I alone is slow.[^2]
- **Key instructions:** `MUL`, `MULH`, `MULHSU`, `MULHU`, `DIV`, `DIVU`, `REM`, `REMU`.[^2]
- **Hardware implications:** Adds a multiplier/divider unit, which can be implemented as a single-cycle or multi-cycle pipeline depending on area/power targets.
- **Use cases:** Almost all general-purpose SoCs, MCUs with moderate performance needs, DSP tasks in embedded and communication stacks.


#### A — Atomic Memory Operations

- **Motivation:** Provide portable primitives for lock-free synchronization and multi-core concurrency without relying on LL/SC quirks or non-standard instructions.[^2]
- **Key instructions:**
    - `LR.W` / `SC.W`, `LR.D` / `SC.D`: load-reserved and store-conditional.
    - AMOs: `AMOADD`, `AMOSWAP`, `AMOAND`, `AMOOR`, `AMOXOR`, `AMOMIN`, `AMOMAX`, `AMOMINU`, `AMOMAXU`.[^2]
- **Hardware implications:** Reservation set or address tracking for LR/SC; atomic read-modify-write in memory subsystem; coherence protocol awareness for multi-core.[^2]
- **Use cases:** OS kernels, concurrent data structures, lock-free queues, user-space atomics in C/C++ (`std::atomic`).


#### F/D/Q — Floating-Point

- **Motivation:** IEEE-754 floating point is fundamental for scientific computing, media processing, and ML workloads.[^2]
- **Key instructions:**
    - F: single-precision arithmetic (`FADD.S`, `FMUL.S`, `FDIV.S`, `FSQRT.S`), conversions, compares, fused multiply-add (`FMADD.S`).
    - D: same set with `.D` suffix operating on 64-bit floats; requires F.[^2]
    - Q: quad-precision operations; requires D.[^2]
- **Hardware implications:** Separate FP register file `f0`–`f31`, FP execution units (adder, multiplier, divider, sqrt, FMA), FP control/status register `fcsr`.[^2]
- **Use cases:** HPC, multimedia, signal processing, ML inference and training (typically F and D; Q is niche for numerical analysis and high-precision finance).


#### C — Compressed Instructions

- **Motivation:** Code size reduction improves I-cache and I-TLB hit rates and reduces memory bandwidth—critical for embedded systems and beneficial even for large cores.[^2]
- **Key features:**
    - 16-bit encodings for a subset of popular instructions (e.g., `C.ADDI`, `C.LW`, `C.SW`, `C.J`, `C.JAL`, `C.LI`, `C.LUI`, `C.ADD`, `C.MV`).[^2]
    - Mixed 16/32-bit stream; decoder expands C instructions into canonical 32-bit internal form.
- **Hardware implications:** Slightly more complex decode front-end to handle 16- and 32-bit instruction boundaries, but gains from reduced fetch bandwidth and code storage.[^2]
- **Use cases:** Almost all production RISC-V cores, from microcontrollers (firmware flash savings) to Linux SBCs (smaller binaries, energy savings).


#### B — Bit Manipulation (Zba, Zbb, Zbc, Zbs)

- **Motivation:** Modern cryptography, graphics, and bit-level algorithms spend significant time doing shifts, masks, rotates, and logical combinations; specialized instructions can reduce instruction count and improve constant-time coding.[^2]
- **Key operations:**
    - Zba: address generation, add with shift (`SH1ADD`, `SH2ADD`, `SH3ADD`).
    - Zbb: basic bit-manip (`ANDN`, `ORN`, `XORN`, `CLZ`, `CTZ`, `PCNT`, `MIN`, `MAX`).
    - Zbc: carry-less operations for crypto (e.g., polynomial multiply).
    - Zbs: single-bit set/clear/invert/extract (`BSET`, `BCLR`, `BINV`, `BEXT`).[^2]
- **Hardware implications:** Additional ALU sub-blocks for bit operations; usually modest area overhead.
- **Use cases:** Cryptography libraries, bitset operations in databases, network stacks, and compression.


#### V — Vector Extension (RVV 1.0)

- **Motivation:** High-performance computing, media, and ML workloads benefit from SIMD/vectorization, but fixed-width SIMD (like SSE/NEON) ages poorly as vector widths grow; RVV’s scalable vectors abstract over physical width.[^2]
- **Key concepts:** See Section 2.9; includes vector integer and FP arithmetic, loads/stores, reductions, permutations, and masks with dynamic `vsetvli` configuration.[^2]
- **Hardware implications:** Vector register file `v0`–`v31`, vector ALUs, load/store units, and mask registers; area scales with maximum VLEN (e.g., 128, 256, 512 bits).[^2]
- **Use cases:** HPC, signal processing, ML inference/training, graphics, and any throughput-oriented workloads.


#### H — Hypervisor

- **Motivation:** Efficient virtualization of RISC-V systems for cloud and data-center use; support multiple guest OS instances with good performance isolation.[^2]
- **Key features:**
    - Additional privilege mode HS (host supervisor) and virtual equivalents VS/VU.
    - Two-stage address translation via `hgatp` (host) and `vsatp` (guest).
    - New CSRs for delegation and virtualization (e.g., `hstatus`, `hideleg`, `hvip`).[^2]
- **Hardware implications:** TLB and MMU extended for nested page tables; additional privilege checks; interrupt virtualization logic.
- **Use cases:** KVM-RISC-V in Linux, cloud hypervisors, container host kernels when combined with hardware VMs for isolation.


#### K — Crypto Scalar Extensions

- **Motivation:** Hardware acceleration for common cryptographic primitives reduces latency and mitigates side-channel leakage relative to naive software loops.[^2]
- **Key operations:** Sub-extensions Zkn (AES, SM4), Zks (SHA2, SM3), Zkr (entropy source), Zknd/Zkne (AES round functions, etc.).[^2]
- **Hardware implications:** Dedicated AES/SHA/SM datapaths and S-box logic; entropy source IP; constant-time pipelines.
- **Use cases:** TLS, VPN, disk encryption, secure boot, and general-purpose system security.

***

### 2.4 Control and Status Registers (CSRs)

CSRs are special registers that control privileged behavior, expose performance counters, and hold exception/interrupt state. CSR access instructions are defined by the Zicsr extension and are required for any realistic system.[^2]

#### CSR Access Instructions (Zicsr)

- `CSRRW rd, csr, rs1` — Atomic read/write CSR.
- `CSRRS rd, csr, rs1` — Read and set bits.
- `CSRRC rd, csr, rs1` — Read and clear bits.
- Immediate variants `CSRRWI`, `CSRRSI`, `CSRRCI` use a 5-bit immediate instead of rs1.[^2]

These use the I-type format with the CSR address in bits [31:20] and funct3 differentiating the op.[^2]

#### Key Machine-Mode CSRs

Some of the most important CSRs (names and roles):[^2]

- `mstatus`: Global machine status (interrupt enable bits, privilege bits, etc.).
- `misa`: Encodes XLEN and implemented ISA extensions (base + letters).
- `medeleg`, `mideleg`: Exception and interrupt delegation to S-mode.
- `mip`, `mie`: Pending and enabled interrupt bits.
- `mtvec`: Machine trap-vector base address.
- `mepc`: Machine exception program counter (where to return after trap).
- `mcause`: Encodes the cause of the last exception or interrupt.
- `mtval`: Trap value (e.g., faulting address).
- `mcycle`, `minstret`: Cycle and retired instruction counters.
- `mhpmcounterN`, `mhpmeventN`: Hardware performance monitoring counters and event selectors.[^2]

Supervisor and user modes have analogous CSRs (`sstatus`, `stvec`, `sepc`, `scause`, etc.).[^2]

#### `misa` Layout and Extension Discovery

`misa` is an XLEN-bit CSR; its high bits encode the base XLEN and lower bits encode which standard extensions are present:[^2]

- Bits [XLEN−1:XLEN−2]: Encoded XLEN (01 for 32, 10 for 64, 11 for 128).
- Bits [25:0]: Each bit corresponds to an extension letter (bit 0 = ‘A’, bit 1 = ‘B’, …, bit 25 = ‘Z’).[^2]

Example: a system with RV64IMAFDCV might have the bits for I, M, A, F, D, C, V set. Software can read `misa` at runtime to discover available extensions and adapt (e.g., use vector code paths only when V is present).[^2]

***

### 2.5 Instruction Encoding Philosophy

The unprivileged spec documents several explicit encoding goals:[^1][^2]

- **Uniform register field positions:** `rs1`, `rs2`, and `rd` are in the same bit positions for all formats that use them (rd at [11:7], rs1 at [19:15], rs2 at [24:20]); this simplifies decoder hardware.[^1]
- **Sign-extension efficiency:** For all immediates except CSR immediates, the sign bit (*) is placed in bit 31; scattered immediate fields are arranged so that sign-extension hardware is simple and consistent across formats.[^5][^1]
- **Opcode space partitioning:** 7-bit opcode field at [6:0] is partitioned into major opcode groups that leave room for future standard and custom extensions, and that are compatible with future 48/64-bit encodings.[^1][^2]
- **Variable-length compatibility:** By requiring 16-bit alignment and reserving certain opcode patterns, the ISA supports mixing 16-bit C instructions with 32-bit base instructions, and leaves space for future 48/64-bit encodings while keeping decode relatively straightforward.[^2]


#### Major Opcode Map (Simplified)

A simplified table of some major opcodes in RV32I/RV64I:[^1][^2]


| Opcode (binary) | Hex | Mnemonic group | Example instructions |
| :-- | :-- | :-- | :-- |
| 0110111 | 0x37 | LUI | `LUI rd, imm20` |
| 0010111 | 0x17 | AUIPC | `AUIPC rd, imm20` |
| 1101111 | 0x6F | JAL | `JAL rd, offset` |
| 1100111 | 0x67 | JALR (I-type) | `JALR rd, rs1, offset` |
| 1100011 | 0x63 | BRANCH | `BEQ`, `BNE`, `BLT`, … |
| 0000011 | 0x03 | LOAD | `LB`, `LH`, `LW`, `LD` |
| 0100011 | 0x23 | STORE | `SB`, `SH`, `SW`, `SD` |
| 0010011 | 0x13 | OP-IMM | `ADDI`, `SLTI`, `ORI`, … |
| 0110011 | 0x33 | OP (reg–reg) | `ADD`, `SUB`, `AND`, … |
| 0001111 | 0x0F | MISC-MEM | `FENCE`, `FENCE.I` |
| 1110011 | 0x73 | SYSTEM | `ECALL`, `EBREAK`, CSR ops |

Additional opcodes are used for floating-point ops, atomics, and custom spaces.[^2]

The “custom” major opcodes (e.g., 0x0B, 0x2B, 0x5B, 0x7B) are reserved for implementer-defined extensions and never used by standard instructions, ensuring that vendor extensions cannot collide with ratified ISA features.[^2]

***

### 2.6 Memory Model — RVWMO (RISC-V Weak Memory Ordering)

RISC-V defines RVWMO, a weak memory ordering model designed to support high-performance, out-of-order multi-core hardware while still being amenable to formal reasoning. It is weaker than x86’s TSO but allows stronger ordering via fences and atomics.[^4][^2]

Key aspects:

- **Relaxed ordering:** Loads and stores from different cores may be observed in different orders unless the program uses fences or atomics to constrain reordering.[^2]
- **Per-location SC with atomics:** Atomic operations provide strong guarantees for synchronization variables, similar to C/C++ atomic semantics.[^2]
- **FENCE instruction:** `FENCE [pred],[succ]` constrains memory and I/O ordering, ensuring that prior memory operations are globally visible before subsequent operations matching the mask.[^2]


#### LR/SC and AMOs in the Memory Model

- **LR/SC (Load-Reserved/Store-Conditional):** Provide a loop-based primitive for building locks and lock-free algorithms: `LR` reads a value and sets a reservation; `SC` attempts to store and succeeds only if no conflicting writes occurred.[^2]
- **AMOs:** Single-instruction read-modify-write operations that are atomic with respect to other cores (e.g., `AMOADD.W`, `AMOSWAP.D`).[^2]

These are integrated into RVWMO’s formal model so that properly synchronized code behaves as if memory were sequentially consistent for those synchronization operations.[^2]

#### Comparison to x86 TSO and ARM

- **x86 TSO:** Stronger; nearly all writes are observed in order, and many reorderings are forbidden, simplifying programmer reasoning but constraining microarchitectural optimizations.[^2]
- **ARM (ARMv8/ARMv9):** Also weakly ordered, with explicit barriers (`DMB`, `DSB`, `ISB`) for ordering.[^2]
- **RISC-V RVWMO:** Similar in spirit to ARM’s model—allows many reorderings, but fences and atomic operations provide the necessary guarantees for data-race-free programs.[^4][^2]

***

### 2.7 Exception and Interrupt Handling (Unprivileged View)

The unprivileged spec relies on the privileged architecture for trap handling; here we summarise essentials relevant to ISA-level understanding.[^2]

#### Privilege Levels

RISC-V defines up to four privilege levels:[^2]

- **M (Machine, 3):** Highest privilege; firmware, bootloader, low-level runtime.
- **S (Supervisor, 1):** OS kernels, hypervisors (without H) or guest kernels (with H).
- **U (User, 0):** Applications.
- **HS/VS/VU:** Additional virtualized modes introduced by H-extension (see Section 7.9 for details).[^2]

Many microcontrollers implement only M-mode; Linux-capable systems implement at least M and S, with optional U for user-space.[^2]

#### Trap Causes

Traps (exceptions + interrupts) include:[^2]

- **Synchronous exceptions:** Illegal instruction, instruction or data access fault, misaligned load/store/jump, breakpoint (`EBREAK`), environment calls (`ECALL`) from U/S/M, page faults (if virtual memory is enabled).
- **Asynchronous interrupts:** Timer interrupts, software interrupts (IPIs), external device interrupts.[^2]


#### Core Trap CSRs

Trap handling is orchestrated via CSRs:[^2]

- `mtvec`: Base address of trap handler; can be direct or vectored mode.
- `mepc`: PC of the instruction that caused the trap (or next PC for some traps).
- `mcause`: Encodes whether the trap is interrupt or exception and its code.
- `mtval`: Additional value (e.g., faulting address or instruction bits).

A typical trap flow in M-mode:

1. Hardware saves the faulting PC into `mepc`, cause into `mcause`, and optional info into `mtval`.
2. PC is set to `mtvec` (or `mtvec + 4*cause` in vectored mode).
3. Trap handler inspects `mcause`/`mtval`, services the trap, possibly adjusts `mepc`.
4. `MRET` returns to `mepc` (or `mepc + 4` depending on semantics).[^2]

#### ECALL as System Call Mechanism

- **From U-mode:** `ECALL` raises an environment call exception; delegated via `medeleg` to S-mode, whose trap handler implements system calls (e.g., Linux’s `syscall` entry).[^2]
- **From S-mode:** `ECALL` may trap to M-mode for hypervisor or firmware services, depending on delegation.[^2]

`mcause` encodes distinct cause codes for ECALL from U, S, and M, allowing handlers to distinguish origin.[^2]

***

### 2.8 Floating-Point Architecture

When F/D/Q are present, RISC-V adds a separate FP register file and control/status mechanisms conforming to IEEE-754.[^2]

#### FP Register File and NaN Boxing

- **Registers:** `f0`–`f31`, each XLEN bits in RV32/RV64 (32 or 64 bits), but capable of holding narrower FP values using *NaN boxing*.[^2]
- **NaN boxing:** When a narrower FP value (e.g., 32-bit float) is stored in a wider register (e.g., 64-bit FP register under D), the upper bits are filled with a canonical NaN pattern so that operations treating it as a wider value still see a NaN if interpreted incorrectly.[^2]
- **Register classes:** The ABI defines calling conventions for passing FP arguments and return values when FP is enabled (e.g., LP64D uses both x and f registers).[^4][^2]


#### Rounding Modes and Exception Flags

- **Rounding modes:** Encoded in `frm` field of `fcsr` or in instructions:
    - RNE (round to nearest, ties to even),
    - RTZ (towards zero),
    - RDN (towards −∞),
    - RUP (towards +∞),
    - RMM (round to nearest, ties to max magnitude).[^2]
- **Exception flags in `fflags` (part of `fcsr`):**
    - NX (inexact),
    - UF (underflow),
    - OF (overflow),
    - DZ (divide-by-zero),
    - NV (invalid operation).[^2]

These flags are sticky and can be examined/cleared by software.

#### Layering of F, D, Q

- F is the base floating-point extension.
- D requires F and adds double-precision instructions and semantics.
- Q requires D and adds quad-precision.[^2]

This layering ensures incremental hardware complexity growth and consistent behavior across precisions.

***

### 2.9 Vector Extension (RVV 1.0) — Overview

RVV 1.0 is a ratified scalable vector extension that decouples the number of architectural elements processed per instruction from the physical vector width, enabling portable high-performance code across implementations with different VLEN.[^2]

#### Key Parameters and Registers

- **VLEN:** Hardware vector register width in bits (e.g., 128, 256, 512, 1024). Fixed per microarchitecture.[^2]
- **ELEN:** Maximum element width supported (e.g., 32 or 64 bits).[^2]
- **LMUL:** Vector register grouping multiplier; groups multiple physical vector registers to form a logical vector register with more lanes (e.g., LMUL = 2, 4, 8) or fractional groups (1/2, 1/4).[^2]
- **Vector register file:** `v0`–`v31`; each a vector of elements. Some are reserved for masks or special use.[^2]
- **CSRs:** `vl` (current vector length in elements) and `vtype` (current vector type: SEW, LMUL, tail/mask policies).[^2]


#### Vector Configuration — `vsetvli` / `vsetivli`

- `vsetvli rd, rs1, imm`: Sets `vl` based on requested element width (SEW) and LMUL encoded in `imm`, and the available VLEN and ELEN; returns actual `vl` in `rd`.[^2]
- `vsetivli rd, uimm, imm`: Same but with immediate element count.[^2]

This dynamic configuration lets the same binary adapt to different vector lengths at runtime, unlike fixed-width SIMD where binaries must be recompiled per width.[^2]

#### Categories of Vector Instructions

RVV defines rich instruction categories:[^2]

- **Integer arithmetic:** `vadd`, `vsub`, `vmul`, `vdiv`, `vmin`, `vmax`, etc.
- **Floating-point:** `vfadd`, `vfsub`, `vfmul`, `vfdiv`, `vfsqrt`, `vfmacc`, etc.
- **Logical and mask ops:** `vand`, `vor`, `vxor`, mask set/clear operations.
- **Reduction:** `vredsum`, `vredmax`, etc., to reduce vectors to scalars.
- **Permutation:** `vslide`, `vrgather`, `vcompress` for shuffles and compactions.
- **Vector loads/stores:** Unit-stride, strided, indexed, segmented variants.
- **Masking:** Nearly all operations support masking, executing only on lanes where mask bit is 1.[^2]


#### Why RVV is Architecturally Superior to Fixed-Width SIMD

- **Scalability:** The same program can scale from 128-bit to 1024-bit hardware without recompilation, merely by changing `vsetvli` behavior.[^2]
- **Portability:** Compilers emit generic RVV code once; hardware vendors choose their preferred VLEN and microarchitectural tricks.
- **Energy efficiency:** Hardware may trade off more lanes vs. frequency/voltage while preserving software semantics.
- **Masking and tail handling:** Explicit mask and tail policies avoid the need for scalar epilogues in many vectorized loops.[^2]


#### Example: Vector Dot Product in RVV Assembly

Pseudocode to compute dot product of two float32 arrays `a` and `b` of length `n`:

```asm
# a0: pointer to a[]
# a1: pointer to b[]
# a2: n
    vsetvli t0, a2, e32,m1      # Configure for 32-bit elements, LMUL=1
    vle32.v v0, (a0)            # Load chunk of a
    vle32.v v1, (a1)            # Load chunk of b
    vfmul.vv v2, v0, v1         # v2 = v0 * v1
    vfredsum.vs v3, v2, v3      # Reduce into v3 (accumulator)
    # Adjust a0, a1, a2 by vl and loop until a2 == 0
```

The actual code would loop, updating `a0`, `a1`, and `a2` by `vl` (the number of elements processed) until the entire vector is consumed. The same code runs efficiently on any VLEN implementation because `vsetvli` chooses `vl` at runtime.[^2]

***

***
⏸️ PAUSED — Completed up to Section. Reply with "continue" to receive Section  onwards. Do NOT restart from the beginning.[^7][^8]
---
<span style="display:none">[^10][^11][^12][^13][^14][^15][^9]</span>

<div align="center">⁂</div>

[^1]: https://docs.riscv.org/reference/isa/unpriv/rv32.html

[^2]: https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf

[^3]: https://www.businesstoday.in/latest/story/digital-india-risc-v-microprocessor-dir-v-program-launched-331530-2022-04-27

[^4]: https://courses.grainger.illinois.edu/ece391/sp2025/docs/unpriv-isa-20240411.pdf

[^5]: http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf

[^6]: https://msyksphinz-self.github.io/riscv-isadoc/html/rvi.html

[^7]: https://www.electronicsforyou.biz/industry-buzz/meity-launches-digital-india-risc-v-microprocessor-dir-v-program/

[^8]: https://ftp.radix-linux.su/3pp/Sophgo/doc/Milk-V/SG2042_Draft_Spec_V1.0.pdf

[^9]: https://www.drishtiias.com/daily-updates/daily-news-analysis/digital-india-risc-v-dir-v-program

[^10]: https://riscv.org/ecosystem-news/2023/10/linux-patches-updated-for-64-core-risc-v-milk-v-pioneer-matx-board/

[^11]: https://www.pib.gov.in/PressReleasePage.aspx?PRID=1820621

[^12]: https://milkv.io/docs/pioneer/getting-started/processor

[^13]: https://www.india-briefing.com/news/india-launches-digital-india-risc-v-microprocessor-dir-v-program-for-next-generation-microprocessors-24917.html/

[^14]: https://www.crowdsupply.com/milk-v/milk-v-pioneer

[^15]: https://lunar.computer/news/linux-patches-for-64-core-risc-v-milk-v/