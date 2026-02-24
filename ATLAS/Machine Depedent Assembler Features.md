# **1. Instruction Formats and Addressing Modes**

SIC/XE provides four instruction formats and a variety of flag bits ($n, i, x, b, p, e$) that an assembler must set correctly based on the source code.

- **Format Selection Logic:**
	 - **Format 1 & 2:** Used for register-to-register operations (e.g., `COMPR A, S`). No memory addresses are involved.
	 - **Format 3 (3-byte):** The default for memory instructions. It utilizes a **12-bit displacement ($disp$)**.
	 - **Format 4 (4-byte):** The **Extended Format**, indicated by a **plus (+) prefix** (e.g., `+JSUB`). It contains a **20-bit address** field.
- **Industry Insight:** Programmers and compilers prioritize Format 3 because it is 25% smaller than Format 4, leading to better cache utilization and smaller executable sizes. Format 4 is only used when the target address is outside the range of relative addressing.

## **A. PC-Relative Addressing ($p=1, b=0$)**

The assembler calculates the target address ($TA$) relative to the **Program Counter (PC)**.

- **The Formula:** $TA = (PC) + disp$ $\Rightarrow$ $disp = TA - (PC)$.
- **Displacement Range:** -2048 to +2047 (signed 12-bit integer).
- **Technical Execution Detail:** The "PC" used in the calculation is the address of the **next instruction** after the one currently being assembled.
- **Example from the Book:** At address `0000`, the instruction is `FIRST STL RETADR`. The next instruction starts at `0003`. If `RETADR` is at address `0030`, the assembler calculates $disp = 0030 - 0003 = 002D$. The instruction bits for $p$ are set to 1, and $b$ is set to 0.

## **B. Base-Relative Addressing ($p=0, b=1$)**

Used when the target address is too far for PC-relative displacement.

- **The Formula:** $TA = (B) + disp$ $\Rightarrow$ $disp = TA - (B)$.
- **Displacement Range:** 0 to 4095 (unsigned 12-bit integer).
- **Directive `BASE`:** This informs the assembler which address will be held in the **Base register (B)** during execution.
- **Technical Warning:** The `BASE` directive itself **does not generate any machine code**. The programmer must explicitly load the register using a machine instruction (like `LDB #LENGTH`) **before** the assembler can use it for calculations.

## **C. Immediate and Indirect Addressing**

- **Immediate (#):** The value is part of the instruction. If the value is a small constant (e.g., `LDA #3`), the assembler puts `3` directly in the $disp$ field.
- **Indirect (@):** The instruction points to a memory location that contains the _actual_ target address.

---

# **2. Program Relocation**

Relocation is the ability to load a program at any memory address, a fundamental requirement for **multiprogramming** where the OS must fit several programs into memory at once.

- **Absolute vs. Relative Values:**
	 - **Relative Terms:** Addresses that shift when the program is moved (e.g., labels for variables or jumps).
	 - **Absolute Terms:** Constants that never change (e.g., the value `4096` or hardware device codes).
- **The Modification Record (M-Record):** The assembler marks instructions that contain absolute addresses so the loader can update them at run-time.
	 - **Format:** `M^[starting address]^[length in half-bytes]`.
	 - **Example from Figure 2.8:** The instruction `+JSUB RDREC` at address `000006` contains the absolute address of `RDREC`. The assembler produces the record `M00000705`. This tells the loader: "Starting at the 7th half-byte (address 000007), take the next 5 half-bytes (20 bits) and add the actual starting address of the program to them".
- **Critical Technical Line:** Format 3 instructions **never** require Modification records because they use relative displacements. Since the distance between the instruction and the target is fixed, the code remains valid regardless of where the program is loaded in memory.

# **Summary Table for Exam Review (Addressing Flag Bit Logic)**

|Type|Prefix|n|i|x|b|p|e|Calculation|
|:--|:--|:--|:--|:--|:--|:--|:--|:--|
|**Immediate**|#|0|1|0|-|-|0/1|$TA = \text{Constant}$|
|**Indirect**|@|1|0|0|-|-|0/1|$TA = ((PC/B) + disp)$|
|**Simple**||1|1|0/1|0|1|0|$TA = (PC) + disp + (X)$|
|**Simple**||1|1|0/1|1|0|0|$TA = (B) + disp + (X)$|
|**Extended**|+|1|1|0/1|0|0|1|$TA = \text{Address} + (X)$|

This table represents the core logic used by the assembler to generate the 3-byte or 4-byte machine code found in the **Object Code** columns of the book's examples.
