# **1. Core Functions and Responsibilities**

The primary role of an assembler is to translate assembly source code into an **object program**. This involves:

- **Mnemonic Translation:** Converting mnemonic operation codes (e.g., `LDA`) into their machine language equivalents (e.g., `00`) [Beck, 2.1].
- **Symbolic Operand Translation:** Converting symbolic labels (e.g., `RETADR`) into the actual machine addresses assigned to those memory locations [Beck, 2.1].
- **Constant Generation:** Converting data constants (e.g., `C'EOF'`) into the internal representation of the machine (e.g., `454F46` in hex) [Beck, 2.1].
- **Object Program Formatting:** Creating the final output in a specific record format (Header, Text, End) that a loader can understand [Beck, 2.1].

---

# **2. Assembler Directives (Pseudo-Instructions)**

Directives are commands specifically for the assembler. They **do not** generate machine instructions but provide metadata or reserve memory [Beck, 2.1].

- **`START`**: Defines the program name and its starting memory address (e.g., `COPY START 1000`) [Beck, Fig 2.1].
- **`END`**: Marks the end of the source and specifies the entry point (the first instruction to execute) [Beck, 2.1].
- **`BYTE`**: Generates a constant occupying a specific number of bytes (e.g., `EOF BYTE C'EOF'`) [Beck, 2.1].
- **`WORD`**: Generates a 3-byte integer constant [Beck, 2.1].
- **`RESB` / `RESW`**: Reserves a specific number of bytes or words. **Industry Insight:** These do not generate object code in the Text record; they simply tell the assembler to skip over a block of memory by incrementing the Location Counter [Beck, 2.1].

---

# **3. The Design Challenge: Forward References**

The most critical problem in assembler design is the **forward reference**. This occurs when a program jumps to a label that hasn't been defined yet (e.g., a `JMP` to a label 50 lines down) [Beck, 2.1]. Because the assembler doesn't know the address of that label during the first scan, it cannot complete the instruction.

**The Solution:** Most assemblers use a **two-pass logic** [Beck, 2.1].

---

# **4. The Two-Pass Assembler Algorithm (Figure 2.3)**

## **Pass 1: Defining Symbols**

- **Tasks:**
	 1. Assign addresses to all statements.
	 2. Save values assigned to labels in the **Symbol Table (SYMTAB)**.
	 3. Process directives like `RESB` and `RESW` to determine how much space to allocate.
- **The Location Counter (LOCCTR):** This internal variable tracks the "current" address. It starts at the address in the `START` statement. For every instruction, the assembler adds the instruction length (3 bytes for SIC) to the LOCCTR [Beck, 2.1].

## **Pass 2: Code Generation**

- **Tasks:**
	 1. Assemble instructions by looking up opcodes in **OPTAB** and operand addresses in **SYMTAB**.
	 2. Generate data constants (from `BYTE` and `WORD`).
	 3. Write the object program records and the assembly listing [Beck, 2.1].

---

# **5. Key Data Structures (Figure 2.4)**

- **OPTAB (Operation Code Table):** Predefined and static. It contains mnemonics, machine codes, and (in advanced machines) instruction formats. It is usually implemented as a **hash table** for near-instant lookup during Pass 2 [Beck, 2.1].
- **SYMTAB (Symbol Table):** Dynamic and built during Pass 1. It stores symbol names and their assigned addresses. It also tracks error flags, such as "Duplicate Symbol" if a label is defined twice [Beck, 2.1].

---

# **6. Detailed Example: Figure 2.1 and 2.2 (The COPY Program)**

Figure 2.1 shows the source for a program that copies a file. Let’s look at a specific line from **Figure 2.2** (the translation) to understand the process:

- **Source Line:** `10 1000 FIRST STL RETADR`
- **Explanation:**
	 - `1000` is the address (from `LOCCTR`).
	 - `STL` is the mnemonic. Looking it up in **OPTAB**, we find its hex code is `14`.
	 - `RETADR` is the operand. During Pass 1, the assembler scanned down and found `RETADR` at address `1033`.
	 - **Resulting Object Code:** `141033`. The first two digits are the opcode; the last four are the 15-bit address [Beck, Fig 2.2].

---

# **7. The Object Program Format (Figure 2.5)**

The assembler output is a series of ASCII records [Beck, 2.1]:

- **Header Record (H):** Contains Program Name, Start Address, and Total Length.
	 - _Exam Tip:_ Length is calculated as `Last Address - First Address`.
