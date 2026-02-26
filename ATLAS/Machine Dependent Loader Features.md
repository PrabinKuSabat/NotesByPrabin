# I. The Necessity and Mechanics of Relocation

In a multi-programming environment, the operating system cannot guarantee that a program will always be loaded at the exact memory address the programmer or assembler initially assumed. **Relocation** is the system-level process of modifying a program so that it can execute correctly at a memory address different from the one specified at assembly time. Most assemblers generate object code relative to a starting address of zero, which is known as **relative addressing**. The loader’s primary responsibility is to "fix up" these addresses once the actual starting point, known as the **Load Address**, is determined by the Operating System.

## Relocation via Modification Records (SIC/XE)

For the SIC/XE architecture, relocation is handled primarily through the **Modification Record (M-Record)**. This approach is intrinsically tied to the instruction format; since SIC/XE uses Format 4 (extended format) instructions for direct addressing, only these specific instructions need to be relocated.

- **The M-Record Anatomy:** The record consists of the record type 'M', the starting location of the address field to be modified (relative to the start of the control section), and the length of the address field in half-bytes.
- **Practical Insight:** From a hardware reality standpoint, Format 3 instructions in SIC/XE often use **PC-relative** or **Base-relative** addressing. Because these addresses are relative to the current instruction pointer or a base register, they do not change even if the entire program is shifted in memory. Therefore, a senior engineer knows that only Format 4 instructions, which contain absolute 20-bit addresses, require M-records. This significantly reduces the size of the object file and the work the loader must perform at runtime.

## Relocation via Bit Masks (Standard SIC)

Standard SIC machines, which lack the sophisticated relative addressing modes of the XE model, require a different approach. Because every instruction might contain a direct memory address, a massive number of M-records would be required, leading to inefficient object files. Instead, these systems use a **Relocation Bit Mask**.

- **Decoding Figure 3.6:** In this visual representation, each Text (T) record is accompanied by a **Relocation Bit Mask**—typically a 12-bit hexadecimal value where each bit corresponds to one word (3 bytes) of object code in that record.
- **The Logic:** If a bit in the mask is set to '1', the loader adds the program's starting address to the corresponding word. If the bit is '0', the word is left unchanged.
- **Comparison:** Unlike the M-record which targets specific "half-bytes," the bit mask is a "blunt force" tool that scans the entire Text record word-by-word. This is a hardware-constrained trade-off: it simplifies the loader's logic at the cost of slightly less granular control.

# II. Program Linking and Symbol Resolution

In industry-level software engineering, programs are rarely written as a single, massive file; they are composed of multiple **Control Sections** that are assembled independently. **Program Linking** is the process of resolving references between these sections.

## External Definitions and References (D and R Records)

To facilitate linking, the loader must process two specific types of metadata provided by the assembler:

1. **Define (D) Records:** These list symbols that are defined within the current control section and are available for use by other sections. The record contains the symbol name and its relative address within the section.
2. **Refer (R) Records:** These list symbols that the current control section uses but are defined elsewhere. Crucially, the assembler does not know the addresses of these symbols, so it leaves them as zeros in the object code.

## The Mathematics of Linking (Figure 3.10 and 3.11)

Let’s walk through the exhaustive example provided in the textbook involving three control sections: **PROGA**, **PROGB**, and **PROGC**.

- In **PROGA**, a symbol `LISTA` is defined at relative address `0040`.
- In **PROGB**, the programmer wants to load the address of `LISTA` using the instruction `+LDT LISTA`.
- **Pass 1 of Linking:** The loader identifies `LISTA` in PROGA’s **D-record**. It calculates the actual address by adding PROGA’s load address (e.g., `4000`) to the relative address (`0040`), resulting in an absolute address of `4040`.
- **Pass 2 of Linking (The M-Record Logic):** The object code for PROGB contains an M-record: `M00005405+LISTA`. When the loader reaches this, it looks up `LISTA` in its internal table, finds the value `4040`, and **adds** it to the 5 half-bytes starting at PROGB's relative address `0054`.
- **Advanced Calculation:** The linking loader also supports subtraction. A record like `M00005405-LISTB` would subtract the address of `LISTB` from the specified memory location. This is vital for calculating the _distance_ between two external labels at load time.

