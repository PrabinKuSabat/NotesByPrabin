---
title: "U-Boot from Zero"
subtitle: "A Beginner-First Handbook of Firmware, Device Trees, Kernel Handoff, Debugging, and Bootloader Design"
author: "OpenAI"
date: "20 August 2026"
lang: en
rights: "Created as an educational handbook. Consult platform documentation before modifying real hardware."
---

# Preface

## Who this book is for

This book is for a reader who can use a terminal and has seen a little C code, but has no prior knowledge of firmware, bootloaders, U-Boot, Device Tree, privilege levels, or Linux kernel startup.

It does not assume a particular board, processor vendor, or past project. Examples use common ARM64 and RISC-V ideas because those architectures make the firmware layers visible, but the mental model applies more broadly.

## What this book can and cannot do

After studying the early chapters and completing the labs, you should be able to:

- explain the boot chain from reset to the first userspace program;
- use a U-Boot prompt without treating commands as magic;
- load a kernel, optional initramfs, and Device Tree into RAM;
- explain what <code>booti</code>, <code>bootm</code>, and a kernel handoff actually do;
- inspect and make controlled edits to a Device Tree;
- isolate a failure to a specific boot boundary;
- read the important parts of a U-Boot board port;
- design a small educational loader for your own kernel.

The book cannot make someone an independent production firmware engineer by reading alone. A full board port requires exact SoC manuals, board schematics, boot-ROM rules, memory-controller knowledge, toolchains, hardware access, serial output, and repeated debugging. Vendor DRAM training code or security firmware may also be unavailable. Those are engineering inputs, not gaps that a generic text can guess.

## The three competence levels

| Level | Practical claim | Evidence you should have |
|---|---|---|
| 1. Understand | “I can explain an existing boot.” | You can label every stage, artifact, address, and handoff in one captured boot log. |
| 2. Operate and debug | “I can use U-Boot and isolate common boot failures.” | You have manually loaded artifacts, inspected memory and DT data, changed one variable at a time, and recovered from errors. |
| 3. Modify or port | “I can change U-Boot for a board.” | You have built the exact source, traced initialization, changed code or configuration, tested on hardware, and documented rollback. |

Do not skip directly to Level 3 vocabulary. A beginner learns faster by making one complete boot understandable before exploring every subsystem.

## How to use the book

Read Parts I–III in order. Then complete Labs 1–6. Parts IV–VI become much easier after you have seen one boot in practice.

Each chapter uses four labels:

- **Mental model** — the simplest accurate picture.
- **Mechanism** — what software and hardware actually do.
- **Boundary evidence** — what proves control reached or crossed a stage.
- **Beginner trap** — a common but misleading conclusion.

Commands are examples. Addresses, device numbers, filenames, UART settings, and partition numbers are board-specific unless explicitly described as generic.

# Part I — Foundations

# 1. What “booting” means

## 1.1 The central problem

When power is first applied, RAM does not already contain Linux. The processor cannot search a filesystem, understand an ext4 directory, or decide which file is a kernel without software that teaches it how.

Booting is the staged process that turns a minimally initialized machine into a running operating system. Each stage begins with only a limited set of resources, prepares more of the machine, locates the next program, and transfers control to it.

The shortest useful model is:

**Reset → immutable firmware → early loader → runtime firmware → U-Boot → kernel → userspace**

Not every platform contains every named stage. A small microcontroller may start one application directly from flash. A PC commonly uses UEFI and perhaps GRUB. An ARM system may use Boot ROM, U-Boot SPL, Trusted Firmware-A, U-Boot proper, and Linux. A RISC-V system may use Boot ROM, an early loader, OpenSBI, U-Boot, and Linux.

## 1.2 Control is not a physical object

“Passing control” means changing the processor’s program counter so that its next instruction comes from a new program’s entry address. Before the branch or exception return, the old stage must also establish the state promised by the next stage’s calling convention:

- the correct CPU execution mode or privilege level;
- valid RAM containing the next image;
- required register arguments;
- a stack if the new stage expects one;
- coherent instruction and data caches;
- an allowed interrupt state;
- a valid hardware-description pointer;
- ownership or quiescence of devices that must not keep changing memory.

The branch is one instruction. Preparing a valid handoff is most of the work.

## 1.3 Loading, authenticating, and starting are different

A boot stage may perform three separate operations:

1. **Load** — copy bytes from flash, eMMC, SD, USB, network, or another location into RAM.
2. **Validate or authenticate** — check format, checksum, cryptographic signature, version, or policy.
3. **Start** — establish the required machine state and jump to the entry point.

Successful loading does not prove that an image is valid. Successful validation does not prove that its load address is safe. A printed “Starting kernel” proves that the loader reached its last visible boundary; it does not prove that the kernel executed a visible instruction.

## 1.4 Cold boot, warm boot, and reset

A cold boot begins from power-on or a state close to it. A warm reset may leave some devices, RAM, clocks, or security state different from a true power cycle. Good debugging records which reset was used. A bug that appears only after warm reset often points to state that was not reinitialized.

## 1.5 Boundary evidence

Useful evidence is tied to a boundary:

| Boundary | Strong evidence |
|---|---|
| Reset → Boot ROM | vendor-defined boot-mode behavior, ROM error code, ROM UART or recovery enumeration |
| ROM → early loader | early loader’s first unique UART marker or debugger PC at its entry |
| early loader → U-Boot | U-Boot banner and build identity |
| U-Boot → kernel | final U-Boot message plus the kernel’s earliest independently generated marker |
| kernel → userspace | init process output, a shell, or a recorded system state |

**Beginner trap:** “The screen is black, so the bootloader crashed.” A black display only proves the display path is not presenting new pixels. The CPU may be running elsewhere, blocked, reset, or printing to a serial console.

# 2. Hardware concepts needed before U-Boot

## 2.1 CPU, program counter, registers, and privilege

The CPU repeatedly fetches an instruction from the address in its program counter, executes it, and advances or branches. Registers are very small, fast storage locations used for values and pointers.

Modern processors have privilege levels. Higher-privilege firmware configures resources that lower-privilege software cannot directly control.

Typical examples:

| Architecture | Common levels in a boot chain |
|---|---|
| AArch64 | EL3 secure monitor, EL2 hypervisor, EL1 kernel, EL0 application |
| RISC-V | M-mode machine firmware, S-mode supervisor/kernel, U-mode application |
| x86-64 | firmware environment followed by ring 0 kernel and ring 3 applications |

U-Boot’s exact level depends on the platform. On a RISC-V Linux system, OpenSBI commonly remains in M-mode and enters U-Boot or Linux in S-mode. On an ARM64 system, Trusted Firmware-A commonly provides EL3 runtime services while U-Boot runs as non-secure BL33.

## 2.2 ROM, SRAM, DRAM, and non-volatile storage

- **ROM** is immutable or effectively immutable code, commonly containing the first instructions.
- **SRAM** is small on-chip RAM that works before the external DRAM controller is initialized.
- **DRAM** is the large main memory used by U-Boot, the kernel, and applications. It normally requires controller setup and training.
- **Non-volatile storage** retains bytes without power: NOR flash, NAND flash, eMMC, SD card, NVMe, or similar media.

An early loader is often tiny because it must fit in SRAM. Its most important job may be to make DRAM reliable, after which a much larger U-Boot image can run.

## 2.3 Memory-mapped I/O

Many devices expose control and status registers at physical addresses. Firmware reads and writes these addresses to configure UARTs, clocks, GPIO, storage controllers, interrupt controllers, and timers. This is called memory-mapped I/O, or MMIO.

A normal pointer dereference to an MMIO address can cause a hardware action. Incorrect addresses can hang the interconnect or reset the system. Device manuals specify register widths, ordering, reserved bits, and required delays.

## 2.4 Clocks, reset lines, pin multiplexing, and power domains

A UART driver can be correct and still print nothing if:

- its clock is disabled or running at an unexpected rate;
- the UART is held in reset;
- the physical pins are configured for GPIO instead of UART;
- the relevant power domain is off;
- the selected UART is not wired to the connector.

Firmware bring-up therefore proceeds from prerequisites outward:

**power → clock → reset release → pinmux → controller configuration → device use**

## 2.5 Serial console

A serial console is the most valuable early-boot interface because it is simple and can work before graphics, USB, filesystems, or networking. Common board UARTs use 3.3 V TTL logic, not the voltage levels of classic RS-232.

Three wires are commonly enough:

- board TX → adapter RX;
- board RX → adapter TX;
- ground → ground.

Never connect an adapter’s power pin unless the board documentation explicitly requires it. Verify voltage and pinout. A common UART configuration is 115200 baud, 8 data bits, no parity, 1 stop bit, but the board’s documentation is authoritative.

## 2.6 Caches and the MMU

Caches keep copies of memory to reduce latency. The memory-management unit translates virtual addresses and applies permissions. A loader may copy a kernel into memory using cached writes. Before executing that memory, it must perform the architecture-required cache maintenance and instruction synchronization.

The kernel’s boot protocol also defines whether the MMU and caches must be on or off. “Jump to the address” is unsafe if the surrounding machine state violates that protocol.

# 3. Firmware stages before U-Boot

## 3.1 Boot ROM

Boot ROM is code supplied by the SoC vendor. The processor begins at a defined reset vector, which maps to ROM or another vendor-defined location. ROM typically:

- samples boot-mode straps, fuses, or registers;
- selects a boot source;
- initializes only the controller needed to read an early image;
- reads an image header or fixed storage region;
- may authenticate the image;
- loads it into SRAM or executes it in place;
- transfers control according to a vendor-specific contract.

Boot ROM normally cannot be replaced. To understand it, use the SoC technical reference manual or boot user guide, not a generic U-Boot document.

## 3.2 TPL, VPL, and SPL

U-Boot can be divided into small stages when the main binary is too large for what ROM can load. Current upstream documentation describes optional TPL, VPL, and SPL phases before U-Boot proper:

- **TPL**, when used, performs extremely early initialization and loads a later phase.
- **VPL** is intended for a verifying and selection phase in verified A/B designs.
- **SPL** commonly initializes DRAM and loads U-Boot proper or other firmware.
- **U-Boot proper** provides the full command environment and OS-loading policy.

These names describe U-Boot components, but vendor documentation may use “BL2,” “FSBL,” “MLO,” “preloader,” or another name for comparable roles. Names are not universal; responsibilities and handoff contracts matter more.