- **Text Record (T):** Contains the actual object code.
	 - _Technical Detail:_ A Text record starts with 'T', followed by the start address for that block of code, the length of the record (in hex), and the code itself. **Industry Insight:** Text records are limited in length (usually 60 hex characters) to fit into small buffers during loading [Beck, 2.1].
- **End Record (E):** Contains the 'E' flag and the address of the first executable instruction [Beck, 2.1].

# **Summary of Practical Insights**

1. **Instruction Length:** In SIC, every instruction is 3 bytes. If your LOCCTR starts at `1000`, the next instruction **must** be at `1003`. Failure to increment correctly is the #1 cause of assembly errors.
2. **Addressing:** The assembler manages the tedious task of binary address calculation. If you add a line of code in the middle of a program, the assembler automatically shifts all subsequent addresses; a human programmer would have to manually recalculate every jump in the entire file.

---

Figure 2.2 provides an **assembly listing** for the `COPY` program, showing the original source code alongside its translation into machine (object) code. The unique translations found in this figure can be categorized into four distinct types based on how the assembler processes the instructions and directives.

## **1. Standard Instruction Translation**

Most lines in the figure represent standard machine instructions where a mnemonic is translated into an opcode and a symbolic operand is translated into a memory address.

- **Mechanism:** The assembler looks up the mnemonic in **OPTAB** and the operand address in **SYMTAB**.
- **Example (Line 10):** `1000 FIRST STL RETADR` translates to **`141033`**.
	 - The opcode for `STL` is `14`.
	 - The address assigned to the label `RETADR` (found later in the listing at line 265) is `1033`.
	 - The assembler combines them to form the 3-byte object code `141033`.

## **2. Indexed Addressing Translation**

This unique translation occurs when the operand includes `,X`, signifying that the index register should be used for address calculation.

- **Mechanism:** In the standard SIC architecture, indexed addressing is indicated by setting the **index bit (x)** to 1. This bit is the most significant bit of the 15-bit address field in the instruction.
- **Example (Line 160):** `1051 LDCH BUFFER,X` translates to **`509039`**.
	 - The opcode for `LDCH` is `50`.
	 - The address for `BUFFER` is `1039`. In binary, this is `001 0000 0011 1001`.
	 - Setting the index bit (adding `8000` hex to the address) changes `1039` to `9039`.
	 - The final object code is `50` + `9039` = `509039`.

## **3. Data Generation (Constants)**

The directives `BYTE` and `WORD` instruct the assembler to generate specific data values directly into the object program rather than translating a mnemonic.

- **Character Constants (`BYTE C'…'`):**
	 - **Example (Line 250):** `105D EOF BYTE C'EOF'` translates to **`454F46`**. The assembler converts the characters 'E', 'O', and 'F' into their ASCII hexadecimal equivalents: `45`, `4F`, and `46`.
- **Hexadecimal Constants (`BYTE X'…'`):**
	 - **Example (Line 255):** `1060 THREE BYTE X'000003'` translates to **`000003`**. The hex value is copied directly into the object code.
- **Word Constants (`WORD`):**
	 - **Example (Line 260):** `1063 ZERO WORD 0` translates to **`000000`**. The decimal value `0` is converted into a 3-byte integer representation.

## **4. Memory Reservation (No Translation)**

The directives `RESB` (Reserve Byte) and `RESW` (Reserve Word) represent a unique case where **no object code is generated**.

- **Mechanism:** These lines are assigned an address (stored in SYMTAB), but they do not result in any hex code in the object program. The assembler simply increments the **Location Counter (LOCCTR)** by the specified amount (e.g., 3 bytes for `RESW 1` or 4096 bytes for `RESB 4096`) to "skip" that space in memory.
- **Example (Line 265):** `1066 RETADR RESW 1`. Note that the object code column for this line is **empty** because the loader will simply allocate this space at run-time.

## **Summary of Figure 2.2 Object Code Structure**

|Instruction Type|Translation Logic|Result Format|
|:--|:--|:--|
|**Simple**|Opcode + Address|6 hex digits|
|**Indexed**|Opcode + (Address + 8000 hex)|6 hex digits|
|**BYTE/WORD**|Convert value to Hex|Variable length|
|**RESB/RESW**|Increment LOCCTR only|No object code|

These translations demonstrate how the assembler manages the transition from symbolic logic to the literal byte sequences required by the hardware.