# III. Algorithm and Data Structures for a Linking Loader

Implementing a linking loader requires a sophisticated two-pass approach and specialized data structures to manage the global symbol space.

## The ESTAB (External Symbol Table)

The core data structure is the **ESTAB**, which is analogous to the assembler's SYMTAB but functions on a global scale across all control sections.

- **ESTAB Content:** It stores the name of each external symbol, its absolute address, and the control section it belongs to.
- **Structural Detail (Figure 3.9):** To ensure high performance, the ESTAB is typically implemented as a **Hash Table**. This allows the loader to resolve thousands of external references in near-constant time, which is critical for large system builds.

## The Two-Pass Logic Flow

1. **Pass 1 (Resource Allocation and Address Assignment):** The loader's primary goal in Pass 1 is to assign an absolute address to every control section and every external symbol.
	 - It begins with a **PROGADDR** (Program Starting Address) provided by the OS.
	 - For each control section, it reads the Header (H) record, determines the section’s length (**CSLTH**), and assigns it a **CSADDR** (Control Section Address).
	 - It then enters every symbol from the Define (D) records into the **ESTAB**, calculating their absolute addresses as `CSADDR + relative address`.
2. **Pass 2 (Loading and Relocation):** This is the "heavy lifting" phase where the actual object code is moved into memory.
	 - The loader reads the Text (T) records and places the bytes at `CSADDR + relative address`.
	 - When it encounters a Modification (M) record, it searches the **ESTAB** for the required symbol.
	 - It then performs the specified addition or subtraction directly on the values already residing in memory.

**Programming Implication:** A senior industry engineer realizes that this "patching" in Pass 2 is why linking loaders require random access to the memory where the program is being loaded. If the memory is write-protected or fragmented, the loader must interface with the kernel's memory management unit (MMU) to gain the necessary permissions. This two-pass architecture ensures that regardless of the order in which control sections appear in the object file, all external references are resolved accurately before the program begins execution.

---

# 3.2.1

## Advanced Engineering Analysis of Program Relocation

In the domain of systems architecture, the transition from an assembled object program to an executing process is fundamentally dependent on the concept of **relocation**. As a senior engineer, you must understand that in any modern multiprogramming environment, we cannot predict at assembly time exactly where in memory a program will reside. While the assembler generates code as if the program starts at relative address zero, the **loader** must adjust these addresses to reflect the actual physical or logical starting point assigned by the operating system. This section explores the two primary technical mechanisms for achieving this: **Modification Records** and **Relocation Bit Masks**.

### I. Relocation via Modification Records (The SIC/XE Approach)

The SIC/XE architecture provides a highly efficient environment for relocation due to its support for **relative addressing modes**. When using **Program-Counter (PC) relative** or **Base-relative** addressing, the operand is stored as a displacement from the current instruction address or a base register. Because this displacement—the physical "distance" between the instruction and the data—remains constant regardless of where the program is loaded in memory, these instructions are inherently **self-relocating** and require no intervention from the loader.

However, relocation becomes mandatory for **Format 4 instructions**, which utilize direct 20-bit addressing. In these cases, the address field contains an absolute value relative to the start of the program, which must be updated once the actual **load address** is known. To facilitate this, the assembler produces a **Modification Record (M-record)** for every such address field.

**Structural Anatomy of the Modification Record** The M-record is designed to be surgically precise, targeting only the specific bits that require adjustment.

- **Column 1:** The record type 'M'.
- **Columns 2–7:** The **starting location** of the address field to be modified, expressed as a hexadecimal offset from the beginning of the control section.
- **Columns 8–9:** The **length** of the field to be modified, expressed in **half-bytes** (nibbles). For a 20-bit SIC/XE address, this value is typically `05`.

**Exhaustive Walkthrough: Figures 3.4 and 3.5** The textbook illustrates this using a sample program where `RDREC` is a label for a subroutine.

