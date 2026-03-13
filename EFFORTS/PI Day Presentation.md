```text
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
```

***

# SECTION 1 — WHAT IS RISC-V? FOUNDATIONS OF THE ISA

## 1.1 What is an Instruction Set Architecture (ISA)?

An Instruction Set Architecture (ISA) is the abstract contract between software and processor hardware: it defines the instructions, registers, memory model, and visible state that compiled binaries can rely on. Compilers, operating systems, and applications target the ISA, not any particular microarchitecture, so any CPU that correctly implements the ISA can run the same binaries (modulo OS/ABI differences).[^2][^1]

Key roles of an ISA:

- **Portability:** A program compiled for RV64GC can run on any conforming RV64GC processor implementation (Rocket, SiFive, SHAKTI, etc.) without recompilation.[^1][^2]
- **Toolchain and ecosystem focus:** Compilers (GCC/LLVM), assemblers, linkers, debuggers, and profilers all target the ISA specification; this amortizes effort across many hardware designs.[^2][^1]
- **Binary compatibility and longevity:** An ISA defines a long-lived binary interface; x86-64 binaries from 2005 run on 2026 CPUs because Intel/AMD preserved the ISA contract.[^2]
- **Performance envelope:** While an ISA doesn’t fix microarchitectural details like pipeline depth or cache size, it strongly influences achievable IPC, pipeline complexity, out-of-order execution design, and energy efficiency.[^1][^2]

In practice, the ISA specification is written text plus formal models that define, for each instruction, how architectural state (registers, memory, CSRs) changes step by step.[^4][^2]

***

## 1.2 RISC Philosophy in Context

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

## 1.3 What is RISC-V Specifically?

RISC-V (“RISC Five”) is a modern RISC ISA created at UC Berkeley around 2010, led by Krste Asanović and David Patterson with key contributors Yunsup Lee, Andrew Waterman, and the Berkeley Architecture Research group. It is explicitly designed as the _fifth_ major RISC from Berkeley after earlier experimental RISC projects, hence the “V”.[^4][^2]

Core characteristics:

- **Clean-slate, post-2010 design:** RISC-V was designed after decades of industry experience with x86, ARM, MIPS, and SPARC, allowing the designers to deliberately avoid legacy baggage such as condition code flags, branch delay slots, and convoluted encodings.[^1][^2]
- **Open, royalty-free ISA:** The specification is published openly; anyone can implement a RISC-V compatible core without paying ISA license fees or royalties.[^3][^2]
- **Governance by RISC-V International:** The ISA is standardized and evolved by RISC-V International, a non-profit organization headquartered in Switzerland with global membership from industry and academia.[^3][^2]
- **Modular and extensible:** A small base ISA plus a large library of standardized extensions (M, A, F, D, C, V, K, etc.) and reserved opcode space for vendor-defined custom extensions.[^2][^4]
- **Multiple word sizes:** Families RV32, RV64, and RV128 allow RISC-V to scale from microcontrollers to large memory servers within one architectural framework.[^2]

Unlike ARM or x86, RISC-V’s _ISA_ is open even though specific core implementations may be proprietary; this is analogous to how TCP/IP is open but individual NICs can be proprietary.[^3][^2]

***

## 1.4 Modular Design Philosophy

RISC-V is explicitly modular. Software sees a base ISA plus an ordered list of extensions encoded both textually (e.g., RV64IMAFDCV) and in the hardware-reported `misa` CSR.[^1][^2]

### Base ISAs

- **RV32I:** 32-bit base integer ISA with 32 general-purpose registers and 32-bit addresses.[^1]
- **RV32E:** 32-bit embedded subset with only 16 integer registers (x0–x15) for very small microcontrollers.[^2]
- **RV64I:** 64-bit base integer ISA with 64-bit registers and addresses; adds “W” instructions for 32-bit subword operations.[^1][^2]
- **RV64E:** Embedded 64-bit variant with 16 integer registers (less common in practice).[^2]
- **RV128I:** Architected but not yet commercially implemented; 128-bit registers and addresses for extreme-scale systems.[^2]

### Standard Extensions (selection)

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

### The “G” Shorthand

Historically, **G** was used as a shorthand for the set `{I, M, A, F, D}`, i.e., a “general-purpose” profile capable of running full OSes like Linux: “RV64GC” meant RV64IMAFD with compressed. Newer profile specs move away from G in favor of explicit profile names (RVA22, RVA23), but the shorthand remains widely used in documentation and toolchains.[^4][^2]

### Ratified vs. Frozen vs. Draft

RISC-V Intl lifecycle for extensions:[^2]

- **Draft:** Under active development; semantics can still change. Not for production silicon meant to be long-lived.
- **Frozen:** Semantics are believed complete; changes are restricted to minor clarifications and editorial fixes, enabling early hardware and toolchain work.
- **Ratified:** Finalized and officially part of the standard; changes require a full deprecation process.

Many widely used extensions—M, A, F, D, C, V, K, B—are now ratified; some newer security and AI-related “Z*” extensions are frozen or draft.[^2]

### Privileged vs. Unprivileged ISA

The RISC-V spec is split into:

- **Unprivileged ISA:** Base instructions, extensions, and user-visible state such as integer/FP registers and CSRs accessible from U-mode (e.g., `mcycle`, `fcsr`).[^4][^2]
- **Privileged Architecture:** Defines privilege modes (M/S/U), virtual memory, traps, interrupts, and system-level CSRs (e.g., `mstatus`, `satp`, `mtvec`).[^2]

This separation allows microcontrollers to implement only the unprivileged spec plus minimal M-mode, while Linux-capable SoCs implement the full privileged architecture with S-mode and (optionally) H-extension.[^4][^2]

***

## 1.5 Why RISC-V When MIPS/ARM/SPARC Already Exist?

By 2010, several RISC ISAs existed, but each had obstacles that made them unsuitable as _open, long-term academic and industrial standards_:[^3][^2]

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

# SECTION 2 — RISC-V ISA: DEEP TECHNICAL DIVE (UNPRIVILEGED ISA)

This section is structured to roughly mirror the official Unprivileged ISA Manual and give you a spec-level understanding of the core ISA.[^4][^2]

***

## 2.1 Base Integer ISA — RV32I

### Register File and ABI

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

### XLEN

XLEN is the native integer register width and address width of the ISA variant:

- RV32: XLEN = 32
- RV64: XLEN = 64
- RV128: XLEN = 128

All integer registers, PC, and integer immediates are XLEN bits, and the unprivileged spec is parameterized by XLEN.[^1][^2]

### Instruction Length and Formats

In the base ISA, every instruction word is 32 bits, aligned on 32-bit boundaries (addresses divisible by 4). Instruction formats pack opcode, register indices, function subcodes, and immediates in fixed bit positions to simplify decode.[^5][^1]

The main 32‑bit formats are:

- R-type: register–register operations (ADD, SUB, AND, OR, shifts, etc.)
- I-type: immediate arithmetic, loads, JALR, system instructions
- S-type: stores
- B-type: conditional branches
- U-type: upper-immediate instructions (LUI, AUIPC)
- J-type: JAL (jump and link)

#### R-type

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

#### I-type

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

#### S-type (Stores)

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
	 - imm[4:0] = 01000₂

Encoding: 0x00532323.[^5]

#### B-type (Branches)

```text
31      25 24   20 19   15 14  12 11      7 6      0
|imm[12|10:5]| rs2 | rs1 |funct3|imm[4:1|11]|opcode|
```

The 13-bit branch offset (multiple of 2 bytes) is spread across bits for efficient sign-extension, with bit 0 implicitly 0. Example: `BEQ x1, x2, offset` uses opcode 1100011₂ and funct3 000₂.[^5]

#### U-type (LUI, AUIPC)

```text
31                     12 11    7 6      0
|         imm[31:12]      |  rd  | opcode |
```

The 20-bit immediate is placed in bits [31:12] and represents bits [31:12] of a 32-bit value with lower 12 bits zeroed. Instructions:[^5]

- `LUI rd, imm20`: rd = imm20 << 12
- `AUIPC rd, imm20`: rd = PC + (imm20 << 12)[^6]

#### J-type (JAL)

```text
31        12 11    7 6      0
|   imm[20|10:1|11|19:12] | rd | opcode |
```

The 21-bit signed offset (multiple of 2 bytes) is reassembled from scattered bits, with bit 0 implicit 0.[^5]

Example: `JAL x1, offset` saves return address in x1 and jumps PC-relative.[^1]

### Example Encodings Summary

- `ADD x3, x1, x2` → opcode 0x33, encoding 0x002081B3.[^5][^1]
- `ADDI x5, x6, 10` → opcode 0x13, encoding 0x00A30313.[^5][^1]
- `LW x5, 8(x6)` → opcode 0x03, funct3=010₂, encoding 0x00832303.[^5][^1]
- `SW x5, 8(x6)` → opcode 0x23, encoding 0x00532323.[^5]
- `BEQ x1, x2, offset` → opcode 0x63; offset encoding follows B-type layout.[^5]
- `JAL x1, offset` → opcode 0x6F; immediate per J-type format.[^5]

### Instruction Categories

RV32I defines instructions in several functional groups:[^1][^2]

- **Arithmetic/Logical:** `ADD`, `SUB`, `SLT`, `SLTU`, `AND`, `OR`, `XOR`, `SLL`, `SRL`, `SRA` and their immediate forms (`ADDI`, `SLTI`, etc.).
- **Shifts:** Logical and arithmetic shifts (register and immediate forms).
- **Loads/Stores:** Byte, halfword, word loads with sign/zero extension; stores for byte/halfword/word.
- **Control Flow:** Conditional branches (`BEQ`, `BNE`, `BLT`, `BGE`, etc.), `JAL`, `JALR`.
- **System:** `ECALL`, `EBREAK`, plus CSR instructions via Zicsr (`CSRRW`, `CSRRS`, etc.).[^2]
- **No dedicated flags register:** Instead of condition codes, branches compare register values directly (e.g., `BEQ x1, x2, label`) or rely on `SLT`/`SLTU`.[^1][^2]

### Notable Design Choices

- **No condition code flags:** Eliminates global status flags (NZCV) and associated hazards; comparisons produce boolean values in registers or are embedded within branch instructions.[^1][^2]
- **PC-relative addressing:** `AUIPC` and branches make PC-relative code easy, improving position-independent code and linker relaxation.[^6]
- **Defined division behavior:** For `DIV`/`DIVU` (in M extension), division by zero returns a defined result (−1 or all 1s) instead of trapping, simplifying low-level code.[^2]
- **x0 as zero:** Frequent use of zero constants costs no register and helps encode moves and clears cheaply (e.g., `ADD x5, x0, x6` is a move).[^1][^2]

***

## 2.2 RV64I — 64-bit Base ISA

RV64I generalizes RV32I to 64-bit integer registers and addresses. Key implications:[^4][^2]

- **Registers:** 32 general-purpose registers, each 64 bits (XLEN = 64).[^2]
- **Address space:** 64-bit virtual and physical addresses (exact virtual address scheme governed by the privileged spec and `satp`/paging mode).[^2]

### Additional Instructions

RV64I adds instructions that operate on 32-bit subwords but store sign-extended results in 64-bit registers:[^4][^2]

- **Loads/stores:**
	 - `LWU rd, offset(rs1)`: Load 32-bit word and zero-extend to 64 bits.
	 - `LD rd, offset(rs1)`: Load 64-bit doubleword.
	 - `SD rs2, offset(rs1)`: Store 64-bit doubleword.
- **W-suffix arithmetic/logical:**
	 - `ADDIW rd, rs1, imm`: 32-bit add, then sign-extend result to 64 bits.
	 - `ADDW`, `SUBW`, `SLLW`, `SRLW`, `SRAW`, and immediate variants (`SLLIW`, `SRLIW`, `SRAIW`).[^2]

The W-forms treat operands as 32-bit values (lower 32 bits of register), perform the operation in 32 bits, then sign-extend to 64 bits; this matches C’s int32 arithmetic semantics on a 64-bit platform.[^4][^2]

### Sign Extension and Memory Model Implications

- **32→64-bit promotion:** Many instructions implicitly sign-extend their 32-bit results (e.g., `ADDW`); compilers must be aware when mixing 32-bit and 64-bit operations to avoid redundant `SEXT` or masking.[^2]
- **Loads:**
	 - `LW`: sign-extends 32-bit word.
	 - `LWU`: zero-extends.
	 - `LD`: does not extend (already 64 bits).[^2]