See the upstream [TPL/SPL boot-phase documentation](https://docs.u-boot.org/en/latest/usage/spl_boot.html).

## 3.3 Runtime firmware

Some privileged services must remain available after the bootloader finishes.

On ARM64, Trusted Firmware-A may install an EL3 runtime monitor. Its conventional names include BL31 for the EL3 runtime, optional BL32 for a secure payload, and BL33 for non-secure firmware such as U-Boot. The exact chain varies by SoC. The [Trusted Firmware-A firmware design](https://trustedfirmware-a.readthedocs.io/en/stable/design/firmware-design.html) documents the reference model.

On RISC-V, OpenSBI commonly runs in M-mode and exposes the Supervisor Binary Interface to software in S-mode. It can be packaged as:

- <code>FW_DYNAMIC</code>, where the previous stage supplies information about the next stage;
- <code>FW_JUMP</code>, which jumps to a fixed next-stage address;
- <code>FW_PAYLOAD</code>, which includes the next-stage binary.

The official [OpenSBI platform firmware documentation](https://github.com/riscv-software-src/opensbi/blob/master/docs/firmware/fw.md) defines these modes.

## 3.4 U-Boot is not always the first programmable stage

On many modern boards, replacing only <code>u-boot.bin</code> cannot repair a problem in:

- ROM image packaging;
- SRAM placement;
- DRAM training;
- security-controller firmware;
- clock initialization;
- Trusted Firmware-A;
- OpenSBI;
- a vendor system controller.

Always inventory the actual images and who loads each one.

## 3.5 A boot-chain inventory

Before changing software, create a table like this:

| Order | Stage | Stored where | Runs from | Privilege | Loads next | First observable marker |
|---|---|---|---|---|---|---|
| 1 | Boot ROM | SoC ROM | ROM/SRAM | highest/vendor | early image | ROM recovery behavior |
| 2 | early loader | boot media | SRAM | platform-specific | firmware/U-Boot | early UART tag |
| 3 | runtime firmware | boot media/package | SRAM/DRAM | EL3 or M-mode | U-Boot | firmware banner |
| 4 | U-Boot | boot media/package | DRAM | EL2/EL1 or S-mode | kernel | U-Boot banner |
| 5 | kernel | filesystem/raw/FIT | DRAM | kernel level | userspace | early console |

If any cell is unknown, mark it “unknown.” Do not fill a gap with a guess.

# Part II — U-Boot as a Program

# 4. What U-Boot is and is not

## 4.1 Definition

Das U-Boot is an open-source bootloader widely used in embedded systems. It is a configurable program and ecosystem that can:

- initialize board hardware not handled earlier;
- expose storage, network, USB, console, and other drivers;
- read partition tables and filesystems;
- load images into RAM;
- select a boot target and configuration;
- edit the working Device Tree;
- verify signed images when configured;
- establish the operating system’s required entry state;
- transfer control to an OS, hypervisor, firmware, or application;
- provide recovery and manufacturing commands.

It is not a complete operating system. It normally has no protected multi-process userspace, no general virtual memory environment like Linux, and no responsibility after a successful non-returning kernel handoff.

## 4.2 U-Boot versus “a boot script”

A boot script is data interpreted by U-Boot. It can sequence commands such as selecting an MMC device, loading files, and running <code>booti</code>. The script is not itself a hardware-initializing bootloader.

Similarly, an environment variable such as <code>bootcmd</code> stores policy. The compiled U-Boot code provides the commands, drivers, parsers, image handlers, and architecture-specific handoff.

## 4.3 U-Boot proper and SPL

SPL and U-Boot proper may be built from the same source tree but solve different constraints:

| Property | SPL | U-Boot proper |
|---|---|---|
| Typical memory | small SRAM, then DRAM | DRAM |
| Primary purpose | initialize enough hardware and load next stage | select, load, validate, and start OS |
| Size pressure | severe | lower, but still important |
| Commands | few or none | interactive command set |
| Driver coverage | only boot-critical subset | broader configured set |

## 4.4 Configuration is part of the program

Two U-Boot binaries built from the same release can behave very differently because Kconfig options, Device Trees, environment defaults, board code, binary blobs, and image packaging differ.

When reporting a build, record:

- upstream or vendor repository URL;
- exact commit ID;
- board defconfig;
- toolchain identity;
- build command;
- important configuration differences;
- external firmware or binary blobs;
- generated artifact names and hashes;
- storage placement.

“U-Boot 2025.x” alone is not a reproducible identity.

# 5. How U-Boot starts

## 5.1 Entry state

U-Boot begins at an entry point selected by the previous stage. Its earliest assembly cannot assume that a C runtime already exists. Depending on the platform, it may need to:

- establish a temporary stack;
- clear the uninitialized-data section;
- set exception or trap vectors;
- identify the boot CPU;
- make caches and address translation safe;
- access a control Device Tree;
- initialize enough console support for early messages.

The exact order is architecture- and board-specific.

## 5.2 Pre-relocation and post-relocation

U-Boot often starts at the address where an earlier stage placed it, performs minimal initialization, then relocates itself to a selected region of DRAM. Relocation allows U-Boot to run from a stable address while leaving useful RAM regions available for loaded images.

Conceptually:

1. early assembly establishes a usable execution environment;
2. pre-relocation C code discovers RAM and essential devices;
3. U-Boot reserves memory for itself and related structures;
4. code and data are copied or adjusted for the final location;
5. execution continues in the relocated image;
6. the full driver model, environment, console, and main loop become available.

A driver needed before relocation may require special configuration or pre-relocation properties. A device working at the normal prompt does not prove it was available to SPL or early U-Boot.

## 5.3 Global data

U-Boot keeps central runtime state in a structure commonly called global data. It tracks items such as flags, RAM information, relocation addresses, console state, environment state, and references to Device Tree or driver-model data. Architecture conventions often reserve a register or another efficient mechanism for accessing it during early execution.

A beginner does not need to memorize the structure. The important lesson is that early firmware cannot rely on ordinary global variables exactly as a normal Linux application does; its runtime environment is still being constructed.

## 5.4 Driver model

Modern U-Boot uses a driver model that separates:

- a **uclass**, the common interface for a type of device, such as GPIO or I2C;
- a **driver**, code that implements the interface for a hardware design;
- a **device**, one concrete instance associated with hardware configuration.

“Bind” associates a configuration node with a driver. “Probe” performs initialization needed for use. A bound device need not yet be probed.

The upstream [driver-model design documentation](https://docs.u-boot.org/en/latest/develop/driver-model/design.html) includes a sandbox example and detailed lifecycle rules.

## 5.5 Main loop and autoboot

After initialization, U-Boot obtains boot policy from compiled defaults, persistent environment, Standard Boot, board logic, or a combination. It may count down, execute <code>bootcmd</code>, scan boot targets, or stop at the prompt.

At the prompt, the CPU is still running U-Boot. Files have not magically become memory; commands explicitly cause device probing, reads, validation, memory changes, and control transfer.

# 6. The U-Boot prompt and environment

## 6.1 Start with read-only discovery

The safest first commands are:

~~~~text
help
version
bdinfo
printenv
env info
dm tree
~~~~

Availability depends on the build configuration.

- <code>version</code> identifies the build.
- <code>bdinfo</code> shows board and memory information.
- <code>printenv</code> displays environment variables.
- <code>env info</code> reports environment state in builds that support it.
- <code>dm tree</code> displays driver-model devices.

Do not begin with erase, write, partition-modifying, fuse, or environment-save commands.

## 6.2 Environment variables

An environment variable is a string with a name. Examples include addresses, filenames, boot arguments, and command sequences.

~~~~text
env print bootcmd
env set demo_value 1234
env print demo_value
env delete demo_value
~~~~

Changes made by <code>env set</code> usually affect the current session. <code>env save</code> writes the environment to its configured persistent backend and is therefore a mutation. On some boards, an incorrect saved environment can prevent normal boot.

Important distinction:

- **compiled default environment** is part of the built software;
- **active environment** is the in-memory set currently being used;
- **persistent environment** is stored in flash, eMMC, a file, or another backend;
- **variables imported by a script or boot method** may be temporary.

## 6.3 Numbers are commonly hexadecimal

Many U-Boot commands interpret addresses and sizes as hexadecimal. For example, <code>100000</code> commonly means 0x100000, not decimal one hundred thousand.

Always write units and base in notes:

| Text | Meaning |
|---|---|
| <code>0x80000000</code> | hexadecimal physical address |
| <code>0x02000000</code> | 32 MiB size |
| <code>33554432</code> | the same size in decimal |

## 6.4 Command success and failure

Commands set a return status, often visible through <code>$?</code>. Scripts can use conditional execution. A printed message is not necessarily success; check the command’s documented return behavior and subsequent state.

The <code>bootd</code> command runs the command stored in <code>bootcmd</code>, as described in the official [bootd documentation](https://docs.u-boot.org/en/latest/usage/cmd/bootd.html).

## 6.5 Memory inspection

Common commands include:

~~~~text
md.b ADDRESS LENGTH
md.l ADDRESS COUNT
mw.l ADDRESS VALUE COUNT
cmp.b ADDRESS1 ADDRESS2 LENGTH
crc32 ADDRESS LENGTH
~~~~

<code>md</code> reads memory. <code>mw</code> writes memory and is dangerous if the region contains U-Boot, firmware, stacks, Device Trees, loaded images, or MMIO. Never experiment with a guessed address.

## 6.6 Resetting changes the experiment

If a command mutates the active Device Tree, environment, or RAM, record it. A reset may restore compiled defaults, reload persistent environment, or reinitialize hardware. “It worked once” is weak evidence unless the entire starting state is reproducible.

# 7. Storage, partitions, filesystems, and loading

## 7.1 Four different layers

Beginners often combine these layers:

1. **controller/interface** — MMC, USB mass storage, SATA, NVMe, SPI flash;
2. **device** — a specific card or drive, such as MMC device 0;
3. **partition table** — MBR or GPT divides a device;
4. **filesystem** — FAT, ext4, or another format stores named files inside a partition.

A raw firmware image may live at a fixed byte offset outside any filesystem. A kernel may be a normal file inside an ext4 partition. These are different loading paths.

## 7.2 Device and partition notation

Notation such as <code>0:1</code> often means device 0, partition 1 for a particular interface. It does not mean “the first USB partition” universally. Device numbering depends on enumeration and the command.

Discover before loading:

~~~~text
mmc list
mmc dev 0
part list mmc 0
ls mmc 0:1 /
~~~~

Equivalent commands exist for USB and other interfaces if compiled in.

## 7.3 Loading copies bytes into RAM

A filesystem load command conceptually takes:

**interface + device:partition + destination RAM address + path**

Example:

~~~~text
load mmc 0:1 0x90000000 /boot/Image
~~~~

After success, the file’s bytes are in RAM at 0x90000000. U-Boot commonly updates variables such as <code>filesize</code> and <code>fileaddr</code>. The example address is not safe for every board.

## 7.4 Network loading

TFTP or HTTP can accelerate development because storage media need not be rewritten for every test. It adds dependencies: link state, MAC configuration, IP addressing, server paths, firewalls, and network drivers.

Use network loading only after the console, RAM, and network device are independently known to work. Keep a local recovery path.

## 7.5 Raw reads

Commands such as <code>mmc read</code>, <code>sf read</code>, or NAND operations use blocks or offsets rather than filenames. Units vary by command. Confusing bytes, blocks, sectors, and erase blocks can corrupt firmware or load the wrong range.

For every raw operation record:

- medium and device;
- offset and its unit;
- length and its unit;
- destination/source address;
- expected image identity;
- recovery method.

## 7.6 Preventing memory overlap

The kernel, initramfs, Device Tree, decompression workspace, U-Boot itself, firmware reservations, stacks, and DMA buffers must not overlap improperly.

Use <code>bdinfo</code>, the board’s documented DRAM map, <code>printenv</code>, and image sizes. Draw an interval table:

| Object | Start | End | Owner | May move? |
|---|---:|---:|---|---|
| U-Boot reservation | known from runtime | known from runtime | U-Boot | no |
| kernel image | chosen safe address | start + file size | boot flow | maybe |
| initramfs | chosen safe address | start + size | boot flow | maybe |
| working DTB | chosen safe address | start + expanded size | boot flow | maybe |

If two intervals overlap, do not boot until the behavior is intentional and documented.

# Part III — Images, Device Trees, and the Kernel Handoff

# 8. Kernel and boot image formats

## 8.1 A filename is not a format

Names such as <code>Image</code>, <code>zImage</code>, <code>uImage</code>, <code>fitImage</code>, and <code>vmlinuz</code> suggest conventions but are not a sufficient inspection method. Use build documentation and tools such as <code>file</code>, <code>dumpimage</code>, <code>mkimage -l</code>, <code>readelf</code>, or architecture-specific header inspection.

## 8.2 Raw Linux Image

On ARM64 and RISC-V, the kernel build commonly produces a flat <code>Image</code> with an architecture-defined header and entry behavior. U-Boot’s <code>booti</code> command is designed for a flat or supported compressed Linux <code>Image</code>.

Its documented syntax is:

~~~~text
booti [kernel_address [initrd_address:size] [fdt_address]]
~~~~

Use a hyphen for the initramfs argument when there is no initramfs but a DTB is supplied:

~~~~text
booti KERNEL_ADDRESS - FDT_ADDRESS
~~~~

The current upstream [booti command documentation](https://docs.u-boot.org/en/latest/usage/cmd/booti.html) describes format and decompression requirements.

## 8.3 Legacy uImage

The legacy U-Boot image format wraps an image with a header containing metadata and a checksum. It is still found on older systems, but upstream documentation describes it as limited and insecure for modern use.

The host tool <code>mkimage</code> can create legacy images. Do not add a legacy header simply because a boot failed; the command, architecture, format, load address, and entry address must agree.

## 8.4 FIT: Flattened Image Tree

A FIT image can package multiple kernels, Device Trees, ramdisks, firmware components, hashes, signatures, and configurations. It uses a Device Tree representation to describe image components and their relationships.

A FIT configuration answers: “Which kernel, DTB, optional ramdisk, and firmware belong together?” This supports multi-board products and signed boot policy more cleanly than a loose set of files.

Upstream recommends FIT for <code>bootm</code> use. See the [FIT source format](https://docs.u-boot.org/en/latest/usage/fit/source_file_format.html) and [bootm documentation](https://docs.u-boot.org/en/latest/usage/cmd/bootm.html).

## 8.5 EFI applications and unified kernel images

U-Boot can implement UEFI services and start EFI applications when configured. In such a boot, U-Boot may start GRUB, systemd-boot, an EFI-stub Linux kernel, or a unified kernel image instead of using <code>booti</code> directly.

This changes the interface and policy layer but not the fundamental work: locate an image, establish trust and configuration, load it into valid memory, satisfy its entry contract, and transfer control.

## 8.6 Initramfs

An initramfs is an archive unpacked into an initial RAM-backed root filesystem by the kernel. It can provide early userspace tools and an <code>/init</code> program before a persistent root filesystem is mounted.

The bootloader’s job is to place the initramfs safely in memory and communicate its range using the architecture’s boot data, commonly through Device Tree chosen-node properties or another boot protocol.

If the kernel starts but reports that it cannot find or execute init, the U-Boot-to-kernel handoff probably succeeded. Debug the initramfs content, kernel configuration, command line, or root filesystem instead of changing ROM or SPL.

## 8.7 Image inspection checklist

Before booting, answer:

- What is the exact format?
- For which architecture and endianness was it built?
- Is it compressed? Who decompresses it?
- What is its load address?
- What is its entry address?
- How large is the loaded form, not just the compressed file?
- Does it require a DTB, initramfs, EFI environment, or other metadata?
- Is it authenticated, checksummed, or neither?

# 9. Device Tree from first principles

## 9.1 The problem Device Tree solves

The kernel contains drivers, but it still needs to know which devices exist in this particular system, where their registers are, which interrupts they use, which clocks and reset lines feed them, and how devices are connected.

A Device Tree is structured data describing non-discoverable hardware and firmware-provided configuration. It is not executable driver code. It does not initialize a device by itself.

## 9.2 DTS, DTSI, DTB, and overlay

| Term | Meaning |
|---|---|
| DTS | Human-readable Device Tree Source for a board |
| DTSI | Source fragment included by other Device Tree sources |
| DTB | Flattened binary blob consumed at boot |
| DTBO | Overlay blob containing changes to apply to a base tree |
| DTC | Device Tree Compiler, commonly used for source/binary conversion |

The [Devicetree Specification](https://devicetree-specification.readthedocs.io/en/latest/) defines the data model and flattened format. Linux-specific bindings add device requirements beyond the base specification.

## 9.3 Nodes and properties

A simplified DTS fragment:

~~~~dts
/dts-v1/;

/ {
    compatible = "example,learning-board";
    #address-cells = <2>;
    #size-cells = <2>;

    chosen {
        stdout-path = "serial0:115200n8";
        bootargs = "console=ttyS0,115200";
    };

    aliases {
        serial0 = &uart0;
    };

    memory@80000000 {
        device_type = "memory";
        reg = <0x0 0x80000000 0x0 0x20000000>;
    };

    uart0: serial@10000000 {
        compatible = "ns16550a";
        reg = <0x0 0x10000000 0x0 0x100>;
        clock-frequency = <3686400>;
        current-speed = <115200>;
        status = "okay";
    };
};
~~~~

This is educational, not a board-ready tree. The value and number of cells in <code>reg</code> depend on the parent’s <code>#address-cells</code> and <code>#size-cells</code>. Interrupts, clocks, resets, pinctrl, DMA, IOMMU, power domains, and bus translation commonly require references to provider nodes.

## 9.4 compatible and bindings

The <code>compatible</code> property is an ordered list of identifiers. A driver uses it to match hardware. The associated binding specifies required and optional properties and their meaning.

Do not invent a compatible string that “looks close.” A driver may match but program incompatible registers. Use the exact binding and hardware identity.

Linux maintains many bindings as YAML schemas. A robust workflow validates DTS against them with kernel build targets such as <code>dt_binding_check</code> and <code>dtbs_check</code>, using the kernel version and tool requirements documented by the source tree.

## 9.5 Phandles and dependency graphs

A Device Tree is visually hierarchical but functionally a graph. A UART node may reference:

- a clock provider;
- a reset controller;
- a pin controller state;
- an interrupt controller;
- a power domain.

Deleting an apparently unrelated provider can break a kept consumer. This is the central reason that “minimal DTB” cannot be produced safely by deleting every node for an unused peripheral.

## 9.6 The chosen node

The <code>/chosen</code> node communicates boot-time information rather than describing a physical device. Common examples include:

- kernel command line in <code>bootargs</code>;
- console selection in <code>stdout-path</code>;
- initramfs start and end;
- firmware-generated seeds or boot measurements, depending on platform.

U-Boot may create or update these properties just before boot.

## 9.7 reserved-memory

The <code>/reserved-memory</code> subtree describes RAM that general kernel allocation must not use. Reasons include firmware runtime data, secure-world memory, DMA pools, remote processors, framebuffers, or crash logs.

Removing a reservation to make a DT “smaller” can allow Linux to overwrite live firmware memory. Minimality is not measured by the fewest nodes; correctness and explicit ownership come first.

# 10. The two Device Trees in U-Boot

## 10.1 Control FDT

U-Boot itself may use a Device Tree to discover and configure its drivers. This is the **control FDT**. With <code>CONFIG_OF_CONTROL</code>, it can be embedded, appended, separated, or otherwise packaged according to the board build.

The control FDT answers U-Boot’s own questions: which UART, MMC controller, GPIO, clocks, and other devices should U-Boot bind?

See [Devicetree Control in U-Boot](https://docs.u-boot.org/en/latest/develop/devicetree/control.html).

## 10.2 Working FDT

The **working FDT** is the tree U-Boot prepares for the operating system. It may begin as a DTB loaded from storage, extracted from FIT, selected by a boot method, or derived from another source. U-Boot can modify it before passing its address to the kernel.

The control and working FDT may contain similar hardware descriptions, but they serve different consumers. Editing the working FDT does not necessarily change the tree U-Boot already used to bind its own drivers.

## 10.3 Does U-Boot generate its own DTB?

The accurate answer has several cases:

1. The build system can compile DTS/DTSI sources into a DTB used by U-Boot.
2. Firmware or a previous stage may supply an FDT.
3. U-Boot may select one DTB from several candidates.
4. U-Boot may copy, resize, edit, overlay, or fix up a working FDT.
5. Some platforms can synthesize portions of hardware description from platform data or firmware interfaces.

U-Boot does **not generally discover arbitrary embedded hardware and generate a complete correct Linux DTB from nothing**. Non-discoverable wiring, regulator relationships, clocks, reset topology, pinmux, and board variants must come from authoritative platform knowledge.

## 10.4 Inspecting and editing with the fdt command

A typical safe sequence is:

~~~~text
fdt addr FDT_ADDRESS
fdt header
fdt print /
fdt print /chosen
~~~~

Before adding data, the blob may need extra writable space:

~~~~text
fdt resize 4096
fdt set /chosen bootargs "console=ttyS0,115200"
fdt print /chosen
~~~~

Exact subcommands depend on the build. The upstream [fdt command documentation](https://docs.u-boot.org/en/latest/usage/cmd/fdt.html) distinguishes the working tree from the control tree.

Do not modify a DTB in place until you have preserved the original and confirmed adequate space. A property can be syntactically valid but semantically wrong.

## 10.5 Typical boot-time fixups

U-Boot or platform firmware may update:

- detected RAM size and banks;
- serial number, MAC address, or board revision;
- <code>/chosen/bootargs</code>;
- initramfs range;
- reserved-memory regions;
- enabled or disabled peripherals;
- display or firmware-owned framebuffer information;
- measured-boot event log pointers.

Because the final tree may differ from the file on storage, debugging should capture the DTB immediately before kernel entry when possible.

# 11. Building a minimal Device Tree safely

## 11.1 Define “minimal”

Possible goals conflict:

- smallest DTB file;
- fewest enabled devices;
- shortest boot time;
- smallest kernel driver set;
- least attack surface;
- sufficient hardware for a rescue shell;
- sufficient hardware for a product workload.

State the goal and acceptance tests first. A useful learning target is:

> One CPU description, correct RAM, timer and interrupt path, firmware interface, one serial console, and one root-filesystem path, with all dependencies required by those components.

Even that list is architecture- and platform-specific.

## 11.2 Start from a known-good tree

The safest reduction method is:

1. obtain a DTB that boots the exact board with the exact firmware/kernel family;
2. preserve it and its source identity;
3. decompile only for inspection if source is unavailable;
4. map the required boot path and dependency graph;
5. disable one leaf device at a time;
6. compile and schema-check;
7. boot and capture complete logs;
8. compare behavior against the baseline;
9. keep a recovery medium.

Do not write a board DT from an empty file unless the hardware documentation is complete and the goal is explicitly a board port.

## 11.3 Disable before deleting

Setting <code>status = "disabled"</code> on a leaf device is usually easier to review and reverse than deleting the node. Providers shared by active devices must remain. A later cleanup can remove proven-unused definitions if size truly matters.

## 11.4 The dependency closure

For every required device:

1. locate its node;
2. read its binding;
3. list referenced clocks, resets, interrupts, GPIOs, regulators, power domains, pinctrl states, DMA channels, IOMMUs, and buses;
4. recursively retain their providers;
5. retain required aliases and chosen properties;
6. retain firmware and reserved-memory contracts.

The resulting closure, not the visible leaf list, is the minimal correct graph.

## 11.5 Host-side tools

Common inspection commands:

~~~~bash
dtc -I dtb -O dts -o inspected.dts board.dtb
dtc -I dts -O dtb -o rebuilt.dtb board.dts
fdtdump board.dtb
fdtget board.dtb / compatible
fdtdiff baseline.dtb candidate.dtb
~~~~

Tool availability and output differ by distribution. Decompilation does not reproduce original includes, labels, comments, or source organization.

## 11.6 Validation is layered

| Layer | Question |
|---|---|
| DTC syntax | Can the source be represented as a valid blob? |
| Schema | Do nodes follow documented bindings? |
| U-Boot inspection | Can the loader find and edit the intended tree? |
| Kernel parse | Does Linux accept the FDT and enumerate expected devices? |
| Functional test | Do console, storage, interrupts, clocks, and workload behave correctly? |
| Reboot test | Do cold and warm boots remain reliable? |

Passing DTC alone proves only structural encodability.

# 12. The Linux kernel handoff

## 12.1 What U-Boot must finish

Immediately before entry, U-Boot typically:

1. identifies and validates the kernel image;
2. decompresses or relocates it if required;
3. places the initramfs safely, if used;
4. prepares and finalizes the working FDT or other boot parameters;
5. performs architecture-specific cleanup;
6. synchronizes caches and executable memory;
7. quiesces or hands off devices as required;
8. places arguments in defined registers;
9. branches or exception-returns to the kernel entry point.

Which exact operations are required comes from the kernel boot protocol and platform firmware contract.

## 12.2 RISC-V Linux

The current Linux [RISC-V boot requirements](https://docs.kernel.org/arch/riscv/boot.html) define constraints for firmware and bootloaders. In the common direct DT boot convention:

- <code>a0</code> contains the hart ID of the boot CPU;
- <code>a1</code> contains the physical address of the Device Tree;
- the kernel enters in supervisor mode when an SBI implementation provides machine-mode services;
- required privilege, interrupt, address-translation, and multi-hart conditions must be satisfied;
- the kernel image placement and alignment rules must be honored.

OpenSBI and U-Boot have different responsibilities. OpenSBI supplies the machine-mode runtime interface; U-Boot supplies boot policy, loading, DT preparation, and the supervisor-mode handoff.

## 12.3 ARM64 Linux

The ARM64 boot protocol defines the <code>Image</code> header, placement rules, DTB requirements, register arguments, exception level, MMU state, cache expectations, and CPU state. A common DT boot passes the DTB address in <code>x0</code> while other argument registers are zero, but the current architecture document is authoritative.

Consult the Linux source documentation for the kernel version being used; do not generalize 32-bit ARM ATAG conventions to ARM64.

## 12.4 32-bit ARM Linux

Older 32-bit ARM systems historically used ATAGs, while modern DT-aware boot uses a DTB. The official [Booting ARM Linux](https://docs.kernel.org/arch/arm/booting.html) document describes machine preparation, tag or DT placement, register conventions, and the jump.

## 12.5 x86 Linux

x86 Linux has a detailed boot protocol with real-mode setup, boot parameters, command line, protected/long-mode paths, and EFI entry possibilities. PC bootloaders such as GRUB usually interact with this protocol or the EFI stub rather than an ARM/RISC-V-style DT handoff.

See the [Linux x86 boot documentation](https://docs.kernel.org/arch/x86/boot.html).

## 12.6 Why “Starting kernel ...” may be the last message

After printing that line, U-Boot may disable or reconfigure facilities used by its console and then transfer control. Silence can mean:

- wrong entry or load address;
- kernel overwritten by another image;
- incorrect architecture or format;
- invalid or overwritten DTB;
- incorrect privilege or MMU state;
- cache-coherency failure;
- kernel exception before console initialization;
- wrong <code>console=</code> device;
- wrong <code>earlycon</code> parameters;
- UART driver, clock, or pinmux mismatch;
- immediate watchdog reset.

The line is a boundary marker, not a diagnosis.

## 12.7 Kernel command line

The command line influences console selection, root filesystem, log level, init program, debugging, and many subsystems. Common educational options include:

~~~~text
console=ttyS0,115200
earlycon
loglevel=8
ignore_loglevel
init=/init
~~~~

The correct console name and earlycon syntax are driver- and platform-specific. The comprehensive [kernel parameter reference](https://docs.kernel.org/admin-guide/kernel-parameters.html) documents supported options.

## 12.8 Handoff evidence ladder

Use increasingly strong evidence:

1. U-Boot validates the image header.
2. U-Boot reports DTB and initramfs relocation.
3. U-Boot prints its final kernel-start message.
4. a debugger observes the PC at the kernel entry.
5. the kernel emits an earliest architecture marker.
6. early console prints decompressor or kernel initialization.
7. normal console starts.
8. initramfs <code>/init</code> runs.
9. the persistent root filesystem mounts.

Each step rules out a different class of failures.

# 13. Manual boot and automated boot

## 13.1 Manual boot as a learning tool

A transparent manual flow might be:

~~~~text
load mmc DEVICE:PART KERNEL_ADDRESS /boot/Image
load mmc DEVICE:PART FDT_ADDRESS /boot/board.dtb
load mmc DEVICE:PART RAMDISK_ADDRESS /boot/initramfs.cpio.gz
fdt addr FDT_ADDRESS
fdt print /chosen
booti KERNEL_ADDRESS RAMDISK_ADDRESS:RAMDISK_SIZE FDT_ADDRESS
~~~~

Every capitalized item must be replaced with a board-correct value. After each load, preserve its reported size before another load overwrites <code>filesize</code>.

## 13.2 bootcmd and boot scripts

The <code>bootcmd</code> environment variable can contain a command sequence or run other variables. A text script can be packaged into a U-Boot script image using <code>mkimage</code>, then loaded and executed with <code>source</code>.

A script should:

- fail visibly on a required load error;
- avoid relying on stale <code>filesize</code>;
- print selected device, partition, filenames, and addresses;
- preserve recovery access;
- separate policy from destructive update operations;
- be version-controlled as source, not only stored as binary <code>boot.scr</code>.

## 13.3 Extlinux configuration

The distro-style boot method can parse an <code>extlinux.conf</code> containing labeled entries, kernel path, initrd, DTB, and append line. This keeps ordinary boot policy in readable files rather than a long environment string.

## 13.4 Standard Boot

Modern U-Boot Standard Boot models:

- **bootdev** — a device that may hold or access bootable content;
- **bootmeth** — a method for locating a bootflow;
- **bootflow** — a discovered description of how to boot.

Commands such as <code>bootflow scan</code> can enumerate candidates when configured. See the [Standard Boot overview](https://docs.u-boot.org/en/latest/develop/bootstd/overview.html).

## 13.5 Choosing between booti, bootm, and EFI

| Path | Typical input | Main benefit | Main caution |
|---|---|---|---|
| <code>booti</code> | flat ARM64/RISC-V Linux Image + optional initramfs + DTB | direct and understandable | separate artifacts and addresses must agree |
| <code>bootm</code> | FIT or legacy image | packaging, configurations, verification | FIT design and keys add build complexity |
| EFI boot | EFI application, EFI-stub kernel, UKI, GRUB | standard firmware interface and distro integration | different variables, filesystems, and secure-boot policy |

There is no universally best path. Choose the smallest mechanism that meets the product’s update, security, recovery, and portability requirements.

# Part IV — Learning and Debugging in Practice

# 14. Build U-Boot without risking a board

## 14.1 Why start with sandbox

U-Boot sandbox is a host program that implements U-Boot interfaces using the development machine. It does not emulate a particular physical SoC, but it lets a beginner:

- build the source tree;
- reach a U-Boot prompt;
- learn environment and command syntax;
- inspect driver model;
- run many tests;
- use a normal debugger;
- change code without flashing hardware.

Sandbox cannot validate DRAM training, real UART pinmux, ROM packaging, storage timing, or a physical board’s boot chain.

## 14.2 Obtain and identify source

Use the official upstream repository or a board vendor’s documented fork. For upstream:

~~~~bash
git clone https://source.denx.de/u-boot/u-boot.git
cd u-boot
git status
git describe --always --dirty
~~~~

For reproducible study, select a documented release tag or commit. Do not build an arbitrary moving branch and later describe it only as “latest.”

The official [Build U-Boot](https://docs.u-boot.org/en/latest/build/index.html) documentation covers dependencies, GCC/Clang builds, reproducibility, and build tools.

## 14.3 Build sandbox

A common upstream sequence is:

~~~~bash
make sandbox_defconfig
make -j"$(nproc)"
./u-boot -d u-boot.dtb
~~~~

Build requirements change. Follow the documentation for the checked-out version if a dependency or target differs.

At the prompt, try:

~~~~text
version
bdinfo
help
printenv
dm tree
ut all
~~~~

The complete unit test command may take time and may vary with configuration. The driver-model documentation also describes Python tests.

## 14.4 Understand defconfig and .config

- A file in <code>configs/*_defconfig</code> stores a board’s reproducible configuration seed.
- <code>make BOARD_defconfig</code> expands it and dependency defaults into <code>.config</code>.
- <code>make menuconfig</code> provides an interactive configuration UI.
- <code>make savedefconfig</code> writes a minimized defconfig representation.

Do not manually copy a random <code>.config</code> between releases or architectures. Kconfig dependencies and symbol names evolve.

## 14.5 Cross-compilation

To build for another architecture, use an appropriate cross-compiler prefix:

~~~~bash
make CROSS_COMPILE=aarch64-linux-gnu- BOARD_defconfig
make CROSS_COMPILE=aarch64-linux-gnu- -j"$(nproc)"
~~~~

The prefix, defconfig, required blobs, and packaging target are board-specific. Some builds also set <code>ARCH</code>, although modern U-Boot often derives architecture from the configuration.

## 14.6 Build artifacts are not interchangeable

Possible outputs include:

- <code>u-boot</code> — ELF with symbols, useful for debugging;
- <code>u-boot.bin</code> — raw binary;
- <code>u-boot-nodtb.bin</code> — binary without appended DTB;
- <code>u-boot.dtb</code> — U-Boot control DTB;
- <code>u-boot.img</code> — image with a U-Boot header;
- <code>u-boot.itb</code> — FIT-packaged image;
- <code>spl/u-boot-spl.bin</code> — SPL binary;
- board-specific combined images created by Binman or vendor tools.

ROM may require a signed header, checksum, padding, fixed offset, or combined firmware package. Flashing the wrong artifact is a packaging failure, not evidence that its C code is wrong.

## 14.7 First source-navigation map

The exact tree changes, but these areas are useful:

| Path | What to learn there |
|---|---|
| <code>arch/ARCH/</code> | architecture entry, cache, CPU, and handoff code |
| <code>board/VENDOR/BOARD/</code> | board-specific hooks |
| <code>configs/</code> | defconfig seeds |
| <code>drivers/</code> | driver implementations by subsystem |
| <code>cmd/</code> | command implementations |
| <code>common/</code> | common startup and boot code |
| <code>boot/</code> | image loading and OS boot logic |
| <code>env/</code> | environment backends |
| <code>dts/</code>, architecture DTS paths | U-Boot Device Tree sources and build integration |
| <code>include/configs/</code> | remaining board configuration headers |
| <code>tools/</code> | host tools such as image creation and Binman support |
| <code>test/</code> | unit, command, and Python tests |

Search by symbol, message, compatible string, or Kconfig option:

~~~~bash
rg 'Starting kernel' .
rg 'CONFIG_CMD_BOOTI' .
rg 'vendor,device-compatible' .
git grep 'board_init'
~~~~

# 15. A boundary-first debugging method

## 15.1 The rule

Debug one boundary at a time. Do not change the kernel, DTB, bootloader, boot script, and storage layout in the same experiment.

For each trial record:

- exact artifacts and hashes;
- exact storage placement;
- power/reset method;
- console configuration;
- complete output from first byte to failure;
- one intended variable;
- observation and conclusion;
- next discriminating test.

## 15.2 Establish a known-good baseline

Preserve a boot medium or firmware set that reaches the furthest verified stage. Record its partition table, images, environment, and logs. Test recovery before experimenting with persistent writes.

The baseline is not necessarily “correct Linux.” It is the most reproducible evidence boundary.

## 15.3 Locate the last proven boundary

Use unique markers, not expectations:

| Last unique marker | First subsystem to inspect |
|---|---|
| no output, ROM recovery absent | power, reset, UART wiring, boot mode, ROM rules |
| early-loader marker only | DRAM init, packaging, next-stage load |
| runtime-firmware banner only | U-Boot address, entry, privilege, embedded DT |
| U-Boot banner stops during init | relocation, driver probe, environment, watchdog |
| prompt works, file load fails | controller, device enumeration, partition, filesystem, path |
| header validation fails | image format, corruption, architecture, command |
| final kernel-start marker only | handoff state, memory overlap, DT, early console |
| kernel log reaches root mount | storage/root arguments/filesystem support |
| kernel starts init and fails | initramfs content, executable, libraries, permissions |

## 15.4 Capture the complete serial log

Do not photograph only the last screen. Record:

- serial device identity;
- voltage and wiring;
- baud/data/parity/stop configuration;
- terminal command;
- timestamped raw capture;
- cold versus warm boot;
- interruptions during autoboot;
- any resets or repeated banners.

A repeated early banner may indicate a watchdog or exception loop.

## 15.5 Interrogate U-Boot without mutation

At a stable prompt:

~~~~text
version
bdinfo
printenv
dm tree
mmc list
part list mmc 0
ls mmc 0:1 /
~~~~

Then inspect the planned memory map and DTB before loading. Avoid <code>saveenv</code>, flash writes, and partition edits during diagnosis.

## 15.6 Verify each artifact after loading

For every load:

1. record destination address;
2. record reported byte size;
3. inspect format/header;
4. compute a checksum if a host reference exists;
5. confirm the next load does not overlap it;
6. preserve the initramfs size before loading another file;
7. inspect the working FDT header and chosen node.

An example checksum comparison:

~~~~text
crc32 KERNEL_ADDRESS KERNEL_SIZE
~~~~

Compare with a host-computed CRC only if both tools use the same range and representation. Cryptographic hashes are stronger for artifact identity; CRC is useful for detecting accidental differences, not malicious changes.

## 15.7 Separate console failure from CPU failure

If Linux is silent after handoff, use an independent observation:

- early console on the correct UART;
- a GPIO stage marker, if safely implemented;
- a watchdog with stage-specific reset reason;
- JTAG halt and program-counter inspection;
- persistent crash log prepared by firmware/kernel;
- network activity only after its prerequisites are proven.

Changing HDMI graphics is rarely the first diagnostic for an early kernel failure.

## 15.8 Memory-overlap diagnosis

Create sorted intervals using actual loaded sizes. Include decompressed size and U-Boot-reported relocation ranges. Common mistakes:

- DTB loaded inside the kernel’s decompression destination;
- initramfs overlapping U-Boot relocation;
- kernel overwriting DTB during self-relocation;
- an oversized compressed kernel exceeding the configured workspace;
- firmware-reserved memory absent from the final DT.

## 15.9 Device Tree diagnosis

Inspect:

~~~~text
fdt addr FDT_ADDRESS
fdt header
fdt print /compatible
fdt print /chosen
fdt print /memory
fdt print /reserved-memory
~~~~

Then compare the final boot-time blob with the known-good source. Verify the exact board compatible, RAM, console, interrupt controller, timer, firmware interface, and storage path.

## 15.10 Common false conclusions

- **“U-Boot boots, so DRAM is completely reliable.”** It proves enough DRAM behavior for the observed path, not stress reliability at all addresses and frequencies.
- **“QEMU boots, so the board should boot.”** QEMU validates the generic architecture path and emulated machine, not a different SoC’s clocks, memory controller, or devices.
- **“The DTB compiled, so it is correct.”** DTC proves syntax and binary structure.
- **“The kernel file loaded, so its address is correct.”** Loading and safe execution are different.
- **“No kernel text means the kernel never ran.”** Console setup may be wrong.
- **“The vendor logo appeared, so U-Boot proper initialized.”** A prior stage may own the framebuffer.

# 16. Practical labs

## Lab 1 — Draw a boot chain from evidence

**Goal:** distinguish facts from assumptions.

1. Select any documented development board or virtual platform.
2. Find its boot-ROM, early-loader, runtime-firmware, U-Boot, and kernel documentation.
3. Create the boot-chain inventory from Chapter 3.
4. Mark each cell as source-backed, log-backed, experiment-backed, or unknown.

**Pass condition:** no inferred stage is presented as observed fact.

## Lab 2 — Run U-Boot sandbox

**Goal:** reach a harmless prompt.

1. Build <code>sandbox_defconfig</code>.
2. run the host <code>u-boot</code> binary;
3. capture <code>version</code>, <code>bdinfo</code>, <code>help</code>, <code>printenv</code>, and <code>dm tree</code>;
4. exit using the sandbox-documented reset behavior.

**Pass condition:** explain why sandbox does not validate a real board port.

## Lab 3 — Trace one command

**Goal:** connect prompt syntax to C code.

1. Choose a harmless command such as <code>echo</code> or <code>version</code>.
2. find its command registration and handler;
3. identify its Kconfig dependency;
4. add a temporary debug print;
5. rebuild and observe the changed output;
6. revert the change.

**Pass condition:** explain registration, parsing, handler execution, and return status.

## Lab 4 — Environment lifetime

**Goal:** understand active versus persistent state.

1. In sandbox, display a variable.
2. set a temporary value;
3. use it in an echo or command;
4. reset without saving;
5. observe whether it persists;
6. inspect the configured environment backend.

**Pass condition:** distinguish compiled default, active, and saved environment.

## Lab 5 — Compile and inspect a Device Tree

**Goal:** learn the source/binary relationship.

1. write a small educational DTS with root, CPUs, memory, chosen, aliases, and UART;
2. compile with DTC;
3. decompile it;
4. use <code>fdtget</code> to read <code>compatible</code>;
5. change one property and use <code>fdtdiff</code>.

**Pass condition:** explain what information was lost during decompilation and why the tree is not automatically valid for hardware.

## Lab 6 — Build U-Boot for QEMU

**Goal:** exercise a real target without physical flash.

1. choose a currently documented U-Boot QEMU board configuration;
2. build the matching architecture;
3. run it with the documented QEMU machine and firmware combination;
4. capture the complete boot;
5. identify who entered U-Boot and at which privilege level.

**Pass condition:** state exactly what the virtual boot proves and does not prove.

## Lab 7 — Load a file into RAM

**Goal:** separate storage from memory.

Using sandbox host binding or a documented virtual disk:

1. enumerate the device and partition;
2. list the filesystem;
3. load a known file into a documented safe address;
4. record <code>fileaddr</code> and <code>filesize</code>;
5. inspect its first bytes and checksum;
6. compare with the host file.

**Pass condition:** show byte identity and a non-overlapping destination.

## Lab 8 — Inspect a FIT

**Goal:** understand components and configurations.

1. obtain or build an unsigned educational FIT;
2. list it with <code>dumpimage</code> or <code>mkimage -l</code>;
3. identify kernel, DTB, ramdisk, hashes, and default configuration;
4. explain what <code>bootm ADDRESS#CONFIG</code> selects.

**Pass condition:** distinguish a component from a configuration.

## Lab 9 — Manual kernel boot in QEMU

**Goal:** observe the full handoff.

1. use a kernel and DTB built for the same QEMU machine;
2. load them by a documented method;
3. record addresses and sizes;
4. inspect the DT chosen node;
5. boot with the correct command;
6. capture both final U-Boot and first kernel messages.

**Pass condition:** label the last U-Boot instruction boundary conceptually and the first independent kernel evidence.

## Lab 10 — Break one input on purpose

**Goal:** learn diagnostic signatures.

Repeat Lab 9 with exactly one controlled error in a recoverable virtual machine:

- invalid DTB address; or
- wrong console argument; or
- corrupted image header.

Predict the expected boundary, run the trial, then compare prediction and observation.

**Pass condition:** do not change a second variable to “make it boot.”

## Lab 11 — Reduce a Device Tree

**Goal:** apply dependency-aware minimization.

1. begin with a known-good virtual-platform DTS;
2. define essential devices;
3. map provider dependencies;
4. disable one unused leaf;
5. compile and validate;
6. boot and compare logs;
7. repeat.

**Pass condition:** maintain a change log linking each disabled node to test evidence.

## Lab 12 — Source trace from booti to architecture entry

**Goal:** understand the handoff implementation.

In a fixed U-Boot commit:

1. find <code>booti</code> command registration;
2. trace image parsing;
3. locate DT and initramfs preparation;
4. locate architecture cleanup;
5. locate the final call or branch to the kernel;
6. compare register setup with the Linux architecture boot document.

**Pass condition:** cite file paths and functions from the chosen commit, without claiming they are permanent across releases.

## Lab 13 — Add a non-destructive command

**Goal:** make a small U-Boot code change.

Create a sandbox-only command that prints a fixed learning message and one read-only runtime value. Add its Kconfig and build integration, build it, test its help and return status, and run relevant tests.

**Pass condition:** the command disappears when its Kconfig option is disabled.

## Lab 14 — Create an automated boot policy

**Goal:** turn a proven manual flow into reproducible policy.

1. begin with a manual QEMU boot that works;
2. write an extlinux entry or boot script;
3. add explicit failure messages;
4. retain an interruptible recovery path;
5. compare the selected kernel, DTB, arguments, and addresses with the manual flow.

**Pass condition:** automation introduces no unrecorded artifact or address change.

## Lab 15 — Interview explanation

**Goal:** convert activity into understanding.

Give a five-minute explanation of:

1. why ROM cannot normally load Linux directly;
2. why SPL exists;
3. the difference between OpenSBI/TF-A and U-Boot;
4. why a DTB is data, not a driver;
5. what <code>booti</code> prepares;
6. how you would isolate silence after kernel entry.

Record yourself. For every term you cannot define without another unexplained term, return to the relevant chapter.

# Part V — Designing and Modifying Boot Software

# 17. Write a small loader for your own kernel

## 17.1 First choose the real goal

“Write my own U-Boot” can mean three very different projects:

1. **A boot shim** entered by existing firmware that immediately starts a fixed kernel already in RAM.
2. **A small loader** that can locate, validate, load, and start one kernel on one known platform.
3. **A general bootloader** with portable drivers, filesystems, networking, shell, configuration, updates, security, and support for many boards.

Project 1 may take tens or hundreds of lines. Project 2 is a serious firmware project. Project 3 is comparable in scope to maintaining a mature bootloader, not a beginner exercise.

## 17.2 Define the input contract

Before writing code, document what the previous stage guarantees:

- CPU architecture and privilege level;
- boot CPU and secondary CPU state;
- endianness;
- MMU and cache state;
- interrupt state;
- working RAM ranges;
- stack availability;
- pointer arguments such as DTB or firmware tables;
- runtime firmware services;
- where the loader image resides;
- how control arrives and whether relocation is needed.

If you cannot state this contract, the first task is not coding; it is reading the platform boot documentation and previous-stage source.

## 17.3 Define your kernel’s entry ABI

If it is your own kernel, you control the interface. Keep version 1 small and explicit:

| Item | Example contract |
|---|---|
| entry address | fixed physical address or read from a validated header |
| privilege | RISC-V S-mode or ARM64 EL1, chosen deliberately |
| argument 0 | boot CPU ID |
| argument 1 | pointer to immutable boot-info structure or DTB |
| MMU | disabled |
| interrupts | disabled |
| caches | documented, coherent state |
| stack | kernel establishes its own |
| return | forbidden; reset if it returns |

Do not copy Linux register conventions unless you intentionally want Linux compatibility.

## 17.4 Minimum loader pipeline

A defensible small loader performs:

1. establish early stack and C runtime;
2. initialize a diagnostic UART or use a prior-stage console;
3. validate its input contract;
4. initialize or confirm RAM;
5. initialize the boot medium, unless the image is already in RAM;
6. locate a kernel;
7. validate header, size, architecture, address ranges, and checksum/signature;
8. copy or decompress without overlap;
9. construct or validate boot information;
10. stop DMA and mask interrupts that will not be handed over;
11. perform cache and instruction synchronization;
12. establish required privilege and translation state;
13. place arguments in registers;
14. transfer control;
15. reset or panic if the kernel unexpectedly returns.

Each step should emit a short stage code during development.

## 17.5 Memory map and linker script

The linker script controls where code, read-only data, writable data, uninitialized data, and stacks are placed. A basic firmware linker script must agree with:

- the address where the previous stage loads the binary;
- executable memory permissions;
- SRAM or DRAM boundaries;
- relocation strategy;
- stack growth and guard space;
- kernel and DTB destination ranges.

Important sections:

- <code>.text</code> — executable code;
- <code>.rodata</code> — constants;
- <code>.data</code> — initialized writable globals;
- <code>.bss</code> — zero-initialized globals;
- stack — often reserved by symbols rather than an input section.

The entry assembly must zero <code>.bss</code> before C code expects uninitialized globals to contain zero.

## 17.6 An educational handoff skeleton

This is pseudocode, not board-ready firmware:

~~~~c
struct boot_info {
    unsigned long version;
    unsigned long memory_base;
    unsigned long memory_size;
    unsigned long dtb_address;
};

typedef void (*kernel_entry_fn)(unsigned long boot_cpu,
                                const struct boot_info *info);

__attribute__((noreturn))
void start_kernel(unsigned long entry_address,
                  unsigned long boot_cpu,
                  const struct boot_info *info)
{
    validate_entry_and_boot_info(entry_address, info);
    stop_loader_owned_dma();
    disable_interrupt_sources();
    architecture_cache_and_instruction_sync(entry_address);
    establish_kernel_entry_state();

    kernel_entry_fn entry = (kernel_entry_fn)entry_address;
    entry(boot_cpu, info);

    platform_reset();
    for (;;) {
    }
}
~~~~

Every helper hides architecture- and platform-specific work. That is intentional: the code shows the responsibilities without pretending a generic C call is a complete handoff.

## 17.7 Minimal RISC-V assembly idea

If a loader already runs in the intended RISC-V privilege mode and all state requirements are satisfied, the final assembly concept is:

~~~~asm
/* Inputs chosen by our ABI:
 * a0 = boot hart ID
 * a1 = boot-info or DTB address
 * t0 = kernel entry address
 */
fence rw, rw
fence.i
jr t0
~~~~

This does not initialize RAM, change privilege, configure PMP, stop secondary harts, disable interrupts, validate addresses, or provide SBI services. Those omissions are exactly why a three-instruction jump is not a bootloader.

## 17.8 Loading from storage is often the largest step

To read a file by name, the loader needs:

- a controller driver;
- media protocol;
- partition-table parser;
- filesystem reader;
- buffer and error handling.

A first educational loader can avoid this by receiving a kernel preloaded in RAM, embedding a payload, or reading a fixed raw extent. Each simplification must be documented. A raw fixed extent is easier than ext4, but update safety and corruption handling become your responsibility.

## 17.9 Image header design

A small custom header might contain:

- magic value;
- format version;
- target architecture;
- payload type;
- header and payload sizes;
- load and entry addresses;
- compression identifier;
- payload hash;
- signature metadata;
- minimum compatible loader version.

Validate integer overflow before calculating end addresses. Validate that the header itself is fully present before trusting lengths inside it. Authenticate metadata as well as payload, otherwise an attacker may alter addresses or sizes.

## 17.10 Error strategy

Early firmware should fail in a diagnosable and safe way:

- unique numeric or textual stage code;
- bounded timeouts instead of infinite peripheral waits;
- explicit error reason;
- watchdog policy;
- recovery source or fallback slot;
- no automatic destructive “repair” without authenticated policy.

## 17.11 Learning sequence

Build in this order:

1. entry assembly prints one character on a virtual UART;
2. stack and C function work;
3. linker symbols and BSS are verified;
4. boot-info structure is validated;
5. preloaded test payload is entered;
6. payload return is detected;
7. image header and hash are added;
8. fixed raw storage loading is added;
9. DTB passing is added if needed;
10. recovery and update logic are designed.

Do not begin with USB, networking, ext4, signatures, menus, and a shell simultaneously.

# 18. Port U-Boot to a new board

## 18.1 What “porting” means

A board port integrates U-Boot with a particular SoC, board, firmware chain, and product policy. It is not merely adding a DTS file.

The difficulty depends on existing support:

- **new board using an upstream-supported SoC** — often mostly DTS, defconfig, board identification, environment, packaging, and testing;
- **new SoC in a supported architecture** — new low-level drivers and architecture integration;
- **new DRAM/controller/security chain** — may require vendor code and extensive bring-up;
- **undocumented hardware** — reverse engineering may dominate.

## 18.2 Required knowledge

You should be able to work with:

- C and small amounts of architecture assembly;
- linker scripts, ELF files, relocation, symbols, and sections;
- cross-compilation and Kconfig;
- MMIO, volatile access, barriers, caches, and the MMU;
- reset, clocks, PLLs, pinmux, GPIO, power domains;
- UART and at least one boot-storage controller;
- DRAM controller initialization and training concepts;
- Device Tree and binding schemas;
- the SoC boot ROM’s image and placement rules;
- privilege/security firmware;
- JTAG or another low-level debugger;
- version control, bisecting, tests, and reproducible builds.

You do not need to master all topics before starting, but you must recognize which layer owns a failure.

## 18.3 Documentation and hardware inputs

Minimum inputs usually include:

- SoC technical reference manual;
- board schematic;
- memory datasheet and layout/training parameters;
- boot-ROM/image-format guide;
- clock and reset tree;
- pinmux table;
- UART connector pinout and voltage;
- storage layout;
- security and fuse lifecycle documentation;
- known-good vendor firmware and boot log;
- an upstream reference board using the same SoC.

Without these, label the port experimental.

## 18.4 Incremental bring-up milestones

Use visible milestones:

1. entry marker from SRAM;
2. stable UART;
3. reset cause and clock identity;
4. DRAM initialization;
5. DRAM address/size and basic test;
6. relocation into DRAM;
7. timer;
8. one boot-storage path;
9. environment or read-only boot policy;
10. working DT selection;
11. kernel entry;
12. kernel early console;
13. root filesystem;
14. reset, watchdog, and recovery;
15. security and update policy.

Each milestone should be reproducible from a cold boot.

## 18.5 Board Device Tree and defconfig

The board DTS describes hardware instances and dependencies. The defconfig chooses compiled capabilities. Both must agree:

- a DT node without a compiled driver may remain unusable;
- a compiled driver without a matching enabled device may never bind;
- SPL often has a separate, smaller configuration and DT filtering;
- pre-relocation devices need early availability;
- aliases can affect sequence numbers and console selection.

## 18.6 DRAM is a special boundary

DRAM initialization is not a normal driver problem. It may require:

- PLL and clock setup;
- controller and PHY configuration;
- memory timing derived from datasheets;
- calibration or training;
- temperature/voltage considerations;
- board trace topology;
- vendor binary firmware.

A UART banner before DRAM and a checksum after copying data across DRAM are separate milestones. A memory size print is not a complete stress test.

## 18.7 Packaging and flashing

Map the exact final image:

| Component | Offset/container | Load address | Entry | Authenticated by |
|---|---|---:|---:|---|
| early loader | ROM-defined | SRAM address | reset/entry | Boot ROM |
| runtime firmware | package entry | secure/firmware RAM | firmware entry | ROM/SPL |
| U-Boot | package/raw/FIT | DRAM address | U-Boot entry | SPL/firmware |
| U-Boot DTB | embedded/separate | chosen address | data | package policy |

Test with a recoverable medium before modifying soldered storage. Verify exact targets before any write.

## 18.8 Upstream-quality expectations

A maintainable port should:

- reuse generic drivers and bindings;
- avoid unexplained magic constants;
- document external firmware requirements;
- pass formatting and relevant tests;
- separate SoC and board responsibilities;
- include maintainers and board documentation;
- preserve bisectability;
- avoid secrets and production keys;
- submit small reviewable changes.

# 19. Boot security, update, and recovery

## 19.1 Checksums are not signatures

A checksum or unkeyed hash detects accidental corruption but an attacker can replace both data and checksum. A digital signature is verified using a trusted public key and can establish authorization when private keys are protected.

Secure boot needs a root of trust anchored earlier than writable storage. If an attacker can replace both U-Boot and the public key U-Boot trusts, U-Boot-level verification alone does not create a complete chain of trust.

## 19.2 Chain of trust

A typical chain:

1. immutable ROM trusts a fused or embedded key;
2. ROM authenticates the early loader;
3. early loader authenticates runtime firmware and U-Boot;
4. U-Boot authenticates the kernel, DTB, initramfs, and configuration;
5. the OS verifies later software according to product policy.

Every executable component and security-relevant configuration must be covered. Authenticating a kernel but not the DTB may allow changes to memory reservations, devices, or command line.

## 19.3 FIT verified boot

U-Boot FIT can associate hashes and signatures with images and configurations. The public verification key is commonly placed in U-Boot’s control DT or another trusted component, while the private signing key remains off-device in controlled build infrastructure.

Security depends on configuration details:

- which nodes are signed;
- whether signatures are required;
- where the trusted key lives;
- whether unsigned alternate commands remain reachable;
- rollback policy;
- key revocation and rotation;
- protection of the environment.

Use the current upstream [U-Boot verified boot documentation](https://docs.u-boot.org/en/latest/usage/fit/verified-boot.html) and threat-model the complete device.

## 19.4 Verified boot versus measured boot

- **Verified boot** blocks an image that fails authorization policy.
- **Measured boot** records cryptographic measurements into a protected mechanism such as TPM PCRs for later attestation.

A measured untrusted image can still execute unless a verification policy also blocks it. A verified image may not produce an attestation record unless measurement is implemented.

## 19.5 Rollback protection

A correctly signed old image may contain a known vulnerability. Rollback protection compares a security version to monotonic trusted state. The design must handle interrupted updates and avoid permanently bricking devices through a partially advanced counter.

## 19.6 A/B update model

A robust A/B system has:

- two independently bootable slots;
- authenticated metadata;
- a trial-boot counter;
- a definition of “boot successful” confirmed late enough by the OS;
- automatic rollback;
- power-loss-safe metadata updates;
- a recovery path independent of both slots.

U-Boot may select slots, but the entire lifecycle spans update service, storage, kernel, userspace, watchdog, and manufacturing policy.

## 19.7 Environment security

If boot order, command line, addresses, or verification bypasses are stored in writable environment, an attacker or accidental edit may change policy. Options include:

- immutable compiled policy;
- authenticated environment;
- restricted commands;
- locked console in production;
- physically controlled recovery mode;
- separate development and production configurations.

## 19.8 Secrets

Do not embed private signing keys, disk-decryption keys, or reusable manufacturing secrets in the U-Boot source or ordinary environment. Firmware may obtain device-specific secrets through secure hardware or a trusted service, depending on the design.

## 19.9 Recovery is part of security

A device that cannot recover safely encourages insecure debug bypasses. Define:

- how recovery is requested;
- who is authorized;
- which images are accepted;
- whether rollback is allowed;
- which storage can be overwritten;
- how power loss is handled;
- how recovery actions are audited.

# 20. Alternatives and neighboring technologies

## 20.1 Do not compare names at the wrong layer

Some projects replace U-Boot, some precede it, and some can run on top of its UEFI implementation.

| Technology | Typical layer/role | Common setting |
|---|---|---|
| Boot ROM | immutable first stage | SoC |
| coreboot | hardware initialization firmware | x86 and selected platforms |
| Trusted Firmware-A | ARM trusted boot/runtime firmware | ARMv8+ systems |
| OpenSBI | RISC-V machine-mode runtime firmware | RISC-V systems |
| U-Boot | embedded platform init and OS loader | ARM, RISC-V, PowerPC, x86, others |
| barebox | embedded bootloader alternative | embedded Linux |
| UEFI implementation | standardized firmware interface | PCs, servers, ARM systems |
| GRUB 2 | feature-rich OS boot manager/loader | PCs, servers, EFI systems |
| systemd-boot | simple UEFI boot manager | UEFI Linux systems |
| iPXE | network boot firmware/loader | PXE and network provisioning |
| MCUboot | secure image boot/update for MCUs | RTOS/microcontroller systems |
| Android bootloader/AVB components | Android-specific boot and verification | phones/embedded Android |

## 20.2 U-Boot and GRUB 2

U-Boot often performs low-level board initialization and directly manages embedded peripherals. GRUB commonly relies on BIOS or UEFI firmware that has already initialized a PC-like platform and exposed standard services.

U-Boot can itself provide UEFI services and then start GRUB. In that chain they are not direct replacements:

**platform firmware/U-Boot → UEFI interface → GRUB → kernel**

GRUB excels at menus, filesystems, modular loading, multiboot, and desktop/server boot policy. Its official [GNU GRUB manual](https://www.gnu.org/software/grub/manual/grub/grub.html) is the primary reference.

## 20.3 U-Boot and UEFI

UEFI is a specification defining firmware interfaces, boot services, runtime services, protocols, variables, and executable formats. It is not one bootloader implementation.

U-Boot can implement part of the UEFI environment. EDK II is another implementation ecosystem. The latest specifications are published by the [UEFI Forum](https://uefi.org/specifications).

Use UEFI when OS portability, standard boot variables, Secure Boot ecosystems, and distro compatibility outweigh the cost. Use a direct U-Boot path when the platform and product benefit from explicit embedded control and a smaller policy surface.

## 20.4 barebox

barebox is an embedded bootloader with Linux-like design influences, device model, filesystems, shell, boot management, and update mechanisms. Evaluate actual SoC/board support, recovery, team experience, long-term maintenance, and security features rather than choosing by shell appearance.

See the official [barebox documentation](https://docs.barebox.org/).

## 20.5 coreboot and LinuxBoot

coreboot primarily performs minimal hardware initialization and then starts a payload. Payloads can include SeaBIOS, Tianocore/EDK II, GRUB, or other programs. LinuxBoot uses a Linux kernel and userspace tools as server firmware components after early hardware initialization.

These approaches are especially relevant to open server firmware and x86 platforms. They do not eliminate the need for silicon initialization and trusted early stages.

See [coreboot documentation](https://doc.coreboot.org/) and [LinuxBoot documentation](https://www.linuxboot.org/).

## 20.6 iPXE

iPXE specializes in network boot and supports protocols and scripting beyond traditional PXE. It may be ROM-resident, chainloaded by UEFI/BIOS/U-Boot, or used in provisioning infrastructure.

It does not normally replace the earliest SoC initialization stages.

## 20.7 MCUboot

MCUboot is designed for secure boot and upgrade of microcontroller images, commonly in RTOS ecosystems. Its assumptions about execution, flash slots, image trailers, and resource limits differ from rich Linux bootloaders.

## 20.8 Selection questions

Choose by requirements:

- Who initializes DRAM and silicon?
- Which CPUs and boards are supported?
- Must standard UEFI operating systems boot unchanged?
- Are interactive recovery and manufacturing commands needed?
- Which filesystems and networks are required?
- What is the verified-boot and rollback model?
- What are boot-time and flash-size budgets?
- Who maintains the port for the product lifetime?
- Can the team debug the chosen stack?

# Part VI — Deeper Reference

# 21. U-Boot internals worth learning next

## 21.1 Initialization sequences

U-Boot organizes early and later initialization through architecture and common code. Function names and exact order change, so trace the selected commit rather than memorizing one online call graph.

When reading source, identify:

- reset vector and first assembly file;
- C entry transition;
- pre-relocation initialization list;
- RAM discovery and reservation;
- relocation calculation and copy/fixups;
- post-relocation initialization;
- environment import;
- console selection;
- autoboot decision;
- command main loop;
- OS image state machine;
- architecture-specific OS jump.

Use the serial banner text to find which source produced an observed line.

## 21.2 Linker-generated lists

U-Boot uses build/link mechanisms to collect command tables, drivers, uclasses, initialization entries, and other descriptors. A source file can register an object without a hand-written central array.

This explains a common beginner surprise: writing a handler function is not sufficient. The object must be registered, selected by configuration, compiled, linked, and sometimes associated with a Device Tree node.

## 21.3 Bind, of_to_plat, probe, remove, and unbind

A simplified driver lifecycle:

1. **bind** — create a device instance and associate it with a driver;
2. **of_to_plat** — translate Device Tree properties into platform data, depending on driver design;
3. **probe** — initialize the device for use;
4. **remove** — make it inactive and release runtime resources;
5. **unbind** — detach and remove the instance.

Parent buses, power domains, clocks, resets, and pinctrl can be probed as dependencies. Probe ordering bugs often reflect a missing dependency description or unsupported provider.

## 21.4 Sequence numbers and aliases

User-facing device numbering such as <code>mmc 0</code> may be influenced by aliases, driver-model sequence assignment, probe order, and build behavior. Never assume Linux’s <code>mmcblk0</code> is the same physical device as U-Boot’s <code>mmc 0</code>. Match stable hardware identity, capacity, CID, path, or partition UUID.

## 21.5 Environment import and redundancy

Persistent environments usually contain a header/check data and serialized variables. Some designs keep redundant copies with validity or sequence metadata. Power-loss behavior depends on the backend and implementation.

Default-environment fallback can occur because:

- persistent environment is absent;
- checksum/format is invalid;
- storage driver is unavailable;
- offset or partition is wrong;
- encryption/authentication fails;
- build intentionally uses nowhere/volatile environment.

Seeing default values does not prove a successful read from persistent storage.

## 21.6 Hush shell

Many U-Boot builds use the Hush parser for shell-like conditionals, variables, loops, and command sequences. It is not Bash. Quoting, expansion, available built-ins, arithmetic, and error handling are limited by U-Boot configuration and implementation.

Keep boot scripts simple. Prefer explicit <code>if</code> checks and short named variables. Test failure branches, not only the success path.

## 21.7 Boot image state machine

Boot commands commonly pass through states resembling:

- find/identify image;
- verify header and contents;
- determine OS, architecture, type, compression;
- choose configuration;
- load/decompress/relocate;
- prepare ramdisk;
- prepare FDT or boot parameters;
- perform OS-specific preparation;
- shut down bootloader-owned facilities;
- jump.

U-Boot may expose sub-state execution for debugging in some paths. Read the selected release’s <code>bootm</code> implementation and documentation before using internal states manually.

## 21.8 Flattened versus live Device Tree

A flattened DT is a compact serialized blob suitable for handoff. U-Boot can also use a live hierarchical representation internally. Operations and pointers are not interchangeable. When code accepts an FDT offset, it may be operating on the flat blob; when it accepts an <code>ofnode</code> or live node, it uses an abstraction.

The [U-Boot live-tree documentation](https://docs.u-boot.org/en/latest/develop/driver-model/livetree.html) explains this distinction.

## 21.9 Binman

Binman builds composite firmware images from described entries. It can place U-Boot, SPL, DTBs, firmware, padding, and vendor-specific components at defined positions.

It solves packaging, not arbitrary ROM compatibility by itself. The board description and entry types must encode the ROM-required layout, and external signing tools may still be required.

## 21.10 Bloblists and handoff data

Firmware stages need to pass data such as memory information, timestamps, TPM logs, or selected firmware details. U-Boot has mechanisms including Device Tree, bloblists, global data, platform structures, and architecture-specific parameters. A multi-stage design should prefer versioned, bounded, validated formats over undocumented global memory locations.

## 21.11 EFI loader in U-Boot

When U-Boot provides UEFI:

- handles and protocols describe devices and services;
- boot services exist until <code>ExitBootServices()</code>;
- EFI variables may store boot order and security policy;
- EFI applications use PE/COFF;
- a memory map is passed to the OS;
- runtime services may remain if supported.

This path must be debugged using EFI contracts, not only <code>booti</code> assumptions.

## 21.12 Secondary CPUs

At kernel entry, only the boot CPU normally executes the primary entry path. Other cores must be held in a defined state or started through an architecture/platform method such as PSCI on ARM or SBI HSM on RISC-V. Releasing all cores into the same uncoordinated entry point can corrupt stacks and initialization data.

## 21.13 Watchdogs

A watchdog may be enabled by ROM, firmware, U-Boot, or hardware default. Each stage must:

- know whether it is running;
- service, reconfigure, or deliberately transfer it;
- avoid infinite waits;
- preserve reset-cause evidence;
- coordinate with update rollback.

A periodic reset at a stable interval is a strong watchdog clue.

## 21.14 Time and performance

Measure boot time with stage-specific timestamps:

- reset to first early-loader marker;
- DRAM initialization;
- U-Boot relocation and device probe;
- boot-target scan;
- storage load;
- authentication;
- decompression;
- kernel entry;
- kernel init;
- userspace-ready criterion.

Removing a U-Boot command that is never executed does not reduce boot latency. Optimizing an already-small DTB may save negligible time compared with storage retries or DRAM training. Measure before assigning effort.

# 22. A structured 10-week learning path

## Week 1 — Reset to first output

Study Chapters 1–3. Learn CPU state, memory types, MMIO, UART, ROM, SPL, and runtime firmware. Complete Lab 1.

**Deliverable:** a source-backed boot-chain inventory for one virtual or documented board.

## Week 2 — Use U-Boot safely

Study Chapters 4–7. Complete Labs 2 and 4.

**Deliverable:** annotated prompt transcript explaining each command and which state it reads or changes.

## Week 3 — Images and memory

Study Chapter 8 and the interval method in Chapter 7. Complete Lab 7.

**Deliverable:** an image-and-memory map with format, address, size, and ownership.

## Week 4 — Device Tree

Study Chapters 9–11. Complete Lab 5.

**Deliverable:** a compiled educational DTB, a property query, a diff, and a written warning about hardware validity.

## Week 5 — Kernel handoff

Study Chapters 12–13 and the official boot document for one architecture. Complete Lab 9.

**Deliverable:** a complete boot log with the handoff contract annotated.

## Week 6 — Failure isolation

Study Chapter 15. Complete Lab 10.

**Deliverable:** hypothesis, single change, predicted boundary, observation, and conclusion.

## Week 7 — Source reading

Study Chapters 14 and 21. Complete Labs 3 and 12.

**Deliverable:** commit-specific source trace from command registration to final architecture handoff.

## Week 8 — Configuration and automation

Study defconfig, environment, scripts, Standard Boot, and FIT. Complete Labs 8 and 14.

**Deliverable:** reproducible automated boot matching a proven manual boot.

## Week 9 — Write a small loader

Study Chapter 17. Implement only a virtual-platform entry marker, C runtime, and preloaded-payload jump.

**Deliverable:** documented input ABI, memory map, linker map, and return/error behavior.

## Week 10 — Porting, security, and explanation

Study Chapters 18–20. Complete Labs 13 and 15.

**Deliverable:** a board-port prerequisite checklist and a five-minute technically accurate explanation.

# 23. Interview and review questions

## 23.1 Beginner questions

1. Why does the CPU not start Linux directly after power-on?
2. What is the difference between ROM, SRAM, DRAM, and storage?
3. What does “pass control” mean?
4. Why might SPL exist?
5. What is U-Boot responsible for?
6. What is an environment variable?
7. What does a load command do?
8. Why must loaded images not overlap?
9. What is the difference between DTS and DTB?
10. Why is Device Tree not a driver?

## 23.2 Intermediate questions

1. Distinguish U-Boot’s control and working FDT.
2. What does <code>booti</code> need to know?
3. Why can a kernel be silent after U-Boot prints its final message?
4. How would you prove that bytes loaded from storage are correct?
5. Why can disabling one DT node break another device?
6. What do bind and probe mean in driver model?
7. Why is a vendor <code>u-boot.bin</code> sometimes not directly flashable?
8. How do OpenSBI and U-Boot differ?
9. How do Trusted Firmware-A and U-Boot differ?
10. What does a QEMU boot prove?

## 23.3 Advanced questions

1. Describe the entry-state contract for a chosen Linux architecture.
2. Trace relocation and explain why pre-relocation driver support is special.
3. Design a memory map that avoids kernel, initramfs, DTB, U-Boot, and decompression overlap.
4. Explain how FIT configuration signatures can cover the kernel/DTB relationship.
5. Design power-loss-safe A/B metadata and success confirmation.
6. Explain why authenticating only the kernel is insufficient.
7. Compare direct <code>booti</code>, FIT <code>bootm</code>, and UEFI boot for a product.
8. Diagnose a system that cold-boots but repeatedly fails after warm reset.
9. Explain how an incorrect clock rate can appear as a UART baud problem.
10. Plan an incremental U-Boot port for a new board on an already-supported SoC.

## 23.4 Strong-answer pattern

A strong systems answer states:

1. the contract or invariant;
2. the mechanism;
3. the observable evidence;
4. plausible failure modes;
5. the next discriminating test;
6. the boundary of what the evidence proves.

Avoid confidence words as a substitute for evidence.

# 24. Glossary

**A/B update** — An update design with two bootable software slots so a failed trial can fall back.

**ABI** — Application Binary Interface; here, the exact register, memory, state, and calling conventions between binary stages.

**Address** — A number identifying a byte or device register in a processor-visible address space.

**Alias** — A Device Tree name mapping, often used for stable device references such as <code>serial0</code>.

**ARM Trusted Firmware-A (TF-A)** — Reference trusted-world firmware for ARM application processors, often supplying EL3 runtime services.

**ATAG** — Older ARM boot parameter list predating common Device Tree use.

**Authentication** — Establishing that data is authorized by a trusted identity or key.

**Autoboot** — U-Boot automatically executing configured policy after a countdown or other condition.

**BL31** — Common TF-A name for EL3 runtime firmware on AArch64.

**BL33** — Common TF-A name for non-secure payload firmware, often U-Boot.

**Blob** — An opaque or binary data object; meaning depends on context.

**Boot CPU/hart** — The processor core that runs the primary boot path.

**Boot ROM** — Immutable SoC code executed at or immediately after reset.

**bootargs** — Common U-Boot variable and Device Tree chosen property containing the kernel command line.

**bootcmd** — Environment variable commonly containing automatic boot commands.

**bootdev** — Standard Boot abstraction for a device that may provide bootflows.

**bootflow** — Standard Boot description of discovered bootable content and how to start it.

**bootmeth** — Standard Boot method used to discover a bootflow.

**booti** — U-Boot command for booting a flat or supported compressed Linux Image on applicable architectures.

**bootm** — General U-Boot OS boot command, especially for FIT and older U-Boot images.

**bootz** — U-Boot command commonly used for a 32-bit ARM zImage.

**BSS** — Program section for zero-initialized global/static data.

**Cache** — Small fast memory holding copies of data or instructions.

**Chain of trust** — Sequence in which each trusted stage authenticates the next, rooted in an immutable trust anchor.

**Cold boot** — Boot from power-on or a near-power-off hardware state.

**Console** — Input/output channel used for firmware or OS interaction.

**Control FDT** — Device Tree used by U-Boot to configure itself.

**Dcache/Icache** — Data and instruction caches.

**Defconfig** — Minimal saved Kconfig seed for a board or target.

**Device Tree** — Structured hardware/configuration description passed between firmware and operating system.

**DMA** — Direct Memory Access, where devices transfer memory without the CPU copying each byte.

**DRAM** — Large external main memory that normally requires initialization and training.

**Driver** — Code implementing operations for a hardware design.

**Driver model** — U-Boot framework organizing drivers, devices, buses, and uclasses.

**DTB** — Compiled flattened Device Tree Blob.

**DTC** — Device Tree Compiler.

**DTS/DTSI** — Device Tree Source and included source fragment.

**ELF** — Executable and Linkable Format containing sections, symbols, and loading information.

**Endianness** — Byte order used to represent multi-byte values.

**Entry point** — Address of the first instruction a stage is expected to execute.

**Environment** — Named string variables used for U-Boot state and policy.

**Exception level** — AArch64 privilege level, EL0 through EL3.

**FDT** — Flattened Device Tree; commonly the in-memory DTB representation.

**FIT** — Flattened Image Tree, a U-Boot image container and configuration format.

**Firmware** — Software closely responsible for hardware initialization, platform services, or boot.

**Flash** — Non-volatile electronic storage; NOR and NAND have different access and erase properties.

**FSBL** — First-stage bootloader; vendor term whose exact responsibility varies.

**Global data** — U-Boot’s central early/runtime state structure.

**GPT** — GUID Partition Table.

**GRUB 2** — GNU boot manager/loader commonly used on PC and server systems.

**Hart** — RISC-V hardware thread.

**Handoff** — Preparing required state and transferring execution to the next stage.

**Hash** — Fixed-size digest used to detect data changes; unkeyed hashes do not establish authorization.

**Initramfs** — Initial RAM filesystem archive unpacked by the kernel for early userspace.

**Kconfig** — Configuration language and dependency system used by U-Boot and Linux.

**Kernel** — Privileged core of an operating system.

**Linker script** — File controlling memory layout of program sections and symbols.

**Load address** — Address where bytes are placed in memory.

**Magic value** — Constant used to identify a structure or image format.

**Measured boot** — Recording cryptographic measurements for later attestation.

**Memory map** — Definition of address ranges and what occupies them.

**MMIO** — Memory-mapped I/O device registers accessed through addresses.

**MMU** — Memory-management unit for address translation and permissions.

**M-mode/S-mode/U-mode** — RISC-V machine, supervisor, and user privilege modes.

**OpenSBI** — Open-source implementation of the RISC-V Supervisor Binary Interface runtime.

**Overlay** — Device Tree change set applied to a base tree.

**Payload** — Program or data carried by a firmware/image container or started by a stage.

**Phandle** — Device Tree reference identifier connecting consumer and provider nodes.

**Pinmux** — Pin multiplexer configuration selecting which peripheral function appears on a physical pin.

**Probe** — Driver-model operation that initializes a bound device for use.

**Program counter** — CPU register identifying the next instruction address.

**PSCI** — ARM Power State Coordination Interface used for CPU/system power operations.

**Relocation** — Moving U-Boot code/data to its final runtime region and adjusting references.

**Reserved memory** — RAM excluded from general allocation because firmware or a device owns it.

**Reset vector** — Address where a CPU begins execution after reset.

**Rollback protection** — Policy preventing execution of an authorized but too-old vulnerable version.

**ROM** — Read-only memory, typically containing immutable first-stage code.

**Root filesystem** — Filesystem used as <code>/</code> after Linux startup.

**Root of trust** — Earliest implicitly trusted key, code, or hardware state.

**SBI** — RISC-V Supervisor Binary Interface between supervisor software and machine-mode firmware.

**Secure boot** — Boot process that enforces authorization of executable/configuration components.

**Signature** — Cryptographic proof created by a private key and checked with a public key.

**SPL** — U-Boot Secondary Program Loader, commonly responsible for DRAM and loading U-Boot proper.

**SRAM** — Small on-chip RAM usable early in boot.

**Standard Boot** — U-Boot framework for discovering bootflows through boot devices and methods.

**TPL/VPL** — Optional U-Boot phases before SPL for very early loading and verification/selection roles.

**UART** — Universal Asynchronous Receiver/Transmitter, commonly used for serial debugging.

**uclass** — Common U-Boot driver-model interface for a device category.

**UEFI** — Standardized firmware interface and execution environment.

**Verified boot** — Boot policy that checks authorization and blocks unacceptable images.

**Warm reset** — Reset without a full loss of power, potentially retaining hardware state.

**Watchdog** — Timer that resets the system unless software services it or deliberately changes its policy.

**Working FDT** — Device Tree U-Boot prepares and passes to the operating system.

# 25. Primary references and how to continue

## 25.1 U-Boot

- [U-Boot documentation — latest](https://docs.u-boot.org/en/latest/)
- [Build U-Boot](https://docs.u-boot.org/en/latest/build/index.html)
- [TPL/SPL boot phases](https://docs.u-boot.org/en/latest/usage/spl_boot.html)
- [Standard Boot overview](https://docs.u-boot.org/en/latest/develop/bootstd/overview.html)
- [Device Tree control](https://docs.u-boot.org/en/latest/develop/devicetree/control.html)
- [Driver-model design](https://docs.u-boot.org/en/latest/develop/driver-model/design.html)
- [booti](https://docs.u-boot.org/en/latest/usage/cmd/booti.html)
- [bootm](https://docs.u-boot.org/en/latest/usage/cmd/bootm.html)
- [fdt command](https://docs.u-boot.org/en/latest/usage/cmd/fdt.html)
- [FIT format](https://docs.u-boot.org/en/latest/usage/fit/source_file_format.html)
- [U-Boot source repository](https://source.denx.de/u-boot/u-boot)

## 25.2 Linux boot protocols

- [RISC-V kernel boot requirements](https://docs.kernel.org/arch/riscv/boot.html)
- [32-bit ARM Linux boot](https://docs.kernel.org/arch/arm/booting.html)
- [Linux x86 boot protocol](https://docs.kernel.org/arch/x86/boot.html)
- [Linux and Device Tree](https://docs.kernel.org/devicetree/usage-model.html)
- [Kernel command-line parameters](https://docs.kernel.org/admin-guide/kernel-parameters.html)

For ARM64, use the <code>Documentation/arch/arm64/booting.rst</code> file in the exact Linux source release because rendered documentation paths can move.

## 25.3 Device Tree

- [Devicetree Specification](https://devicetree-specification.readthedocs.io/en/latest/)
- [Linux Device Tree usage model](https://docs.kernel.org/devicetree/usage-model.html)
- [Linux kernel Devicetree binding schemas](https://github.com/devicetree-org/dt-schema)

## 25.4 Runtime and trusted firmware

- [OpenSBI documentation](https://github.com/riscv-software-src/opensbi/tree/master/docs)
- [Trusted Firmware-A firmware design](https://trustedfirmware-a.readthedocs.io/en/stable/design/firmware-design.html)
- [Trusted Firmware-A porting guide](https://trustedfirmware-a.readthedocs.io/en/latest/porting-guide.html)

## 25.5 Alternatives

- [GNU GRUB manual](https://www.gnu.org/software/grub/manual/grub/grub.html)
- [UEFI specifications](https://uefi.org/specifications)
- [barebox documentation](https://docs.barebox.org/)
- [coreboot documentation](https://doc.coreboot.org/)
- [LinuxBoot](https://www.linuxboot.org/)
- [iPXE documentation](https://ipxe.org/docs)
- [MCUboot documentation](https://docs.mcuboot.com/)

## 25.6 Source discipline

Use sources in this order:

1. exact board and SoC documentation;
2. exact source commit and its documentation;
3. architecture specification or Linux boot protocol;
4. current upstream project documentation;
5. vendor guides for the matching release;
6. community discussions as clues, not final authority.

Record the version and date. “Latest” links are convenient for learning but may not describe a vendor fork from years earlier.

# 26. Final competency checklist

## Understanding

- [ ] I can explain why boot is staged.
- [ ] I can distinguish loading, validation, authentication, and execution.
- [ ] I can distinguish ROM, SRAM, DRAM, and storage.
- [ ] I can state which stage initializes DRAM on one real or virtual platform.
- [ ] I can distinguish runtime firmware from a bootloader.
- [ ] I can explain U-Boot proper versus SPL.

## U-Boot operation

- [ ] I can identify a U-Boot build by commit/configuration, not banner alone.
- [ ] I can inspect environment without saving it.
- [ ] I can enumerate a device, partition, filesystem, and file separately.
- [ ] I can load a file to a proven-safe RAM address.
- [ ] I preserve actual sizes and check for overlap.
- [ ] I understand the selected boot command and image format.

## Device Tree

- [ ] I can distinguish DTS, DTSI, DTB, and overlay.
- [ ] I can explain compatible strings and bindings.
- [ ] I can trace a device’s provider dependencies.
- [ ] I can distinguish U-Boot’s control and working FDT.
- [ ] I know that U-Boot usually selects/fixes a DTB rather than discovering a whole board from nothing.
- [ ] I can compile, decompile, query, diff, and schema-check a DT.

## Kernel handoff

- [ ] I can state the entry register/state contract for one architecture.
- [ ] I can identify kernel, initramfs, DTB, and decompression regions.
- [ ] I can explain the final bootloader cleanup.
- [ ] I can separate a console failure from a CPU execution failure.
- [ ] I know what the last U-Boot message proves and does not prove.

## Debugging

- [ ] I preserve a known-good baseline and recovery path.
- [ ] I capture complete serial logs.
- [ ] I change one variable per experiment.
- [ ] I locate the last proven boundary.
- [ ] I use independent evidence for the next boundary.
- [ ] I state uncertainty rather than filling gaps.

## Modification and porting

- [ ] I have built and changed U-Boot sandbox.
- [ ] I can navigate command, driver, board, architecture, config, and test code.
- [ ] I understand linker scripts, entry code, BSS, and relocation at a working level.
- [ ] I can define a small loader’s input and kernel ABI.
- [ ] I can list the documentation required for a board port.
- [ ] I do not claim a full port from a DTS-only change.

## Security and production

- [ ] I can distinguish checksum, hash, signature, verified boot, and measured boot.
- [ ] I can identify the root of trust.
- [ ] I consider DTB, command line, and configuration part of security policy.
- [ ] I can explain rollback protection and A/B success confirmation.
- [ ] I protect private keys and do not store them in ordinary source or environment.
- [ ] I treat recovery as part of system design.

# Conclusion

U-Boot becomes understandable when it is treated as one program in a chain of contracts.

The stage before U-Boot must place it in usable memory and enter it in a defined CPU state. U-Boot must initialize the devices it needs, choose policy, load compatible artifacts into non-overlapping memory, construct accurate boot data, and satisfy the kernel’s architecture-specific entry contract. The kernel then takes ownership and creates the environment in which userspace can run.

The productive habit is not memorizing commands. It is asking, at every boundary:

1. Who is executing?
2. In which privilege and memory state?
3. Which artifact was loaded from where to where?
4. How was it validated?
5. Which data is passed to the next stage?
6. What exact observation proves the handoff?
7. What remains unknown?

That habit scales from a beginner’s first QEMU boot to professional firmware bring-up.