1. In the source code, we see the instruction `+JSUB RDREC` located at relative address `000006`.
2. The assembler determines that `RDREC` is located at relative address `001036`.
3. The resulting **object code** for this instruction is `4B101036`.
	 - `4B` is the opcode for `JSUB`.
	 - `1` represents the extended Format 4 flag.
	 - `01036` is the 20-bit relative address.
4. Because this is a direct address, the assembler generates an M-record: `M00000705`.
	 - `000007` points to the 7th half-byte of the control section (where the `01036` portion of the `JSUB` instruction begins).
	 - `05` tells the loader to modify exactly 5 hex digits.
5. **Execution Logic:** If the loader assigns this program to start at address `005000`, it will retrieve the value `01036` from memory location `005007`, add `005000` to it, and store the updated absolute address `006036` back into that instruction.

From a system-level perspective, this method is advantageous because it minimizes the size of the object program. Only a small fraction of SIC/XE instructions (those using Format 4) require these records, making the relocation process significantly faster than in older architectures.

### II. Relocation via Bit Masks (The Standard SIC Approach)

Standard SIC machines, and some older architectures, lack relative addressing modes. In such systems, nearly every instruction contains a direct memory address that must be relocated. If we used Modification Records for every instruction, the object file would become excessively large and inefficient. To solve this, architects use a **Relocation Bit Mask**.

**The Logic of the Relocation Mask** In this approach, the relocation information is embedded directly into the **Text (T) records**. Each Text record is expanded to include a **mask field**, typically a three-digit hexadecimal value (12 bits).

- Each bit in this mask corresponds to one **word** (3 bytes) of object code in the Text record.
- If a bit is set to **1**, the corresponding word contains an address that must be relocated.
- If a bit is set to **0**, the word contains a value (such as a constant or an instruction with no address) that should remain unchanged.

**Detailed Decoding: Figure 3.6** Consider the Text record example in **Figure 3.6**: `T^000000^1E^FFC^140033^481039…`.

1. **Header:** Starting address `000000`, length `1E` (30 bytes).
2. **The Mask:** `FFC` is the hexadecimal relocation mask.
3. **Binary Translation:** `FFC` in binary is `1111 1111 1100`.
4. **Bit-to-Word Mapping:**
	 - The first 10 bits are `1`, indicating that the first 10 words (30 bytes) of this record require relocation.
	 - The last 2 bits are `0`, meaning if there were any remaining bytes in this specific record (up to the 12th word), they would not be modified.
5. **Practical Real-Life Application:** When the loader processes this record, it loops through the bits. For every bit that is `1`, it adds the program's actual load address to the 15-bit address field of that SIC instruction.

### III. Engineering Comparisons and Insights

As an architect, you must weigh these two methods based on the hardware's addressing capabilities.

- **Modification Records** are the industry standard for **complex instruction set computers (CISC)** and systems with **multiple control sections**. They allow for highly granular "patching" of code, which is essential when linking different modules together where you might need to add one symbol's address but subtract another.
- **Relocation Bit Masks** are more common in simpler, **word-oriented architectures** where the instruction format is rigid. While they are efficient for "bulk" relocation, they are less flexible for modular programming because they assume every modification is a simple addition of the starting address.

In practice, relocation is what allows the operating system to perform **dynamic memory allocation**. Without these mechanisms, we would be forced to run only one program at a time at a fixed address, a limitation that would render modern multitasking and server environments impossible. Mastering these "half-byte" and "bit-mask" details is what enables a systems engineer to debug low-level loader failures and optimize the boot sequence of an operating system.

---

# 3.2

## Advanced Discourse on Program Linking: Section 3.2.2

In the lifecycle of a system-level program, the transition from independent source modules to a unified executable is mediated by the process of **Program Linking**. As we analyzed in Section 3.2.1, relocation allows a single program to move within memory; however, **Program Linking** allows a program to be partitioned into multiple **Control Sections**, which are assembled separately and then "stitched" together at load time. This modularity is the bedrock of modern software engineering, enabling library reuse and collaborative development.

### I. The Architectural Foundation: Control Sections and Symbols