- **Pointer size:** Pointers and `size_t` are typically 64-bit under the LP64D ABI (Linux RISC-V), impacting stack frame layout and struct alignment.[^4][^2]

The semantics are chosen so that compiled 32-bit code ported to 64-bit RISC-V behaves naturally, similar to x86-64’s 32-bit register write semantics (zero-extend to 64 bits) but using sign-extension for W operations in line with C integer rules.[^2]

***

## 2.3 Standard Extensions — Detailed Coverage

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

### M — Integer Multiply/Divide

- **Motivation:** Many workloads (DSP, graphics, cryptography, general integer code) require fast multiplication and division; doing them in software on RV32I alone is slow.[^2]
- **Key instructions:** `MUL`, `MULH`, `MULHSU`, `MULHU`, `DIV`, `DIVU`, `REM`, `REMU`.[^2]
- **Hardware implications:** Adds a multiplier/divider unit, which can be implemented as a single-cycle or multi-cycle pipeline depending on area/power targets.
- **Use cases:** Almost all general-purpose SoCs, MCUs with moderate performance needs, DSP tasks in embedded and communication stacks.

### A — Atomic Memory Operations

- **Motivation:** Provide portable primitives for lock-free synchronization and multi-core concurrency without relying on LL/SC quirks or non-standard instructions.[^2]
- **Key instructions:**
	 - `LR.W` / `SC.W`, `LR.D` / `SC.D`: load-reserved and store-conditional.
	 - AMOs: `AMOADD`, `AMOSWAP`, `AMOAND`, `AMOOR`, `AMOXOR`, `AMOMIN`, `AMOMAX`, `AMOMINU`, `AMOMAXU`.[^2]
- **Hardware implications:** Reservation set or address tracking for LR/SC; atomic read-modify-write in memory subsystem; coherence protocol awareness for multi-core.[^2]
- **Use cases:** OS kernels, concurrent data structures, lock-free queues, user-space atomics in C/C++ (`std::atomic`).

### F/D/Q — Floating-Point

- **Motivation:** IEEE-754 floating point is fundamental for scientific computing, media processing, and ML workloads.[^2]
- **Key instructions:**
	 - F: single-precision arithmetic (`FADD.S`, `FMUL.S`, `FDIV.S`, `FSQRT.S`), conversions, compares, fused multiply-add (`FMADD.S`).
	 - D: same set with `.D` suffix operating on 64-bit floats; requires F.[^2]
	 - Q: quad-precision operations; requires D.[^2]
- **Hardware implications:** Separate FP register file `f0`–`f31`, FP execution units (adder, multiplier, divider, sqrt, FMA), FP control/status register `fcsr`.[^2]
- **Use cases:** HPC, multimedia, signal processing, ML inference and training (typically F and D; Q is niche for numerical analysis and high-precision finance).

### C — Compressed Instructions

- **Motivation:** Code size reduction improves I-cache and I-TLB hit rates and reduces memory bandwidth—critical for embedded systems and beneficial even for large cores.[^2]
- **Key features:**
	 - 16-bit encodings for a subset of popular instructions (e.g., `C.ADDI`, `C.LW`, `C.SW`, `C.J`, `C.JAL`, `C.LI`, `C.LUI`, `C.ADD`, `C.MV`).[^2]
	 - Mixed 16/32-bit stream; decoder expands C instructions into canonical 32-bit internal form.
- **Hardware implications:** Slightly more complex decode front-end to handle 16and 32-bit instruction boundaries, but gains from reduced fetch bandwidth and code storage.[^2]
- **Use cases:** Almost all production RISC-V cores, from microcontrollers (firmware flash savings) to Linux SBCs (smaller binaries, energy savings).

### B — Bit Manipulation (Zba, Zbb, Zbc, Zbs)

- **Motivation:** Modern cryptography, graphics, and bit-level algorithms spend significant time doing shifts, masks, rotates, and logical combinations; specialized instructions can reduce instruction count and improve constant-time coding.[^2]
- **Key operations:**
	 - Zba: address generation, add with shift (`SH1ADD`, `SH2ADD`, `SH3ADD`).
	 - Zbb: basic bit-manip (`ANDN`, `ORN`, `XORN`, `CLZ`, `CTZ`, `PCNT`, `MIN`, `MAX`).
	 - Zbc: carry-less operations for crypto (e.g., polynomial multiply).
	 - Zbs: single-bit set/clear/invert/extract (`BSET`, `BCLR`, `BINV`, `BEXT`).[^2]
- **Hardware implications:** Additional ALU sub-blocks for bit operations; usually modest area overhead.
- **Use cases:** Cryptography libraries, bitset operations in databases, network stacks, and compression.

### V — Vector Extension (RVV 1.0)

- **Motivation:** High-performance computing, media, and ML workloads benefit from SIMD/vectorization, but fixed-width SIMD (like SSE/NEON) ages poorly as vector widths grow; RVV’s scalable vectors abstract over physical width.[^2]
- **Key concepts:** See Section 2.9; includes vector integer and FP arithmetic, loads/stores, reductions, permutations, and masks with dynamic `vsetvli` configuration.[^2]
- **Hardware implications:** Vector register file `v0`–`v31`, vector ALUs, load/store units, and mask registers; area scales with maximum VLEN (e.g., 128, 256, 512 bits).[^2]
- **Use cases:** HPC, signal processing, ML inference/training, graphics, and any throughput-oriented workloads.

### H — Hypervisor

- **Motivation:** Efficient virtualization of RISC-V systems for cloud and data-center use; support multiple guest OS instances with good performance isolation.[^2]
- **Key features:**
	 - Additional privilege mode HS (host supervisor) and virtual equivalents VS/VU.
	 - Two-stage address translation via `hgatp` (host) and `vsatp` (guest).
	 - New CSRs for delegation and virtualization (e.g., `hstatus`, `hideleg`, `hvip`).[^2]
- **Hardware implications:** TLB and MMU extended for nested page tables; additional privilege checks; interrupt virtualization logic.
- **Use cases:** KVM-RISC-V in Linux, cloud hypervisors, container host kernels when combined with hardware VMs for isolation.

### K — Crypto Scalar Extensions

- **Motivation:** Hardware acceleration for common cryptographic primitives reduces latency and mitigates side-channel leakage relative to naive software loops.[^2]
- **Key operations:** Sub-extensions Zkn (AES, SM4), Zks (SHA2, SM3), Zkr (entropy source), Zknd/Zkne (AES round functions, etc.).[^2]
- **Hardware implications:** Dedicated AES/SHA/SM datapaths and S-box logic; entropy source IP; constant-time pipelines.
- **Use cases:** TLS, VPN, disk encryption, secure boot, and general-purpose system security.

***

## 2.4 Control and Status Registers (CSRs)

CSRs are special registers that control privileged behavior, expose performance counters, and hold exception/interrupt state. CSR access instructions are defined by the Zicsr extension and are required for any realistic system.[^2]

### CSR Access Instructions (Zicsr)

- `CSRRW rd, csr, rs1` — Atomic read/write CSR.
- `CSRRS rd, csr, rs1` — Read and set bits.
- `CSRRC rd, csr, rs1` — Read and clear bits.
- Immediate variants `CSRRWI`, `CSRRSI`, `CSRRCI` use a 5-bit immediate instead of rs1.[^2]

These use the I-type format with the CSR address in bits [31:20] and funct3 differentiating the op.[^2]

### Key Machine-Mode CSRs

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

### `misa` Layout and Extension Discovery

`misa` is an XLEN-bit CSR; its high bits encode the base XLEN and lower bits encode which standard extensions are present:[^2]

- Bits [XLEN−1:XLEN−2]: Encoded XLEN (01 for 32, 10 for 64, 11 for 128).
- Bits [25:0]: Each bit corresponds to an extension letter (bit 0 = ‘A’, bit 1 = ‘B’, …, bit 25 = ‘Z’).[^2]

Example: a system with RV64IMAFDCV might have the bits for I, M, A, F, D, C, V set. Software can read `misa` at runtime to discover available extensions and adapt (e.g., use vector code paths only when V is present).[^2]

***

## 2.5 Instruction Encoding Philosophy

The unprivileged spec documents several explicit encoding goals:[^1][^2]

- **Uniform register field positions:** `rs1`, `rs2`, and `rd` are in the same bit positions for all formats that use them (rd at [11:7], rs1 at [19:15], rs2 at [24:20]); this simplifies decoder hardware.[^1]
- **Sign-extension efficiency:** For all immediates except CSR immediates, the sign bit (*) is placed in bit 31; scattered immediate fields are arranged so that sign-extension hardware is simple and consistent across formats.[^5][^1]
- **Opcode space partitioning:** 7-bit opcode field at [6:0] is partitioned into major opcode groups that leave room for future standard and custom extensions, and that are compatible with future 48/64-bit encodings.[^1][^2]
- **Variable-length compatibility:** By requiring 16-bit alignment and reserving certain opcode patterns, the ISA supports mixing 16-bit C instructions with 32-bit base instructions, and leaves space for future 48/64-bit encodings while keeping decode relatively straightforward.[^2]

### Major Opcode Map (Simplified)

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

## 2.6 Memory Model — RVWMO (RISC-V Weak Memory Ordering)

RISC-V defines RVWMO, a weak memory ordering model designed to support high-performance, out-of-order multi-core hardware while still being amenable to formal reasoning. It is weaker than x86’s TSO but allows stronger ordering via fences and atomics.[^4][^2]

Key aspects:

- **Relaxed ordering:** Loads and stores from different cores may be observed in different orders unless the program uses fences or atomics to constrain reordering.[^2]
- **Per-location SC with atomics:** Atomic operations provide strong guarantees for synchronization variables, similar to C/C++ atomic semantics.[^2]
- **FENCE instruction:** `FENCE [pred],[succ]` constrains memory and I/O ordering, ensuring that prior memory operations are globally visible before subsequent operations matching the mask.[^2]

### LR/SC and AMOs in the Memory Model

- **LR/SC (Load-Reserved/Store-Conditional):** Provide a loop-based primitive for building locks and lock-free algorithms: `LR` reads a value and sets a reservation; `SC` attempts to store and succeeds only if no conflicting writes occurred.[^2]
- **AMOs:** Single-instruction read-modify-write operations that are atomic with respect to other cores (e.g., `AMOADD.W`, `AMOSWAP.D`).[^2]

These are integrated into RVWMO’s formal model so that properly synchronized code behaves as if memory were sequentially consistent for those synchronization operations.[^2]

### Comparison to x86 TSO and ARM

- **x86 TSO:** Stronger; nearly all writes are observed in order, and many reorderings are forbidden, simplifying programmer reasoning but constraining microarchitectural optimizations.[^2]
- **ARM (ARMv8/ARMv9):** Also weakly ordered, with explicit barriers (`DMB`, `DSB`, `ISB`) for ordering.[^2]
- **RISC-V RVWMO:** Similar in spirit to ARM’s model—allows many reorderings, but fences and atomic operations provide the necessary guarantees for data-race-free programs.[^4][^2]

***

## 2.7 Exception and Interrupt Handling (Unprivileged View)

The unprivileged spec relies on the privileged architecture for trap handling; here we summarise essentials relevant to ISA-level understanding.[^2]

### Privilege Levels

RISC-V defines up to four privilege levels:[^2]

- **M (Machine, 3):** Highest privilege; firmware, bootloader, low-level runtime.
- **S (Supervisor, 1):** OS kernels, hypervisors (without H) or guest kernels (with H).
- **U (User, 0):** Applications.
- **HS/VS/VU:** Additional virtualized modes introduced by H-extension (see Section 7.9 for details).[^2]

Many microcontrollers implement only M-mode; Linux-capable systems implement at least M and S, with optional U for user-space.[^2]

### Trap Causes

Traps (exceptions + interrupts) include:[^2]

- **Synchronous exceptions:** Illegal instruction, instruction or data access fault, misaligned load/store/jump, breakpoint (`EBREAK`), environment calls (`ECALL`) from U/S/M, page faults (if virtual memory is enabled).
- **Asynchronous interrupts:** Timer interrupts, software interrupts (IPIs), external device interrupts.[^2]

### Core Trap CSRs

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

### ECALL as System Call Mechanism

