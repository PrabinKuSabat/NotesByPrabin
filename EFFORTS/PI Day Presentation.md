RISC-V is an open, modular instruction set architecture (ISA) designed as a clean, modern RISC ISA that scales from tiny microcontrollers to large servers, and it is now a central pillar of India’s semiconductor strategy. This chapter builds a rigorous foundation in the ISA itself, then connects it to hardware, ARM comparisons, and the Indian ecosystem.riscv+2

---

# SECTION 1 — WHAT IS RISC-V? FOUNDATIONS OF THE ISA

# 1.1 What is an Instruction Set Architecture (ISA)?

An Instruction Set Architecture (ISA) is the abstract contract between software and processor hardware: it defines the instructions, registers, memory model, and visible state that compiled binaries can rely on. Compilers, operating systems, and applications target the ISA, not any particular microarchitecture, so any CPU that correctly implements the ISA can run the same binaries (modulo OS/ABI differences).lists.riscv+1

Key roles of an ISA:

- **Portability:** A program compiled for RV64GC can run on any conforming RV64GC processor implementation (Rocket, SiFive, SHAKTI, etc.) without recompilation.riscv+1
- **Toolchain and ecosystem focus:** Compilers (GCC/LLVM), assemblers, linkers, debuggers, and profilers all target the ISA specification; this amortizes effort across many hardware designs.lists.riscv+1
- **Binary compatibility and longevity:** An ISA defines a long-lived binary interface; x86-64 binaries from 2005 run on 2026 CPUs because Intel/AMD preserved the ISA contract.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Performance envelope:** While an ISA doesn’t fix microarchitectural details like pipeline depth or cache size, it strongly influences achievable IPC, pipeline complexity, out-of-order execution design, and energy efficiency.riscv+1

In practice, the ISA specification is written text plus formal models that define, for each instruction, how architectural state (registers, memory, CSRs) changes step by step.courses.grainger.illinois+1

---

# 1.2 RISC Philosophy in Context

RISC (Reduced Instruction Set Computing) emerged in the 1980s from research at UC Berkeley and Stanford as a reaction against increasingly complex CISC ISAs such as VAX. Key empirical findings of that era:courses.grainger.illinois+1