A **Control Section** is defined as a part of a program that maintains its own internal logic and can be relocated independently. Because these sections are assembled at different times, they lack information about each other’s physical memory locations. To bridge this gap, the assembler provides specialized metadata:

1. **External Definitions (EXTDEF):** These are symbols defined within a control section that are intended to be "exported" or made visible to other sections. A senior engineer views these as the "Public API" of an assembly module.
2. **External References (EXTREF):** These are symbols used within a control section but defined in a different section. These are the "dependencies" that the loader must satisfy.

**Practical System Reality:** When the assembler encounters an **EXTREF**, it cannot determine the address or even the relative offset of that symbol. Consequently, it assigns a placeholder value of `000000` in the object code and delegates the resolution to the loader via specialized records.

### II. Metadata Records: The D, R, and M Format

The communication between the assembler and the linking loader is codified in the object program through three primary record types:

- **Define (D) Records:** These records list every symbol specified in an `EXTDEF` statement. They include the symbol name and its **relative address** within that control section.
- **Refer (R) Records:** These records list every symbol appearing in an `EXTREF` statement, providing the names the loader will need to look up in other modules.
- **Modification (M) Records (Linking Variant):** In basic relocation, M-records only pointed to addresses to be shifted. In **linking**, the M-record is expanded to include a **symbol name** and an **operator** (`+` or `-`).
	 - **Format:** `M [Address] [Length] [+/-] [SymbolName]`
	 - **Logic:** The loader is instructed to take the value already at the specified memory address, add or subtract the absolute address of the named symbol, and store the result back in that location.

### III. Comprehensive Walkthrough: Figure 3.7 through 3.11

To master this, we must exhaustively analyze the textbook's primary example involving three control sections: **PROGA**, **PROGB**, and **PROGC**.

**A. The Source Code Logic (Figure 3.7)** In **PROGA**, the symbols `LISTA` and `ENDA` are defined. `LISTA` is at relative address `0040`. PROGA also makes external references to `LISTB`, `ENDB`, `LISTC`, and `ENDC`. In **PROGB**, the programmer defines `LISTB` and `ENDB`. Crucially, PROGB refers to `LISTA`, `ENDA`, `LISTC`, and `ENDC`.

**B. Object Program Generation (Figure 3.8)** Let’s decode the object code for **PROGB** at relative address `000054`. The programmer wrote the expression `ENDA - LISTA + LISTC`.

1. The assembler can only resolve the internal parts of an expression. Since all these are external to PROGB, the assembler puts `000000` as the initial value in the object code.
2. It then generates three Modification Records for that same address (`000054`):
	 - `M00005405+ENDA`
	 - `M00005405-LISTA`
	 - `M00005405+LISTC`.

**C. The Memory Layout and Calculation (Figure 3.10 and 3.11)** Assume the loader starts at memory address `4000`.

- **PROGA** is loaded at `4000`. Its length is `0063`, so **PROGB** starts at `4063`. **PROGC** starts at `4063 + 007F = 40E2`.
- Now, let's calculate the absolute address of `LISTA`. In PROGA, `LISTA` was at relative address `0040`. Therefore, its absolute address is `4000 + 0040 = 4040`.

**The Linking Operation Step-by-Step:** Look at the address field for the instruction at `PROGB + 000054` (Absolute address `4063 + 0054 = 40B7`).

1. **Initial Value:** The loader places the Text record value `000000` at address `40B7`.
2. **Processing M-Record 1 (`+ENDA`):** The absolute address of `ENDA` is `4054`. The loader adds `4054` to the value at `40B7`. (Current value: `004054`).
3. **Processing M-Record 2 (`-LISTA`):** The absolute address of `LISTA` is `4040`. The loader subtracts `4040` from `40B7`. (`004054 - 4040 = 000014`).
4. **Processing M-Record 3 (`+LISTC`):** The absolute address of `LISTC` is `4124`. The loader adds `4124`. (`000014 + 4124 = 004138`). **Final Result:** The memory at `40B7` now contains `004138`, which is the correctly resolved absolute address for the cross-module expression.

### IV. The Linking Loader Algorithm: Data Structures and Passes