- **From U-mode:** `ECALL` raises an environment call exception; delegated via `medeleg` to S-mode, whose trap handler implements system calls (e.g., Linux’s `syscall` entry).[^2]
- **From S-mode:** `ECALL` may trap to M-mode for hypervisor or firmware services, depending on delegation.[^2]

`mcause` encodes distinct cause codes for ECALL from U, S, and M, allowing handlers to distinguish origin.[^2]

***

## 2.8 Floating-Point Architecture

When F/D/Q are present, RISC-V adds a separate FP register file and control/status mechanisms conforming to IEEE-754.[^2]

### FP Register File and NaN Boxing

- **Registers:** `f0`–`f31`, each XLEN bits in RV32/RV64 (32 or 64 bits), but capable of holding narrower FP values using _NaN boxing_.[^2]
- **NaN boxing:** When a narrower FP value (e.g., 32-bit float) is stored in a wider register (e.g., 64-bit FP register under D), the upper bits are filled with a canonical NaN pattern so that operations treating it as a wider value still see a NaN if interpreted incorrectly.[^2]
- **Register classes:** The ABI defines calling conventions for passing FP arguments and return values when FP is enabled (e.g., LP64D uses both x and f registers).[^4][^2]

### Rounding Modes and Exception Flags

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

### Layering of F, D, Q

- F is the base floating-point extension.
- D requires F and adds double-precision instructions and semantics.
- Q requires D and adds quad-precision.[^2]

This layering ensures incremental hardware complexity growth and consistent behavior across precisions.

***

## 2.9 Vector Extension (RVV 1.0) — Overview

RVV 1.0 is a ratified scalable vector extension that decouples the number of architectural elements processed per instruction from the physical vector width, enabling portable high-performance code across implementations with different VLEN.[^2]

### Key Parameters and Registers

- **VLEN:** Hardware vector register width in bits (e.g., 128, 256, 512, 1024). Fixed per microarchitecture.[^2]
- **ELEN:** Maximum element width supported (e.g., 32 or 64 bits).[^2]
- **LMUL:** Vector register grouping multiplier; groups multiple physical vector registers to form a logical vector register with more lanes (e.g., LMUL = 2, 4, 8) or fractional groups (1/2, 1/4).[^2]
- **Vector register file:** `v0`–`v31`; each a vector of elements. Some are reserved for masks or special use.[^2]
- **CSRs:** `vl` (current vector length in elements) and `vtype` (current vector type: SEW, LMUL, tail/mask policies).[^2]

### Vector Configuration — `vsetvli` / `vsetivli`

- `vsetvli rd, rs1, imm`: Sets `vl` based on requested element width (SEW) and LMUL encoded in `imm`, and the available VLEN and ELEN; returns actual `vl` in `rd`.[^2]
- `vsetivli rd, uimm, imm`: Same but with immediate element count.[^2]

This dynamic configuration lets the same binary adapt to different vector lengths at runtime, unlike fixed-width SIMD where binaries must be recompiled per width.[^2]

### Categories of Vector Instructions

RVV defines rich instruction categories:[^2]

- **Integer arithmetic:** `vadd`, `vsub`, `vmul`, `vdiv`, `vmin`, `vmax`, etc.
- **Floating-point:** `vfadd`, `vfsub`, `vfmul`, `vfdiv`, `vfsqrt`, `vfmacc`, etc.
- **Logical and mask ops:** `vand`, `vor`, `vxor`, mask set/clear operations.
- **Reduction:** `vredsum`, `vredmax`, etc., to reduce vectors to scalars.
- **Permutation:** `vslide`, `vrgather`, `vcompress` for shuffles and compactions.
- **Vector loads/stores:** Unit-stride, strided, indexed, segmented variants.
- **Masking:** Nearly all operations support masking, executing only on lanes where mask bit is 1.[^2]

### Why RVV is Architecturally Superior to Fixed-Width SIMD

- **Scalability:** The same program can scale from 128-bit to 1024-bit hardware without recompilation, merely by changing `vsetvli` behavior.[^2]
- **Portability:** Compilers emit generic RVV code once; hardware vendors choose their preferred VLEN and microarchitectural tricks.
- **Energy efficiency:** Hardware may trade off more lanes vs. frequency/voltage while preserving software semantics.
- **Masking and tail handling:** Explicit mask and tail policies avoid the need for scalar epilogues in many vectorized loops.[^2]

### Example: Vector Dot Product in RVV Assembly

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

The actual code would loop, updating `a0`, `a1`, and `a2` by `vl` (the number of elements processed) until the entire vector is consumed. The same code runs efficiently on any VLEN implementation because `vsetvli` chooses `vl` at runtime.  
<span style="display:none">[^10][^11][^12][^13][^14][^15][^9]</span>

RISC-V and ARM differ fundamentally in licensing, extensibility, and several ISA design choices, and RISC-V’s openness plus modular ISA make it particularly attractive for India’s academic and strategic needs. This continuation covers Sections 3–8 and the Quick Reference, starting now with a rigorous ARM comparison.[^1][^2]

# SECTION 3 — RISC-V vs ARM: RIGOROUS SIDE-BY-SIDE COMPARISON

## 3.1 Licensing and Openness

RISC-V and ARM sit at opposite ends of the licensing spectrum.

| Attribute | RISC-V | ARM |
| :-- | :-- | :-- |
| License type | Open ISA, royalty-free; spec downloadable and implementable by anyone | Proprietary ISA; implementers must sign licenses and pay up-front and per-unit royalties |
| Who owns it | RISC-V International, non-profit based in Switzerland | ARM Holdings plc (SoftBank Group; listed as ARM on Nasdaq) |
| Chip design cost | No ISA license or royalty cost; only implementation costs | ISA and core-IP licensing can be in the \$1M–\$10M+ range per project, plus royalties |
| Custom extensions | First-class citizen; dedicated opcode space reserved for vendors | Not allowed in architectural opcode space; only ARM-defined extensions and coprocessors |
| HDL cores available | Many open RTL cores (Rocket, BOOM, CVA6, Ibex, SHAKTI, PicoRV32, etc.) | ARM does not publish synthesizable RTL for its mainstream cores |
| Formal spec | Full unprivileged + privileged specs public; Sail formal model public | ARM ARM (Architecture Reference Manual) is public, but no corresponding open formal model |
| Export control risk | ISA itself is not export-controlled | ARM Holdings and its IP fall under UK and often US export control regimes |

RISC-V’s openness allows Indian institutes and startups to implement full Linux-capable cores (e.g., SHAKTI, VEGA) without negotiating with a foreign IP holder, which is a core reason for DIR-V’s focus on RISC-V.[^3][^1]

***

## 3.2 ISA Design: RISC-V vs ARMv8/AArch64 (ARMv9-compatible)

ARM’s modern 64-bit ISA (AArch64, used in ARMv8 and ARMv9) is also a RISC ISA but with some legacy baggage and different design trade-offs.[^4][^5]

| Attribute | RISC-V (RV64G + V) | ARM (AArch64 / ARMv9-family) |
| :-- | :-- | :-- |
| ISA age / revision | Designed ~2010; unprivileged spec evolving (e.g., 2024 PDFs) | ARM family since 1980s; ARMv8 AArch64 introduced ~2011; ARMv9 from 2021 |
| Integer registers | 32 × 64-bit GPRs (x0–x31), x0 hardwired to 0 | 31 × 64-bit GPRs (x0–x30) + xzr (zero) and sp; PC not a GPR |
| FP/SIMD registers | 32 FP regs (f0–f31) if F/D; 32 vector regs (v0–v31) if V | 32 128-bit SIMD/FP regs (v0–v31) shared between NEON/SVE and FP |
| Instruction length | Base 32-bit; optional 16-bit C extension; future 48/64 | A64: fixed 32-bit; AArch32 Thumb-2: 16/32-bit mix |
| Condition codes | None; no NZCV flags; comparisons via SLT/branches | Global NZCV flags in PSTATE.NZCV, updated by many arithmetic instructions |
| Predication | No general predication; uses branches and RVV masks | Conditional branches + CSEL/CSINC/CSINV/CSNEG; SVE has predicate registers |
| Zero register | x0 | xzr (pseudo-register mapped to zero) |
| Link register | x1 by ABI convention (ra) | x30 (LR) architecturally designated |
| SIMD / Vector | V extension: scalable VLEN, LMUL, SEW; RVV 1.0 | NEON (128-bit fixed) + SVE/SVE2 scalable vectors (AArch64 only) |
| Atomics | LR/SC + AMO ops (A-ext) | Load-acquire/store-release (LDAXR/STLXR), CAS, SWP, and atomics |
| Memory model | RVWMO: weak ordering with fences | Weak ordering with DMB/DSB/ISB; slightly stronger than RISC-V in some cases |
| Secure world | PMP + TEEs (e.g., Keystone) in RISC-V context | TrustZone splits secure/non-secure worlds at hardware level |
| Virtualization | H-extension: HS/VS/VU modes | EL2 hypervisor mode; mature virtualization support |
| Pointer safety | Draft CFI extensions (Zicfilp, Zicfiss, etc.) | PAC (Pointer Authentication Codes) and BTI (Branch Target Identification) |
| Memory safety | Proposed pointer masking/tagging work | MTE (Memory Tagging Extension) with 4-bit tags per 16-byte granule |

Architecturally, both ISAs are clean RISC designs, but RISC-V emphasizes minimalism and modularity, while ARM carries stronger backward-compatibility constraints and a more monolithic extension strategy.[^5][^2]

***

## 3.3 Performance and Pipeline Design

Performance depends on microarchitecture, process node, and implementation details rather than just the ISA. As of 2024–2025, ARM has a more mature ecosystem of high-end cores, but RISC-V is catching up in mid-range out-of-order cores.[^6][^7]

### Representative ARM High-Performance Cores

- Cortex-A75/A76/A77, Cortex-A78, Cortex-X1/X2/X3, and ARMv9 cores like Cortex-X4 deliver double-digit SPECint2006/GHz scores and are used in smartphones and laptops.[^5]
- These cores are deeply out-of-order, wide-issue (up to 6-wide decode/issue), and coupled with advanced prefetchers and large shared caches.[^5]

### Representative High-Performance RISC-V Cores

- **SiFive Performance P550:** 13-stage, triple-issue out-of-order RV64GBC core; SiFive reports ~8.65 SPECint2006/GHz, comparable to ARM Cortex-A75.[^8][^6]
- **T-Head XuanTie C910:** 4-wide out-of-order RV64GCV core used in TH1520 SoC; benchmarked somewhat below Cortex-A73/A75 per SPEC CPU2017 data.[^7]
- **SiFive P800/P870 series:** Newer cores targeting >18 SPECint2006/GHz (marketing numbers for P870), competing against newer ARM cores, though silicon and independent benchmarks are still emerging.[^9][^8]
- **Academic cores:** BOOM (Berkeley Out-of-Order Machine) is a Chisel-based superscalar core that demonstrates high-IPC research pipelines but is not a commercial CPU.[^2]

### IPC Gap and Current Status

Independent benchmarking of P550 and C910 shows them trailing well-established ARM cores such as Cortex-A73/A75 in single-thread SPEC CPU2017, largely due to more conservative microarchitectures and lower clocks. SiFive’s own numbers suggest P550 is roughly in the same ballpark as Cortex-A75 on a per-GHz basis, but ARM’s latest Cortex-X series cores are significantly ahead.[^8][^6][^7]

The most accurate statement today:

- For cutting-edge smartphone-class CPUs, ARM remains roughly 30–50% ahead in single-thread IPC at comparable process nodes.
- In mid-range cores targeting embedded Linux, networking, and edge compute, RISC-V (P550, XuanTie C910, SpacemIT X-series) is competitive and improving rapidly.[^7][^8]

***

## 3.4 Ecosystem and Software Support

From a software perspective, ARM has had decades of production deployment, while RISC-V is ~10 years old but advancing quickly.