- Most compiled code uses relatively simple operations (loads/stores, adds, branches) more frequently than complex instructions.
- Simple, fixed-length instructions are easier to pipeline and can yield higher clock rates and IPC.
- A large register file reduces memory traffic and simplifies compiler optimization.[[courses.grainger.illinois](https://courses.grainger.illinois.edu/ece391/sp2025/docs/unpriv-isa-20240411.pdf)]​

Canonical RISC principles that RISC-V follows:

- **Load–store architecture:** Only explicit load and store instructions access memory; arithmetic/logical operations work only on registers (no “add memory, register” instruction).riscv+1
- **Fixed-length base encodings:** The base instructions are 32 bits wide (with an optional compressed 16-bit subset) which simplifies instruction fetch, alignment, and decode.courses.grainger.illinois+1
- **Large, uniform register file:** 32 general-purpose integer registers in RV32I/RV64I; when F/D/Q are present, 32 floating-point registers as well.lists.riscv+1
- **Pipeline friendliness:** Simple, regular encodings and absence of obscure multi-step side effects reduce hazard and forwarding complexity and support deep, high-frequency pipelines.riscv+1
- **Simple addressing modes:** PC-relative, base+immediate, and simple displacement addressing instead of many complex modes.cse.cuhk+1

Many commercial RISC architectures—MIPS, SPARC, early ARM—took similar ideas to silicon; RISC-V can be seen as the fifth-generation refinement in this lineage.lists.riscv+1

---

# 1.3 What is RISC-V Specifically?

RISC-V (“RISC Five”) is a modern RISC ISA created at UC Berkeley around 2010, led by Krste Asanović and David Patterson with key contributors Yunsup Lee, Andrew Waterman, and the Berkeley Architecture Research group. It is explicitly designed as the _fifth_ major RISC from Berkeley after earlier experimental RISC projects, hence the “V”.courses.grainger.illinois+1

Core characteristics:

- **Clean-slate, post-2010 design:** RISC-V was designed after decades of industry experience with x86, ARM, MIPS, and SPARC, allowing the designers to deliberately avoid legacy baggage such as condition code flags, branch delay slots, and convoluted encodings.riscv+1
- **Open, royalty-free ISA:** The specification is published openly; anyone can implement a RISC-V compatible core without paying ISA license fees or royalties.businesstoday+1
- **Governance by RISC-V International:** The ISA is standardized and evolved by RISC-V International, a non-profit organization headquartered in Switzerland with global membership from industry and academia.businesstoday+1
- **Modular and extensible:** A small base ISA plus a large library of standardized extensions (M, A, F, D, C, V, K, etc.) and reserved opcode space for vendor-defined custom extensions.lists.riscv+1
- **Multiple word sizes:** Families RV32, RV64, and RV128 allow RISC-V to scale from microcontrollers to large memory servers within one architectural framework.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

Unlike ARM or x86, RISC-V’s _ISA_ is open even though specific core implementations may be proprietary; this is analogous to how TCP/IP is open but individual NICs can be proprietary.businesstoday+1

---

# 1.4 Modular Design Philosophy

RISC-V is explicitly modular. Software sees a base ISA plus an ordered list of extensions encoded both textually (e.g., RV64IMAFDCV) and in the hardware-reported `misa` CSR.riscv+1

# Base ISAs

- **RV32I:** 32-bit base integer ISA with 32 general-purpose registers and 32-bit addresses.[[docs.riscv](https://docs.riscv.org/reference/isa/unpriv/rv32.html)]​
- **RV32E:** 32-bit embedded subset with only 16 integer registers (x0–x15) for very small microcontrollers.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **RV64I:** 64-bit base integer ISA with 64-bit registers and addresses; adds “W” instructions for 32-bit subword operations.riscv+1
- **RV64E:** Embedded 64-bit variant with 16 integer registers (less common in practice).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **RV128I:** Architected but not yet commercially implemented; 128-bit registers and addresses for extreme-scale systems.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

# Standard Extensions (selection)

Common extension letters (each letter may bundle several “Z*” sub-extensions):courses.grainger.illinois+1

- **M – Integer Multiply/Divide:** Hardware integer multiplication and division (e.g., `MUL`, `DIV`, `REM`).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **A – Atomic Memory Operations:** LR/SC and AMO instructions for lock-free synchronization.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **F – Single-precision Floating Point (32-bit, IEEE 754).**[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **D – Double-precision Floating Point (64-bit, IEEE 754), requires F.**[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Q – Quad-precision Floating Point (128-bit), requires D.**[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **C – Compressed Instructions:** 16-bit encodings for common instructions, reducing code size by roughly 20–30%.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **B – Bit Manipulation:** A family of “Zb*” sub-extensions (Zba, Zbb, Zbc, Zbs) for rotates, bit deposits/extracts, population counts, etc.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **V – Vector Extension:** RVV 1.0 scalable vector extension with configurable vector length, element width, and grouping.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **H – Hypervisor:** Adds hardware support for virtualization (HS/VS/VU modes and related CSRs).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **K – Scalar Crypto:** AES, SHA2, SM3, SM4, and entropy-source extensions grouped under Zk*.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

Other important “Z*” extensions include Zicsr (CSR instructions) and Zifencei (instruction-fetch fence), which are foundational for privileged code and self-modifying code support.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

# The “G” Shorthand

Historically, **G** was used as a shorthand for the set `{I, M, A, F, D}`, i.e., a “general-purpose” profile capable of running full OSes like Linux: “RV64GC” meant RV64IMAFD with compressed. Newer profile specs move away from G in favor of explicit profile names (RVA22, RVA23), but the shorthand remains widely used in documentation and toolchains.courses.grainger.illinois+1

# Ratified vs. Frozen vs. Draft

RISC-V Intl lifecycle for extensions:[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

- **Draft:** Under active development; semantics can still change. Not for production silicon meant to be long-lived.
- **Frozen:** Semantics are believed complete; changes are restricted to minor clarifications and editorial fixes, enabling early hardware and toolchain work.
- **Ratified:** Finalized and officially part of the standard; changes require a full deprecation process.

Many widely used extensions—M, A, F, D, C, V, K, B—are now ratified; some newer security and AI-related “Z*” extensions are frozen or draft.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

# Privileged vs. Unprivileged ISA

The RISC-V spec is split into:

- **Unprivileged ISA:** Base instructions, extensions, and user-visible state such as integer/FP registers and CSRs accessible from U-mode (e.g., `mcycle`, `fcsr`).courses.grainger.illinois+1
- **Privileged Architecture:** Defines privilege modes (M/S/U), virtual memory, traps, interrupts, and system-level CSRs (e.g., `mstatus`, `satp`, `mtvec`).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

This separation allows microcontrollers to implement only the unprivileged spec plus minimal M-mode, while Linux-capable SoCs implement the full privileged architecture with S-mode and (optionally) H-extension.courses.grainger.illinois+1

---

# 1.5 Why RISC-V When MIPS/ARM/SPARC Already Exist?

By 2010, several RISC ISAs existed, but each had obstacles that made them unsuitable as _open, long-term academic and industrial standards_:businesstoday+1

- **MIPS:** Originally an elegant RISC from Stanford, it later became encumbered by a complex licensing history and was controlled by various companies (SGI, Imagination, Wave Computing, etc.). License terms, fragmentation between MIPS32/MIPS64 and microMIPS/MIPS16, and uncertainties about future governance discouraged new adopters.[[businesstoday](https://www.businesstoday.in/latest/story/digital-india-risc-v-microprocessor-dir-v-program-launched-331530-2022-04-27)]​
- **ARM:** Ubiquitous in embedded and mobile, but the ISA is proprietary and licensed by ARM Holdings; implementers pay up-front license fees and per-core or per-chip royalties, and cannot legally define arbitrary custom opcode extensions in the ARM ISA space.[[businesstoday](https://www.businesstoday.in/latest/story/digital-india-risc-v-microprocessor-dir-v-program-launched-331530-2022-04-27)]​
- **x86/x86‑64:** Architecturally complex CISC ISA with massive legacy baggage (real mode, segmentation, a large and irregular instruction set) and tightly controlled by an Intel/AMD duopoly.businesstoday+1
- **SPARC/PowerPC:** Once significant, but their ecosystems have declined; governance is tied to specific vendors (Oracle/Fujitsu for SPARC, IBM for Power) and the cost/benefit of adopting them for new designs is poor.[[businesstoday](https://www.businesstoday.in/latest/story/digital-india-risc-v-microprocessor-dir-v-program-launched-331530-2022-04-27)]​

RISC-V’s answer:

- **Open standard, no royalties:** Anyone can implement it, from students doing small FPGA cores to national programs building server-grade CPUs, with no licensing payments.businesstoday+1
- **Extensibility without fragmentation:** A well-defined extension mechanism and reserved opcode ranges for custom vendor extensions allow innovation while keeping a coherent base ISA.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Academic + commercial freedom:** Universities can use the ISA in teaching and tape-outs without NDAs; startups can build proprietary cores or open ones (Rocket, BOOM, CVA6, SHAKTI) on equal footing.courses.grainger.illinois+1
- **Modern, clean design:** RISC-V removes legacy artifacts like branch delay slots or condition code flags, making it attractive both for research (formal verification, microarchitectural exploration) and commercial high-performance designs.riscv+1

---

# SECTION 2 — RISC-V ISA: DEEP TECHNICAL DIVE (UNPRIVILEGED ISA)

This section is structured to roughly mirror the official Unprivileged ISA Manual and give you a spec-level understanding of the core ISA.courses.grainger.illinois+1

---

# 2.1 Base Integer ISA — RV32I

# Register File and ABI

RV32I defines 32 general-purpose integer registers x0x0x0–x31x31x31, each 32 bits wide (XLEN = 32). Register x0 is hardwired to constant zero; writes to x0 are discarded, and reads always return 0.riscv+1

The standard ABI assigns conventional roles and names:riscv+1

|Register|ABI name|Purpose (typical)|
|---|---|---|
|x0|zero|Constant 0|
|x1|ra|Return address|
|x2|sp|Stack pointer|
|x3|gp|Global pointer|
|x4|tp|Thread pointer|
|x5–x7|t0–t2|Temporaries|
|x8–x9|s0/fp,s1|Saved / frame pointer|
|x10–x17|a0–a7|Function args / returns|
|x18–x27|s2–s11|Saved registers|
|x28–x31|t3–t6|Temporaries|

This ABI is used by GCC/LLVM and the standard C libraries for RISC-V.courses.grainger.illinois+1

# XLEN

XLEN is the native integer register width and address width of the ISA variant:

- RV32: XLEN = 32
- RV64: XLEN = 64
- RV128: XLEN = 128

All integer registers, PC, and integer immediates are XLEN bits, and the unprivileged spec is parameterized by XLEN.riscv+1

# Instruction Length and Formats

In the base ISA, every instruction word is 32 bits, aligned on 32-bit boundaries (addresses divisible by 4). Instruction formats pack opcode, register indices, function subcodes, and immediates in fixed bit positions to simplify decode.cse.cuhk+1

The main 32‑bit formats are:

- R-type: register–register operations (ADD, SUB, AND, OR, shifts, etc.)
- I-type: immediate arithmetic, loads, JALR, system instructions
- S-type: stores
- B-type: conditional branches
- U-type: upper-immediate instructions (LUI, AUIPC)
- J-type: JAL (jump and link)

# R-type

text

`31          25 24   20 19   15 14  12 11    7 6      0 |  funct7     | rs2  | rs1  |funct3|   rd   | opcode |`

Example: `ADD x3, x1, x2` (x3 = x1 + x2) has fields:cse.cuhk+1

- opcode = 0110011₂ (0x33)
- funct3 = 000₂
- funct7 = 0000000₂
- rd = 3, rs1 = 1, rs2 = 2

Encoded as 0x002081B3 (little-endian word in memory).cse.cuhk+1

# I-type

text

`31             20 19   15 14  12 11    7 6      0 |   imm[11:0]    | rs1  |funct3|  rd   | opcode |`

Example: `ADDI x5, x6, 10` (x5 = x6 + 10):

- opcode = 0010011₂ (0x13)
- funct3 = 000₂
- rs1 = 6, rd = 5, imm = 10 (0x00A)

Encoding: 0x00A30313.cse.cuhk+1

Loads (`LB`, `LH`, `LW`), JALR, and system instructions (`ECALL`, `EBREAK`, CSR ops when Zicsr is present) also use I-type.[[docs.riscv](https://docs.riscv.org/reference/isa/unpriv/rv32.html)]​

# S-type (Stores)

text

`31      25 24   20 19   15 14  12 11      7 6      0 |imm[11:5]| rs2  | rs1  |funct3|imm[4:0] | opcode |`

Stores split the 12-bit signed immediate between two fields that are concatenated and sign-extended. Example: `SW x5, 8(x6)` (store word):[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​

- opcode = 0100011₂ (0x23)
- funct3 = 010₂ (word)
- rs1 = 6 (base), rs2 = 5 (value)
- imm = 8 (binary 000000001000₂)
	 - imm[11:5] = 0000000₂
	 - imm[4:0] = 01000₂

Encoding: 0x00532323.[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​

# B-type (Branches)

text

`31      25 24   20 19   15 14  12 11      7 6      0 |imm[12|10:5]| rs2 | rs1 |funct3|imm[4:1|11]|opcode|`

The 13-bit branch offset (multiple of 2 bytes) is spread across bits for efficient sign-extension, with bit 0 implicitly 0. Example: `BEQ x1, x2, offset` uses opcode 1100011₂ and funct3 000₂.[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​

# U-type (LUI, AUIPC)

text

`31                     12 11    7 6      0 |         imm[31:12]      |  rd  | opcode |`

The 20-bit immediate is placed in bits [31:12] and represents bits [31:12] of a 32-bit value with lower 12 bits zeroed. Instructions:[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​

- `LUI rd, imm20`: rd = imm20 << 12
- `AUIPC rd, imm20`: rd = PC + (imm20 << 12)[[msyksphinz-self.github](https://msyksphinz-self.github.io/riscv-isadoc/html/rvi.html)]​

# J-type (JAL)

text

`31        12 11    7 6      0 |   imm[20|10:1|11|19:12] | rd | opcode |`

The 21-bit signed offset (multiple of 2 bytes) is reassembled from scattered bits, with bit 0 implicit 0.[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​

Example: `JAL x1, offset` saves return address in x1 and jumps PC-relative.[[docs.riscv](https://docs.riscv.org/reference/isa/unpriv/rv32.html)]​

# Example Encodings Summary

- `ADD x3, x1, x2` → opcode 0x33, encoding 0x002081B3.cse.cuhk+1
- `ADDI x5, x6, 10` → opcode 0x13, encoding 0x00A30313.cse.cuhk+1
- `LW x5, 8(x6)` → opcode 0x03, funct3=010₂, encoding 0x00832303.cse.cuhk+1
- `SW x5, 8(x6)` → opcode 0x23, encoding 0x00532323.[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​
- `BEQ x1, x2, offset` → opcode 0x63; offset encoding follows B-type layout.[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​
- `JAL x1, offset` → opcode 0x6F; immediate per J-type format.[[cse.cuhk.edu](http://www.cse.cuhk.edu.hk/~byu/CENG3420/2025Spring/doc/RV32-reference-2.pdf)]​

# Instruction Categories

RV32I defines instructions in several functional groups:riscv+1

- **Arithmetic/Logical:** `ADD`, `SUB`, `SLT`, `SLTU`, `AND`, `OR`, `XOR`, `SLL`, `SRL`, `SRA` and their immediate forms (`ADDI`, `SLTI`, etc.).
- **Shifts:** Logical and arithmetic shifts (register and immediate forms).
- **Loads/Stores:** Byte, halfword, word loads with sign/zero extension; stores for byte/halfword/word.
- **Control Flow:** Conditional branches (`BEQ`, `BNE`, `BLT`, `BGE`, etc.), `JAL`, `JALR`.
- **System:** `ECALL`, `EBREAK`, plus CSR instructions via Zicsr (`CSRRW`, `CSRRS`, etc.).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **No dedicated flags register:** Instead of condition codes, branches compare register values directly (e.g., `BEQ x1, x2, label`) or rely on `SLT`/`SLTU`.riscv+1

# Notable Design Choices

- **No condition code flags:** Eliminates global status flags (NZCV) and associated hazards; comparisons produce boolean values in registers or are embedded within branch instructions.riscv+1
- **PC-relative addressing:** `AUIPC` and branches make PC-relative code easy, improving position-independent code and linker relaxation.[[msyksphinz-self.github](https://msyksphinz-self.github.io/riscv-isadoc/html/rvi.html)]​
- **Defined division behavior:** For `DIV`/`DIVU` (in M extension), division by zero returns a defined result (−1 or all 1s) instead of trapping, simplifying low-level code.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **x0 as zero:** Frequent use of zero constants costs no register and helps encode moves and clears cheaply (e.g., `ADD x5, x0, x6` is a move).riscv+1

---

# 2.2 RV64I — 64-bit Base ISA

RV64I generalizes RV32I to 64-bit integer registers and addresses. Key implications:courses.grainger.illinois+1

- **Registers:** 32 general-purpose registers, each 64 bits (XLEN = 64).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Address space:** 64-bit virtual and physical addresses (exact virtual address scheme governed by the privileged spec and `satp`/paging mode).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

# Additional Instructions

RV64I adds instructions that operate on 32-bit subwords but store sign-extended results in 64-bit registers:courses.grainger.illinois+1

- **Loads/stores:**
	 - `LWU rd, offset(rs1)`: Load 32-bit word and zero-extend to 64 bits.
	 - `LD rd, offset(rs1)`: Load 64-bit doubleword.
	 - `SD rs2, offset(rs1)`: Store 64-bit doubleword.
- **W-suffix arithmetic/logical:**
	 - `ADDIW rd, rs1, imm`: 32-bit add, then sign-extend result to 64 bits.
	 - `ADDW`, `SUBW`, `SLLW`, `SRLW`, `SRAW`, and immediate variants (`SLLIW`, `SRLIW`, `SRAIW`).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

The W-forms treat operands as 32-bit values (lower 32 bits of register), perform the operation in 32 bits, then sign-extend to 64 bits; this matches C’s int32 arithmetic semantics on a 64-bit platform.courses.grainger.illinois+1

# Sign Extension and Memory Model Implications

- **32→64-bit promotion:** Many instructions implicitly sign-extend their 32-bit results (e.g., `ADDW`); compilers must be aware when mixing 32-bit and 64-bit operations to avoid redundant `SEXT` or masking.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Loads:**
	 - `LW`: sign-extends 32-bit word.
	 - `LWU`: zero-extends.
	 - `LD`: does not extend (already 64 bits).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Pointer size:** Pointers and `size_t` are typically 64-bit under the LP64D ABI (Linux RISC-V), impacting stack frame layout and struct alignment.courses.grainger.illinois+1

The semantics are chosen so that compiled 32-bit code ported to 64-bit RISC-V behaves naturally, similar to x86-64’s 32-bit register write semantics (zero-extend to 64 bits) but using sign-extension for W operations in line with C integer rules.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

---

# 2.3 Standard Extensions — Detailed Coverage

The table below summarizes selected major extensions and their status:[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

|Extension|Letter|Description|Status|
|---|---|---|---|
|Integer Multiply/Divide|M|Integer MUL/DIV/REM|Ratified|
|Atomic Memory Ops|A|LR/SC, AMO add/swap/and/or/xor/min/max|Ratified|
|Single-Precision Float|F|IEEE-754 binary32 FP, 32 FP regs|Ratified|
|Double-Precision Float|D|IEEE-754 binary64 FP, extends F|Ratified|
|Quad-Precision Float|Q|IEEE-754 binary128 FP, extends D|Ratified|
|Compressed|C|16-bit encodings for common ops|Ratified|
|Bit Manipulation|B|Zba, Zbb, Zbc, Zbs sub-extensions|Ratified|
|Vector|V|Scalable vector extension (RVV 1.0)|Ratified|
|Hypervisor|H|Virtualization support (HS/VS/VU)|Ratified|
|Crypto Scalar|K|AES, SHA, SM3/4, entropy (Zk*)|Ratified|

Below we give motivation, key operations, hardware implications, and use cases.

# M — Integer Multiply/Divide

- **Motivation:** Many workloads (DSP, graphics, cryptography, general integer code) require fast multiplication and division; doing them in software on RV32I alone is slow.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key instructions:** `MUL`, `MULH`, `MULHSU`, `MULHU`, `DIV`, `DIVU`, `REM`, `REMU`.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Hardware implications:** Adds a multiplier/divider unit, which can be implemented as a single-cycle or multi-cycle pipeline depending on area/power targets.
- **Use cases:** Almost all general-purpose SoCs, MCUs with moderate performance needs, DSP tasks in embedded and communication stacks.

# A — Atomic Memory Operations

- **Motivation:** Provide portable primitives for lock-free synchronization and multi-core concurrency without relying on LL/SC quirks or non-standard instructions.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key instructions:**
	 - `LR.W` / `SC.W`, `LR.D` / `SC.D`: load-reserved and store-conditional.
	 - AMOs: `AMOADD`, `AMOSWAP`, `AMOAND`, `AMOOR`, `AMOXOR`, `AMOMIN`, `AMOMAX`, `AMOMINU`, `AMOMAXU`.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Hardware implications:** Reservation set or address tracking for LR/SC; atomic read-modify-write in memory subsystem; coherence protocol awareness for multi-core.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Use cases:** OS kernels, concurrent data structures, lock-free queues, user-space atomics in C/C++ (`std::atomic`).

# F/D/Q — Floating-Point

- **Motivation:** IEEE-754 floating point is fundamental for scientific computing, media processing, and ML workloads.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key instructions:**
	 - F: single-precision arithmetic (`FADD.S`, `FMUL.S`, `FDIV.S`, `FSQRT.S`), conversions, compares, fused multiply-add (`FMADD.S`).
	 - D: same set with `.D` suffix operating on 64-bit floats; requires F.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
	 - Q: quad-precision operations; requires D.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Hardware implications:** Separate FP register file `f0`–`f31`, FP execution units (adder, multiplier, divider, sqrt, FMA), FP control/status register `fcsr`.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Use cases:** HPC, multimedia, signal processing, ML inference and training (typically F and D; Q is niche for numerical analysis and high-precision finance).

# C — Compressed Instructions

- **Motivation:** Code size reduction improves I-cache and I-TLB hit rates and reduces memory bandwidth—critical for embedded systems and beneficial even for large cores.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key features:**
	 - 16-bit encodings for a subset of popular instructions (e.g., `C.ADDI`, `C.LW`, `C.SW`, `C.J`, `C.JAL`, `C.LI`, `C.LUI`, `C.ADD`, `C.MV`).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
	 - Mixed 16/32-bit stream; decoder expands C instructions into canonical 32-bit internal form.
- **Hardware implications:** Slightly more complex decode front-end to handle 16and 32-bit instruction boundaries, but gains from reduced fetch bandwidth and code storage.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Use cases:** Almost all production RISC-V cores, from microcontrollers (firmware flash savings) to Linux SBCs (smaller binaries, energy savings).

# B — Bit Manipulation (Zba, Zbb, Zbc, Zbs)

- **Motivation:** Modern cryptography, graphics, and bit-level algorithms spend significant time doing shifts, masks, rotates, and logical combinations; specialized instructions can reduce instruction count and improve constant-time coding.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key operations:**
	 - Zba: address generation, add with shift (`SH1ADD`, `SH2ADD`, `SH3ADD`).
	 - Zbb: basic bit-manip (`ANDN`, `ORN`, `XORN`, `CLZ`, `CTZ`, `PCNT`, `MIN`, `MAX`).
	 - Zbc: carry-less operations for crypto (e.g., polynomial multiply).
	 - Zbs: single-bit set/clear/invert/extract (`BSET`, `BCLR`, `BINV`, `BEXT`).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Hardware implications:** Additional ALU sub-blocks for bit operations; usually modest area overhead.
- **Use cases:** Cryptography libraries, bitset operations in databases, network stacks, and compression.

# V — Vector Extension (RVV 1.0)

- **Motivation:** High-performance computing, media, and ML workloads benefit from SIMD/vectorization, but fixed-width SIMD (like SSE/NEON) ages poorly as vector widths grow; RVV’s scalable vectors abstract over physical width.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key concepts:** See Section 2.9; includes vector integer and FP arithmetic, loads/stores, reductions, permutations, and masks with dynamic `vsetvli` configuration.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Hardware implications:** Vector register file `v0`–`v31`, vector ALUs, load/store units, and mask registers; area scales with maximum VLEN (e.g., 128, 256, 512 bits).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Use cases:** HPC, signal processing, ML inference/training, graphics, and any throughput-oriented workloads.

# H — Hypervisor

- **Motivation:** Efficient virtualization of RISC-V systems for cloud and data-center use; support multiple guest OS instances with good performance isolation.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key features:**
	 - Additional privilege mode HS (host supervisor) and virtual equivalents VS/VU.
	 - Two-stage address translation via `hgatp` (host) and `vsatp` (guest).
	 - New CSRs for delegation and virtualization (e.g., `hstatus`, `hideleg`, `hvip`).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Hardware implications:** TLB and MMU extended for nested page tables; additional privilege checks; interrupt virtualization logic.
- **Use cases:** KVM-RISC-V in Linux, cloud hypervisors, container host kernels when combined with hardware VMs for isolation.

# K — Crypto Scalar Extensions

- **Motivation:** Hardware acceleration for common cryptographic primitives reduces latency and mitigates side-channel leakage relative to naive software loops.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Key operations:** Sub-extensions Zkn (AES, SM4), Zks (SHA2, SM3), Zkr (entropy source), Zknd/Zkne (AES round functions, etc.).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
- **Hardware implications:** Dedicated AES/SHA/SM datapaths and S-box logic; entropy source IP; constant-time pipelines.
- **Use cases:** TLS, VPN, disk encryption, secure boot, and general-purpose system security.

---

# 2.4 Control and Status Registers (CSRs)

CSRs are special registers that control privileged behavior, expose performance counters, and hold exception/interrupt state. CSR access instructions are defined by the Zicsr extension and are required for any realistic system.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

# CSR Access Instructions (Zicsr)

- `CSRRW rd, csr, rs1` — Atomic read/write CSR.
- `CSRRS rd, csr, rs1` — Read and set bits.
- `CSRRC rd, csr, rs1` — Read and clear bits.
    
- Immediate variants `CSRRWI`, `CSRRSI`, `CSRRCI` use a 5-bit immediate instead of rs1.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

These use the I-type format with the CSR address in bits [31:20] and funct3 differentiating the op.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## Key Machine-Mode CSRs

Some of the most important CSRs (names and roles):[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

- `mstatus`: Global machine status (interrupt enable bits, privilege bits, etc.).
    
- `misa`: Encodes XLEN and implemented ISA extensions (base + letters).
    
- `medeleg`, `mideleg`: Exception and interrupt delegation to S-mode.
    
- `mip`, `mie`: Pending and enabled interrupt bits.
    
- `mtvec`: Machine trap-vector base address.
    
- `mepc`: Machine exception program counter (where to return after trap).
    
- `mcause`: Encodes the cause of the last exception or interrupt.
    
- `mtval`: Trap value (e.g., faulting address).
    
- `mcycle`, `minstret`: Cycle and retired instruction counters.
    
- `mhpmcounterN`, `mhpmeventN`: Hardware performance monitoring counters and event selectors.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

Supervisor and user modes have analogous CSRs (`sstatus`, `stvec`, `sepc`, `scause`, etc.).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## `misa` Layout and Extension Discovery

`misa` is an XLEN-bit CSR; its high bits encode the base XLEN and lower bits encode which standard extensions are present:[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

- Bits [XLEN−1:XLEN−2]: Encoded XLEN (01 for 32, 10 for 64, 11 for 128).
    
- Bits [25:0]: Each bit corresponds to an extension letter (bit 0 = ‘A’, bit 1 = ‘B’, …, bit 25 = ‘Z’).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

Example: a system with RV64IMAFDCV might have the bits for I, M, A, F, D, C, V set. Software can read `misa` at runtime to discover available extensions and adapt (e.g., use vector code paths only when V is present).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

---

## 2.5 Instruction Encoding Philosophy

The unprivileged spec documents several explicit encoding goals:riscv+1

- **Uniform register field positions:** `rs1`, `rs2`, and `rd` are in the same bit positions for all formats that use them (rd at [11:7], rs1 at [19:15], rs2 at [24:20]); this simplifies decoder hardware.[[docs.riscv](https://docs.riscv.org/reference/isa/unpriv/rv32.html)]​
    
- **Sign-extension efficiency:** For all immediates except CSR immediates, the sign bit (*) is placed in bit 31; scattered immediate fields are arranged so that sign-extension hardware is simple and consistent across formats.cse.cuhk+1
    
- **Opcode space partitioning:** 7-bit opcode field at [6:0] is partitioned into major opcode groups that leave room for future standard and custom extensions, and that are compatible with future 48/64-bit encodings.riscv+1
    
- **Variable-length compatibility:** By requiring 16-bit alignment and reserving certain opcode patterns, the ISA supports mixing 16-bit C instructions with 32-bit base instructions, and leaves space for future 48/64-bit encodings while keeping decode relatively straightforward.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

## Major Opcode Map (Simplified)

A simplified table of some major opcodes in RV32I/RV64I:riscv+1

|Opcode (binary)|Hex|Mnemonic group|Example instructions|
|---|---|---|---|
|0110111|0x37|LUI|`LUI rd, imm20`|
|0010111|0x17|AUIPC|`AUIPC rd, imm20`|
|1101111|0x6F|JAL|`JAL rd, offset`|
|1100111|0x67|JALR (I-type)|`JALR rd, rs1, offset`|
|1100011|0x63|BRANCH|`BEQ`, `BNE`, `BLT`, …|
|0000011|0x03|LOAD|`LB`, `LH`, `LW`, `LD`|
|0100011|0x23|STORE|`SB`, `SH`, `SW`, `SD`|
|0010011|0x13|OP-IMM|`ADDI`, `SLTI`, `ORI`, …|
|0110011|0x33|OP (reg–reg)|`ADD`, `SUB`, `AND`, …|
|0001111|0x0F|MISC-MEM|`FENCE`, `FENCE.I`|
|1110011|0x73|SYSTEM|`ECALL`, `EBREAK`, CSR ops|

Additional opcodes are used for floating-point ops, atomics, and custom spaces.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

The “custom” major opcodes (e.g., 0x0B, 0x2B, 0x5B, 0x7B) are reserved for implementer-defined extensions and never used by standard instructions, ensuring that vendor extensions cannot collide with ratified ISA features.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

---

## 2.6 Memory Model — RVWMO (RISC-V Weak Memory Ordering)

RISC-V defines RVWMO, a weak memory ordering model designed to support high-performance, out-of-order multi-core hardware while still being amenable to formal reasoning. It is weaker than x86’s TSO but allows stronger ordering via fences and atomics.courses.grainger.illinois+1

Key aspects:

- **Relaxed ordering:** Loads and stores from different cores may be observed in different orders unless the program uses fences or atomics to constrain reordering.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **Per-location SC with atomics:** Atomic operations provide strong guarantees for synchronization variables, similar to C/C++ atomic semantics.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **FENCE instruction:** `FENCE [pred],[succ]` constrains memory and I/O ordering, ensuring that prior memory operations are globally visible before subsequent operations matching the mask.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

## LR/SC and AMOs in the Memory Model

- **LR/SC (Load-Reserved/Store-Conditional):** Provide a loop-based primitive for building locks and lock-free algorithms: `LR` reads a value and sets a reservation; `SC` attempts to store and succeeds only if no conflicting writes occurred.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **AMOs:** Single-instruction read-modify-write operations that are atomic with respect to other cores (e.g., `AMOADD.W`, `AMOSWAP.D`).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

These are integrated into RVWMO’s formal model so that properly synchronized code behaves as if memory were sequentially consistent for those synchronization operations.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## Comparison to x86 TSO and ARM

- **x86 TSO:** Stronger; nearly all writes are observed in order, and many reorderings are forbidden, simplifying programmer reasoning but constraining microarchitectural optimizations.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **ARM (ARMv8/ARMv9):** Also weakly ordered, with explicit barriers (`DMB`, `DSB`, `ISB`) for ordering.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **RISC-V RVWMO:** Similar in spirit to ARM’s model—allows many reorderings, but fences and atomic operations provide the necessary guarantees for data-race-free programs.courses.grainger.illinois+1
    

---

## 2.7 Exception and Interrupt Handling (Unprivileged View)

The unprivileged spec relies on the privileged architecture for trap handling; here we summarise essentials relevant to ISA-level understanding.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## Privilege Levels

RISC-V defines up to four privilege levels:[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

- **M (Machine, 3):** Highest privilege; firmware, bootloader, low-level runtime.
    
- **S (Supervisor, 1):** OS kernels, hypervisors (without H) or guest kernels (with H).
    
- **U (User, 0):** Applications.
    
- **HS/VS/VU:** Additional virtualized modes introduced by H-extension (see Section 7.9 for details).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

Many microcontrollers implement only M-mode; Linux-capable systems implement at least M and S, with optional U for user-space.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## Trap Causes

Traps (exceptions + interrupts) include:[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

- **Synchronous exceptions:** Illegal instruction, instruction or data access fault, misaligned load/store/jump, breakpoint (`EBREAK`), environment calls (`ECALL`) from U/S/M, page faults (if virtual memory is enabled).
    
- **Asynchronous interrupts:** Timer interrupts, software interrupts (IPIs), external device interrupts.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

## Core Trap CSRs

Trap handling is orchestrated via CSRs:[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

- `mtvec`: Base address of trap handler; can be direct or vectored mode.
    
- `mepc`: PC of the instruction that caused the trap (or next PC for some traps).
    
- `mcause`: Encodes whether the trap is interrupt or exception and its code.
    
- `mtval`: Additional value (e.g., faulting address or instruction bits).
    

A typical trap flow in M-mode:

1. Hardware saves the faulting PC into `mepc`, cause into `mcause`, and optional info into `mtval`.
    
2. PC is set to `mtvec` (or `mtvec + 4*cause` in vectored mode).
    
3. Trap handler inspects `mcause`/`mtval`, services the trap, possibly adjusts `mepc`.
    
4. `MRET` returns to `mepc` (or `mepc + 4` depending on semantics).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

## ECALL as System Call Mechanism

- **From U-mode:** `ECALL` raises an environment call exception; delegated via `medeleg` to S-mode, whose trap handler implements system calls (e.g., Linux’s `syscall` entry).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **From S-mode:** `ECALL` may trap to M-mode for hypervisor or firmware services, depending on delegation.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

`mcause` encodes distinct cause codes for ECALL from U, S, and M, allowing handlers to distinguish origin.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

---

## 2.8 Floating-Point Architecture

When F/D/Q are present, RISC-V adds a separate FP register file and control/status mechanisms conforming to IEEE-754.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## FP Register File and NaN Boxing

- **Registers:** `f0`–`f31`, each XLEN bits in RV32/RV64 (32 or 64 bits), but capable of holding narrower FP values using _NaN boxing_.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **NaN boxing:** When a narrower FP value (e.g., 32-bit float) is stored in a wider register (e.g., 64-bit FP register under D), the upper bits are filled with a canonical NaN pattern so that operations treating it as a wider value still see a NaN if interpreted incorrectly.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **Register classes:** The ABI defines calling conventions for passing FP arguments and return values when FP is enabled (e.g., LP64D uses both x and f registers).courses.grainger.illinois+1
    

## Rounding Modes and Exception Flags

- **Rounding modes:** Encoded in `frm` field of `fcsr` or in instructions:
    
    - RNE (round to nearest, ties to even),
        
    - RTZ (towards zero),
        
    - RDN (towards −∞),
        
    - RUP (towards +∞),
        
    - RMM (round to nearest, ties to max magnitude).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
        
- **Exception flags in `fflags` (part of `fcsr`):**
    
    - NX (inexact),
        
    - UF (underflow),
        
    - OF (overflow),
        
    - DZ (divide-by-zero),
        
    - NV (invalid operation).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
        

These flags are sticky and can be examined/cleared by software.

## Layering of F, D, Q

- F is the base floating-point extension.
    
- D requires F and adds double-precision instructions and semantics.
    
- Q requires D and adds quad-precision.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

This layering ensures incremental hardware complexity growth and consistent behavior across precisions.

---

## 2.9 Vector Extension (RVV 1.0) — Overview

RVV 1.0 is a ratified scalable vector extension that decouples the number of architectural elements processed per instruction from the physical vector width, enabling portable high-performance code across implementations with different VLEN.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## Key Parameters and Registers

- **VLEN:** Hardware vector register width in bits (e.g., 128, 256, 512, 1024). Fixed per microarchitecture.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **ELEN:** Maximum element width supported (e.g., 32 or 64 bits).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **LMUL:** Vector register grouping multiplier; groups multiple physical vector registers to form a logical vector register with more lanes (e.g., LMUL = 2, 4, 8) or fractional groups (1/2, 1/4).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **Vector register file:** `v0`–`v31`; each a vector of elements. Some are reserved for masks or special use.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **CSRs:** `vl` (current vector length in elements) and `vtype` (current vector type: SEW, LMUL, tail/mask policies).[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

## Vector Configuration — `vsetvli` / `vsetivli`

- `vsetvli rd, rs1, imm`: Sets `vl` based on requested element width (SEW) and LMUL encoded in `imm`, and the available VLEN and ELEN; returns actual `vl` in `rd`.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- `vsetivli rd, uimm, imm`: Same but with immediate element count.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

This dynamic configuration lets the same binary adapt to different vector lengths at runtime, unlike fixed-width SIMD where binaries must be recompiled per width.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

## Categories of Vector Instructions

RVV defines rich instruction categories:[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

- **Integer arithmetic:** `vadd`, `vsub`, `vmul`, `vdiv`, `vmin`, `vmax`, etc.
    
- **Floating-point:** `vfadd`, `vfsub`, `vfmul`, `vfdiv`, `vfsqrt`, `vfmacc`, etc.
    
- **Logical and mask ops:** `vand`, `vor`, `vxor`, mask set/clear operations.
    
- **Reduction:** `vredsum`, `vredmax`, etc., to reduce vectors to scalars.
    
- **Permutation:** `vslide`, `vrgather`, `vcompress` for shuffles and compactions.
    
- **Vector loads/stores:** Unit-stride, strided, indexed, segmented variants.
    
- **Masking:** Nearly all operations support masking, executing only on lanes where mask bit is 1.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

## Why RVV is Architecturally Superior to Fixed-Width SIMD

- **Scalability:** The same program can scale from 128-bit to 1024-bit hardware without recompilation, merely by changing `vsetvli` behavior.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    
- **Portability:** Compilers emit generic RVV code once; hardware vendors choose their preferred VLEN and microarchitectural tricks.
    
- **Energy efficiency:** Hardware may trade off more lanes vs. frequency/voltage while preserving software semantics.
    
- **Masking and tail handling:** Explicit mask and tail policies avoid the need for scalar epilogues in many vectorized loops.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​
    

## Example: Vector Dot Product in RVV Assembly

Pseudocode to compute dot product of two float32 arrays `a` and `b` of length `n`:

text

`# a0: pointer to a[] # a1: pointer to b[] # a2: n     vsetvli t0, a2, e32,m1      # Configure for 32-bit elements, LMUL=1    vle32.v v0, (a0)            # Load chunk of a    vle32.v v1, (a1)            # Load chunk of b    vfmul.vv v2, v0, v1         # v2 = v0 * v1    vfredsum.vs v3, v2, v3      # Reduce into v3 (accumulator)    # Adjust a0, a1, a2 by vl and loop until a2 == 0`

The actual code would loop, updating `a0`, `a1`, and `a2` by `vl` (the number of elements processed) until the entire vector is consumed. The same code runs efficiently on any VLEN implementation because `vsetvli` chooses `vl` at runtime.[[lists.riscv](https://lists.riscv.org/g/sig-documentation/attachment/266/0/riscv-unprivileged.pdf)]​

---

---

## ⏸️ PAUSED — Completed up to Section. Reply with "continue" to receive Section onwards. Do NOT restart from the beginning.electronicsforyou+1