To perform this complex coordination, the loader utilizes a two-pass logic and a central data structure called the **ESTAB (External Symbol Table)**.

**The ESTAB Architecture (Figure 3.9)** The **ESTAB** is used to store the name and absolute address of every external symbol across all control sections. It also records which control section the symbol belongs to, ensuring there are no name collisions (e.g., if two different sections mistakenly used the name `LISTA`). In industry, the ESTAB is implemented as a **Hash Table** to ensure that symbol resolution—which can involve thousands of names in large software projects—remains high-performance.

**The Two-Pass Execution Flow:**

- **Pass 1 (Symbol Definition):** The loader reads all Header (H) records and Define (D) records. It calculates the **Control Section Address (CSADDR)** for each segment and enters every external symbol into the **ESTAB** with its absolute address.
- **Pass 2 (Loading and Patching):** The loader reads the Text (T) records to place the actual code in memory. Then, it processes the Modification (M) records, looking up the required addresses in the **ESTAB** and performing the arithmetic fixes directly in memory.

**Industry Insight:** This "patching" in Pass 2 is why the loader needs a writable segment of memory. On modern systems with hardware protection, the loader must work closely with the Kernel's memory manager to temporarily lift "write-protect" flags on code pages so it can perform these final address injections before the program is marked "executable".

By the end of this process, the disjointed pieces of **PROGA**, **PROGB**, and **PROGC** have been unified into a single logical memory image, with every jump and data reference correctly pointing across the original file boundaries. This mastery of the Linking Loader is what separates a standard coder from a systems architect.

---

# 3.2.3

Welcome to this intensive, master-level discourse on **Section 3.2.3: Algorithms and Data Structures for a Linking Loader**. As we move from the conceptual foundations of relocation and linking into the actual implementation mechanics, we transition from theoretical "what" to the architectural "how." For a senior industry engineer or a computer science student facing a rigorous exam, understanding the internal plumbing of the loader is non-negotiable. The linking loader is a complex, **two-pass system** designed to solve a fundamental problem: resolving address dependencies between independently assembled modules. We will now dissect the data structures, the logic flow, and the memory-level realities of this system.

## I. The Core Data Structure: The External Symbol Table (ESTAB)

The entire linking process hinges on a specialized data structure known as the **ESTAB (External Symbol Table)**. Much like the assembler’s Symbol Table (SYMTAB), the ESTAB is used to store symbol names and their corresponding addresses. However, there is a critical distinction in scope: while SYMTAB is local to a single assembly module, **ESTAB is global**, spanning the entire collection of control sections being loaded together.

**Practical Insight and Architectural Reality** In a professional execution environment, the ESTAB must be exceptionally high-performing. It is typically implemented as a **Hash Table** to ensure near-constant time complexity for symbol lookups. Each entry in the ESTAB contains:

- **Symbol Name:** The unique identifier for an external definition.
- **Absolute Address:** The final physical memory location assigned to that symbol.
- **Control Section Name:** The specific section where the symbol was defined.
- **Error Flags:** To detect and report duplicate external definitions or undefined references.

Without the ESTAB, the loader would be blind to the addresses of symbols defined in other files, making cross-module communication impossible.

## II. Variable Management: PROGADDR and CSADDR

To manage the physical placement of code, the loader utilizes two primary address variables:

1. **PROGADDR (Program Address):** This is the starting address in memory where the entire linked program is to be loaded. This value is typically provided by the Operating System based on currently available memory fragments.
2. **CSADDR (Control Section Address):** This variable tracks the starting address of the **specific control section** currently being processed.

**The Stacking Logic** At the start of the loading process, `CSADDR` is initialized to `PROGADDR`. As each control section is loaded, its length (**CSLTH**) is added to the current `CSADDR` to determine the starting point for the _next_ section. This ensures that all modules are "stacked" continuously in memory without overlaps or gaps, optimizing memory utilization at the system level.

## III. The Two-Pass Algorithm: A Deep Dive

A linking loader requires two passes for the same reason an assembler does: the "forward reference" problem. A control section might refer to a symbol defined in a module that hasn't been read yet.