| Attribute | RISC-V | ARM |
| :-- | :-- | :-- |
| Linux kernel | Mainline support since 4.15 (2018); runs on many SoCs | Fully mainlined; dominant in mobile/embedded |
| GCC / LLVM | Full upstream support (riscv64/riscv32 backends) | Full support |
| Android | Officially added RISC-V support in AOSP (2023+); early ports | Primary mobile platform; all major SoCs run Android on ARM |
| Windows | No official Windows for RISC-V yet | Windows 11 on ARM available from Microsoft |
| Distros | Debian, Fedora, Ubuntu, Arch, Alpine ports for RISC-V | All major distros, often as primary architectures |
| JVM / .NET | OpenJDK ports underway/experimental; some commercial builds | Mature Java and .NET runtimes available |
| QEMU | Full-system and user-mode RISC-V emulation | Full-system and user-mode ARM emulation |
| Binary repos | Growing package archives; some gaps in niche packages | Dense, mature binary repos across many OS distributions |

For teaching and research, mainline Linux, GCC/LLVM, QEMU, and Spike already give a rich environment on RISC-V SBCs like VisionFive 2 and Banana Pi F3.[^2][^7]

***

## 3.5 Security Architecture Deep Comparison

Security features span from ISA attributes to system-on-chip integration. Here we focus on ISA-level and architectural support.

### Trusted Execution Environments

- **ARM TrustZone:** Splits the system into secure and non-secure worlds with separate address spaces and permissions mediated by the NS bit and TZASC; widely used in phones for DRM, payment, and key storage.[^5]
- **RISC-V PMP + Keystone:** Physical Memory Protection (PMP) and extensions like Smepmp allow hardware-enforced memory isolation. Keystone TEE builds a secure enclave abstraction on top of PMP and RISC-V Linux.[^2]

TrustZone is more tightly specified and widely deployed commercially today, whereas RISC-V TEEs are open and research-driven but rapidly maturing.

### Memory Safety

- **ARM MTE (Memory Tagging Extension):** Adds 4-bit tags to 16-byte granules in memory and matches them with pointer top-byte tags, catching spatial and temporal memory safety errors in hardware.[^5]
- **RISC-V:** Ongoing work around pointer masking/tagging and capability-like features, but there is no ratified equivalent of MTE yet. Some proposals and vendor-specific implementations exist.[^2]

### Control-Flow Integrity (CFI)

- **ARM PAC + BTI:** Pointer Authentication Codes cryptographically sign return addresses and function pointers, while BTI (Branch Target Identification) marks valid indirect branch targets, mitigating ROP/JOP attacks.[^5]
- **RISC-V CFI proposals:** Extensions such as Zicfilp (landing pads for control-flow integrity) and Zicfiss (shadow stacks) have been proposed to strengthen RISC-V’s defenses against control-flow attacks, but deployment is early.[^2]

### Secure Boot

- **ARM ecosystem:** Many SoCs implement secure boot using ROM-based keys, TrustZone, and proprietary security controllers.
- **RISC-V ecosystem:** OpenTitan (Google/lowRISC) is a notable open-source root-of-trust chip built around an RV32IMC Ibex core with a formally verified security-focused microarchitecture, demonstrating that high-assurance secure boot can be built around RISC-V.[^2]

Today, ARM offers more standardized and widely deployed security extensions (TrustZone, MTE, PAC), while RISC-V offers a more open and customizable foundation with ongoing standardization in CFI and security extensions.

***

## 3.6 Power Efficiency

Both ARM and RISC-V rely on RISC-style principles, so power efficiency mainly depends on microarchitecture and process node. However, ISA features like compressed instructions and vector design also play roles.

### Code Size and I-Cache

- **RISC-V C extension:** Typically achieves 20–30% code size reduction for typical embedded and OS workloads by compressing high-frequency patterns to 16 bits; this translates to fewer I-cache misses and reduced fetch energy.[^2]
- **ARM Thumb-2 (AArch32):** Historically provided similar benefits, but AArch64 dropped Thumb in favor of fixed 32-bit encoding (though it gained SVE/SVE2 and other power-aware microarchitectural techniques).[^5]

### big.LITTLE vs Heterogeneous RISC-V

- **ARM big.LITTLE / DynamIQ:** Pairs high-performance “big” cores (e.g., Cortex-A78/X1) with energy-efficient “LITTLE” cores (A55) in heterogeneous clusters under a shared coherence fabric.[^5]
- **RISC-V:** The ISA places no constraints preventing similar heterogeneous topologies. Several vendors are already combining small and large RISC-V cores or pairing scalar cores with AI accelerators and NPUs on a single die (e.g., SpacemIT K1/X100 with NPUs).[^7]

In embedded MCUs, RISC-V cores (e.g., low-power RV32IMC cores like VexRiscv or Ibex) are competitive with ARM Cortex-M0/M3/M4 in mW/CoreMark, but direct apples-to-apples, process-matched comparisons remain sparse in public literature and often come from vendor marketing.[^7][^2]

***

## 3.7 Unique Advantages of RISC-V Over ARM (Professor-Ready)

For an MTech course or research group, the following are robust arguments:

- **No ISA licensing cost:** University projects can tape out RISC-V silicon under academic shuttle programs paying only foundry/shuttle costs; no IP license fees are needed, unlike ARM.[^1][^2]
- **Open extensibility:** RISC-V reserves opcode ranges for vendors and defines a modular extension mechanism, allowing AI accelerator companies or cryptography vendors to add custom matrix or crypto instructions without fragmenting the base ISA.[^2]
- **Single ISA with explicit extensions:** Instead of separate ISAs (ARM vs Thumb vs Thumb-2 vs AArch64), RISC-V has one base plus extensions, encoded and discoverable via `misa`; this simplifies compiler backends and dynamic feature probing.[^2]
- **Formal specification:** RISC-V has a public Sail formal model that defines executable semantics for the ISA, facilitating machine-checked proofs of correctness and microarchitectural verification.[^2]
- **Sovereign chip design:** Countries like India can design and manufacture RISC-V processors (VEGA, SHAKTI, DHRUV-class cores) without depending on a foreign commercial vendor’s approval, which is a central point in MeitY’s DIR-V roadmap.[^10][^1]
- **Educational accessibility:** Any student can download the full ISA spec and formal model without NDAs; this dramatically lowers the barrier to serious architecture and verification research compared to ARM/x86.[^2]

For professors, this means RISC-V can unify teaching (ISA, microarchitecture, OS, compilers) with real-world industrial relevance.

***

# SECTION 4 — WHY RISC-V WHEN MIPS IS ALREADY A RISC ISA?

## 4.1 Brief History of MIPS

MIPS (Microprocessor without Interlocked Pipeline Stages) originated at Stanford in the early 1980s as one of the foundational RISC designs. It introduced many classic RISC ideas—fixed-length instructions, load-store architecture, and relatively few addressing modes—and later evolved into commercial MIPS32 and MIPS64 ISAs used in SGI workstations, networking equipment, and embedded devices.[^2]

Through the 1990s and early 2000s, MIPS had a significant presence in consumer electronics (routers, set-top boxes, game consoles) but gradually lost ground to ARM in mobile and embedded.[^11]

***

## 4.2 MIPS Licensing Saga

MIPS’s ownership history is complex:[^11]

- Initially commercialized by **MIPS Computer Systems**, then associated with **Silicon Graphics (SGI)**.
- IP transitioned via **MIPS Technologies** and later was sold to **Imagination Technologies**, which used MIPS alongside its PowerVR GPU IP.
- Subsequently, rights moved through entities like **Wave Computing** and **MIPS Tech LLC**, with conflicting roadmaps and partial attempts to “open” the ISA that never fully materialized.
- This led to considerable uncertainty about long-term governance, licensing terms, and the viability of the ecosystem.

This turbulence and proprietary licensing discouraged new academic and industrial users, especially once RISC-V emerged as a clean, open alternative.

***

## 4.3 MIPS Architectural Technical Debt

Despite being RISC, MIPS carries some legacy baggage that RISC-V deliberately avoids:[^2]

- **Branch delay slots:** Classic MIPS requires that the instruction following a branch is always executed (whether or not the branch is taken), complicating pipelines, compilers, and formal reasoning; later MIPS revisions tried to deprecate this, but it remains architectural baggage.
- **ISA fragmentation:** MIPS16 and microMIPS were compressed encodings added later to reduce code size, resulting in multiple encodings and modes, complicating toolchains and debugging.
- **Limited modular extension mechanism:** Extensions tend to be vendoror generation-specific (e.g., MIPS DSP, MSA for SIMD) rather than a clean, modular, standard extension framework like RISC-V’s lettered + Z* scheme.
- **Governance:** There is no open non-profit body like RISC-V International overseeing the spec and ratifying extensions; decisions follow whichever company holds IP at the time.

RISC-V explicitly learned from such pain points, particularly in removing delay slots and designing modular standard extensions from the outset.[^2]

***

## 4.4 ARM vs MIPS in the Market

ARM beat MIPS in the embedded/mobile market for a combination of technical and business reasons:[^11]

- **Ecosystem and tooling:** ARM built extensive partnerships with OEMs, OS vendors, and tool vendors; ARM’s GNU toolchains, debuggers, and OS ports were more consistently supported.
- **Licensing flexibility:** ARM’s IP business model allowed many SoC vendors (Qualcomm, Samsung, Apple, MediaTek, etc.) to license cores or architecture in tailored ways, whereas MIPS’s licensing and ownership changes created uncertainty.
- **Mobile timing:** ARM’s cores were integrated into early mobile SoCs just as smartphones exploded, giving ARM huge volume and an ecosystem flywheel; MIPS was largely relegated to networking and some CE devices.
- **Perception and momentum:** Once ARM became the default for mobile, investment in ARM tooling and software far outpaced that for MIPS, deepening the gap.

By the time RISC-V appeared, MIPS’s ecosystem was already in decline, making RISC-V more attractive as a fresh, open foundation.

***

## 4.5 Why RISC-V Beats MIPS Today

Key reasons RISC-V has effectively displaced MIPS as “the” open RISC ISA for research and new designs:[^11][^2]

- **Clear, open governance:** RISC-V International provides transparent processes for spec evolution, membership, and voting; MIPS has no analogous independent body.
- **No branch delay slots:** RISC-V’s control flow model is simpler for modern pipelines, compilers, and formal tools.
- **First-class modular extensions:** ISA extensions are standardized with explicit letters and Z*-prefix names, ratified by working groups; MIPS’s extensions are more ad-hoc and tied to vendors.
- **Toolchain focus:** GCC/LLVM, Linux, and major OS projects are actively investing in RISC-V; MIPS support is stable but largely in maintenance mode.
- **Community and momentum:** Global open-hardware and academic communities have rallied around RISC-V, from Berkeley’s Rocket/BOOM to IIT Madras SHAKTI and C-DAC VEGA, while MIPS-based academic projects have dwindled.[^10][^3]

For an MTech curriculum in 2026, choosing MIPS would mean fighting against the ecosystem tide; RISC-V is now the canonical open ISA.

***

## 4.6 Four-Way Comparison: RISC-V vs MIPS vs ARM vs x86

| Attribute | RISC-V | MIPS | ARM (AArch64) | x86/x86-64 |
| :-- | :-- | :-- | :-- | :-- |
| License | Open, royalty-free ISA | Proprietary, vendor-owned | Proprietary, ARM Holdings | Proprietary, Intel/AMD |
| Branch delay slot | No | Yes (in classic ISA) | No | No |
| Integer registers | 32 (x0–x31) + x0=0 | 32 (r0–r31) | 31 (x0–x30) + xzr/sp | 8 GPR in x86, extended in x86-64 but irregular |
| Vector support | RVV 1.0 scalable vectors | MSA (fixed 128-bit SIMD) | NEON + SVE/SVE2 scalable vectors | SSE/AVX/AVX-512 fixed-width SIMD |
| Atomic ops | LR/SC + AMO | LL/SC | LDAXR/STLXR + CAS, SWP, atomics | LOCK-prefixed instructions (CMPXCHG, etc.) |
| Governance | RISC-V International (non-profit) | No independent public body | ARM Holdings | Intel/AMD ISA control |
| Ecosystem 2026 | Rapidly growing, many new SoCs | Near-dormant in new designs | Very mature, especially mobile | Dominant in PCs/servers |
| Open formal spec | Yes, with Sail formal model | No public formal model | No public full formal model | No public full formal model |

This table motivates RISC-V as the natural choice for an open ISA focus in research and teaching.

***

# SECTION 5 — INDIA’S RISC-V STRATEGIC LANDSCAPE

## 5.1 Policy and Government Programs

India has explicitly centered RISC-V in its semiconductor and digital sovereignty strategy.

