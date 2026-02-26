# The Architecture and Philosophy of Absolute Loaders

An **absolute loader** is the simplest design possible because it abdicates the responsibilities of relocation and linking. It assumes that every instruction in the object program is already assigned to a specific, fixed memory address that will not change. From an industry perspective, absolute loaders are rarely seen in modern general-purpose operating systems like Linux or Windows, which rely heavily on virtual memory and dynamic relocation. However, they remain critical in **embedded systems firmware**, microcontrollers, and the **bootstrap process** of a computer, where the initial code must reside at a hardcoded hardware address to begin the "wake-up" sequence of the machine.

The primary technical requirement for an absolute loader is that the object program must contain the exact physical address where each piece of code should be placed. The loader’s job is purely mechanical: it reads the object file and moves the bytes to the specified locations.

# Decoding the Object Program Structure (Figure 3.1)

To understand the loader, we must first decode the format of the object program it consumes. **Figure 3.1** provides a sample object program for a SIC machine. Each line in this file is a **record**, and each record begins with a type indicator.

- **The Header (H) Record:** This is the metadata for the program. In Figure 3.1, it reads `H^COPY ^001000^00107A`.
	 - **Field 1 (Col 1):** 'H' identifying the Header.
	 - **Field 2 (Col 2-7):** The Program Name (`COPY`).
	 - **Field 3 (Col 8-13):** The **Starting Address** in hexadecimal (`001000`).
	 - **Field 4 (Col 14-19):** The total **Length** of the program in bytes (`00107A`).
- **The Text (T) Record:** These records contain the actual machine instructions and data. A typical record looks like `T^001000^1E^141033^482039…`.
	 - **Field 1 (Col 1):** 'T' identifying the Text record.
	 - **Field 2 (Col 2-7):** The **Starting Address** for the object code in this specific record (`001000`).
	 - **Field 3 (Col 8-9):** The **Length** of the object code in this record, in bytes (`1E`, which is 30 in decimal).
	 - **Field 4 (Col 10-69):** The object code itself, represented in hexadecimal (each byte is two hex characters).
- **The End (E) Record:** This signals the end of the object program and specifies where execution should begin. In Figure 3.1, it is `E^001000`, meaning once the loader is finished, it should jump to address `1000` to start the program.

**Practical Reality:** In a real-life execution environment, the loader must be highly efficient at parsing these strings. Since the object code is stored as character strings (e.g., the byte `14` is stored as the ASCII characters '1' and '4'), the loader must perform a **hex-to-binary conversion** for every single byte it reads before storing it in memory.

# Step-by-Step Algorithm Walkthrough (Figure 3.2)

The logic for an absolute loader is elegantly simple, as illustrated in the algorithm in **Figure 3.2**. The process follows these exact steps:

1. **Read the Header Record:** The loader first checks the program name and length to ensure the program will fit into the available memory.
2. **Iterative Record Processing:** The loader enters a loop where it reads the next record from the object file.
3. **Type Checking:** If the record type is 'T' (Text), the loader extracts the starting address and the length.
4. **Byte-by-Byte Transfer:** For each byte of object code in the Text record:
	 - It converts the pair of hexadecimal characters into a single byte.
	 - It stores that byte into memory at the address calculated as `(Record Starting Address + Offset)`.
5. **Completion and Jump:** When the loader encounters an 'E' (End) record, the loop terminates. The loader then jumps to the address specified in the End record to transfer control to the loaded program.

One tiny but vital technical detail mentioned in the text is the handling of **memory gaps**. If an assembler uses a `RESB` or `RESW` directive, it reserves space but generates no object code. The absolute loader handles this naturally: because each Text record has its own starting address, the loader simply skips over the reserved addresses, leaving whatever was previously in memory there (or zeroing it out, depending on the system implementation).

# The Bootstrap Loader: Starting from "Bare Metal"

A fascinating architectural problem arises: if the loader is a program that brings other programs into memory, **who loads the loader?**. This is solved by the **Bootstrap Loader**.

When a computer is first turned on (a "cold start"), the main memory is empty or volatile. Some specialized hardware logic, or code stored in permanent **Read-Only Memory (ROM)**, must be invoked. On the SIC machine, the bootstrap loader is often placed at address 0. When the "Read" button or power switch is pressed, the machine is hardwired to begin executing at this address.

**Technical Execution of the Bootstrap:** The bootstrap loader is an extremely minimalist absolute loader. Its only task is to read the _actual_ Operating System loader from a fixed primary device (like a disk or tape) and place it into memory. Once the OS loader is in place, the bootstrap loader jumps to it, and the system "pulls itself up by its bootstraps"—hence the term "booting".

In the SIC implementation, the bootstrap loader often reads from device 'F1'. It must be written with extreme care to be as small as possible, <mark style="background: #FFF3A3A6;">as it often fits into a very limited amount of ROM or a specific hardware buffer.</mark> This section highlights the "hardware/software boundary" that every systems engineer must master to understand how a machine transitions from a hunk of silicon to a functional computing environment.
