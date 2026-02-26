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