- **Digital India RISC-V (DIR-V) Program:** Launched on 26–27 April 2022 by MeitY, with Prof. V. Kamakoti (Director, IIT Madras) as Chief Architect. The program’s roadmap targets indigenous RISC-V-based SoCs for mobile devices, servers, IoT, and automotive applications by mid-decade.[^10][^1]
- **Semicon India Program:** A ₹76,000 crore (about \$10B) incentive scheme to support semiconductor manufacturing, design, and packaging, including fab and OSAT initiatives.[^12]
- **Chips-to-Startups (C2S):** A national capacity-building program aiming to train tens of thousands of students and professionals in semiconductor design, with RISC-V design flows as a central pillar.[^12]
- **Design Linked Incentive (DLI) Scheme:** Provides staged financial support for Indian fabless semiconductor startups from design through tape-out and commercialization, with several RISC-V startups as early beneficiaries.[^12]
- **DIR-V ecosystem roadshows and symposia:** Regular events (e.g., roadshows in 2023 and beyond) to coordinate IITs, C-DAC, ISRO, industry partners, and startups; IIT Madras has hosted multiple DIR-V symposia bringing together SHAKTI, VEGA, industrial and international RISC-V partners.[^13][^10]

Together, these programs signal a long-term national commitment to RISC-V-based processor design and ecosystem development.

***

## 5.2 C-DAC VEGA and TEJAS Processors

C-DAC (Centre for Development of Advanced Computing) leads VEGA, a family of RISC-V-based processors under MeitY funding.[^14][^10]

- **VEGA processors:** Include 32-bit and 64-bit RISC-V cores targeting MCU, embedded, and high-performance applications; tape-outs have been completed at nodes like 130 nm and 180 nm at SCL Mohali and Silterra.[^14][^10]
- **ARIES development boards:** C-DAC has released ARIES boards (ARIES Micro, ARIES V2, ARIES V3, ARIES IoT, ARIES DOT) based on VEGA chips as fully indigenous “Made in India” kits for learning, embedded systems, and IoT design.[^10]
- **Tejas-class chips:** Some VEGA cores (such as a 32-bit RISC-V SoC) have been integrated into boards with Arduino-compatible headers, aiming to facilitate curriculum integration across Indian universities.[^14][^10]

These platforms give Indian academia and startups a local, supported RISC-V option for teaching and product prototyping.

***

## 5.3 IIT Madras — SHAKTI Processor Family

The SHAKTI project at IIT Madras (RISE group) is India’s flagship academic RISC-V core family.[^13][^10]

Core classes include:

- **E-class:** Ultra-low-power RV32IM-class cores for IoT and deeply embedded systems.
- **C-class:** RV64GC-class, Linux-capable embedded cores for mid-range applications.
- **I-class:** Higher-performance, out-of-order RV64GC-class cores targeting server and desktop workloads.
- **M/S/H classes:** Gradually more advanced cores (multicore, coherent, high-frequency) and security-enhanced variants for safety-/mission-critical domains.[^13]

Tape-outs:

- SHAKTI cores have been fabricated at SCL Chandigarh on 180 nm and at Intel foundry on 22 nm under the DIR-V program.[^10]
- These chips have been used in prototypes for drones, satellites, and other mission-critical domains where sovereign control of the ISA is important.[^10]

SHAKTI’s open-source RTL and toolflows also serve as a reference for Indian MTech projects in microarchitecture and SoC design.

***

## 5.4 DHRUV64 — India’s Milestone 64-bit Processor

Under DIR-V, MeitY has highlighted 64-bit RISC-V designs such as SHAKTI 64-bit cores and VEGA 64-bit processors fabricated at SCL. While individual chip names and branding evolve (e.g., DHRUV-class processors mentioned in policy documents and press coverage), the central point is:[^14][^10]

- India has successfully taped out and validated fully indigenous 64-bit RISC-V cores running at hundreds of MHz to GHz-class frequencies at domestic and international foundries, marking a transition from pure design to actual hardware products.[^14][^10]

For teaching and research in India, this indicates that RISC-V projects can scale from student RTL to national production platforms.

***

## 5.5 Mindgrove Technologies (IIT Madras Spinoff)

Mindgrove Technologies, a startup from IIT Madras, is building RISC-V-based secure IoT and vision chips and is an early DLI scheme beneficiary.[^3]

- It has developed secure IoT RISC-V SoCs that meet a very high number of STQC compliance metrics, positioning them for government and industrial adoption.
- The company is also prototyping vision chips for edge AI scenarios, integrating RISC-V cores with imaging and ML accelerators.[^3]

Mindgrove exemplifies the academia-to-startup pipeline around RISC-V that DIR-V aims to foster.

***

## 5.6 Vervesemi Microelectronics

Vervesemi Microelectronics is among the first companies publicly highlighted as receiving DLI scheme support for RISC-V-based chip development, targeting embedded and IoT markets. Its product roadmap illustrates that India’s incentive schemes are yielding real commercial RISC-V designs, not just academic prototypes.[^3]

***

## 5.7 Karnataka’s RISC-V AI PC Initiative

State-level programs complement central initiatives. The Karnataka government has promoted affordable AI-enabled PCs for education and e-governance, with some pilot projects leveraging RISC-V-based platforms running Linux to reduce per-seat cost and avoid foreign ISA/IP lock-in. These deployments typically feature:[^3]

- RISC-V SoCs with integrated GPUs/NPUs.
- 4G/Wi-Fi connectivity.
- Linux distributions tailored for education, coding, and AI applications.[^3]

While volumes are modest today, they signal that RISC-V is entering end-user-visible products in India.

***

## 5.8 India as a Global RISC-V Talent and Supply Hub

DIR-V’s roadmap explicitly positions India as a global hub for RISC-V IP and SoC design.[^1][^3]

- **Talent:** C2S and aligned programs target training 100,000+ engineers in chip design flows by the mid-2020s, most of them exposed to RISC-V-based flows.[^12]
- **IP and design houses:** Indian companies and design centers of multinationals (Arm, Intel, Apple, Qualcomm, SiFive, etc.) increasingly work on RISC-V hardware and software in India.[^3]
- **Space and defense:** ISRO and DRDO have publicized interest in SHAKTI/VEGA-class cores for space and strategic applications, leveraging the open ISA for autonomy and radiation-hardening work.[^10][^3]

For professors and MTech students in India, this makes RISC-V not just academically elegant but also highly aligned with career and national priorities.

***

# SECTION 6 — RISC-V HARDWARE ECOSYSTEM: BOARDS, CPUs, AND SUPPORT

## 6.1 Hardware Category Definitions

Before listing boards, it is useful to classify them:

- **SBC (Single Board Computer):** Raspberry Pi-style boards with CPU, RAM, storage (SD/eMMC), Ethernet, USB, HDMI and GPIO on a single PCB for hobbyist, education, and light desktop/server tasks (e.g., VisionFive 2, Banana Pi F3).[^7]
- **Development Board:** Typically larger, with more exposed interfaces (PCIe slots, M.2, extra headers, debug connectors) aimed at developers building OS ports, drivers, and SoCs, such as Milk-V Pioneer and SiFive HiFive Premier P550.[^6][^7]
- **Server/Workstation Board:** Full mATX/ATX or server boards with high RAM capacity, ECC support, multiple PCIe slots, and server-oriented form factors (e.g., Milk-V Pioneer mATX board).[^15][^16]
- **Embedded Module (SoM):** System-on-Module cards (e.g., LicheePi 4A modules) designed to be plugged into custom carrier boards, useful for products needing industrial connectors and custom I/O while reusing the compute complex.[^7]

***

## 6.2 Notable RISC-V Boards (2025–2026)

The RISC-V board ecosystem is evolving rapidly; below we summarize some representative high-end boards that are realistic for labs and advanced projects.

| Board | CPU / SoC | Cores/Freq | Max RAM | RAM Type | GPU | PCIe | Availability in India |
| :-- | :-- | :-- | :-- | :-- | :-- | :-- | :-- |
| Milk-V Pioneer | SOPHGO SG2042 | 64 × RV64GC @ ~2.0 GHz | 128 GB ECC | 4× DDR4-3200 ECC | External via x16 slot | x16 + x8 + x4 Gen4 | Mouser India, import from vendors |
| HiFive Premier P550 | ESWIN EIC7700X (P550) | 4 × RV64GBC OoO @ ~1.4 GHz | 32 GB | DDR5 | IMG AXM-8-256 (onboard) | PCIe 3.0 x4 (M.2, slot) | SiFive direct (ships to India) |
| Milk-V Jupiter | SpacemIT M1 | 8 × RV64GC @ ~1.8 GHz | 16 GB | LPDDR4X | IMG BXE-2-32 | PCIe 2.0 x4 | Direct from Milk-V, some resellers |
| Banana Pi BPI-F3 | SpacemIT K1 | 8 × RV64GC @ ~1.6 GHz | 16 GB | LPDDR4 | IMG BXE-2-32 | PCIe 2.x (M.2, mPCIe) | Robu.in, Amazon India, others |
| VisionFive 2 | StarFive JH7110 | 4 × RV64GC @ ~1.5 GHz | 8 GB | LPDDR4 | IMG BXE-4-32 | PCIe 2.0 x1 | Robu.in, Hubtronics.in |
| LicheePi 4A | T-Head TH1520 | 4 × RV64GCV @ ~2.0 GHz | 16 GB | LPDDR4X | IMG BXE-2-32 | PCIe 2.0 (M.2) | Mouser India, AliExpress |

Below we elaborate per board (specs aggregated from vendor docs and community sources).[^16][^15][^7]

### Milk-V Pioneer (SG2042 mATX)

- **Primary use case:** RISC-V server experimentation, kernel/firmware development, high-core-count parallel workloads, discrete GPU experiments.[^15][^16]
- **Linux support:** Mainline Linux patches exist; vendor images typically based on newer 5.x/6.x kernels with RISC-V and SG2042-specific patches.[^17][^18]
- **Connectivity:**
	 - Multiple USB 3.x ports (on rear I/O).
	 - Dual GbE or faster Ethernet (varies by board revision); 10GbE via PCIe NIC.[^15]
	 - HDMI via discrete GPU (board itself does not include an integrated display output; uses GPU card or other PCIe graphics).[^15]
- **Expansion:**
	 - 1× PCIe 4.0 x16 slot (CPU root complex) for GPUs.
	 - Additional x8 and x4 slots; M.2 NVMe support for PCIe SSDs.[^16][^15]
- **Price \& procurement (India):** Roughly ₹1,00,000–₹1,70,000 depending on RAM and vendor; obtainable via Mouser India or import through global distributors.[^16]

### SiFive HiFive Premier P550 (EIC7700X)

- **Primary use case:** High-performance per-core RISC-V development, OS research, DDR5/memory-subsystem experiments, graphics driver work on Imagination AXM GPU.[^8][^6]
- **Linux support:** Vendor images with 5.x/6.x kernels; support for DDR5, PCIe 3.0, and the on-board IMG AXM GPU driver under active development.[^6]
- **Connectivity:**
	 - USB 3.2 Gen1 ports (Type-A/Type-C, depending on reference board).
	 - HDMI 2.0 for 4K60 output via the AXM GPU or display interface.[^8]
	 - GbE Ethernet onboard.[^8]
- **Expansion:**
	 - DDR5 DIMM slots (up to 32 GB).
	 - M.2 slot for NVMe SSD (PCIe 3.0 x4).
	 - Possibly additional PCIe slot for external devices depending on carrier board design.[^6][^8]
- **Price \& procurement (India):** Estimated ₹42,000–₹58,000 including taxes and shipping from SiFive’s global store; shipping to Indian universities is common for research collaborations.[^6][^8]

### Milk-V Jupiter (SpacemIT M1)

- **Primary use case:** Higher-end SBC/mini-PC form factor for desktop Linux, lightweight development, and GPU-accelerated workloads using the IMG BXE GPU.[^7]
- **Linux support:** Vendor distributions with 5.x/6.x kernel, GPU drivers for Vulkan 1.2 and OpenGL ES 3.2.[^7]
- **Connectivity:**
	 - USB 3.x ports, HDMI 2.0, Gigabit Ethernet.
	 - Onboard Wi-Fi/BT modules vary by SKU.
- **Expansion:**
	 - PCIe 2.0 x4 via M.2 or edge connector (depends on revision), enabling NVMe SSD or low-end GPU.[^7]