### Pass 1: Resource Allocation and Symbol Definition

The sole objective of Pass 1 is to assign an absolute address to every control section and every external symbol defined via `EXTDEF`.

- **Processing Header (H) Records:** When the loader encounters an H-record, it extracts the control section name and its length (**CSLTH**). It checks the ESTAB to ensure the name is not a duplicate. If valid, the current **CSADDR** is assigned as the starting address for this section.
- **Processing Define (D) Records:** Every symbol listed in a D-record is entered into the ESTAB. Its absolute address is calculated as `CSADDR + relative_address_from_record`. If a symbol is already in the ESTAB, the loader flags a "duplicate external symbol" error.
- **Section Advancement:** After the H and D records of a section are processed, `CSADDR` is updated: `CSADDR = CSADDR + CSLTH`.

### Pass 2: The Loading, Relocation, and Linking Phase

With all addresses now safely stored in the ESTAB, Pass 2 performs the actual work of moving bytes and "patching" the code.

- **Processing Text (T) Records:** The object code in each T-record is moved into memory at the location `CSADDR + relative_address`. Note that this is a simple memory transfer; the relocation and linking haven't happened yet.
- **Processing Modification (M) Records:** This is where the "linking" magic occurs. For every M-record, the loader:
	 1. Looks up the symbol name in the **ESTAB**.
	 2. Extracts the absolute address of that symbol.
	 3. Performs the specified arithmetic (addition or subtraction) on the value currently residing at the targeted memory location.
- **Transfer of Control (E Record):** The process concludes with the End (E) record. If the E-record contains an address, the loader uses it as the entry point for execution. In a linked program, only the very first section typically specifies a starting address.

## IV. Comprehensive Walkthrough: Figures 3.10 and 3.11

To cement these concepts, we must analyze the textbook’s master example of three control sections: **PROGA**, **PROGB**, and **PROGC**.

**Figure 3.10: The ESTAB Result** Assume **PROGADDR** is `4000`.

1. **PROGA:** Length is `0063`. ESTAB records PROGA at `4000`. Symbols `LISTA` (`4040`) and `ENDA` (`4054`) are entered.
2. **PROGB:** Starts at `4063` (PROGA's start + PROGA's length). Symbols `LISTB` (`40C3`) and `ENDB` (`40D7`) are entered.
3. **PROGC:** Starts at `40E2` (PROGB's start + PROGB's length). Symbols `LISTC` (`4124`) and `ENDC` (`41D3`) are entered.

**Figure 3.11: Decoding the Linking Math** Consider an instruction in **PROGB** at relative address `000054` (Absolute: `4063 + 0054 = 40B7`). The programmer wrote the expression `ENDA - LISTA + LISTC`.

- The assembler leaves the address field at `40B7` as `000000` because all terms are external.
- The loader processes three M-records for address `40B7`:
	 1. `+ENDA`: Adds `4054` to the memory word. (Value: `004054`)
	 2. `-LISTA`: Subtracts `4040` from the memory word. (Value: `000014`)
	 3. `+LISTC`: Adds `4124` to the memory word. (Final Value: `004138`).

**Real-World Reality:** This "patching" happens directly in the RAM where the program will execute. This is why the loader must have write-access to the code segment, even if that segment will later be marked "read-only" or "execute-only" by the operating system’s memory protection unit.

## V. Summary of Machine-Dependent Realities

As a senior engineer, you must recognize that while these algorithms are robust, they are bound by the machine's architecture. On the SIC/XE, relocation via **M-records** is preferred because it handles the 20-bit absolute addresses of Format 4 instructions with surgical precision. Conversely, systems without relative addressing (standard SIC) would find this algorithm too "heavy" due to the volume of M-records required, opting instead for **bitmasks**.

This algorithm and its associated data structures form the "final stage" of the software manufacturing process. Once Pass 2 of the linking loader completes its final modification, the program is no longer a collection of files—it is a live, executable process ready for the CPU. Mastering this section is the gateway to understanding advanced topics like **dynamic linking** and **automatic library search**, which we will explore in subsequent lectures.