- **Price \& procurement (India):** Typically imported from Milk-V or international resellers; after customs, effective price may land in the ₹15,000–₹30,000 range depending on RAM and storage options.

### Banana Pi BPI-F3 (SpacemIT K1)

- **Primary use case:** Mid-range RISC-V SBC with strong connectivity (CAN bus, MIPI) for robotics, automotive, and industrial control; also capable of desktop Linux and AI edge tasks via the integrated NPU.[^7]
- **Linux support:** Vendor Debian/Ubuntu-based images with kernel support for CPU, LPDDR4, IMG GPU, and peripherals.[^7]
- **Connectivity:**
	 - Gigabit Ethernet.
	 - USB 3.x, HDMI 2.0 for display.
	 - Optional Wi-Fi 6 / BT via M.2 Key E module.
	 - CAN FD controllers for automotive networks.[^7]
- **Expansion:**
	 - PCIe 2.x lanes exposed via M.2/mPCIe slots.
	 - 40-pin GPIO header with RPi-like pinout.
	 - MIPI CSI-2 for camera, MIPI DSI for display.[^7]
- **Price \& procurement (India):** Commonly available on Robu.in and Amazon India in the ₹7,000–₹14,000 range depending on RAM and storage options.

### VisionFive 2 (StarFive JH7110)

- **Primary use case:** Entry-level RISC-V Linux SBC for experimentation, teaching OS/driver development, and hobbyist projects with GPIO.[^7]
- **Linux support:** Official images based on Debian/Fedora with mainline-leaning kernels; GPU drivers for IMG BXE-4-32 support Vulkan 1.2 and OpenGL ES 3.2 on recent Mesa stacks.[^7]
- **Connectivity:**
	 - USB 3.0 ports.
	 - HDMI 2.0 4K@60 display.
	 - Gigabit Ethernet.
	 - Optional Wi-Fi via M.2 or USB dongles.
- **Expansion:**
	 - PCIe 2.0 x1 slot for NVMe SSD or low-speed PCIe devices.
	 - 40-pin GPIO header with Raspberry Pi-compatible pinout.
	 - MIPI CSI-2 and DSI connectors for camera and display.
	 - Onboard eMMC 5.1 on some variants.[^7]
- **Price \& procurement (India):** 4 GB models around ₹5,500–₹8,000, 8 GB models around ₹8,000–₹11,000 from Robu.in and Hubtronics.in.[^7]

### LicheePi 4A (TH1520)

- **Primary use case:** High-clock SBC/SoM with T-Head C910 cores and vector extension (pre-RVV1.0) aimed at desktop and AI edge workloads.[^7]
- **Linux support:** Vendor distributions with TH1520-specific kernel and drivers; support for IMG BXE-2-32 GPU and hardware video decode.[^7]
- **Connectivity:**
	 - USB, HDMI, Ethernet on reference carrier boards.
	 - Wi-Fi and BT via on-module radios.
- **Expansion:**
	 - M.2 for NVMe.
	 - GPIO and other headers depending on carrier.
- **Price \& procurement (India):** Via Mouser India and Chinese marketplaces (AliExpress); typical landed cost in the ₹12,000–₹18,000 range.

***

## 6.3 Most Powerful RISC-V CPUs/SoCs (2025–2026 Snapshot)

ISA-level research often focuses on core microarchitectures. Representative high-performance RISC-V SoCs include:[^8][^6][^7]

- **SOPHGO SG2042 (C920-based):**
	 - 64 out-of-order RV64GC cores with vector support (earlier RVV 0.7x drafts) on TSMC 6 nm.[^19][^15]
	 - 4-channel DDR4-3200 ECC up to 256 GB; multiple PCIe 4.0 lanes.
	 - Aimed at servers, edge compute, and AI acceleration when paired with GPUs/NPUs.
- **SiFive P870 (core IP, not yet widely sampled on dev boards):**
	 - Part of SiFive Performance P800 family; marketing claims >18 SPECint2006/GHz and a 6-wide out-of-order pipeline at modern process nodes, targeting high-end application processors.[^9][^8]
- **SiFive P550 (EIC7700X SoC):**
	 - Quad-core cluster of P550 cores running around 1.4–1.8 GHz; SPECint2006 around 8.65/GHz according to SiFive, competitive with Cortex-A75-class ARM cores.[^6][^8]
- **SpacemIT X100 series:**
	 - 8-core RV64GCV with RVV 1.0 vectors and NPUs delivering multiple TOPS of AI inference; targeted at edge AI and automotive.[^7]
- **UltraRISC UR-DP1000 (announced IP):**
	 - Multicore out-of-order RV64GCBH core cluster targeting server and network workloads with hypervisor and vector support; third-party estimates around mid-teens SPECint/GHz.[^7]
- **T-Head XuanTie C910 (TH1520):**
	 - 4-wide out-of-order core implementing RV64GCV; widely used in TH1520 SoCs (LicheePi 4A) and showing SPECint2017/GHz in the midrange compared to ARM v8 cores.[^7]
- **Esperanto ET-SoC-1:**
	 - 1,088 “ET-Minion” RISC-V cores plus a few big cores on TSMC 7 nm; designed for massively parallel AI inference with tens of TOPS aggregate throughput.[^7]

Academic labs can prototype similar microarchitectures on FPGA using BOOM and Rocket in Chipyard, then reason about performance using these commercial cores as real-world reference points.

***

## 6.4 DDR Memory Support on RISC-V Boards

Memory subsystem research is a rich area for students; RISC-V boards now span DDR4, LPDDR4/4X, and DDR5.

- **DDR5:** The HiFive Premier P550 board is among the first commercially available RISC-V dev boards with DDR5 memory, exposing modern memory-controller behaviors and high bandwidth at comparatively low power per bit.[^8][^6]
- **DDR4 ECC:** Milk-V Pioneer’s SG2042 platform offers 4-channel DDR4-3200 ECC, supporting up to around 128 GB or more with server-class DIMMs, enabling server-grade memory experiments (NUMA, paging, large in-memory datasets).[^16][^15]
- **LPDDR4/LPDDR4X:** Banana Pi F3, VisionFive 2, Milk-V Jupiter, and LicheePi 4A all rely on low-power LPDDR4/4X, mirroring smartphone SoC designs and making them good targets for mobile-style power/performance studies.[^7]
- **LPDDR5:** Next-generation SoCs like some upcoming SpacemIT X-series and future SiFive reference platforms are expected to add LPDDR5, but public dev boards are still emerging; this is a near-future area for research.[^8][^7]

For professors, DDR5-capable RISC-V platforms represent a frontier where ISA, memory subsystem, and OS research can intersect.

***

## 6.5 GPU Support on RISC-V Platforms

Graphics and GPGPU are key for desktop usability and AI workloads.

### A. Discrete GPUs via PCIe (Milk-V Pioneer)

- **PCIe x16 Gen4 slot:** Allows standard desktop GPUs; community reports confirm AMD Radeon RX 580, RX 6600 and Intel Arc A380 working under Linux using open-source amdgpu and i915 drivers, respectively.[^18][^17]
- **Display:** Once drivers are configured, the RISC-V system can render full Linux desktops using the discrete GPU with hardware acceleration.
- **GPU compute:** OpenCL 3.0 via Mesa’s Rusticl layer and Vulkan via RADV (for AMD) provide GPGPU capabilities; ROCm-style stacks are more limited due to platform support issues.[^17][^18]
- **NVIDIA support:** Proprietary NVIDIA drivers are tightly coupled to x86/ARM ABIs and kernel interfaces; there is no general support for NVIDIA GPUs on RISC-V yet, and open-source Nouveau for RISC-V is immature.[^17]

This makes Milk-V Pioneer an excellent research platform for studying GPU integration, driver portability, and heterogeneous compute on RISC-V.

### B. Integrated GPUs (Imagination Technologies)

Several RISC-V SoCs embed IMG GPUs:

- **IMG BXE-2-32:** Found in SpacemIT K1 (BPI-F3) and M1 (Jupiter); supports Vulkan 1.2, OpenGL ES 3.2, and OpenCL 3.0 through Imagination’s drivers and Mesa integration.[^7]
- **IMG BXE-4-32:** Present in StarFive JH7110 (VisionFive 2), similarly supporting Vulkan/OpenGL ES/OpenCL.[^7]
- **IMG AXM-8-256:** Integrated into ESWIN EIC7700X (HiFive P550), featuring 8 ALU clusters; driver support and tooling are under active development.[^8]

These GPUs enable:

- Hardware-accelerated desktops (Wayland/X11 compositors).
- Video playback using hardware decode.
- Light 3D graphics and basic ML/CV workloads via OpenCL/Vulkan compute.[^7]

### C. AI/ML-Specific Accelerators

- **SpacemIT K1/X100 SoCs:** Include on-die NPUs delivering multiple TOPS of INT8/INT4 operations for AI inference; RISC-V cores orchestrate workloads.[^7]
- **Esperanto ET-SoC-1:** Eschews GPUs entirely in favor of thousands of RISC-V cores with vector-like extensions, acting as a “software-defined GPU” for AI inference.[^7]

These platforms provide testbeds for heterogeneous programming models, from CPU+GPU to CPU+NPU to many-core RISC-V fabrics.

***

## 6.6 Peripheral / Interface Support Overview

A condensed mapping from interfaces to boards:

| Interface | Representative Board | Notes |
| :-- | :-- | :-- |
| NVMe SSD (PCIe 4.0) | Milk-V Pioneer | NVMe via PCIe 4.0 x4/x8 or x16 slots; good for high-speed storage |
| NVMe SSD (PCIe 3.0) | HiFive Premier P550 | M.2 Key M slot connected via PCIe 3.0 x4 |
| USB 3.2 Gen1 | HiFive P550, BPI-F3 | 5 Gbps USB Type-A/C ports |
| HDMI 2.0 (4K@60) | HiFive P550, VisionFive 2 | Desktop and multimedia output |
| 10 GbE Ethernet | Milk-V Pioneer | Via PCIe NIC (e.g., Intel X540) |
| 1 GbE Ethernet | All above SBCs | Standard on-board PHYs |
| Wi-Fi 6 / BT 5 | BPI-F3 | Via M.2 Key E Wi-Fi module |
| MIPI CSI-2 Camera | BPI-F3, VisionFive 2 | 2-lane/4-lane camera interfaces |
| MIPI DSI Display | BPI-F3, VisionFive 2 | 4-lane displays for embedded/industrial UI |
| 40-pin GPIO | VisionFive 2, BPI-F3 | Raspberry Pi-compatible pin mapping |
| eMMC 5.1 | BPI-F3, VisionFive 2 | On-board flash for OS and data |
| SATA III | Milk-V Pioneer | Through PCIe SATA controllers |
| CAN Bus | SpacemIT K1 (BPI-F3) | Dual CAN FD for automotive and robotics |
| Audio I2S | VisionFive 2, BPI-F3 | On-board codec + I2S headers |
| Video Decode | Most SoCs | H.264/H.265 hardware decode (4K on VisionFive 2, TH1520, etc.) |

For lab work, this breadth of interfaces means RISC-V boards can support everything from OS kernel hacking to robotics and automotive projects.

***

## 6.7 Buying RISC-V Hardware in India — Practical Guide

Indicative availability and prices (which fluctuate with exchange rates and stock):

| Board | Indian Retailer | Approx Price (INR) | Notes |
| :-- | :-- | :-- | :-- |
| VisionFive 2 (4 GB) | Robu.in, Hubtronics | ₹5,500–₹8,000 | Good entry-level Linux SBC |
| VisionFive 2 (8 GB) | Hubtronics.in | ₹8,000–₹11,000 | Better for heavier desktop and compilation workloads |
| Banana Pi BPI-F3 | Robu.in, Amazon.in | ₹7,000–₹14,000 | Edge/robotics with CAN and MIPI |
| LicheePi 4A | Mouser India | ₹12,000–₹18,000 | High-clock, TH1520-based SoM/SBC |
| Milk-V Pioneer | Mouser India, import | ₹1,00,000–₹1,70,000 | 64-core server-class board |
| HiFive Premier P550 | SiFive (international) | ~₹42,000–₹58,000 | DDR5, high per-core performance |
| C-DAC ARIES (VEGA) | C-DAC direct | Institutional pricing | Contact C-DAC; ideal for government-funded labs |

For your context in Andhra Pradesh, shipping from Robu.in, Hubtronics.in, or Mouser India is straightforward; for SiFive and Milk-V, factor in customs and lead times.

---

RISC-V adds a rich ecosystem of profiles, tools, open cores, and system-level features (PMP, hypervisor, debug) that make it ideal for advanced MTech-level work in architecture, OS, and verification, especially in India’s RISC-V-focused context.[^1][^2]

# SECTION 7 — CRITICAL TECHNICAL KNOWLEDGE FOR MTECH/PROFESSORS

## 7.1 ISA Naming Convention — Decoding Strings

RISC-V uses textual ISA strings to describe exactly which base ISA and extensions a binary targets.[^3][^2]

- **General form:** `RV[XLEN][base][extensions…]`
	 - `RV32I`, `RV64I`, `RV32E`, `RV64G`, etc.
	 - Extensions are appended in canonical order (though compilers like LLVM accept relaxed ordering).[^3]
- **Examples:**
	 - `RV32IMC`: 32-bit base integer, M (multiply/divide), C (compressed).
	 - `RV64GC`: 64-bit, with G = IMAFD plus typically Zicsr, Zifencei; C for compressed; a common Linux SBC configuration.[^4][^2]
	 - `RV64GCBHX`: 64-bit, G + C + B (bit-manip) + H (hypervisor) + X* (vendor custom extension set; X prefix indicates non-standard).[^2]

Profiles introduce another layer:

- **RVA22U64 / RVA22S64:** Application Profile 2022; user-mode and supervisor-mode, 64-bit. These describe a minimum required set of extensions for application processors.[^5]
- **RVA23U64 / RVA23S64:** Updated 2023 profiles, adding more mandatory features such as vector and advanced floating-point in some cases.[^1]

### Discovering ISA at Runtime — `misa`

At runtime, software can inspect the `misa` CSR:

- Upper bits encode XLEN.
- Lower bits encode presence of each standard extension letter (A–Z), where bit 0 corresponds to ‘A’, bit 1 to ‘B’, etc.[^2]

Typical pattern in OS or runtime:

- Read `misa`.
- Check whether bits for ‘F’, ‘D’, ‘V’, ‘K’, etc. are set before enabling those code paths.
- Combine this with CSR queries for features not directly encoded in `misa` (e.g., PMP entries, hypervisor support).[^2]

***

## 7.2 RISC-V Profiles System

Profiles define _bundles_ of extensions that software can target, analogous to “ARMv8.2-A with SVE2” style feature baselines.[^5][^1]

### Motivation

- Avoids combinatorial explosion of ISA configurations.
- Gives OS vendors (Linux distributions, Android) and language runtimes a stable baseline to compile against.
- Allows hardware vendors some freedom beyond the baseline, while ensuring common ground.[^1]

### RVA22U64 / RVA22S64

From the RISC-V profiles repo (RVA22/RVA23):[^5][^1]

- **Mandatory base:** RV64I, little-endian.
- **Mandatory extensions in RVA22U64 (user-profile):**
	 - M (multiply/divide).
	 - A (atomics).
	 - F and D (singleand double-precision FP).
	 - C (compressed).
	 - Zicsr (CSR instructions).
	 - Zifencei (instruction-fetch fence).
	 - Zicntr (basic counters: `mcycle`, `minstret`).
	 - Bit-manip sub-extensions: Zba, Zbb, Zbs.
	 - Small-code and hint extensions: Zic64b, Zcmp, Zcb, Zihintpause.
	 - Paging and virtual memory helpers: Svnapot, Svpbmt, Svinval for RVA22S64.[^1][^5]

RVA22S64 adds supervisor-mode features, including SV39 or SV48 paging, and the above virtual-memory extensions.[^1]

### RVA23 Additions

RVA23 profiles (RVA23U64/RVA23S64) build on RVA22 and add:[^1]

- Vector base:
	 - V (RVV 1.0 vector extension).
	 - Zvfhmin (half-precision FP minimal vector support).
- Advanced floating point and atomics:
	 - Zfa (additional FP instructions).
	 - Zawrs (wait-on-reservation-set hint).
- Control-flow and security:
	 - Zicond (conditional instructions).
- Additional performance monitoring requirements, and further clarifications on required privileged features.[^1]

### Why Profiles Matter for You

- **For OS/kernel courses:** Profiles define what you can assume about a “standard RISC-V laptop/server core” across vendors.
- **For compiler courses:** You can target `-march=rv64gc -mprofile=rva22u64` (when compilers add explicit profile support) instead of enumerating dozens of flags.
- **For research:** Profiles make it easier to reason about portability of low-level libraries and JITs.

***

## 7.3 Full Toolchain Stack

A practical RISC-V curriculum should expose students to the full software and hardware toolchain.

### Compilers and Binutils

- **GCC (riscv-gnu-toolchain):** Official RISC-V GCC toolchain supports bare-metal (`riscv64-unknown-elf-gcc`) and Linux (`riscv64-unknown-linux-gnu-gcc`) targets, hosted on GitHub and integrated into mainline GCC.[^3]
- **LLVM/Clang:** Clang provides a `riscv32`/`riscv64` backend. Usage: `clang --target=riscv64-unknown-linux-gnu` with appropriate `-march`/`-mabi` flags.[^3]
- **Binutils:** GNU assembler `as` and linker `ld` fully support RISC-V; `objdump` and `objcopy` can disassemble ELF binaries, inspect CSRs, and dump sections.[^3]

### Debugging and Simulation

- **Spike:** The official RISC-V ISA reference simulator; faithfully models RV32/RV64 ISA plus some privileged features; often used as a golden reference.[^2]
- **QEMU:** Provides user-mode (`qemu-riscv64`) and system-mode (`qemu-system-riscv64`) emulation for running Linux and other OSes under RISC-V.[^2]
- **GDB:** `riscv64-unknown-elf-gdb` or `riscv64-linux-gnu-gdb` integrate with Spike, QEMU, and hardware via OpenOCD for debugging.[^3]

### Hardware Debug

- **OpenOCD:** Supports the RISC-V Debug Spec (0.13.x). Used in combination with JTAG adapters (FTDI, Olimex, etc.) to debug RISC-V boards via the debug module (DM) and debug transport module (DTM).[^6]
- **RISC-V Debug Spec 0.13.x:** Defines external debug mechanisms:
	 - DTM: JTAG-based interface.
	 - DM: On-chip debug module accessible over Debug Module Interface (DMI).
	 - Run-control, register and memory access, breakpoints, and triggers.[^7][^8]

### RTL and SoC Frameworks

- **Verilator:** Fast cycle-accurate simulation of Verilog/SystemVerilog; widely used with open RISC-V cores (Rocket, BOOM, PicoRV32).[^2]
- **Chipyard:** A Chisel-based SoC generator from Berkeley that integrates Rocket, BOOM, CVA6, and infrastructure for caches, interconnects, and peripheral buses.[^2]
- **FireSim:** FPGA-accelerated simulation platform for RISC-V SoCs targeting AWS F1 instances; useful for architectural exploration.[^2]

When designing an MTech lab, combining Spike/QEMU (ISA-level) with Verilator/Chipyard (RTL-level) gives students a full vertical view from ISA to hardware.

***

## 7.4 ABI and Calling Convention

The standard Linux RISC-V ABI is LP64D for 64-bit with double-precision FP.[^9][^2]

Key aspects:

- **Data model:** `long` and pointers are 64-bit; `int` is 32-bit; `double` is 64-bit in FP registers.[^9]
- **Argument passing:**
	 - Integer and pointer arguments: first 8 go in `a0`–`a7` (x10–x17).[^9]
	 - Additional arguments spill to the stack.
	 - FP arguments (when using hard-float ABI) use `f0`–`f7` etc. according to the ABI.[^9]
- **Return values:**
	 - Primary result in `a0`, second result in `a1`. 128-bit returns can be split across `a0`/`a1`.[^9]
- **Callee-saved registers:**
	 - `s0`–`s11` (x8–x9, x18–x27), `sp` (x2), and frame pointer (when used) must be preserved across calls.[^9]
- **Caller-saved registers:**
	 - Temporaries `t0`–`t6` and argument registers `a0`–`a7` can be clobbered by called functions.[^9]
- **Stack pointer alignment:** `sp` must be 16-byte aligned at function entry, to support vector instructions or FP spills with alignment requirements.[^9]
- **Special registers:**
	 - Global pointer `gp` (x3): used to access small-data sections (`.sdata`) efficiently.
	 - Thread pointer `tp` (x4): used by TLS (Thread-Local Storage) mechanisms, similar to ARM’s TPIDRx and x86’s FS/GS bases.[^9]

Understanding this ABI is critical for writing assembly, compiler backends, and calling into RISC-V from other languages (e.g., Rust, Go).

***

## 7.5 RISC-V in AI and Machine Learning

RISC-V is increasingly used in AI accelerators and ML-centric SoCs.

- **RVV 1.0 for ML:** Vector extension supports `int8`, `int16`, `fp16`, `bf16`, and even smaller integer widths via data-type encodings and vector length configuration, enabling SIMD-like acceleration of convolutions and GEMMs.[^2]
- **Vendor matrix extensions:**
	 - T-Head has proprietary matrix-multiply instructions (XTHEADMATRIX) in C910/C920 cores, though they are non-standard and accessed via custom intrinsics.[^10]
- **Standard matrix extensions (in progress):**
	 - RISC-V Intl’s AI/ML and Vector TSCs are working on standard matrix extensions (often referred to informally as RV-MATRIX/IME), aiming to define portable, high-throughput matrix operations akin to Tensor Cores or AMX, but as of 2026 these are still in draft.[^2]
- **Real-world chips:**
	 - Esperanto ET-SoC-1: 1,088 ET-Minion cores with vector-like extensions for large-scale inference.[^10]
	 - SpacemIT K1/X100: RISC-V cores with NPUs delivering ~2 TOPS or more at the edge.[^10]
	 - Future SiFive P670/P870 SoCs: Marketing material and roadmaps emphasize AI workloads, with INT8/FP16 acceleration and vector extensions as key selling points.[^11]

Compiler stacks like TVM, ONNX Runtime, and microTVM are gaining RISC-V backends, and several research compilers target RVV directly.[^2]

***

## 7.6 Open-Source RTL Cores for Research

Open RTL cores are critical for teaching microarchitecture and for research in verification and architecture.

- **Rocket Core (Berkeley):** 5-stage in-order RV64GC core in Chisel, widely used as a “reference implementation” and integrated into Chipyard; boots Linux and is synthesizable for ASIC/FPGA.[^2]
- **BOOM (Berkeley Out-of-Order Machine):** Superscalar out-of-order RV64GC core, also in Chisel, supporting up to 4–6-wide issue; suitable for studying branch prediction, reorder buffers, and speculation.[^2]
- **CVA6 / Ariane (ETH Zurich):** RV64GC capable of running Linux; written in SystemVerilog; widely used in research and some industrial projects.[^2]
- **PicoRV32:** Extremely small RV32IMC core optimized for FPGAs, often fitting in <1000 LUTs on small devices; ideal for teaching and simple SoC projects.[^2]
- **VexRiscv:** Highly configurable RV32 core in SpinalHDL (Scala-based), with plugins for caches, MMUs, and debug; heavily used in LiteX SoCs and FPGA communities.[^2]
- **SHAKTI (IIT Madras):** Family of open-source cores (E/C/I/M/S/H) targeted specifically at Indian academic and commercial adopters, with Verilog/Chisel sources and documentation.[^12][^13]
- **Ibex (lowRISC/Google):** Small RV32IMC core used in OpenTitan (root-of-trust project); formally verified to a high degree, providing an example of industrial-strength verification practice.[^2]

For MTech theses, these cores allow modifications of pipelines, caches, branch predictors, and debug infrastructure without license barriers.

***

## 7.7 FPGA Prototyping Recommendations

FPGA prototyping lets students move from simulation to real timing, I/O, and debug experiences.

- **Budget (students/teaching labs):**
	 - **Digilent Arty A7-35T**: Artix-7 FPGA; can host PicoRV32 or VexRiscv-based SoCs; runs bare-metal firmware and simple RTOSes.[^2]
- **Mid-range (MTech labs):**
	 - **Digilent Nexys A7-100T**: Larger Artix-7 device; suitable for Rocket SoC or small multi-core systems; some groups run Linux on Rocket-based SoCs on this board.[^2]
- **High-end (research groups):**
	 - **Xilinx ZCU104 / ZCU106**: Zynq UltraScale+ MPSoC boards; can host BOOM, CVA6, or complex multi-core Rocket systems; helpful for co-simulation with on-chip ARM cores.[^2]
- **Cloud FPGA:**
	 - **AWS F1 instances**: Run FireSim/Chipyard RISC-V SoCs on Xilinx VU9P FPGAs, enabling large-scale experiments (many-core SoCs, data-center-scale simulation).[^2]

Recommended tool stack:

- Yosys/nextpnr for open-source synthesis where supported.
- Vivado for Xilinx-based flows.
- Verilator + GTKWave for pre-FPGA simulation.
- Chipyard/FireSim for parameterized RISC-V SoC generation.[^2]

***

## 7.8 Physical Memory Protection (PMP)

PMP is the RISC-V mechanism for controlling memory access rights at the physical address level.[^2]

- **Purpose:** Allow M-mode firmware to restrict memory regions accessible to S-mode and U-mode, enforcing isolation between OS, enclaves, and user processes even in the absence of a full MMU.[^14][^2]
- **Entries:** Typically up to 16 PMP entries (implementation-defined), each describing a region plus permissions.[^2]
- **CSRs:**
	 - `pmpcfgN`: Configuration bytes (lock bit, R/W/X permissions, address-matching mode).
	 - `pmpaddrN`: Encodes region addresses for TOR (top-of-range), NA4 (4-byte), or NAPOT (naturally aligned power-of-two) modes.[^2]
- **Smepmp extension:** Enhances PMP for security:
	 - Allows PMP to also constrain M-mode or adjust default executable/readable permissions.
	 - Ratified as “PMP enhancements for memory access and execution prevention (Smepmp)” and used by TEEs like Keystone to harden enclave isolation.[^15][^16]

Research shows practical attacks exploiting PMP misconfiguration and aliasing, motivating advanced work in verification and secure firmware design.[^17]

***

## 7.9 Hypervisor Extension (H-Extension) In Depth

The H-extension adds architectural support for efficient virtualization.[^2]

- **New modes:**
	 - **HS-mode (Hypervisor Supervisor):** Host OS/hypervisor mode.
	 - **VS-mode/VU-mode:** Virtual supervisor/user modes for guest kernels and processes.[^2]
- **Two-stage translation:**
	 - Guest virtual address (GVA) → guest physical address (GPA) via `vsatp` and guest page tables.
	 - GPA → host physical address (HPA) via `hgatp` and hypervisor-controlled second-stage page tables.[^2]
- **New CSRs:**
	 - `hstatus`, `hedeleg`, `hideleg`, `hvip`, `hip`, `hie` for virtualization-specific status and interrupt delegation.
	 - `hgatp` for second-level address translation.
	 - `htval`, `htinst` for additional trap information.[^2]

With H-extension:

- Linux can run as a host hypervisor (KVM-RISC-V), supporting guest operating systems in VS/VU modes.
- QEMU and other VMMs can rely on hardware second-stage translation to reduce virtualization overhead compared to pure software MMU emulation.[^2]

Hypervisor support is a necessary building block for RISC-V servers in cloud/data-center environments.

***

## 7.10 RISC-V Debug Specification

The external debug spec (v0.13.x) standardizes how debuggers interact with RISC-V harts.[^8][^7]

Key components:

- **Debug Transport Module (DTM):**
	 - Usually JTAG-based (IEEE 1149.1).
	 - Exposes JTAG instructions such as `dtmcs` (control/status) and `dmi` (Debug Module Interface access) registers.[^8]
- **Debug Module (DM):**
	 - On-chip block that interfaces with one or more harts via DMI.
	 - Provides run-control (halt, resume, step), register access, memory access (System Bus Access, SBA), and triggers/breakpoints.[^7]
- **Harts:** Each hardware thread (core/SMT context) is a “hart”; the DM can manage many harts via addressing.[^7]
- **Triggers:**
	 - `tselect`, `tdata1`, `tdata2`, `tdata3` CSRs allow hardware breakpoints and watchpoints.
	 - Up to 16 or more triggers, implementation dependent.[^7]
- **Execution-based debug:** One method uses a program buffer in the DM to inject instructions into the core, minimizing core modifications; widely adopted in open-source debug systems.[^18]

OpenOCD, GDB, and vendor-specific tools build on this spec, making hardware debug of RISC-V SoCs convergent across vendors.[^6]

***

# SECTION 8 — WHY RISC-V IS UNIQUELY POSITIONED: THE COMPLETE ARGUMENT

## 8.1 No Legacy Constraint

RISC-V was designed in the 2010s, long after the smartphone, GPU, and multicore revolutions. This timing allowed its designers to:[^2]

- Avoid 16-bit/8-bit historical baggage (no real mode, no segmented memory, no x87-like cruft).
- Avoid mixed 16/32-bit base ISAs like ARM’s old ARM/Thumb dichotomy; compressed C is cleanly additive.[^2]
- Use instruction encodings purpose-built for deep pipelining, superscalar issue, and vector extensions.[^19][^2]

In contrast, x86-64 carries decades of legacy modes and instructions, and ARM must carefully evolve around older AArch32 and Thumb ecosystems.

***

## 8.2 Formal Specification and Verification

RISC-V is the first widely adopted ISA with a full, public formal model.

- **Sail model:** The official Sail specification describes RISC-V semantics in an executable, theorem-prover-friendly language and is hosted on GitHub (riscv/sail-riscv).[^2]
- **Uses:**
	 - Serving as a reference interpreter.
	 - Generating test suites and fuzzers.
	 - Enabling formal ISA-level proofs (e.g., that an RTL implementation refines the Sail model).
- **Industry impact:** CPU vendors and verification tool providers can align properties and proofs around a single machine-readable specification, reducing ambiguities common in legacy ISAs.[^2]

For academia, this creates an ideal environment for courses and research in formal methods, program logics, and hardware verification.

***

## 8.3 Composable Modular Design

RISC-V was built to be _composable_ from day one.

- **Pick only what you need:**
	 - Tiny MCUs: RV32E + M + C (16 registers, no virtual memory) for extreme area and power savings.
	 - General embedded/Linux SBCs: RV64GC with PMP or simple MMU.
	 - Servers and datacenter: RV64GC + A + H + V + B + K, plus advanced virtual memory and profiles (RVA23S64).[^1][^2]
- **Custom opcode space:**
	 - Major opcodes 0x0B, 0x2B, 0x5B, 0x7B (and others) are reserved for custom extensions; standardization guarantees they will never be used by ratified instructions.[^2]
	 - Vendors can add domain-specific instructions (matrix multiply, DSP, crypto) into these slots without conflicting with future standard extensions.
- __Extensible “Z_” naming:_ming:_* Fine-grained sub-extensions (Zicsr, Zifencei, Zba, Zbb, Zbs, Zicond, Zfa, etc.) allow software and hardware to negotiate capabilities explicitly.[^4][^2]

This modularity makes RISC-V more future-proof and adaptable than monolithic ISAs where extensions are bolted on piecemeal.

***

## 8.4 Geopolitical and Strategic Significance

Because the ISA specification itself is open and not controlled by a single country or corporation, RISC-V has outsized geopolitical importance.

- **Export control:** Implementing the RISC-V ISA does not inherently require US or UK government permission, unlike licensing ARM cores or certain x86 IP.[^20][^21]
- **National programs:**
	 - India’s DIR-V (with SHAKTI, VEGA, and commercial chips) explicitly aims at strategic autonomy for processors.[^22][^12]
	 - China has aggressive RISC-V initiatives, such as Alibaba T-Head’s C910/C920 and SOPHGO’s SG2042, for domestic cloud, AI, and edge products.[^21][^4]
	 - European projects (SiPearl, European Processor Initiative) and others examine RISC-V for future HPC and automotive platforms.[^4]
- **ISA independence:** Even if a particular vendor disappears, the ISA itself remains implementable; another company or open community can continue development.

For India specifically, this decouples core national infrastructure (defense, space, telecom, digital identity) from a single foreign IP provider.

***

## 8.5 Academic-to-Production Continuity

A powerful aspect of RISC-V is that the same ISA spans academic cores and cutting-edge commercial CPUs.

- **Students learn one ISA:** From RV32E on a small FPGA board, to RV64GC on VisionFive 2, to massively parallel RISC-V systems like SG2042 servers or Esperanto’s AI chips, all share the same core ISA semantics.[^10][^2]
- **Research impact:** Work done on open cores (Rocket, BOOM, SHAKTI, CVA6) can be transferred to industrial settings where companies use proprietary implementations but the same ISA.[^12][^2]
- **Longevity:** RISC-V’s rapid adoption suggests that expertise gained today will remain relevant for decades—unlike some earlier academic ISAs (DLX, SPARC variants) that never gained broad industrial use.[^21][^4]

This continuity makes RISC-V uniquely attractive for designing long-lived curricula and research programs.

***

## 8.6 Transparency and Community Governance

RISC-V International operates with a level of openness unusual for ISA bodies.[^21][^2]

- **Open repositories:** ISA manuals, profiles, and many extension drafts are maintained on public GitHub repos (riscv/riscv-isa-manual, riscv/riscv-profiles, riscv/riscv-tee, etc.).[^5][^1][^2]
- **Working groups:** Technical Working Groups (TWGs) exist for Vector, Crypto, Debug, Hypervisor, Profiles, Security, AI/ML, and more; organizations and individuals can join and contribute.
- **Ratification process:** Extensions progress from draft to frozen to ratified via transparent procedures; profiles are similarly ratified and versioned.[^5]
- **Participation:** Universities (including IIT Madras and many global campuses), startups, and large companies all participate in specification work, ensuring a diversity of viewpoints and use cases.[^21]

For professors and students, this means:

- You can read and critique the same docs vendors use.
- You can contribute to the ISA’s future (e.g., proposing academic AI extensions).
- You can use RISC-V as a living example of open standards in engineering policy and governance.

***

# QUICK REFERENCE CARD

## Base ISA Naming

| Symbol | Meaning |
| :-- | :-- |
| RV32I | 32-bit base integer ISA |
| RV64I | 64-bit base integer ISA |
| RV32E | 32-bit embedded base (16 integer registers) |
| G | Shorthand for IMAFD (+ Zicsr + Zifencei) |
| C | Compressed 16-bit instruction subset |
| V | Vector extension (RVV 1.0) |
| H | Hypervisor extension |
| B | Bit-manip (Zba + Zbb + Zbs, etc.) |

## Register ABI Quick Reference (Integer)

| Register | ABI Name | Role | Saved by |
| :-- | :-- | :-- | :-- |
| x0 | zero | Constant 0 | N/A |
| x1 | ra | Return address | Caller |
| x2 | sp | Stack pointer | Callee |
| x3 | gp | Global pointer | N/A |
| x4 | tp | Thread pointer | N/A |
| x5–x7 | t0–t2 | Temporaries | Caller |
| x8–x9 | s0–s1 | Saved / frame pointer | Callee |
| x10–x17 | a0–a7 | Function args / returns | Caller |
| x18–x27 | s2–s11 | Saved registers | Callee |
| x28–x31 | t3–t6 | Temporaries | Caller |

## Privilege Modes

| Mode | Level | Purpose |
| :-- | :-- | :-- |
| M | 3 | Machine mode: firmware, boot, low-level |
| S | 1 | Supervisor: OS kernel, hypervisor host |
| U | 0 | User mode: applications |
| HS | H-ext | Host supervisor (hypervisor) |
| VS | H-ext | Guest supervisor (guest OS) |
| VU | H-ext | Guest user (guest user-space) |

## Top RISC-V Boards at a Glance (India-Focused)

| Board | Best For | Typical India Price (approx.) |
| :-- | :-- | :-- |
| VisionFive 2 | Entry Linux, GPIO/teaching | ₹5,500–₹11,000 |
| Banana Pi BPI-F3 | Desktop Linux, robotics, CAN, AI edge | ₹7,000–₹14,000 |
| LicheePi 4A | High-clock desktop/SoM | ₹12,000–₹18,000 |
| HiFive Premier P550 | High per-core perf, DDR5 research | ~₹42,000–₹58,000 |
| Milk-V Pioneer | Server workloads, discrete GPU | ₹1,00,000–₹1,70,000 |

These references should serve as a quick desk-side card when designing labs, lectures, or research projects around RISC-V.
