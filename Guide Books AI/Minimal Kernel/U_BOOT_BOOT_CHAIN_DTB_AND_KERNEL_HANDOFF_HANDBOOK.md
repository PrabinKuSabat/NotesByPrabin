# U-Boot, Boot Chains, Devicetree, and Linux Kernel Handoff

## A senior-staff technical handbook with an Orange Pi RV2 revision-3 case study

**Generated:** 2026-08-16  
**Primary target:** 64-bit RISC-V Linux systems, with cross-architecture comparisons  
**Concrete project context:** Orange Pi RV2, 4 GiB, vendor Linux 6.6.63-ky, U-Boot 2022.10ky, OpenSBI dynamic firmware  
**Audience:** senior firmware, platform, kernel, virtualization, performance, reliability, and security engineers
 
---

## Source/version matrix

| Component | Version/baseline used here | Why it matters |
|---|---|---|
| Upstream U-Boot | Documentation current at v2026.07 where the “latest” site identifies that release | Recent standard boot, LMB, Devicetree, FIT, and driver-model behavior |
| RV2 vendor U-Boot | Branch <code>v2022.10-ky</code>, commit <code>89bff4a7e4cadfb5f130edb1ec44c39bff20a427</code> | The target is older and vendor-modified; current upstream behavior cannot be assumed |
| OpenSBI | Upstream v1.9 is current; target version is not yet captured and uses <code>fw_dynamic</code> | Defines one implementation of the RISC-V M-mode/S-mode boundary |
| SBI specification | Ratified v3.0; discover the target’s implemented base version/extensions at runtime | Defines the firmware interface; a spec version does not prove target support |
| RISC-V privileged specification | Snapshot dated 2026-01-20 for reference; use ratified extension versions selected by the product | Defines privilege, traps, CSRs, PMP, and memory-management rules |
| Target Linux | Vendor 6.6.63-ky; project record pins commit <code>ae9e974d3e19f460b6397bfe8f0f1417a073ce05</code> | Kernel DT bindings, drivers, early boot, and initramfs behavior are build-specific |
| Linux reference docs | Current online docs, plus 6.6 binding/process material when relevant | Architectural boot contracts are more stable than individual implementation paths |
| Devicetree | DTSpec online “latest”; flattened binary format version 17 | Defines DTS semantics and DTB wire format |
| DTC/libfdt | Upstream release 1.8.1 (2026-05-28); record the actual host/kernel-bundled tool used | Compiler and runtime blob manipulation |
| dtschema | PyPI release 2026.6 (2026-06-16); bindings still come from the chosen kernel tree | YAML/meta-schema processing and DT validation |
| Orange Pi vendor tree | U-Boot branch/commit above; Linux vendor repository URL still must be pinned with the recorded commit | Separates public vendor integration from project observations |
| UEFI | UEFI 2.11 | Defines EFI loader and <code>ExitBootServices()</code> contracts |
| GNU GRUB | Release 2.14 (2026-01-14) | Relevant to BIOS/UEFI/Linux/Multiboot comparison |
| RV2 target | Orange Pi RV2, current project board stated as 4 GiB | Other public logs include different RAM variants and are not silently merged |

Release evidence: [OpenSBI v1.9 releases](https://github.com/riscv-software-src/opensbi/releases/), [SBI v3.0 release](https://github.com/riscv-non-isa/riscv-sbi-doc/releases), [DTC release archive](https://www.kernel.org/pub/software/utils/dtc/), [dtschema 2026.6](https://pypi.org/project/dtschema/), and [GNU GRUB archive](https://ftp.gnu.org/gnu/grub/).

---

## Executive summary

A reset vector does not “boot Linux.” It starts a chain of independently built programs. Each stage inherits a narrowly defined machine state, establishes more hardware and policy, locates the next stage, constructs that stage’s input contract, and then transfers control. On a representative Orange Pi RV2 chain, the conceptual sequence is:

~~~mermaid
flowchart TD
    A["Reset / immutable Boot ROM"] --> B["SPL: clocks, pins, DRAM, storage"]
    B --> C["OpenSBI: M-mode runtime"]
    C --> D["U-Boot proper: S-mode policy and loading"]
    D --> E["Linux entry: a0=hart ID, a1=DTB"]
    E --> F["Kernel init, initramfs /init, real root"]
    F --> G["PID 1 and user space"]
~~~

The crucial insight is that U-Boot is not one universal binary with one universal predecessor or successor. It is a portable bootloader framework. Depending on platform configuration:

- U-Boot SPL may be the first mutable stage after Boot ROM.
- Trusted Firmware-A, OpenSBI, coreboot, UEFI, or a vendor loader may precede U-Boot proper.
- U-Boot may load a raw Linux **Image**, a compressed image, a FIT image, an EFI executable, an Android boot image, an extlinux configuration, a script, or another firmware payload.
- The Devicetree used to configure U-Boot itself may be distinct from the working Devicetree passed to Linux.
- U-Boot normally consumes or modifies a DTB; it does not discover an arbitrary board well enough to synthesize a correct hardware description from nothing.

For Linux on RISC-V, the final architectural contract is compact but unforgiving: the kernel entry address must be correctly aligned, the MMU must be disabled (<code>satp=0</code>), <code>a0</code> must contain the boot hart ID, and <code>a1</code> must point to a valid, appropriately reserved DTB. Firmware-owned memory must be described as reserved. All the preceding work—DRAM initialization, loading, verification, relocation avoidance, command-line construction, initramfs placement, cache/platform synchronization, secondary-hart policy, and error recovery—exists to make that jump safe.

This handbook treats three things separately:

1. **Architectural contracts:** stable requirements defined by Linux, Devicetree, RISC-V, SBI, UEFI, and other specifications.
2. **Upstream implementation:** current U-Boot and Linux behavior, using current documentation as of the generated date.
3. **Board/vendor facts:** the Orange Pi RV2 2022.10ky firmware chain and the revision-3 experiment, without projecting QEMU or other-board observations onto the target hardware.

---

## Contents

1. [The boot mental model](#1-the-boot-mental-model)
2. [What happens before U-Boot](#2-what-happens-before-u-boot)
3. [U-Boot architecture and runtime](#3-u-boot-architecture-and-runtime)
4. [Exact Linux kernel handoff](#4-exact-linux-kernel-handoff)
5. [Devicetree in the boot chain](#5-devicetree-in-the-boot-chain)
6. [Building a minimal RV2 DTB](#6-building-a-minimal-dtb-for-the-rv2-requirement)
7. [Writing a custom boot stage](#7-writing-a-boot-stage-for-your-own-kernel)
8. [Building and porting U-Boot](#8-building-configuring-and-porting-u-boot)
9. [Orange Pi RV2 revision-3 case study](#9-orange-pi-rv2-revision-3-case-study)
10. [Debugging boot chains](#10-debugging-boot-chains)
11. [Security and reliability](#11-security-and-reliability)
12. [GRUB 2 and other boot technologies](#12-grub-2-and-other-boot-technologies)
13. [Knowledge and 12-week plan](#13-required-knowledge-and-a-12-week-learning-plan)
14. [Practical labs](#14-practical-labs)
15. [Performance implications](#15-boot-choices-and-performance-measurement)
16. [Reference appendices](#appendix-a-architecture-handoff-quick-reference)

## How to read this handbook

### Four reading tracks

| Goal | Read first | Then |
|---|---|---|
| Understand a boot failure | Chapters 1, 4, 9, 10 | Labs 1–8 and the failure-boundary worksheet |
| Port U-Boot to a board | Chapters 2, 3, 5, 8 | Chapters 11 and 15; Labs 9–14 |
| Write a minimal loader | Chapters 4, 6, 7 | Labs 2, 3, 10, 15, and 16 |
| Review a production boot design | Chapters 11, 12, 15 | Appendices A–D and the competency checklist |

### Evidence labels used in the report

| Label | Meaning |
|---|---|
| **Normative** | Required by a published architecture, ABI, specification, or kernel boot document |
| **Upstream implementation** | Describes a current upstream source tree or manual; re-check against the exact tag being shipped |
| **Project observation** | Captured from the user’s RV2 artifacts or experiment record |
| **External corroboration** | Public evidence from a similar board or independent build; useful but not proof about the target |
| **Hypothesis** | Plausible explanation that needs an observation to confirm or falsify |
| **Teaching example** | Illustrative code or data, deliberately not a board-specific production configuration |

> **Key distinction:** A stage’s category, implementation, and packaging are separate. OpenSBI is a privileged runtime; U-Boot proper is a loader/policy engine; a FIT is a container.

> **Evidence boundary:** The RV2 revision-3 record proves restoration of earlier boot progress and a display transition. It does not yet prove the final U-Boot branch, Linux entry, or <code>/init</code>.

> **Hazard:** A physically valid RAM address can still overlap relocated U-Boot, firmware, a framebuffer, the DTB, an initramfs, or a decompression destination.

> **RV2 note:** No QEMU MMIO address, interrupt, compatible string, clock, reset, pin, or memory carve-out in this handbook is asserted to be an RV2 value.

> **Verification:** Prefer the exact specification for the contract, exact source for implementation, full artifact hashes for identity, and UART/JTAG markers for execution.

### Version-boundary rule

Whenever source-level detail matters, inspect the exact shipping commit. In particular, U-Boot’s LMB handling changed substantially after the target’s 2022.10-derived tree; upstream documentation says the global, persistent LMB model began in the 2025.01 development cycle. See [U-Boot LMB design](https://docs.u-boot.org/en/latest/develop/lmb.html).

---

# 1. The boot mental model

## 1.1 A boot chain is a sequence of contracts

Each arrow in a boot diagram means more than “jump to an address.” The outgoing stage must establish:

- the next instruction address;
- the privilege level and execution mode;
- register arguments or a parameter structure;
- executable memory contents and permissions;
- stack and scratch-state assumptions, when specified;
- interrupt state;
- MMU/cache state;
- ownership of CPUs/harts and devices;
- a memory map that prevents overwrite;
- a hardware description or firmware service interface;
- a success/failure and fallback policy.

The receiving stage may assume only what its boot protocol guarantees. Accidentally relying on a predecessor’s current behavior creates a fragile, undocumented ABI.

## 1.2 Generic embedded Linux chain

~~~mermaid
flowchart TD
    R["Reset vector"] --> ROM["Boot ROM"]
    ROM --> XPL["TPL / SPL / first mutable loader"]
    XPL --> FW["Runtime firmware, optional"]
    FW --> BL["U-Boot proper"]
    BL --> K["Linux kernel"]
    K --> I["initramfs /init"]
    I --> U["real root and PID 1"]
~~~

| Stage | Typical privilege | Address class | Input | Responsibilities | Output | Handoff contract |
|---|---|---|---|---|---|---|
| Boot ROM | Highest/platform reset mode | immutable ROM plus small SRAM destination | reset state, straps/fuses, media header | select, minimally initialize, authenticate/load | first mutable image and boot metadata | vendor ROM header/entry/register contract |
| TPL/SPL/FSBL | M-mode/EL3 or vendor mode | on-chip SRAM; later DRAM | ROM metadata and image | clocks, PMIC, pins, DRAM, minimal storage, verification | runtime firmware and/or U-Boot proper | platform/OpenSBI/TF-A/FIT contract |
| Runtime firmware | RISC-V M-mode or Arm EL3 | reserved DRAM/SRAM, resident | platform description and next-stage metadata | delegate privilege, SBI/PSCI/secure services, CPU/power control | S-mode/EL2/EL1 payload plus service ABI | SBI, PSCI/SMCCC, OPAL, or platform ABI |
| U-Boot proper | commonly RISC-V S-mode; varies elsewhere | relocated DRAM | prior-stage FDT/metadata and boot media | drivers, policy, loading, verification, DT fixups, recovery | kernel, initramfs, working DTB, command line | architecture Linux protocol or EFI |
| Linux | supervisor/kernel mode | physical entry, then virtual memory | entry registers/boot structures plus DT/ACPI/EFI | own CPUs, memory, interrupts, devices, scheduling/security | initramfs or real-root process | syscall/exec and userspace ABI |
| initramfs <code>/init</code> | user mode | kernel-created process address space | rootfs archive, cmdline, device state | early mounts, discovery, unlock, real-root selection | intended PID 1/root | <code>execve</code>, switch_root/pivot strategy |
| PID 1 | user mode | process virtual memory | real root and kernel ABI | service lifecycle and system policy | workload | user-space interfaces |

Not every platform contains every stage. Some combine stages; others introduce verification, management-controller, or hypervisor stages.

## 1.3 Representative RISC-V chain

~~~mermaid
sequenceDiagram
    participant ROM as Boot ROM
    participant SPL as U-Boot SPL
    participant SBI as OpenSBI M-mode
    participant UB as U-Boot S-mode
    participant L as Linux S-mode
    ROM->>SPL: Load authenticated/selected image
    SPL->>SPL: Initialize DRAM and boot media
    SPL->>SBI: Enter fw_dynamic with platform data
    SBI->>UB: a0=hart ID, a1=FDT, dynamic-info pointer
    UB->>UB: Load and validate Image, initramfs, DTB
    UB->>L: a0=boot hart, a1=working DTB, satp=0
    L->>SBI: SBI calls for timers, IPIs, HSM, reset
~~~

OpenSBI remains resident in M-mode while U-Boot and Linux normally run in S-mode. “Control passes to Linux” does not mean all earlier firmware disappears; resident firmware may still service supervisor binary interface calls. The OpenSBI firmware documentation defines three important packaging models:

- **FW_DYNAMIC:** the previous stage supplies the next-stage address and metadata at run time.
- **FW_JUMP:** the next-stage address is fixed at build time.
- **FW_PAYLOAD:** the next-stage payload is embedded into the OpenSBI firmware image.

See [OpenSBI firmware documentation](https://github.com/riscv-software-src/opensbi/blob/master/docs/firmware/fw.md) and the [RISC-V SBI specification](https://github.com/riscv-non-isa/riscv-sbi-doc/blob/master/riscv-sbi.adoc).

## 1.4 Representative x86 UEFI chain

~~~mermaid
flowchart TD
    SEC["SEC / PEI"] --> DXE["DXE drivers and Boot Services"]
    DXE --> BM["UEFI Boot Manager"]
    BM --> GRUB["GRUB 2 or Linux EFI stub"]
    GRUB --> MAP["GetMemoryMap"]
    MAP --> EXIT["ExitBootServices"]
    EXIT --> K["Linux kernel"]
~~~

The UEFI model differs fundamentally from a simple DT-based direct jump. Firmware exposes tables, protocols, memory maps, and boot/runtime services. A loader calls <code>GetMemoryMap()</code> and then <code>ExitBootServices()</code> using the current map key. Once exit succeeds, boot services are unavailable and the OS owns boot-services memory; runtime-service regions remain special. See [UEFI 2.11 Boot Services](https://uefi.org/specs/UEFI/2.11/07_Services_Boot_Services.html) and [Linux EFI stub documentation](https://docs.kernel.org/arch/arm/uefi.html).

## 1.5 Orange Pi RV2 revision-3 chain: facts versus unknowns

~~~mermaid
flowchart TD
    A["Boot ROM: exact internal behavior not captured"] --> B["First 30 MiB raw vendor boot region"]
    B --> C["U-Boot SPL 2022.10ky"]
    C --> D["OpenSBI fw_dynamic"]
    D --> E["U-Boot 2022.10ky"]
    E --> F["boot.cmd / boot.scr policy"]
    F --> X["Observed boundary: bootloader-visible HDMI, then black"]
    X -.-> G["Unproven: booti / Linux 6.6.63-ky entry"]
    G -.-> H["Unproven: initramfs /init and later user space"]
~~~

**Project observations:**

- The USB partition starts at sector 61,440, which is 30 MiB at 512 bytes per sector.
- The first 30 MiB is a raw, non-filesystem vendor boot region that contains early firmware/SPL/OpenSBI/U-Boot material.
- A prior layout starting the filesystem at 1 MiB overwrote the raw firmware region and caused a reset loop.
- Restoring the 30 MiB boundary removed that reset loop.
- The display reaches a bootloader-visible state and then becomes black.
- The captured evidence does not establish whether U-Boot executed the final branch, whether Linux reached its early entry code, whether <code>/init</code> ran, or whether DRM/KMS changed the display.

**What is not proven:** removing the reset loop proves that earlier bytes matter and that more of the chain executes. It does not prove a valid kernel handoff. Without UART, a persistent firmware/kernel trace, or another deliberate progress signal, the last known-good boundary remains before the black-screen transition.

## 1.6 Category taxonomy

| Category | Examples | Distinguishing responsibility |
|---|---|---|
| Immutable initialization/root | Boot ROM | reset entry, root trust, first mutable load |
| Program loader/FSBL | TPL, VPL, SPL, vendor FSBL | SRAM-constrained hardware/DRAM/media enablement |
| Privileged/secure runtime | OpenSBI, TF-A BL31, skiboot/OPAL | remains callable below/alongside the OS |
| Firmware interface/implementation | UEFI, EDK II, U-Boot EFI implementation | protocols, tables, boot/runtime-service contract |
| Firmware Boot Manager | UEFI Boot Manager | selects an EFI boot option/application |
| OS boot manager/loader | U-Boot proper, GRUB, systemd-boot, Petitboot | policy, image selection/loading, OS parameters |
| Kernel | Linux or custom kernel | owns scheduling, memory, interrupts, drivers, syscalls |
| Early user space | initramfs <code>/init</code> | storage/unlock/root transition and recovery |

A component can implement more than one category—U-Boot can provide SPL, a proper loader, and EFI services—but the categories remain distinct. OpenSBI is not “RISC-V U-Boot”; GRUB is not UEFI firmware; an EFI Boot Manager is not the Linux kernel; SPL is not U-Boot proper.

---

# 2. What happens before U-Boot

## 2.1 Reset and the immutable root

At reset, a CPU begins at an architecture/platform-defined reset vector, often in immutable ROM or an alias of it. The exact initial state is SoC-specific. Common ROM responsibilities include:

1. Sampling boot straps, fuses, or persistent boot selection.
2. Establishing a minimal clock and reset topology.
3. Reading a boot header from SPI NOR, eMMC boot partitions, SD, USB, NAND, or a recovery interface.
4. Authenticating the first mutable stage when secure boot is enabled.
5. Copying a bounded image into on-chip SRAM.
6. Passing boot-source or security metadata and branching to that image.

ROM behavior is not safely inferred from Linux. It is learned from the SoC boot manual, ROM header/tool documentation, schematics/straps, verified images, and controlled experiments.

## 2.2 Why an SPL exists

U-Boot proper often does not fit in on-chip SRAM and cannot use DRAM until a controller and PHY are trained. SPL—“Secondary Program Loader” in common U-Boot terminology—is a smaller U-Boot build with a restricted driver and feature set. Some systems add TPL or VPL before SPL. Current U-Boot documentation uses **xPL** as a generic name for a program-loader phase and describes a common order of TPL → VPL → SPL → U-Boot proper, while warning that architecture/platform practice can vary. See [U-Boot board initialization flow](https://docs.u-boot.org/en/latest/develop/init.html).

Typical SPL work:

- safe clocks, PLLs, reset deassertion, and voltage coordination;
- pinmux for console and boot storage;
- DRAM controller/PHY configuration and training;
- watchdog servicing;
- minimal block, SPI, NAND, or USB loading;
- FIT parsing, hashing, and signature verification if configured;
- selecting recovery or alternate slots;
- loading OpenSBI/TF-A and U-Boot proper;
- constructing the parameter block expected by the next firmware.

This is why “replace U-Boot” is ambiguous. Replacing only U-Boot proper may leave the vendor SPL and privileged firmware intact. Replacing the first mutable loader also requires the DRAM and boot-ROM knowledge normally hidden below U-Boot proper.

## 2.3 DRAM initialization is platform firmware

DRAM bring-up involves much more than writing a base address:

- controller timing derived from memory type and frequency;
- PHY calibration and training;
- per-lane delay, impedance, and voltage behavior;
- rank, bank, row, and column topology;
- refresh and low-power policy;
- ECC initialization, if present;
- temperature/frequency corners and retries;
- reserved/training memory exclusion.

Vendors frequently supply binary training code or heavily customized source. A senior review should ask:

- Which stage owns training?
- Is the training result deterministic and logged?
- Is fallback frequency supported?
- Does reported RAM size agree among the controller, U-Boot, DTB, and Linux?
- Which ranges contain firmware, secure memory, crash logs, framebuffer, or DMA carve-outs?
- Can an update overwrite training firmware or its parameters?

## 2.4 Runtime privileged firmware

### RISC-V OpenSBI

RISC-V hardware resets in machine mode on common systems. OpenSBI supplies a standardized supervisor interface for functions such as timers, interprocessor interrupts, remote fences, hart-state management, reset, performance counters, and debug console where supported. U-Boot and Linux can therefore remain in supervisor mode rather than containing platform-specific M-mode control code. The exact extension set is discoverable through SBI calls; never assume every platform implements every ratified extension.

OpenSBI’s previous-stage entry convention uses:

- <code>a0</code>: current hart ID;
- <code>a1</code>: FDT address, 8-byte aligned;
- additional firmware-specific dynamic information for FW_DYNAMIC.

OpenSBI then chooses/configures the next stage and transfers to it at the configured privilege level. See the [OpenSBI firmware types](https://github.com/riscv-software-src/opensbi/blob/master/docs/firmware/fw.md).

### Arm Trusted Firmware-A

On Armv8-A, Trusted Firmware-A commonly provides secure-world and exception-level stages: BL1/BL2 for early loading, BL31 as the EL3 runtime, and optional BL32 secure payload, before a non-secure BL33 such as U-Boot or UEFI. Exact composition varies by platform. See [TF-A firmware design](https://trustedfirmware-a.readthedocs.io/en/stable/design/firmware-design.html).

### IBM Power: Hostboot, OPAL/skiboot, and Petitboot

OpenPOWER servers demonstrate a more elaborate public chain. The service processor and self-boot engine participate in IPL; Hostboot initializes processors, buses, and memory and prepares a payload. The OPAL image contains skiboot as a privileged runtime plus a skiroot Linux environment in which Petitboot discovers and selects the host OS. Petitboot commonly uses <code>kexec</code> to start the production Linux kernel, which later uses OPAL calls. See [OpenPOWER Hostboot](https://github.com/open-power/hostboot) and [skiboot](https://github.com/open-power/skiboot).

### IBM Z: IPL and zIPL

IBM Z uses the term **initial program load (IPL)**. In a traditional device IPL, a boot loader prepared by zIPL is loaded and then loads Linux; in virtualized modes, the hypervisor can supply advanced IPL services. The conceptual contract is still staged selection, loading, parameter construction, and transfer, but the media records and firmware environment differ from a U-Boot board. See [IBM Linux boot/IPL documentation](https://www.ibm.com/docs/en/linux-on-systems?topic=shutdown-booting-linux) and [zIPL disk preparation](https://www.ibm.com/docs/en/linux-on-systems?topic=ipl-disk-preparation).

## 2.5 Packaging is part of the ABI

The bytes on storage are often not “just U-Boot.” A vendor image can contain:

- ROM header and signatures;
- DDR firmware or training blob;
- SPL;
- FIT containing OpenSBI, U-Boot proper, and one or more DTBs;
- environment sectors;
- redundant copies and rollback metadata;
- GPT or vendor partition tables;
- filesystem partitions.

U-Boot’s **binman** tool assembles complex flash/disk images from a declarative description and can incorporate SPL, U-Boot, DTBs, external firmware, padding, offsets, and checksums. See [U-Boot binman documentation](https://docs.u-boot.org/en/latest/develop/package/binman.html).

For an unknown board, preserve a byte-for-byte acquisition before modifying anything:

~~~bash
# Context: Linux host, read-only acquisition from an identified removable device.
# Replace /dev/sdX only after checking lsblk, model, serial, and mount state.
sudo dd if=/dev/sdX of=rv2-original-full.img bs=4M iflag=fullblock status=progress
sync
sha256sum rv2-original-full.img

# Capture the first 30 MiB raw region independently.
sudo dd if=/dev/sdX of=rv2-boot-region-30MiB.bin bs=1M count=30 iflag=fullblock status=progress
sha256sum rv2-boot-region-30MiB.bin
~~~

These are destructive only if <code>if</code> and <code>of</code> are reversed. A second reviewer should verify the target device for production or irreplaceable media.

## 2.6 RISC-V privilege, delegation, and protection

RISC-V defines machine, supervisor, and user privilege modes; implementations may add hypervisor-related modes. A common Linux chain uses:

| Mode | Typical component | Responsibilities at the boot boundary |
|---|---|---|
| M-mode | OpenSBI/platform firmware | own machine traps/interrupts, PMP/domain protection, timer/IPI/reset/hart services, delegate permitted traps to S-mode |
| S-mode | U-Boot proper then Linux | supervisor execution, virtual memory, S-mode traps, OS policy |
| U-mode | initramfs and normal processes | unprivileged execution through Linux syscalls |

OpenSBI configures <code>medeleg</code>/<code>mideleg</code> and machine interrupt/trap routing according to hardware and firmware policy. Linux must not assume every event is delegated. PMP can protect resident firmware and constrain domains; if PMP is absent, OpenSBI cannot provide the same physical isolation. The firmware must grant the next stage access to its RAM, DTB, devices, and kernel destination while excluding protected ranges. See [OpenSBI platform requirements](https://github.com/riscv-software-src/opensbi/blob/master/docs/platform_requirements.md) and the [RISC-V machine-level specification](https://docs.riscv.org/reference/isa/v20260120/priv/machine.html).

For FW_DYNAMIC, the prior stage passes <code>a0=hartid</code>, <code>a1=FDT</code>, and <code>a2</code> pointing to the dynamic-info structure containing the next address, next mode, options, and versioned magic. OpenSBI then enters the next stage with the standard hart/FDT values. Exact structure fields must come from the OpenSBI version being built.

## 2.7 Cold, warm, watchdog, and secondary-hart entry

| Reset path | State that may persist | Engineering consequence |
|---|---|---|
| Power-on/cold | little beyond fuses/retention domains | full DRAM and device initialization expected |
| SoC warm reset | DRAM/device/cache/PMIC state may partially persist | early firmware must normalize or explicitly depend on retained state |
| Watchdog reset | reset scope varies by watchdog | read reset cause early; a CPU-only reset may leave DMA active |
| Software reset/SBI SRST | platform-defined reset type/reason | log requested type and actual hardware effect |
| kexec | almost all firmware and DRAM state persists | Linux must quiesce devices; not a firmware reset |

On multi-hart systems, a ROM may release one or all harts. Firmware needs a rendezvous/lottery or hardware policy, per-hart stacks/scratch, and a clear rule for non-boot harts. Modern RISC-V <mark style="background: #08BFFF99;">Linux prefers ordered boot: one hart enters the kernel and starts others through SBI HSM.</mark> Random/spin-wait entry is a compatibility path and complicates memory/fence reasoning.

## 2.8 How to recover the real chain

Use independent evidence:

1. **Partition/raw map:** identify gaps, boot partitions, vendor offsets, redundant copies, environment.
2. **Magic/header scan:** ROM headers, ELF, FIT/DTB magic, compressed streams, certificates.
3. **Binman/ITS/vendor build scripts:** reconstruct intended ordering, load/entry, signing, padding.
4. **Symbols/maps:** connect addresses and banners to binaries.
5. **UART:** separate stages by banner/build ID and time.
6. **Source trace:** find who packages/loads each named artifact.
7. **Controlled corruption of copies:** in QEMU or disposable media, alter one authenticated component and observe the rejecting stage.

Useful host inspection:

~~~bash
# Context: Linux host, acquired image copy.
fdisk -l acquired.img
binwalk acquired.img
strings -a -t x acquired.img | rg 'U-Boot|OpenSBI|SPL|FIT|EDK|GRUB'
grep -aob $'\xd0\x0d\xfe\xed' acquired.img
grep -aob $'\x7fELF' acquired.img
~~~

Signature searches and <code>binwalk</code> are leads; false positives are possible. Confirm candidate offsets using format bounds and source/package descriptions.

---

# 3. U-Boot architecture and runtime

## 3.1 U-Boot’s roles

U-Boot can provide:

- early program-loader phases;
- DRAM and board initialization;
- a driver model for buses and devices;
- interactive shell and scripting;
- persistent environment;
- boot-source and fallback policy;
- filesystems and block/network protocols;
- image parsing, decompression, hashing, signature verification, and measured boot;
- DTB selection and fixups;
- EFI boot services and EFI application launch;
- final native Linux handoff;
- manufacturing, recovery, and diagnostics.

No particular build includes all of these. Kconfig and board configuration determine the attack surface, footprint, and capability set.

## 3.2 Initialization phases

The common upstream flow is:

~~~mermaid
flowchart TD
    S["start.S"] --> L["lowlevel_init"]
    L --> F["board_init_f"]
    F --> R["relocate code and global data"]
    R --> B["clear BSS"]
    B --> Q["board_init_r"]
    Q --> M["main_loop / standard boot / shell"]
~~~

According to [U-Boot board initialization](https://docs.u-boot.org/en/latest/develop/init.html):

- <code>board_init_f()</code> runs before normal DRAM-relocated execution. It establishes essentials such as SDRAM/UART and uses a stack usually in SRAM. Global data is available, but BSS is not assumed to be initialized.
- U-Boot calculates a relocation address, reserves top-of-RAM regions, copies itself as required, relocates pointers, and clears BSS.
- <code>board_init_r()</code> runs with DRAM and normal global/static state available, initializes the rest of the platform, and enters the main boot logic.

Architecture and xPL flows may differ. Inspect:

| Concern | Upstream source areas to inspect |
|---|---|
| Earliest CPU entry | <code>arch/&lt;arch&gt;/cpu/&lt;soc&gt;/start.S</code>, architecture start code |
| Init sequence arrays | <code>common/board_f.c</code>, <code>common/board_r.c</code> |
| Board hooks | <code>board/&lt;vendor&gt;/&lt;board&gt;/</code> |
| SoC clocks/DRAM/pins | <code>arch/&lt;arch&gt;/mach-_</code>, drivers, vendor board code |
| Link layout | architecture and board linker scripts |
| SPL build | <code>common/spl/</code>, <code>CONFIG_SPL__</code> |

## 3.3 Global data, relocation, and memory reservations

U-Boot’s <code>gd</code> structure carries early global state before normal globals are safe. During relocation, U-Boot reserves memory near the top of usable DRAM for some combination of:

- U-Boot’s relocated text/data/BSS;
- malloc arena;
- board information and global data;
- stacks;
- DTB;
- video framebuffer;
- trace/log buffers;
- architecture-specific areas.

Therefore a load address that “lies inside RAM” can still overwrite U-Boot. Use the board’s <code>bdinfo</code>, <code>meminfo</code> where available, environment addresses, and LMB output/diagnostics rather than guessing.

Current upstream U-Boot uses the **logical memory block (LMB)** subsystem to track usable and reserved ranges for boot images. This prevents the kernel, initramfs, DTB, EFI reservations, and U-Boot-owned regions from colliding. The vendor 2022.10ky tree predates current global persistent-LMB behavior, so inspect its actual <code>lib/lmb.c</code>, <code>bootm</code>, and architecture handoff paths before assuming current rules.

## 3.4 Driver model

U-Boot’s driver model represents hardware using:

- **uclass:** a class API such as serial, MMC, I2C, SPI, Ethernet, GPIO;
- **driver:** code capable of driving a compatible device;
- **device:** one bound instance;
- **parent/child topology:** a bus and devices below it;
- **platform data/private data:** configuration and run-time state.

A typical lifecycle is bind → process firmware/DT data → probe → operate → remove/unbind. Binding creates a device instance; probing usually enables hardware and allocates run-time resources. The Devicetree provides topology and properties, while Kconfig controls which drivers are actually compiled. A DT node cannot materialize a missing driver. See [U-Boot driver-model design](https://docs.u-boot.org/en/latest/develop/driver-model/design.html).

Useful shell inspection, if built:

~~~text
=> dm tree
=> dm uclass
=> dm drivers
=> bind
=> unbind
~~~

## 3.5 Configuration layers

U-Boot configuration is not one file:

| Layer | Purpose |
|---|---|
| Kconfig / defconfig | Compile-time features, driver selection, commands, SPL size trade-offs |
| Devicetree | Hardware instances and their properties |
| Board C code | Hooks not yet represented generically; late fixups and policy |
| Text environment <code>*.env</code> | Default scripts and variables, processed at build time |
| Saved environment | Mutable persistent overrides |
| Runtime variables | Session-local values, including discovered addresses and generated values |
| Boot configuration files | extlinux.conf, boot.scr, EFI variables, Android metadata, FIT config |

A common debugging trap is editing a default environment while an older saved environment overrides it. Inspect with <code>env info</code>, <code>env print</code>, and the platform’s environment storage configuration. Saving an environment is a persistent write and may damage recovery behavior if storage offsets are wrong.

## 3.6 Environment and scripting

Important conventional variables include:

| Variable | Meaning |
|---|---|
| <code>bootcmd</code> | Command executed after boot delay |
| <code>bootdelay</code> | Autoboot wait policy |
| <code>bootargs</code> | Linux command line |
| <code>kernel_addr_r</code> | Suggested RAM load address for kernel |
| <code>ramdisk_addr_r</code> | Suggested initramfs load address |
| <code>fdt_addr_r</code> | Suggested working DTB load address |
| <code>scriptaddr</code> | Suggested script load address |
| <code>fdtcontroladdr</code> | U-Boot control FDT; normally read-only after relocation |
| <code>fdt_high</code>, <code>initrd_high</code> | Relocation constraints; powerful and easy to misuse |
| <code>bootcount</code>, <code>bootlimit</code>, <code>altbootcmd</code> | Boot-attempt/fallback policy when configured |

These addresses are board policy, not universal constants. The authoritative source is the shipping board configuration plus actual DRAM/reservation state.

Example readable environment script:

~~~sh
# Context: U-Boot environment source, not a POSIX shell.
load_kernel=load mmc ${devnum}:${bootpart} ${kernel_addr_r} /boot/Image
load_fdt=load mmc ${devnum}:${bootpart} ${fdt_addr_r} /boot/dtb/vendor/board.dtb
load_initrd=load mmc ${devnum}:${bootpart} ${ramdisk_addr_r} /boot/initramfs.cpio.gz

boot_local=run load_kernel; run load_fdt; run load_initrd; \
    fdt addr ${fdt_addr_r}; \
    fdt resize 4096; \
    fdt set /chosen bootargs "${bootargs}"; \
    booti ${kernel_addr_r} ${ramdisk_addr_r}:${filesize} ${fdt_addr_r}
~~~

There is a subtle bug here: <code>${filesize}</code> is evaluated after the last successful <code>load</code>, which is the initramfs only because the sequence happens to load it last. A later edit that loads the DTB after the initramfs silently passes the DTB size as the ramdisk size. Robust scripts copy sizes immediately:

~~~sh
# Context: U-Boot shell.
load mmc 0:1 ${ramdisk_addr_r} /boot/initramfs.cpio.gz
setenv ramdisk_size ${filesize}
load mmc 0:1 ${fdt_addr_r} /boot/dtb/vendor/board.dtb
booti ${kernel_addr_r} ${ramdisk_addr_r}:${ramdisk_size} ${fdt_addr_r}
~~~

See [U-Boot environment documentation](https://docs.u-boot.org/en/latest/usage/environment.html).

## 3.7 Repository layout

| Path | Primary role |
|---|---|
| <code>arch/</code> | CPU/architecture entry, MMU/cache, handoff, SoC architecture support |
| <code>board/</code> | board-specific hooks, detection, policy |
| <code>drivers/</code> | driver-model implementations: serial, clock, reset, block, net, USB, PCI, etc. |
| <code>common/</code> | common initialization, console, main-loop and shared infrastructure |
| <code>cmd/</code> | shell commands and command registration |
| <code>boot/</code> | image/OS boot, standard boot, FDT preparation, boot methods |
| <code>env/</code> | environment core and persistent backends |
| <code>fs/</code> | filesystem APIs/implementations |
| <code>disk/</code> | partitions and disk abstractions |
| <code>net/</code> | network protocols and boot transports |
| <code>lib/</code> | common libraries, crypto, compression, libfdt integration |
| <code>include/</code> | public/internal headers and legacy board configuration |
| <code>dts/</code>, architecture DTS paths | control trees, shared U-Boot DTS content |
| <code>tools/</code> | host tools: mkimage/dumpimage, binman, dtoc and build helpers |
| <code>test/</code> | unit, sandbox, Python and functional tests |
| Kconfig/Makefiles | feature dependency/selection and build graph |

Host tools run on the build machine and are not U-Boot target code. A generated <code>.config</code>, <code>include/generated/autoconf.h</code>, map, control DTB, SPL binary, proper binary, and final package together describe a build.

## 3.8 Artifacts and phase distinctions

| Artifact | Meaning; verify per board |
|---|---|
| <code>spl/u-boot-spl</code> / <code>u-boot-spl.bin</code> | SPL ELF/raw output |
| <code>u-boot</code> | U-Boot proper ELF with symbols |
| <code>u-boot.bin</code> | flat proper binary, composition depends on build |
| <code>u-boot-nodtb.bin</code> | proper binary without separate control DTB |
| <code>u-boot.dtb</code> | control DTB |
| <code>u-boot-dtb.bin</code> | concatenated proper binary/control DT in older/common flows |
| <code>u-boot.itb</code> | FIT packaging, often proper plus DT(s)/firmware |
| binman platform image | ROM/flash-ready composition with offsets/padding/firmware |

Early malloc may be a tiny pre-relocation allocator; normal malloc is established later in DRAM. Pre-relocation drivers must be explicitly retained/configured and cannot assume the full runtime. Exception vectors, cache/MMU enablement, and debug UART timing are architecture/board specific: read the exact map/start code before placing breakpoints.

## 3.9 Standard boot

Modern U-Boot’s standard boot framework separates:

- **bootdev:** a device/media source and access method;
- **bootmeth:** a method for finding and interpreting an OS description;
- **bootflow:** one discovered candidate with state, files, and chosen method.

Common methods include extlinux, EFI boot manager/applications, scripts, and platform-specific formats. Useful commands:

~~~text
=> bootdev list
=> bootmeth list
=> bootflow scan -lb
=> bootflow list
=> bootflow select 0
=> bootflow info
=> bootflow boot
~~~

The framework improves discoverability and reduces monolithic scripts, but it does not erase the final protocol: a boot method still loads, validates, prepares, and transfers to an OS. See [U-Boot standard boot overview](https://docs.u-boot.org/en/latest/develop/bootstd/overview.html).

## 3.10 Image formats

| Format | Contains | Strengths | Risks/constraints |
|---|---|---|---|
| Raw Linux <code>Image</code> | Uncompressed architecture image | Simple, direct <code>booti</code> | Kernel/DTB/initramfs managed separately |
| Legacy uImage | Header + one payload | CRC, historical tooling | CRC is not authentication; limited composition |
| FIT | Tree of images/configurations, hashes/signatures | Multiple kernels/DTBs/ramdisks, selection, verified boot | Key custody and configuration policy must be correct |
| EFI PE/COFF | EFI application, often Linux EFI stub or UKI | UEFI-compatible discovery and signing | Requires EFI environment and memory-map discipline |
| Android boot/vendor_boot | Android kernel/ramdisk/metadata | Android update ecosystem | Version-specific format and AVB integration |
| boot script | Compiled command script | Flexible board policy | Mutable scripting increases attack and failure surface |

FIT hashes provide corruption detection, not authenticity. FIT signatures can authenticate signed nodes/configurations when the trusted public key is anchored in a protected U-Boot control DT or equivalent trust store. See [U-Boot verified boot](https://docs.u-boot.org/en/latest/usage/fit/verified-boot.html) and [FIT signature verification](https://docs.u-boot.org/en/v2025.01/usage/fit/signature.html).

## 3.11 Boot command semantics

| Command | Intended input | Main validation/preparation | Final action |
|---|---|---|---|
| <code>booti</code> | architecture flat/compressed Linux Image, optional initrd/FDT | image header/setup, decompression/move, LMB, FDT/initrd | native Linux entry |
| <code>bootm</code> | legacy/FIT and supported OS images | parse hashes/signatures/config, relocate auxiliaries, OS state machine | OS-specific entry |
| <code>bootz</code> | Arm zImage | zImage setup plus FDT/initrd | Arm Linux entry |
| <code>bootefi</code> | PE/COFF EFI application | EFI image load, protocols/system table | EFI application entry |
| <code>bootelf</code> | ELF | parse program/section headers according to options | ELF entry |
| <code>go</code> | arbitrary address | minimal argument setup; little format validation | call raw address |
| <code>source</code> | U-Boot script image or selected script format | header/format and command interpretation | executes commands, not an OS ABI |

Availability and exact semantics are Kconfig/release/architecture dependent. <code>go</code> is not a Linux boot protocol; it does not automatically construct DT/initramfs/state. <code>bootelf</code> is appropriate only when the payload’s ABI matches the state U-Boot provides. <code>uInitrd</code> is a legacy U-Boot-wrapped ramdisk, whereas a raw initramfs passed to <code>booti</code> needs its address and exact size.

Network and storage paths feed these commands:

- MMC/eMMC/SD, USB mass storage, NVMe, SATA/SCSI expose block devices, partitions, and filesystems when their drivers/commands are built.
- DHCP/PXE/TFTP can discover configuration and load images; HTTPS/TLS capability and trust configuration are build-specific.
- “distro boot” scripts/extlinux and newer standard boot are related policy ecosystems, not identical implementation in every release.
- <code>pxefile_addr_r</code> and <code>scriptaddr</code> require the same interval analysis as kernel/FDT buffers.

---

# 4. Exact Linux kernel handoff

## 4.1 The universal preparation sequence

Regardless of command syntax, a native boot path performs this state machine:

~~~mermaid
stateDiagram-v2
    [*] --> Locate
    Locate --> Load
    Load --> Authenticate
    Authenticate --> Place
    Place --> Describe
    Describe --> Quiesce
    Quiesce --> Transfer
    Transfer --> [*]
    Authenticate --> Recovery: reject
    Place --> Recovery: overlap
    Describe --> Recovery: invalid parameters
~~~

1. Locate the selected images.
2. Read them completely; detect short reads.
3. Parse format and lengths with overflow checks.
4. Verify integrity/authenticity according to policy.
5. Decompress or relocate safely.
6. reserve non-overlapping physical ranges.
7. Construct DTB, command line, initramfs bounds, and architecture-specific parameters.
8. Stop asynchronous DMA and platform activity that the kernel does not own yet.
9. Put CPUs, interrupts, MMU, caches, and privilege state into the required condition.
10. Transfer once. A normal native kernel boot does not return.

## 4.2 What <code>booti</code> does

The command syntax is:

~~~text
booti [kernel_addr [initrd_addr[:size]] [fdt_addr]]
~~~

Important rules from [U-Boot booti documentation](https://docs.u-boot.org/en/latest/usage/cmd/booti.html):

- A raw initramfs requires an explicit <code>:size</code>.
- Use <code>-</code> for “no initramfs” while still supplying an FDT.
- A flat or supported compressed Linux <code>Image</code> may be handled.
- Compressed images require configured decompression destination/capacity variables such as <code>kernel_comp_addr_r</code> and <code>kernel_comp_size</code>.
- Current documentation limits the uncompressed size to at most ten times the compressed size in this path; verify the exact shipping source.

In current upstream source, <code>cmd/booti.c</code> performs image setup, optional decompression/movement, LMB reservation, auxiliary-image discovery, and then executes bootm preparation/go states. See [current upstream cmd/booti.c](https://github.com/u-boot/u-boot/blob/master/cmd/booti.c). The vendor 2022.10ky path must be traced in its own tree because helper names, LMB lifetime, FIT behavior, and cleanup details can differ.

## 4.3 RISC-V native Linux contract

**Normative requirements** from [Linux RISC-V boot requirements](https://docs.kernel.org/arch/riscv/boot.html):

| Item | Required state at kernel entry |
|---|---|
| Entry argument <code>a0</code> | Hart ID of the boot CPU |
| Entry argument <code>a1</code> | Physical address of the Devicetree |
| MMU | Disabled; <code>satp=0</code> |
| Kernel alignment | 2 MiB aligned for RV64; 4 MiB for RV32 |
| Reserved firmware memory | Marked in DT reserved memory, or conveyed through UEFI memory map |
| Secondary harts | Ordered boot with SBI HSM is preferred; spin-wait is legacy |
| Privilege | Kernel normally enters S-mode under SBI firmware |

Conceptual final branch:

~~~c
// Context: conceptual RISC-V firmware pseudocode, not a complete U-Boot function.
typedef void (*linux_entry_t)(unsigned long hartid, const void *fdt);

static void enter_linux(unsigned long entry_pa,
                        unsigned long boot_hart,
                        const void *working_fdt)
{
    validate_fdt(working_fdt);
    reserve_all_images();
    stop_bootloader_dma();
    stop_or_park_secondary_harts();
    disable_interrupt_delivery_as_required();
    set_satp_zero_and_fence();
    make_loaded_instructions_visible_to_cpu();

    linux_entry_t kernel = (linux_entry_t)entry_pa;
    kernel(boot_hart, working_fdt);
    platform_fatal("Linux returned");
}
~~~

The exact cache maintenance needed is platform-dependent. RISC-V <code>fence.i</code> establishes instruction-stream visibility on the executing hart after code writes; remote harts require an appropriate synchronization mechanism when applicable. A bootloader must follow its platform cache-coherency rules rather than copy an Arm cache sequence. Ordered SBI HSM boot reduces the number of active harts at the boundary.

Linux first constructs an early page-table environment in <code>setup_vm()</code>, switches into virtual addressing, and later establishes the final mapping in <code>setup_vm_final()</code>. A failure before usable early console can look identical to a failure before the branch.

## 4.4 AArch64 native contract

From [Linux arm64 booting](https://docs.kernel.org/arch/arm64/booting.html):

| Item | Required state |
|---|---|
| <code>x0</code> | Physical address of DTB |
| <code>x1..x3</code> | Zero |
| MMU | Off |
| Interrupts | Masked |
| DMA-capable devices | Quiesced unless specifically allowed |
| DTB | 8-byte aligned; size/placement constraints apply |
| initrd | Described in DT <code>/chosen</code>; must meet physical placement rules |

The EFI-stub path has a different predecessor protocol but converges on the architecture’s kernel startup.

## 4.5 x86 Linux boot protocol

From [Linux x86 boot protocol](https://docs.kernel.org/arch/x86/boot.html):

- A BIOS-style loader constructs <code>struct boot_params</code>, loads setup and protected-mode kernel portions, populates memory maps and command line, and enters the designated 32-bit or 64-bit entry.
- For a 32-bit entry, the CPU is in 32-bit protected mode with paging disabled and <code>ESI</code> points to <code>boot_params</code>.
- For a 64-bit entry, the loader establishes long mode and the required page tables; <code>RSI</code> points to <code>boot_params</code>, and the entry is at the protocol-defined offset.
- An EFI-stub boot begins as an EFI application, consumes UEFI services and memory map, exits boot services, and transitions internally.

This is why “all kernels take a DTB register” is false. DT is dominant on many embedded architectures, while x86 conventionally uses boot-parameter structures plus ACPI/EFI information.

## 4.6 PowerPC direct-FDT convention

The PowerPC kernel documentation defines several historical entry modes. In a common direct flattened-Devicetree convention, <code>r3</code> carries the FDT address, <code>r4</code> may carry the kernel address, and <code>r5</code> is null, but Open Firmware and platform-specific conventions differ. See [Linux PowerPC booting](https://docs.kernel.org/arch/powerpc/booting.html). Never generalize one PowerPC path to OPAL/Petitboot without tracing the actual <code>kexec</code> and firmware interface.

## 4.7 Handoff invariants

Before the final branch, assert:

~~~text
kernel_start < kernel_end
initrd absent OR initrd_start < initrd_end
DTB magic == 0xd00dfeed
DTB totalsize is within loaded buffer
all additions and alignments are overflow-safe
kernel, initrd, DTB, loader, firmware, stack, and reserved regions do not overlap
entry address satisfies architecture alignment
DT memory nodes cover the actual usable RAM and exclude firmware carve-outs
bootargs are bounded and terminated
secondary-CPU state matches the architecture protocol
~~~

For a production loader, these are code assertions with observable error codes, not checklist prose.

## 4.8 Why “Starting kernel …” is not proof of kernel execution

That message is normally printed by the bootloader immediately before cleanup/transfer. It proves the loader reached a late path. It does not prove:

- the branch target was correct;
- the instruction stream was visible;
- the kernel passed its first instruction;
- the DTB was valid;
- early console matched the hardware;
- the kernel survived MMU establishment;
- an initramfs existed or contained executable <code>/init</code>.

Define explicit evidence boundaries:

| Observation | Strongest justified claim |
|---|---|
| SPL banner | SPL serial path executed |
| OpenSBI banner | OpenSBI reached its console initialization |
| U-Boot prompt | U-Boot proper initialized enough console/shell state |
| “Starting kernel …” | U-Boot reached its late handoff path |
| Earliest Linux marker | Kernel entry/early code executed |
| <code>Run /init as init process</code> | Kernel reached initramfs exec attempt |
| Marker from <code>/init</code> | User-mode initramfs program executed |
| Real root/PID 1 log | Root pivot/mount and intended user space progressed |

## 4.9 From entry assembly to PID 1 on RISC-V

A representative Linux sequence, with exact symbols varying by release:

1. Architecture entry assembly preserves/records <code>a0/a1</code>, verifies the hart path, establishes very early status, and branches into early setup.
2. The kernel validates the Image/entry context and selects boot-hart versus secondary-hart paths.
3. <code>setup_vm()</code> constructs temporary mappings needed to execute at the linked virtual address.
4. Early FDT scanning learns memory, chosen command line, initrd, and reservations before normal allocators.
5. <code>setup_vm_final()</code> and architecture setup establish final kernel mappings.
6. Trap vectors, per-CPU state, interrupt controllers, timekeeping, and SBI services are initialized.
7. The boot CPU brings up allocators/scheduler and later starts secondary harts through the supported method.
8. Console transitions from earlycon to the normal serial/display driver.
9. Generic initcalls probe drivers and prepare rootfs.
10. The kernel unpacks initramfs if supplied/built in, tries configured init programs, and performs an <code>execve</code>-equivalent transition to the first user process.

On the wire:

| Last evidence | Likely search region; not a conclusion |
|---|---|
| U-Boot pre-jump only | branch target/state/cache/exception before observable kernel code |
| raw entry byte only | early page-table/FDT/trap setup or early console parameters |
| earlycon then silence | memory/interrupt/SMP/initcall/normal-console transition |
| initramfs unpack error | archive compression/corruption/memory |
| “No working init found”/exec error | <code>/init</code> path, mode, ISA, interpreter, dynamic loader |
| root-device timeout | storage/PHY/DT/driver/command-line root policy |
| <code>/init</code> marker then stop | user-space script/mount/device policy |

## 4.10 Tiny RISC-V kernel entry stub

This teaching stub receives the Linux-style <code>a0/a1</code> contract for a custom kernel, validates that the DTB pointer is nonzero and 8-byte aligned, checks the big-endian magic without a stack, then establishes a stack and calls C. It does not validate bounds or hart topology and is not Linux source.

~~~asm
    .section .text.entry
    .globl kernel_entry
kernel_entry:
    beqz a1, bad_entry
    andi t0, a1, 7
    bnez t0, bad_entry

    # DTB bytes d0 0d fe ed; lwu is little-endian on typical RV64 targets.
    lwu t0, 0(a1)
    li t1, 0xedfe0dd0
    bne t0, t1, bad_entry

    la sp, kernel_stack_top
    andi sp, sp, -16
    # a0 remains hart ID; a1 remains DTB physical address.
    tail kernel_main

bad_entry:
    # A real kernel writes a verified-safe progress code or invokes SBI reset.
1:
    wfi
    j 1b

    .section .bss.stack, "aw", @nobits
    .align 12
kernel_stack:
    .space 16384
kernel_stack_top:
~~~

The byte-swapped immediate is correct only for a little-endian CPU reading the four network-order DTB magic bytes as a word. A portable implementation should compare bytes or convert explicitly.

## 4.11 Multiboot2

Multiboot2 defines a boot-loader/OS contract used notably by GRUB and custom kernels. The loader finds a Multiboot2 header, loads the image, and enters with an architecture-defined magic/register plus a pointer to a tagged information structure containing command line, modules, memory map, framebuffer, ACPI/EFI data, and other records. It is not the RISC-V Linux <code>a0/a1</code> protocol and is not a substitute for SBI. See the [Multiboot2 specification](https://www.gnu.org/software/grub/manual/multiboot2/multiboot.html).

---

# 5. Devicetree in the boot chain

## 5.1 What Devicetree is

Devicetree is a hierarchical data structure that describes discoverable and non-discoverable hardware plus selected boot parameters. The source form is DTS/DTSI; the runtime flattened binary is a DTB/FDT. It is neither executable driver code nor a hardware probe database.

A node expresses identity, address translation, registers, interrupts, clocks, resets, DMA relationships, power domains, pin control, reserved memory, CPU topology, and other bindings. The operating system matches <code>compatible</code> strings to drivers compiled into or loadable by that OS.

## 5.2 Three distinct trees engineers often confuse

| Tree | Consumer | Purpose | May be modified? |
|---|---|---|---|
| Build-time DTS/DTSI | dtc/U-Boot build/Linux build | Human-maintained hardware source | Yes, under source control |
| U-Boot control FDT | U-Boot driver model | Configure U-Boot’s devices | Generally treat as read-only after relocation |
| Working FDT | Next OS | Linux hardware/boot description | Yes; U-Boot fixups and overlays commonly apply |

The U-Boot <code>fdt</code> command operates on the working tree by default and can select the control tree with <code>-c</code>. Changing the working tree does not reconfigure an already-bound U-Boot driver model. See [U-Boot fdt command](https://docs.u-boot.org/en/v2026.04/usage/cmd/fdt.html).

## 5.3 How U-Boot gets its control DT

With <code>CONFIG_OF_CONTROL</code>, current U-Boot supports several modes described in [Devicetree control](https://docs.u-boot.org/en/latest/develop/devicetree/control.html):

| Mode | Description | Appropriate use |
|---|---|---|
| <code>OF_SEPARATE</code> | Build <code>u-boot.dtb</code> separately and commonly append/package it | Normal production integration |
| <code>OF_EMBED</code> | DTB embedded into U-Boot binary | Development/debug; documentation discourages production use |
| <code>OF_BOARD</code> | Board supplies FDT at run time | Prior firmware/platform handoff |
| Bloblist path | Prior phase passes structured blobs including FDT | Multi-stage U-Boot/xPL flows |
| Sandbox host file | Sandbox reads a host-side tree | Tests/development |

An external DTB may be substituted at build time using <code>EXT_DTB</code>. U-Boot-specific adjustments commonly live in a <code>*-u-boot.dtsi</code> so the base hardware description can remain aligned with the OS tree. Space-constrained SPL builds may use generated platform data (<code>OF_PLATDATA</code>) rather than a full live DT.

The **flat blob** is the serialized FDT buffer. U-Boot’s optional **live tree** is an in-memory node/property representation used by supporting code; edits must use the correct API and be synchronized/flattened for the consumer. Vendor-era configurations may use names such as <code>CONFIG_OF_PRIOR_STAGE</code>; current trees also describe boardor bloblist-supplied modes. Confirm exact Kconfig symbols in the selected release. <code>fdt_addr</code>, <code>fdt_addr_r</code>, and <code>fdtcontroladdr</code> are not synonyms: scripts/platforms may use <code>fdt_addr</code> for a current blob, <code>fdt_addr_r</code> as a relocatable-load convention, and <code>fdtcontroladdr</code> for U-Boot’s control tree.

## 5.4 Where an OS DTB comes from

U-Boot can obtain the Linux DTB from:

- a filesystem file loaded by a script or boot method;
- a FIT configuration containing one or more FDT images;
- a DTB already supplied by prior firmware;
- a compiled/packaged board DTB;
- a base DTB plus one or more overlays;
- an EFI configuration table in an EFI-oriented path.

It then may apply:

- selected board/revision overlay;
- memory size/range correction;
- MAC addresses and serial numbers;
- <code>/chosen/bootargs</code>;
- initramfs start/end;
- console path;
- reserved-memory carve-outs;
- firmware nodes and runtime interfaces;
- disabled/okay status changes;
- measured/verified-boot metadata.

## 5.5 Does U-Boot generate its own DTB?

The precise answer is:

- U-Boot’s **build** can compile a DTS into the control DTB.
- Packaging tools can include or select DTBs.
- At runtime U-Boot can clone, resize, edit, and overlay a working tree.
- Board code can create nodes/properties programmatically.
- On some virtual or firmware-described platforms, earlier firmware can generate the DT and pass it to U-Boot.
- Generic U-Boot cannot reliably infer an arbitrary board’s wiring, clocks, interrupts, regulators, pinmux, DMA topology, or reserved firmware ranges solely by probing.

Thus “generate a DTB” can mean four different operations:

1. **Compile:** DTS → DTB using <code>dtc</code>.
2. **Receive/select:** accept a prior-stage blob or select a stored/FIT blob.
3. **Copy/fix up:** populate a known schema with bootargs, initrd, RAM, serial, MAC, and other discovered values.
4. **Discover/synthesize completely:** enumerate hardware. This covers only discoverable portions and never replaces platform data for non-discoverable hardware.

## 5.6 DTS anatomy

~~~dts
/dts-v1/;

/ {
    model = "Teaching Board";
    compatible = "example,teaching-board", "example,soc";
    #address-cells = <2>;
    #size-cells = <2>;

    chosen {
        stdout-path = "/soc/serial@10000000:115200n8";
    };

    memory@80000000 {
        device_type = "memory";
        reg = <0x0 0x80000000 0x0 0x08000000>;
    };

    reserved-memory {
        #address-cells = <2>;
        #size-cells = <2>;
        ranges;

        firmware@80000000 {
            reg = <0x0 0x80000000 0x0 0x00200000>;
            no-map;
        };
    };
};
~~~

**Teaching example:** the addresses are generic examples, not Orange Pi RV2 data. The root defines address/size cell widths inherited by children. <code>reg</code> encodes address/length pairs using the parent’s widths. A 64-bit address is represented as two 32-bit big-endian cells.

## 5.7 Required and foundational nodes

DTSpec describes a root node, CPU information, and memory description as foundational. A useful Linux platform DT normally also needs interrupt controllers, timer relationships, chosen boot data, and essential buses/devices.

| Node/property | Purpose |
|---|---|
| <code>/</code>, <code>compatible</code>, <code>model</code> | Platform identity and global cell widths |
| <code>/cpus</code> | CPU/hart nodes, topology, ISA/cache/status, timebase |
| <code>/memory</code> | Usable physical RAM |
| <code>/reserved-memory</code> | Exclude firmware, DMA, framebuffer, crash, secure, or shared ranges |
| <code>/chosen</code> | Bootargs, stdout path, initrd range, random seeds and boot metadata |
| interrupt controller nodes | IRQ topology |
| clock/reset/power nodes | Dependencies needed before device access |
| bus nodes and <code>ranges</code> | Child-to-parent address translation |

See [DTSpec Devicetree basics](https://devicetree-specification.readthedocs.io/en/latest/chapter2-devicetree-basics.html) and [required nodes](https://devicetree-specification.readthedocs.io/en/latest/chapter3-devicenodes.html).

The Linux-specific <code>linux,usable-memory-range</code>, when supported for a specialized path such as crash-kernel boot, further constrains usable memory; it does not repair an incorrect <code>/memory</code> node or missing firmware reservation. Its cells follow the root address/size widths.

## 5.8 Phandles and provider/consumer relationships

Many properties contain a phandle followed by provider-defined specifier cells:

~~~dts
uart0: serial@10000000 {
    compatible = "ns16550a";
    reg = <0x0 0x10000000 0x0 0x100>;
    clocks = <&sysclk>;
    interrupts = <10>;
};
~~~

The clock provider defines <code>#clock-cells</code>; the interrupt parent defines <code>#interrupt-cells</code>. The cell count and meaning come from the binding, not from visual intuition. A syntactically valid DTB can still encode semantically invalid specifiers.

## 5.9 DTB binary format

A flattened Devicetree contains:

- a header beginning with magic <code>0xd00dfeed</code>;
- a memory-reservation block;
- a structure block of begin-node/end-node/property tokens;
- a strings block holding property names.

Header fields and cells are big-endian. Current commonly emitted blobs use format version 17. The <code>totalsize</code>, offsets, and sizes must be validated before traversal. See [DTSpec flattened format](https://devicetree-specification.readthedocs.io/en/latest/chapter5-flattened-format.html).

## 5.10 Compile, inspect, round-trip, and validate

~~~bash
# Context: Linux development host.
dtc -I dts -O dtb -o board.dtb board.dts
dtc -I dtb -O dts -o board.roundtrip.dts board.dtb
fdtdump board.dtb | less
fdtget -t s board.dtb / compatible
fdtget -t x board.dtb /memory@80000000 reg
fdtoverlay -i base.dtb -o merged.dtb feature.dtbo

# In a Linux kernel source tree with dependencies installed:
make ARCH=riscv dtbs
make ARCH=riscv dt_binding_check
make ARCH=riscv dtbs_check W=1
~~~

<code>dtc</code> validates binary structure and some local consistency. Schema validation checks binding rules. Boot testing checks driver behavior. These are separate gates. See [Linux Devicetree schema writing](https://docs.kernel.org/devicetree/bindings/writing-schema.html).

## 5.11 U-Boot working-FDT session

~~~text
# Context: U-Boot shell. Addresses must be valid for the board.
=> load mmc 0:1 ${fdt_addr_r} /boot/dtb/vendor/board.dtb
=> fdt addr ${fdt_addr_r}
=> fdt header
=> fdt print /
=> fdt print /chosen
=> fdt resize 8192
=> fdt set /chosen bootargs "console=ttyS0,115200 earlycon root=/dev/ram0"
=> fdt set /chosen stdout-path "/soc/serial@10000000:115200n8"
=> fdt get value current_args /chosen bootargs
=> echo ${current_args}
~~~

For initramfs bounds, prefer the boot command’s standard fixup path unless the platform requires manual construction. If manually setting 64-bit properties, the cell representation and end-exclusive convention must be correct; do not copy a 32-bit example.

To apply an overlay:

~~~text
# Context: U-Boot shell; base FDT must have space and symbols as required.
=> load mmc 0:1 ${fdt_addr_r} /boot/base.dtb
=> setenv base_size ${filesize}
=> load mmc 0:1 ${loadaddr} /boot/feature.dtbo
=> fdt addr ${fdt_addr_r}
=> fdt resize 65536
=> fdt apply ${loadaddr}
=> fdt print /
~~~

Operate on a disposable loaded DTB during exploration. A failed overlay can leave the working blob unusable; reload the base before retrying.

## 5.12 Common DT failures

| Symptom | DT-related causes | Discriminating check |
|---|---|---|
| No early serial | wrong UART base/type/clock, disabled node, wrong stdout path | compare live DT, binding, SoC manual, and known-good UART |
| Kernel sees less/more RAM | incorrect memory node or reserved ranges | U-Boot <code>bdinfo</code>, DT decode, Linux early memory log |
| External abort/hang probing device | incorrect register window, clock/reset/power missing | disable node; inspect provider links |
| No interrupts | wrong interrupt parent/specifier or controller node | early IRQ-domain logs and schema check |
| Root device absent | controller/PHY/regulator/pinmux/DMA/IOMMU error | boot with focused driver debug and compare known-good DT |
| DTB rejected | corruption, no expansion space, invalid header/size | <code>fdt header</code>, <code>fdt_check_header</code>, hash |
| Driver never binds | compatible mismatch or driver not built | inspect <code>compatible</code>, kernel config, modalias |
| Random later corruption | DTB/initramfs/kernel overlap | build physical-memory interval map |

## 5.13 A minimal DT is requirement-driven, not line-count-driven

“Minimal” means every described resource is correct and only required hardware is enabled. It does not mean deleting clock, reset, interrupt, power, or pinctrl providers that consumers still reference.

A disciplined reduction loop:

1. Start from the vendor/upstream DTB that boots the exact board revision.
2. Decompile and preserve the binary hash/source.
3. Define the target: early console only, initramfs shell, storage root, networking, SMP, or performance tests.
4. Trace dependencies for each required device.
5. Disable leaf nodes first; do not delete shared providers blindly.
6. Recompile and run schema/local checks.
7. Diff normalized DTS and DTB properties.
8. Boot with an observation channel.
9. Change one dependency set at a time.

A minimal RV2 tree cannot be authored correctly from the public product name alone. It requires the exact SoC bindings, board schematics/revision, vendor or upstream DTS, actual memory/carve-outs, clock/reset/power/pin topology, interrupt topology, and verified serial/storage nodes. Chapter 6 applies this method without inventing RV2 addresses.

## 5.14 Address translation, interrupts, DMA, and memory consumers

### Nested buses

<code>reg</code> is expressed in the parent bus address space. Each bus’s <code>ranges</code> maps child addresses to its parent; an empty <code>ranges</code> can mean identity mapping under the binding, while absence has different semantics. <code>dma-ranges</code> describes device DMA address translation toward CPU physical memory. PCIe host bridges use binding-defined <code>ranges</code> cells to distinguish I/O, non-prefetchable, and prefetchable memory windows.

### Interrupt topology

RISC-V platforms vary:

- legacy CLINT-like or ACLINT timer/software-interrupt devices;
- PLIC external interrupt controller;
- AIA designs with APLIC and per-hart IMSIC interrupt files;
- CPU interrupt-controller child nodes anchoring local interrupt specifiers.

Do not mix PLIC and AIA nodes or IDs. The interrupt parent, <code>#interrupt-cells</code>, extended interrupt references, privilege context, and hart association come from their bindings. OpenSBI may own some machine-level timer/IPI hardware while Linux consumes SBI services rather than directly programming it.

### DMA and reserved memory

<code>dma-coherent</code> asserts a coherency property; adding it does not make hardware coherent. IOMMU links and stream IDs must match the real fabric. CMA is a Linux contiguous-memory policy often configured through kernel options and/or reserved-memory nodes. Framebuffers, simple-framebuffer handoff, crash logs, secure/firmware memory, remote processors, and DMA pools require explicit ownership and lifetime. A bootloader framebuffer that remains scanned out must be reserved until the kernel display driver safely takes over.

### Aliases and chosen

<code>/aliases</code> provides stable symbolic paths such as <code>serial0</code>; <code>/chosen/stdout-path</code> may use an alias with UART options. Linux <code>console=</code> and <code>earlycon=</code> are separate command-line mechanisms. All three can disagree, explaining why one stage prints and the next does not.

## 5.15 Complete U-Boot FDT editing exercise

Commands are version/config dependent and operate on a disposable RAM copy:

~~~text
# Context: U-Boot shell. Substitute verified safe addresses.
=> load mmc 0:1 ${fdt_addr_r} /boot/base.dtb
=> fdt addr ${fdt_addr_r}
=> fdt header
=> fdt print /
=> fdt get value model_text / model
=> echo ${model_text}

# Move/copy to a separate verified buffer with 64 KiB capacity.
=> fdt move ${fdt_addr_r} ${loadaddr} 10000
=> fdt addr ${loadaddr} 10000
=> fdt resize 10000

=> fdt mknode /chosen handbook-test
=> fdt set /chosen/handbook-test status "okay"
=> fdt print /chosen/handbook-test
=> fdt rm /chosen/handbook-test status
=> fdt rm /chosen/handbook-test

# Overlay address must not overlap the working FDT or other image.
=> load mmc 0:1 ${scriptaddr} /boot/feature.dtbo
=> fdt apply ${scriptaddr}
=> fdt print /
~~~

The numeric size syntax is command-version dependent and U-Boot generally parses unprefixed numbers as hexadecimal. Confirm with <code>help fdt</code>. <code>fdt move</code> destination/capacity, working DTB, and overlay need non-overlapping RAM. Reload the base after an overlay failure.

## 5.16 Four meanings of “U-Boot generates a DTB”

1. **Build-time compilation:** U-Boot’s build invokes dtc on DTS/DTSI to produce its control DTB.
2. **Receive/select:** U-Boot accepts a prior-stage FDT or chooses a board/OS DTB from a FIT or storage.
3. **Copy/modify:** U-Boot expands a working blob and applies generic, architecture, board, command, EFI, and overlay fixups.
4. **Full synthesis by discovery:** only feasible for discoverable portions of some platforms; not a reliable way to reconstruct non-discoverable clocks, resets, pins, power, interrupts, reserved firmware memory, or board wiring.

The fourth is not what normal board support means. A reviewed DTS or firmware-generated platform description remains the source of topology; discovery supplies bounded facts such as RAM size, MAC, serial, PCI enumeration, or selected board revision.

---

# 6. Building a minimal DTB for the RV2 requirement

## 6.1 First define “boots”

A DTB cannot be minimized until the acceptance criterion is explicit:

| Milestone | Required subsystems, in addition to CPU/RAM/firmware |
|---|---|
| Earliest kernel marker | boot CPU, timer path needed by kernel, interrupt controller, UART/earlycon, reserved memory |
| Initramfs shell | above plus a built-in or passed initramfs and console |
| SMP shell | above plus all CPU nodes, CPU interrupt controllers, SBI HSM/secondary-hart topology |
| Root on USB | above plus USB host, PHY, clocks, resets, power/regulator, pinctrl, DMA/IOMMU dependencies |
| HDMI console | display controller, clocks, resets, power domains, PHY/bridge/panel/connector, reserved framebuffer if any |
| Performance lab | stable timer/counter/PMU description, topology/cache correctness, frequency/power policy, minimal noise |

For the current RV2 debugging goal, **UART plus an initramfs shell** is a better first milestone than HDMI. It turns a display-policy transition into a text observation path.

## 6.2 Establish an authoritative baseline

The project’s observed vendor DTB hash is:

~~~text
05eeb6e9...bfa21d617
~~~

The middle was not retained in the available record, so do not treat the abbreviated value as a reproducible hash. Reacquire and store the complete SHA-256 before any modification:

~~~bash
# Context: Linux host operating on a mounted copy of the RV2 boot filesystem.
sha256sum /path/to/boot/dtb
find /path/to/boot/dtb-6.6.63-ky -type f -name '*.dtb' -print0 \
  | sort -z \
  | xargs -0 sha256sum
file /path/to/boot/dtb
~~~

Resolve whether <code>/boot/dtb</code> is a file, link, directory, or loader-selected name. Then determine what U-Boot actually loads by decoding <code>boot.scr</code> and reading the environment:

~~~bash
# Context: Linux host. mkimage is supplied by U-Boot tools.
dumpimage -l /path/to/boot/boot.scr
dumpimage -T script -p 0 -o boot.scr.txt /path/to/boot/boot.scr
sed -n '1,240p' /path/to/boot/boot.cmd
sed -n '1,240p' /path/to/boot/orangepiEnv.txt
~~~

At the U-Boot prompt, inspect without saving:

~~~text
# Context: U-Boot shell; read-only commands.
=> printenv bootcmd bootargs fdtfile fdt_addr_r kernel_addr_r ramdisk_addr_r
=> printenv
=> mmc list
=> part list mmc 0
=> ls mmc 0:1 /boot
=> iminfo ${scriptaddr}
~~~

The device/partition numbers are examples; use the values shown by the target.

## 6.3 Create a normalized source and manifest

~~~bash
# Context: Linux development host.
mkdir -p rv2-dt-work
cp exact-vendor.dtb rv2-dt-work/baseline.dtb
dtc -I dtb -O dts -s -o rv2-dt-work/baseline.sorted.dts rv2-dt-work/baseline.dtb
fdtdump rv2-dt-work/baseline.dtb > rv2-dt-work/baseline.fdtdump.txt
sha256sum rv2-dt-work/* > rv2-dt-work/SHA256SUMS.initial
~~~

Keep a manifest containing:

- board name and physical revision;
- RAM capacity;
- source image and acquisition date;
- kernel build/commit/config hash;
- U-Boot/OpenSBI versions;
- original DTB full hash;
- exact boot command;
- UART wiring/baud;
- every change and result.

## 6.4 Inventory dependencies before deleting

For each required consumer, trace every provider:

~~~mermaid
flowchart TD
    D["Required device"] --> C["clocks"]
    D --> R["resets / power domain"]
    D --> I["interrupt / DMA / IOMMU"]
    D --> P["pinctrl / PHY / regulator"]
~~~

A serial controller may appear simple but still need:

- parent bus address translation;
- input clock and rate;
- reset deassertion;
- pin multiplexing;
- power domain;
- correct <code>reg-shift</code>/<code>reg-io-width</code>;
- status enabled;
- correct driver-compatible string;
- interrupt only after polled early console.

Use binding schemas from the **same kernel tree**. When vendor bindings do not exist upstream, record that schema validation is incomplete; do not “fix” a vendor property by deleting it simply because an upstream schema does not recognize it.

## 6.5 Reduction procedure

Work in stages:

1. **Copy, never edit the only vendor DTB.**
2. Set nonessential leaf devices to <code>status = "disabled"</code>.
3. Keep CPU, memory, interrupt-controller, timer, SBI/firmware, chosen, serial, and all their providers.
4. Compile and compare:

~~~bash
# Context: Linux development host.
dtc -Wno-interrupt_provider -I dts -O dtb \
  -o candidate-01.dtb candidate-01.dts
dtc -I dtb -O dts -s -o candidate-01.roundtrip.dts candidate-01.dtb
diff -u baseline.sorted.dts candidate-01.roundtrip.dts | less
sha256sum candidate-01.dtb
~~~

1. If source and bindings are available, run:

~~~bash
# Context: matching Linux kernel source tree.
make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- \
  dtbs_check W=1 DT_SCHEMA_FILES='path/to/relevant-schema.yaml'
~~~

1. Load the candidate at a new filename; do not overwrite the baseline.
2. At U-Boot, use <code>fdt addr</code>, <code>fdt header</code>, <code>fdt print /chosen</code>, and <code>fdt print /memory…</code>.
3. Boot once, capture the complete UART log, and update the manifest.
4. If it fails, restore the previous candidate and change one dependency group.

## 6.6 RV2 properties that must be learned, not guessed

The following values are intentionally absent from this handbook:

- SoC MMIO base addresses;
- UART instance/base/clock/reset IDs;
- interrupt-controller register ranges and interrupt IDs;
- exact 4 GiB memory map and holes;
- OpenSBI, SPL, framebuffer, secure, PMU, and media carve-outs;
- USB controller/PHY relationships;
- GPIO/pinctrl encoding;
- HDMI/display graph;
- exact boot-ROM offsets within the raw region.

Authoritative acquisition order:

1. DTS/DTSI that produced the known-good vendor DTB.
2. Decompilation of the known-good DTB.
3. SoC binding schemas and vendor kernel drivers.
4. Board schematics and hardware revision documents.
5. U-Boot <code>bdinfo</code>/<code>fdt</code> inspection and complete UART logs.
6. Carefully scoped register/manual checks.

Public product information identifies the board family and Ky X1 class but is not a register-level specification. See the [official Orange Pi RV2 page](https://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/service-and-support/Orange-Pi-RV2.html), [Orange Pi RV2 wiki](https://www.orangepi.org/orangepiwiki/index.php/Orange_Pi_RV2), and [Orange Pi build repository](https://github.com/orangepi-xunlong/orangepi-build/blob/next/README.md).

## 6.7 Three RV2 minimality profiles

### Profile A — earliest UART plus initramfs

| Keep in Linux working DT | Why |
|---|---|
| exact root compatible/model and cell widths | platform/bus interpretation |
| boot hart/all required CPU nodes and CPU interrupt-controller nodes | boot/SMP topology |
| <code>timebase-frequency</code> and correct timer/SBI relationship | scheduling/timekeeping |
| actual memory banks | allocator input |
| all firmware/OpenSBI/secure/framebuffer reservations still live | prevent overwrite |
| interrupt-controller topology needed by kernel | traps/IRQs |
| verified UART plus clock/reset/pinctrl/power providers | early and normal console |
| <code>/chosen</code> stdout, bootargs, initrd bounds | boot parameters |
| cache/topology nodes required by the binding/kernel | coherency/topology correctness |

The initramfs is data, not a device node, but its range must be passed and reserved. Linux can reach a shell without USB/storage nodes if the archive is complete.

### Profile B — initramfs plus actual USB/storage

Keep Profile A plus:

- USB/storage controller;
- every PHY, clock, reset, power domain, regulator, GPIO enable, and pinctrl state;
- IOMMU/DMA relationships and coherent/noncoherent properties;
- hub/role-switch/type-C dependencies if on the physical route;
- root device/partition/filesystem kernel drivers and command line;
- any firmware required by the controller.

Success is not “controller probed”; it is deterministic read/write or mounted-root behavior through the intended physical port with no hidden initramfs copy.

### Profile C — measurement kernel

Keep Profile A and only the hardware required by the experiment:

| Experiment | Additional DT requirements |
|---|---|
| syscall/privilege | stable CPU/timebase; SBI/PMU description if measured; no unrelated devices |
| interrupt latency | exact interrupt controller, timer/source, test device, affinity topology |
| memory footprint | complete RAM/reservations/CMA; stable initramfs; no hidden framebuffer |
| IPC | participating harts, cache/topology, timer/interrupt transport, shared-memory correctness |

Disable background peripherals at the DT and kernel-config levels only after checking that firmware does not depend on their drivers for thermal, voltage, or system stability.

### Linux working DT versus U-Boot control DT

U-Boot may still need storage, USB, video, keyboard, network, regulators, or clocks to load the minimized kernel—even when Linux will not. Do not prune those nodes from the **control DT** merely because they are absent/disabled in the **working DT**. Conversely, a device initialized by U-Boot may continue working temporarily after its Linux node is removed; this masks a missing kernel driver/clock/reset setup and is not a valid minimal profile.

### Profile validation

~~~bash
# Context: matching Linux source/build tree.
make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- dtbs_check W=1
dt-validate -s processed-schema.json candidate.dtb
fdtdump candidate.dtb > candidate.fdtdump.txt
fdtget -t s candidate.dtb / compatible
~~~

Expected evidence:

- Profile A: earliest kernel UART, normal-console takeover, and unique <code>/init</code> marker.
- Profile B: Profile A plus enumeration and verified I/O through the exact USB/storage route.
- Profile C: Profile A plus stable counter/interrupt/topology checks and a documented noise inventory.

Typical failure signals: no byte before earlycon (handoff/entry); entry byte but no earlycon (DT/UART/MMU); scheduler/timer stall; SMP hang; probe deferral loop due to missing provider; root timeout; interrupt storm; silent corruption from reservation/DMA errors.

## 6.8 Small QEMU <code>virt</code> DTS for teaching

This fragment teaches cells, CPU nodes, a local CPU interrupt controller, memory, chosen, and a UART. It is **not claimed to be a complete Linux-bootable QEMU DT**, because a real QEMU virt tree also describes the platform interrupt controller, timer/interrupt relationships, virtio devices, firmware reservations, and other selected machine features. Prefer QEMU’s generated DTB for execution.

~~~dts
/dts-v1/;

/ {
    model = "Teaching subset of QEMU RISC-V virt";
    compatible = "riscv-virtio";
    #address-cells = <2>;
    #size-cells = <2>;

    chosen {
        stdout-path = "/soc/uart@10000000:115200n8";
    };

    memory@80000000 {
        device_type = "memory";
        reg = <0x0 0x80000000 0x0 0x08000000>;
    };

    cpus {
        #address-cells = <1>;
        #size-cells = <0>;
        timebase-frequency = <10000000>;

        cpu@0 {
            device_type = "cpu";
            reg = <0>;
            status = "okay";
            compatible = "riscv";
            riscv,isa = "rv64imafdc";
            mmu-type = "riscv,sv39";

            cpu0_intc: interrupt-controller {
                compatible = "riscv,cpu-intc";
                interrupt-controller;
                #interrupt-cells = <1>;
            };
        };
    };

    soc {
        compatible = "simple-bus";
        #address-cells = <2>;
        #size-cells = <2>;
        ranges;

        uart@10000000 {
            compatible = "ns16550a";
            reg = <0x0 0x10000000 0x0 0x100>;
            clock-frequency = <3686400>;
            current-speed = <115200>;
            reg-shift = <0>;
            reg-io-width = <1>;
        };
    };
};
~~~

Line/property interpretation:

| Fragment | Teaching point | Why it cannot be transplanted to RV2 |
|---|---|---|
| root <code>#address-cells/#size-cells = &lt;2&gt;</code> | child addresses/sizes use two 32-bit cells | verify the vendor root and every nested bus |
| <code>compatible = "riscv-virtio"</code> | selects QEMU machine compatibility | RV2 needs verified board/SoC compatibles |
| <code>memory@80000000</code> | one illustrative 128 MiB RAM bank | RV2 has a 4 GiB physical layout/holes/reservations to extract |
| <code>timebase-frequency</code> | counter scale for this virtual machine | the Ky platform value comes from vendor firmware/DT/source |
| one <code>cpu@0</code> | single-hart teaching topology | RV2 is eight-core; ISA/cache/topology/status must match hardware |
| <code>riscv,cpu-intc</code> | per-hart local interrupt endpoint | a full RV2 interrupt graph must include its real controllers/contexts |
| <code>simple-bus</code> plus empty <code>ranges</code> | identity-translated virtual SoC bus | nested RV2 buses may translate addresses |
| NS16550A at <code>0x10000000</code> | QEMU polled UART model | address/type/clock/width/pins are not RV2 facts |
| omitted PLIC/AIA/timer/virtio/reservations | keeps syntax example short | omissions make it unsuitable as a complete boot DT |

Generate the real machine tree:

~~~bash
# Context: Linux host with QEMU.
qemu-system-riscv64 \
  -machine virt,dumpdtb=qemu-virt.dtb \
  -m 512M -smp 1 -nographic
dtc -I dtb -O dts -s -o qemu-virt.dts qemu-virt.dtb
less qemu-virt.dts
~~~

QEMU’s generated tree varies with QEMU version and command-line devices. Record both.

## 6.9 Programmatic DT fixup pattern

Production U-Boot board code should use libfdt helpers, validate every return, and expand the blob before adding data:

~~~c
// Context: illustrative U-Boot/libfdt board fixup.
int board_fix_fdt(void *fdt, uint64_t ram_base, uint64_t ram_size)
{
    int rc;

    rc = fdt_check_header(fdt);
    if (rc)
        return rc;

    rc = fdt_open_into(fdt, fdt, fdt_totalsize(fdt) + 4096);
    if (rc)
        return rc;

    rc = fdt_fixup_memory_banks(fdt, &ram_base, &ram_size, 1);
    if (rc)
        return rc;

    return fdt_pack(fdt);
}
~~~

The actual U-Boot helper signatures vary by release. This is a pattern, not a drop-in function for 2022.10ky. Fixups should add discovered serial/MAC/RAM values while the stable topology remains in reviewed DTS.

---

# 7. Writing a boot stage for your own kernel

## 7.1 First choose the layer you are replacing

There are three materially different supported projects, plus a much harder early-firmware project:

| Track | Keep | Replace/build | Use when |
|---|---|---|---|
| A. Minimal S-mode loader | Boot ROM, vendor SPL/DRAM init, OpenSBI | U-Boot proper with a tiny kernel launcher | Prove a narrow RISC-V handoff |
| B. Custom kernel under U-Boot | Entire existing firmware/U-Boot chain | custom image header/protocol and U-Boot command/path if needed | Reuse U-Boot drivers/recovery |
| C. OpenSBI jump/payload | Boot ROM/SPL and OpenSBI packaging | package custom kernel/loader as FW_JUMP/FW_PAYLOAD | Avoid a separate U-Boot stage |
| Early-stage replacement | Only Boot ROM | PMIC/clocks/pins/DRAM/media/security plus runtime/launcher | Full SoC enablement with vendor documentation |

For a custom kernel, Track B is often the fastest first proof because U-Boot already loads memory and supplies a shell; Track A narrows the production launcher; Track C can remove another transition. Replacing early firmware is not simply a fourth equivalent packaging choice: it assumes the DRAM/PMIC/ROM obligations that make it a separate porting program.

## 7.2 Define your own kernel ABI

If the successor is not Linux, you own the contract. Write it as a versioned document:

| Field | Example decision |
|---|---|
| Entry address | image header entry, validated inside executable segment |
| Privilege | RISC-V S-mode under SBI |
| Hart policy | hart 0 enters; others stopped using HSM |
| MMU | off, <code>satp=0</code> |
| Registers | <code>a0=hartid</code>, <code>a1=boot_info*</code> |
| Hardware description | DTB pointer in <code>boot_info</code> |
| Memory map | typed ranges with version/length |
| Interrupts | disabled at entry |
| Stack | kernel supplies its own immediately |
| Return | forbidden; treated as fatal |
| Authentication | signed manifest covers header, image, DTB, config |

Use a length/version pair so old loaders can reject incompatible additions:

~~~c
// Context: example custom ABI shared by loader and kernel.
#define BOOT_INFO_MAGIC 0x424f4f54494e464fULL

struct boot_range {
    uint64_t base;
    uint64_t size;
    uint32_t type;
    uint32_t flags;
};

struct boot_info_v1 {
    uint64_t magic;
    uint32_t version;
    uint32_t struct_size;
    uint64_t fdt_pa;
    uint64_t initrd_start;
    uint64_t initrd_end;
    uint64_t cmdline_pa;
    uint32_t range_count;
    uint32_t range_stride;
    uint64_t ranges_pa;
};
~~~

Do not pass C pointers to transient loader memory that the kernel will reclaim. All physical ranges must be reserved until the kernel copies what it needs.

## 7.3 Minimal loader responsibilities

Even a small production-quality launcher needs:

- entry assembly and a known stack;
- CPU/privilege-state normalization;
- observable console or error channel;
- bounded image source and parser;
- overflow-safe length/alignment arithmetic;
- placement and overlap allocator;
- integrity/authenticity policy;
- DTB/boot-info validation and fixups;
- initramfs/module description;
- cache and instruction synchronization;
- secondary-CPU and interrupt quiescence;
- watchdog/fallback policy;
- one-way transfer.

Omitting filesystems is possible: a ROM/SPL can load one signed bundle at fixed offsets. Omitting validation is not acceptable merely because offsets are fixed.

## 7.4 Educational RISC-V launcher

The following skeleton demonstrates Track A. It runs in S-mode as an OpenSBI payload on QEMU <code>virt</code>, receives <code>a0/a1</code>, copies an embedded uncompressed RV64 Linux <code>Image</code> to a 2 MiB-aligned address, clears supervisor interrupt enables, clears <code>satp</code>, executes fences, and jumps with the Linux register contract.

**Status:** source-reviewed teaching code; **not compiled or executed in this report-generation session**. It deliberately uses QEMU virt’s 16550 address and assumes the input kernel contains a built-in initramfs. It is not RV2 code, has no authentication, performs only basic DTB validation, and must not be used as production firmware.

### <code>start.S</code>

~~~asm
    .section .text.init
    .globl _start
_start:
    .option push
    .option norelax
    la gp, __global_pointer$
    .option pop

    la sp, _stack_top
    andi sp, sp, -16

    # OpenSBI payload convention already supplies a0=hart ID, a1=FDT.
    call loader_main

1:
    wfi
    j 1b

    .section .bss.stack, "aw", @nobits
    .align 12
_stack_bottom:
    .space 16384
_stack_top:
~~~

### <code>loader.c</code>

~~~c
#include <stddef.h>
#include <stdint.h>

#define UART_BASE       0x10000000UL
#define UART_THR        0
#define UART_LSR        5
#define UART_LSR_THRE   0x20
#define LINUX_LOAD_PA   0x80400000UL
#define FDT_MAGIC       0xd00dfeedU
#define FDT_MIN_HEADER  40U
#define FDT_MAX_SIZE    (16U * 1024U * 1024U)

extern const unsigned char _binary_Image_start[];
extern const unsigned char _binary_Image_end[];

static inline uint8_t mmio_read8(uintptr_t address)
{
    return *(volatile uint8_t *)address;
}

static inline void mmio_write8(uintptr_t address, uint8_t value)
{
    *(volatile uint8_t *)address = value;
}

static void putc(char c)
{
    while (!(mmio_read8(UART_BASE + UART_LSR) & UART_LSR_THRE))
        ;
    mmio_write8(UART_BASE + UART_THR, (uint8_t)c);
}

static void puts(const char *s)
{
    while (*s)
        putc(*s++);
}

static uint32_t read_be32(const unsigned char *p)
{
    return ((uint32_t)p[0] << 24) |
           ((uint32_t)p[1] << 16) |
           ((uint32_t)p[2] << 8) |
           (uint32_t)p[3];
}

static int valid_fdt(const void *pointer)
{
    const unsigned char *p = pointer;
    uint32_t size;

    if (((uintptr_t)p & 7U) != 0)
        return 0;
    if (read_be32(p) != FDT_MAGIC)
        return 0;
    size = read_be32(p + 4);
    return size >= FDT_MIN_HEADER && size <= FDT_MAX_SIZE;
}

static void copy_bytes(unsigned char *dst,
                       const unsigned char *src,
                       size_t count)
{
    if (dst < src) {
        for (size_t i = 0; i < count; ++i)
            dst[i] = src[i];
    } else if (dst > src) {
        for (size_t i = count; i != 0; --i)
            dst[i - 1] = src[i - 1];
    }
}

static __attribute__((noreturn)) void halt(const char *why)
{
    puts(why);
    for (;;) {
        __asm__ volatile("wfi");
    }
}

typedef void (*linux_entry_t)(unsigned long hartid, const void *fdt);

void loader_main(unsigned long hartid, const void *fdt)
{
    const unsigned char *image_start = _binary_Image_start;
    const unsigned char *image_end = _binary_Image_end;
    size_t image_size = (size_t)(image_end - image_start);
    linux_entry_t linux_entry = (linux_entry_t)LINUX_LOAD_PA;

    puts("\r\nmini-loader: entered from OpenSBI\r\n");

    if (!valid_fdt(fdt))
        halt("mini-loader: invalid FDT\r\n");
    if (image_size == 0 || image_size > 128U * 1024U * 1024U)
        halt("mini-loader: invalid Image size\r\n");

    copy_bytes((unsigned char *)LINUX_LOAD_PA, image_start, image_size);
    puts("mini-loader: Image copied; transferring\r\n");

    __asm__ volatile(
        "csrw sie, zero\n"
        "csrci sstatus, 2\n"
        "csrw satp, zero\n"
        "sfence.vma\n"
        "fence rw, rw\n"
        "fence.i\n"
        ::: "memory");

    linux_entry(hartid, fdt);
    halt("mini-loader: Linux returned\r\n");
}
~~~

### <code>linker.ld</code>

~~~ld
OUTPUT_ARCH(riscv)
ENTRY(_start)

SECTIONS
{
    . = 0x84000000;

    .text : ALIGN(16) {
        KEEP(*(.text.init))
        *(.text .text.*)
    }

    .rodata : ALIGN(16) {
        *(.rodata .rodata.*)
    }

    .data : ALIGN(16) {
        *(.data .data.*)
        PROVIDE(__global_pointer$ = . + 0x800);
        *(.sdata .sdata.*)
    }

    .bss (NOLOAD) : ALIGN(16) {
        *(.bss .bss.*)
        *(.sbss .sbss.*)
        *(COMMON)
        *(.bss.stack)
    }

    .kernel : ALIGN(4096) {
        _embedded_kernel_begin = .;
        *(.data.kernel)
        _embedded_kernel_end = .;
    }

    /DISCARD/ : {
        *(.comment)
        *(.eh_frame)
        *(.note*)
    }
}
~~~

### <code>Makefile</code>

~~~make
CROSS ?= riscv64-linux-gnu-
CC := $(CROSS)gcc
OBJCOPY := $(CROSS)objcopy

CFLAGS := -march=rv64imac_zicsr_zifencei -mabi=lp64 \
          -mcmodel=medany -msmall-data-limit=0 \
          -ffreestanding -fno-builtin -fno-stack-protector \
          -fno-pic -fno-pie -Wall -Wextra -Werror -O2
LDFLAGS := -nostdlib -nostartfiles -static -no-pie

all: loader.bin

start.o: start.S
	$(CC) $(CFLAGS) -c -o $@ $<

loader.o: loader.c
	$(CC) $(CFLAGS) -c -o $@ $<

kernel.o: Image
	$(OBJCOPY) -I binary -O elf64-littleriscv -B riscv \
	  --rename-section .data=.data.kernel,alloc,load,readonly,data,contents \
	  $< $@

loader.elf: start.o loader.o kernel.o linker.ld
	$(CC) $(LDFLAGS) -Wl,-T,linker.ld -Wl,-Map,loader.map \
	  -o $@ start.o loader.o kernel.o

loader.bin: loader.elf
	$(OBJCOPY) -O binary $< $@

clean:
	rm -f start.o loader.o kernel.o loader.elf loader.bin loader.map
~~~

The use of <code>rm</code> is limited to explicitly named build outputs in the current project directory.

### Build and run

Prepare an RV64 Linux <code>Image</code> with a built-in initramfs and a console appropriate for QEMU virt. Then:

~~~bash
# Context: Linux development host in the mini-loader directory.
make CROSS=riscv64-linux-gnu-

# Context: OpenSBI source tree. The 0x04000000 payload offset places
# the loader at 0x84000000 when firmware starts at 0x80000000.
make PLATFORM=generic \
  FW_PAYLOAD_PATH=/absolute/path/to/loader.bin \
  FW_PAYLOAD_OFFSET=0x04000000

# Context: Linux host. Path depends on OpenSBI build directory.
qemu-system-riscv64 \
  -machine virt \
  -m 512M -smp 1 -nographic \
  -bios build/platform/generic/firmware/fw_payload.elf
~~~

Before trusting the result:

~~~bash
# Context: Linux development host.
riscv64-linux-gnu-readelf -h -l loader.elf
riscv64-linux-gnu-nm -n loader.elf | less
grep -E '(_start|_stack_top|_binary_Image_(start|end))' loader.map
~~~

Potential adjustments after actual compilation:

- toolchain architecture string versus installed multilib;
- linker input section flags generated by the selected <code>objcopy</code>;
- OpenSBI payload address/size overlap;
- Linux console and built-in initramfs configuration;
- QEMU/OpenSBI version-specific ISA choices.

## 7.5 Extending the loader safely

Add features in this order:

1. machine-readable progress/error codes;
2. a physical interval allocator and overlap checker;
3. a bounded image header with load/entry/length/hash;
4. SHA-256 integrity;
5. signature verification anchored in immutable/protected keys;
6. base DTB copy plus <code>/chosen</code> and reserved-memory fixups;
7. separate initramfs placement;
8. storage driver and redundant manifest;
9. rollback-protected A/B policy;
10. recovery transport and field-update protocol.

Do not start with a filesystem, shell, network stack, or dynamic allocation. Each increases parser and state-machine risk before the core contract is proven.

## 7.6 DTB handling in a small loader

At minimum:

1. Check 8-byte alignment where the predecessor protocol requires it.
2. Validate magic, version compatibility, total size, block offsets, and integer overflow.
3. Copy to a buffer with deliberate expansion space.
4. Add/update <code>/chosen/bootargs</code>.
5. Add <code>linux,initrd-start</code> and <code>linux,initrd-end</code> using correct root cell widths.
6. Add every loader/firmware persistent region to the reservation map or <code>/reserved-memory</code>.
7. Pack only after all edits.
8. Reserve the final DTB interval.

Use libfdt rather than hand-editing tokens in production. A tiny read-only parser can validate or query a fixed property set, but a correct writer is a larger project.

## 7.7 What knowledge is required

| Area | Required depth |
|---|---|
| ISA and privileged architecture | reset/entry mode, CSRs/system registers, exceptions, fences, MMU, cache model |
| ABI/toolchain | calling convention, ELF, relocations, linker scripts, freestanding C, assembly startup |
| SoC | memory map, clocks, resets, pins, DRAM, interrupt controller, timer, boot ROM |
| Firmware interface | SBI, PSCI/SMCCC, UEFI, OPAL, or platform runtime |
| Kernel boot protocol | exact register/structure, alignment, CPU, MMU, interrupt, DT/ACPI rules |
| Devicetree | DTS, bindings, cells, phandles, overlays, libfdt, schema validation |
| Storage/image formats | block geometry, GPT, NAND constraints, FIT/ELF/PE/Android formats |
| Security | roots of trust, signatures, rollback, key rotation/revocation, debug lockdown, fault handling |
| Reliability | watchdogs, power loss, redundancy, atomic update state, boot success criteria |
| Debug | UART/JTAG, logic analysis, QEMU, objdump/readelf, trace, fault injection |
| Performance | stable clocks/timers, PMU, topology, firmware overhead, experiment controls |

## 7.8 Recommended source split

The compact example consolidated UART and final handoff in C to keep it readable. A maintainable loader should separate platform-dependent and architecture-dependent code.

### <code>uart.h</code> and <code>uart.c</code>

~~~c
// uart.h
#pragma once
void uart_init(void);
void uart_putc(char c);
void uart_puts(const char *s);
~~~

~~~c
// uart.c — QEMU virt teaching implementation only.
#include <stdint.h>
#include "uart.h"

#define UART_BASE 0x10000000UL

void uart_init(void)
{
    // QEMU/OpenSBI leaves this emulated UART usable for the teaching path.
    // Real hardware must program verified clock, reset, pinmux, format, divisor.
}

void uart_putc(char c)
{
    volatile uint8_t *uart = (volatile uint8_t *)UART_BASE;
    while (!(uart[5] & 0x20))
        ;
    uart[0] = (uint8_t)c;
}

void uart_puts(const char *s)
{
    while (*s)
        uart_putc(*s++);
}
~~~

### <code>handoff.S</code>

~~~asm
    .section .text
    .globl handoff_linux
    .type handoff_linux, @function

    # C ABI:
    #   a0 = boot hart ID
    #   a1 = working FDT physical address
    #   a2 = Linux physical entry address
handoff_linux:
    csrw sie, zero
    csrci sstatus, 2
    csrw satp, zero
    sfence.vma
    fence rw, rw
    fence.i
    jr a2

    .size handoff_linux, .-handoff_linux
~~~

The platform layer must quiesce DMA, handle secondary harts, and ensure firmware/PMP reservations before this function. The assembly deliberately preserves <code>a0/a1</code>.

### libfdt validation/query path

~~~c
// Context: loader variant linked with a pinned libfdt.
#include <libfdt.h>

int inspect_fdt(const void *fdt)
{
    int length;
    const char *model;
    const char *compatible;

    if (fdt_check_header(fdt) != 0)
        return -1;
    if (fdt_totalsize(fdt) < 40 || fdt_totalsize(fdt) > 16 * 1024 * 1024)
        return -2;

    model = fdt_getprop(fdt, 0, "model", &length);
    if (model && length > 0)
        uart_puts(model);

    compatible = fdt_getprop(fdt, 0, "compatible", &length);
    if (!compatible || length <= 0)
        return -3;

    // A compatible property is a NUL-separated string list; iterate within length.
    return 0;
}
~~~

Pin libfdt source, compile it freestanding, and include its objects/header in the Makefile. Do not call <code>strlen</code> beyond the returned property length. For mutation, allocate a known-capacity copy and use <code>fdt_open_into()</code>.

Track B can expose a U-Boot command that validates a custom signed header, constructs <code>boot_info_v1</code>, runs the platform cleanup path, and calls a small architecture handoff. Prefer an established contract—native Linux, EFI, Multiboot where supported, or SBI conventions—when it meets the custom kernel’s needs; every private ABI becomes a compatibility and testing obligation.

## 7.9 Progressive milestones

| Milestone | Pass signal | Typical failure | What it proves | What it does not prove |
|---|---|---|---|---|
| 1. print in QEMU M/S-mode | unique UART marker | no output/trap | entry/link/console for one environment | OpenSBI handoff or kernel |
| 2. receive OpenSBI | marker plus valid hart/FDT values | invalid mode/registers | FW payload/jump boundary | DT semantics |
| 3. validate/print FDT model | bounded libfdt parse | bad magic/offset/property | readable structurally valid input | correct devices |
| 4. locate/copy kernel | size/hash and destination marker | overflow/overlap/hash fail | loader placement path | executable entry |
| 5. jump with documented ABI | breakpoint at entry | immediate trap/no hit | branch and minimal CPU state | kernel initialization |
| 6. kernel first marker | kernel-specific byte | silence after hit | custom entry/startup code | scheduler/user space |
| 7. add initramfs/storage | <code>/init</code> or verified reads | archive/root timeout | selected data path | full board support |
| 8. verified board drivers | UART/storage on exact hardware | probe/training/IRQ errors | those individually verified subsystems | production recovery/security |
| 9. RV2 safe boot | repeatable UART chain and rollback | reset/hang/corruption | target integration for frozen tuple | other board revisions/updates |

At each milestone preserve the last working binary and require one negative test. QEMU validates the generic contract and loader logic only; RV2 requires verified addresses, UART, memory map, cache/coherency behavior, firmware interface, hart policy, media packaging, and recovery.

---

# 8. Building, configuring, and porting U-Boot

## 8.1 Build an existing upstream board

~~~bash
# Context: Linux development host in a clean U-Boot source tree.
make O=out ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- \
  qemu-riscv64_smode_defconfig
make O=out ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- -j"$(nproc)"

# Inspect generated configuration and artifacts.
grep -E 'CONFIG_(SPL|OF_CONTROL|RISCV|BOOTSTD|CMD_BOOTI|FIT)' out/.config
find out -maxdepth 2 -type f \
  \( -name 'u-boot*' -o -name '*.dtb' -o -name '*.itb' -o -name 'spl' \) \
  -print
~~~

Use the board documentation for the exact flashable artifact. <code>u-boot</code>, <code>u-boot.bin</code>, <code>u-boot.itb</code>, <code>u-boot-with-spl.bin</code>, and binman images are not interchangeable.

## 8.2 Typical files in a new board port

| Artifact | Purpose |
|---|---|
| <code>configs/&lt;board&gt;_defconfig</code> | Base Kconfig selection |
| <code>arch/&lt;arch&gt;/dts/&lt;board&gt;.dts</code> | U-Boot control hardware tree |
| <code>&lt;board&gt;-u-boot.dtsi</code> | U-Boot/xPL-specific tweaks |
| <code>board/&lt;vendor&gt;/&lt;board&gt;/</code> | Board hooks, detection, late initialization |
| <code>include/configs/&lt;board&gt;.h</code> | Legacy constants still needed by the platform |
| <code>board/…/&lt;board&gt;.env</code> | Text default environment |
| Kconfig/Makefile entries | Build selection |
| binman/ITS description | Packaged boot image layout |
| board documentation | Build, media offsets, UART, boot, recovery |

Modern ports should prefer Kconfig, DT, driver model, standard boot, and binman over new board-specific macros and monolithic C.

## 8.3 Porting sequence

1. **Document Boot ROM:** image header, media offsets, SRAM, load limits, boot straps, signature expectations.
2. **Choose stage boundary:** use an existing SPL/TF-A/OpenSBI if possible.
3. **Prove UART in the earliest mutable stage.**
4. **Prove clocks and timer** without destabilizing the ROM’s safe state.
5. **Bring up DRAM** with bounds and memory tests that avoid firmware.
6. **Read boot media** using the smallest driver path.
7. **Load and transfer to U-Boot proper.**
8. **Enable driver model and correct control DT.**
9. **Add block/filesystem/network boot paths.**
10. **Implement native Linux/EFI boot.**
11. **Add verified boot, rollback, boot counting, recovery, and manufacturing hooks.**
12. **Upstream bindings/drivers/board support where possible.**

Each phase needs one success marker and one reproducible failure marker.

## 8.4 Size budgeting for SPL

SPL size pressure comes from:

- generic driver-model overhead;
- DTB size;
- filesystem/image parsers;
- cryptography;
- debug strings and logging;
- malloc;
- multiple boot-device drivers.

Manage it deliberately:

- use <code>CONFIG_SPL_*</code> options rather than assuming proper-stage options propagate;
- include only the first boot media and console initially;
- use FIT simple load or raw offsets when ROM/SRAM budgets require it;
- inspect the map and <code>size</code> after every feature;
- keep recovery in a larger later stage when the threat/reliability model allows;
- do not remove validation just to make a size limit without revisiting the architecture.

## 8.5 RV2 vendor build anchors

The public meta-riscv recipe corroborates these integration points:

- U-Boot branch <code>v2022.10-ky</code>;
- source commit <code>89bff4a7e4cadfb5f130edb1ec44c39bff20a427</code>;
- board DTB <code>arch/riscv/dts/x1_orangepi-rv2.dtb</code>;
- OpenSBI <code>fw_dynamic.bin</code>;
- <code>uboot-opensbi.its</code>;
- output <code>u-boot-opensbi.itb</code>.

See the [meta-riscv Orange Pi recipe](https://github.com/riscv/meta-riscv/blob/master/recipes-bsp/u-boot/u-boot-orangepi.bb) and [Orange Pi U-Boot repository](https://github.com/orangepi-xunlong/u-boot-orangepi).

Project records further identify:

| File | Review question |
|---|---|
| <code>configs/x1_defconfig</code> | Which SPL, MMC/USB, FIT, booti, DT, serial, and environment options are enabled? |
| <code>board/ky/x1/x1.env</code> | What exact kernel/DTB/initramfs names, sizes, and addresses are used? |
| <code>include/configs/x1.h</code> | Which legacy addresses/media offsets remain? |
| <code>uboot-opensbi.its</code> | Which load/entry addresses, hashes, configurations, DTBs, and OpenSBI/U-Boot payload order exist? |

Reproduce the vendor build in a container or pinned build environment before changing it. Archive:

- compiler version and target tuple;
- complete <code>.config</code>;
- source commits and dirty state;
- OpenSBI binary hash;
- DTB hash;
- ITS and generated ITB listing;
- final binary hashes and sizes.

## 8.6 FIT inspection

~~~bash
# Context: Linux development host with U-Boot tools.
dumpimage -l u-boot-opensbi.itb
fdtdump u-boot-opensbi.itb | less
sha256sum u-boot-opensbi.itb fw_dynamic.bin u-boot.bin board.dtb
~~~

A FIT is itself Devicetree-formatted, but its nodes describe images/configurations rather than board hardware. Do not confuse a FIT’s embedded firmware FDT image with the FIT container tree.

## 8.7 Port review checklist

- [ ] Reset and every stage entry address are sourced, not guessed.
- [ ] SRAM/DRAM interval map includes stacks, BSS, malloc, DT, firmware, and image buffers.
- [ ] SPL map fits ROM/SRAM constraints with margin.
- [ ] DRAM training failure is observable and recoverable.
- [ ] Every device in DT has a compiled driver and valid dependencies.
- [ ] Control DT and OS DT ownership are explicit.
- [ ] Boot media offsets cannot collide with GPT/filesystems/environment.
- [ ] Image lengths and load addresses are authenticated or bounded.
- [ ] Native Linux entry satisfies architecture rules.
- [ ] Update and power-failure state machines are tested.
- [ ] A UART/recovery path survives a broken OS image.
- [ ] Production debug and shell policy matches the threat model.

## 8.8 Reproducible configuration workflow

~~~bash
# Context: Linux host, pinned U-Boot checkout.
git rev-parse HEAD
${CROSS_COMPILE}gcc --version
make O=out ARCH=riscv CROSS_COMPILE="${CROSS_COMPILE}" x1_defconfig
cp out/.config config.x1.expanded

# Make deliberate changes with menuconfig or scripts/config, then:
make O=out ARCH=riscv CROSS_COMPILE="${CROSS_COMPILE}" savedefconfig
diff -u configs/x1_defconfig out/defconfig

make O=out ARCH=riscv CROSS_COMPILE="${CROSS_COMPILE}" \
  V=1 -j"$(nproc)" 2>&1 | tee build.log
sha256sum out/u-boot out/u-boot.bin out/u-boot.dtb out/u-boot.itb \
  2>/dev/null | tee SHA256SUMS
~~~

<code>CROSS_COMPILE</code> must be set to a reviewed toolchain prefix; the braces show an existing environment variable rather than a literal command. Configuration fragments can be merged with the tree’s supported Kconfig tooling, but always run olddefconfig/savedefconfig and review the expanded diff. A tiny defconfig diff can select many transitive symbols.

Test progression:

1. <code>make sandbox_defconfig</code> plus applicable unit/Python tests.
2. RISC-V QEMU target boot to prompt and standard-boot tests.
3. FIT/parser negative tests.
4. Vendor-emulated tests only for hardware QEMU actually models.
5. Disposable hardware medium with recovery and UART.

## 8.9 Debug UART and upstream-quality porting

U-Boot **debug UART** is a deliberately small pre-driver-model output path usable before normal serial probing. It needs compile-time verified base/clock/shift/width and can conflict with clocks/pins changed later. A driver-model serial console is the normal device-tree-bound runtime path. Seeing one does not prove the other.

An upstream-quality port:

- adds or reuses binding schemas before/with new DT nodes;
- puts reusable clock/reset/pinctrl/timer/storage logic in drivers/SoC code, not board hacks;
- minimizes pre-relocation requirements;
- documents ROM media offsets, artifact names, UART, build, boot, and recovery;
- passes coding style, build matrix, DT schema, sandbox/unit tests, and maintainers’ review;
- avoids carrying Linux DT divergence without documented necessity;
- separates product boot policy from reusable hardware enablement;
- identifies maintainers and a path for security updates.

---

# 9. Orange Pi RV2 revision-3 case study

## 9.1 Known target facts

**Project observations/current context:**

- Orange Pi RV2; the current target board is 4 GiB and uses an eight-core RV64 Ky X1-class SoC.
- Vendor Linux branch is 6.6.63-ky.
- Boot artifacts include <code>Image</code>, <code>dtb</code>, <code>dtb-6.6.63-ky/</code>, <code>uInitrd-6.6.63-ky</code>, <code>initrd.img-6.6.63-ky</code>, <code>boot.cmd</code>, <code>boot.scr</code>, and <code>orangepiEnv.txt</code>.
- Vendor firmware banners/anchors include <code>U-Boot SPL 2022.10ky</code>, OpenSBI <code>fw_dynamic</code>, <code>U-Boot 2022.10ky</code>, and <code>u-boot.itb</code>/<code>u-boot-opensbi.itb</code> integration.
- The initramfs is intended to contain statically linked BusyBox 1.38.0 with <code>/init</code>.
- Recorded full values are available for only some hashes in abbreviated project context:
  - Linux Image: <code>4f145f27…a8b3239</code>
  - initramfs: <code>541234bd…2f5ad7f66f2cf1e</code>
  - vendor DTB: <code>05eeb6e9…bfa21d617</code>
- Kernel configuration observations include <code>CONFIG_SERIAL_EARLYCON=y</code>, <code># CONFIG_PSTORE is not set</code>, and <code># CONFIG_WATCHDOG is not set</code>.

Abbreviated hashes are identifiers for discussion, not verification material. Reacquire the complete values.

## 9.2 Evidence-state table

| Claim/state | Evidence category | Boundary |
|---|---|---|
| Corrected filesystem begins at LBA 61,440/30 MiB | **Confirmed on hardware/project artifact** | partition geometry |
| A 1 MiB start overwrote required early bytes and reset-looped | **Confirmed on hardware** | symptom correlation; exact destroyed subcomponent not isolated |
| Restoring the 30 MiB raw region removes that reset loop | **Confirmed on hardware** | proves more early boot, not kernel entry |
| Bootloader-visible HDMI then black | **Confirmed on hardware** | no precise stage after display transition |
| Target is current 4 GiB RV2 | **Confirmed project context** | do not merge public 2/8 GiB logs |
| U-Boot vendor branch/commit and OpenSBI FIT composition | **Expected from public source/recipe** | rebuild/target binary identity still needs full hashes |
| RISC-V Linux receives <code>a0/a1</code> with <code>satp=0</code> | **Expected from specification** | target U-Boot implementation must be traced |
| QEMU virt reached OpenSBI/U-Boot in recorded runs | **Confirmed only in QEMU** | runs with OpenSBI 1.7/U-Boot 2025.10 and a separate 1.8 run must remain separate |
| QEMU proves RV2 DT/USB/HDMI/PMU/firmware layout | **False/unsupported inference** | QEMU proves generic RV64 contracts only |
| Kernel entry, earlycon, or <code>/init</code> on revision 3 | **Unknown** | no UART/beacon evidence retained |
| Black screen is a DRM/KMS transition | **Inferred hypothesis** | simultaneous UART would discriminate |
| pstore/ramoops and extra revision-4 logging | **Prepared but not executed/evidenced** | do not report as a result or silently resume |

## 9.3 The corrected storage geometry

~~~mermaid
flowchart LR
    A["LBA 0"] --> B["Raw vendor boot region"]
    B --> C["LBA 61440 = 30 MiB"]
    C --> D["Filesystem partition and /boot files"]
~~~

At 512 bytes/sector:

\[  
61{,}440 \times 512 = 31{,}457{,}280\ \text{bytes} = 30\ \text{MiB}  
\]

The earlier 1 MiB filesystem start overlapped raw firmware. The reset loop was therefore consistent with corrupted early boot material. Restoring the first 30 MiB removed that symptom. The raw region must remain byte-identical in revision-3 experiments unless an explicitly reviewed firmware test is the goal.

Capture the layout:

~~~bash
# Context: Linux host; read-only inspection of a correctly identified device.
sudo sfdisk --dump /dev/sdX
sudo fdisk -l /dev/sdX
sudo dd if=/dev/sdX of=rv2-r3-first-30MiB.bin \
  bs=1M count=30 iflag=fullblock status=progress
sha256sum rv2-r3-first-30MiB.bin
~~~

## 9.4 What public evidence supports

The meta-riscv build recipe independently supports the vendor U-Boot branch/commit and OpenSBI/FIT integration. A public boot log from another RV2 unit shows SPL FIT verification of OpenSBI, U-Boot, and FDT and a 115200 serial console, but that log concerns another physical unit/RAM variant and is **external corroboration**, not evidence for the current 4 GiB board. See [public RV2 boot log](https://dmesgd.nycbug.org/dmesgd?do=view&id=8920).

The exact vendor Linux commit in the project record was not independently resolved to a public first-party repository during this report. Treat <code>ae9e974d…</code> as a **project-record pin** until the repository URL and object are captured. A secondary public article corroborates the value but is not the authority.

## 9.5 Current failure boundary

Observed:

1. The restored raw region avoids the reset loop.
2. Bootloader-visible HDMI output appears.
3. The display becomes black.

Unknown:

- whether <code>boot.scr</code> loads all intended files;
- whether the selected DTB matches this board/revision/RAM;
- whether the initramfs is passed in the format expected by <code>booti</code>;
- whether U-Boot prints or executes “Starting kernel”;
- whether Linux executes an instruction;
- whether early console is pointed at the correct UART;
- whether <code>/init</code> is executable for RV64 and linked as intended;
- whether DRM/KMS merely changes the display mode.

An earlier manual <code>booti</code> attempt showed two concrete problems at different moments: an unsuitable ramdisk format/argument and a missing FDT. Those failures validate command construction, not the state of the final revision-3 scripted boot.

HDMI continuity is particularly weak evidence. U-Boot may render through its video driver/framebuffer, then stop touching it. Linux may initially preserve a simple-framebuffer, change <code>stdout-path</code>/console, blank the display, reclaim an unreserved framebuffer, or hand off from simplefb to the native DRM/KMS driver and change clocks/mode. A black screen can therefore coincide with successful continued boot. Conversely, a bad DT display reservation can corrupt memory. Simultaneous UART plus the live working DT’s <code>/chosen</code>, framebuffer, reserved-memory, and display graph are the discriminators.

## 9.6 Hypothesis matrix

| Hypothesis | Fits black screen? | Confirm | Falsify |
|---|---:|---|---|
| Script stops before <code>booti</code> | Yes | U-Boot UART/script trace ends before command | trace shows final command and late handoff |
| Wrong load address/overlap | Yes | interval map or memory hash changes unexpectedly | verified non-overlap and stable hashes |
| Raw initramfs size/format wrong | Yes | <code>booti</code> diagnostic; dumpimage/file/cpio tests | correct raw address:size or supported image path |
| Missing/invalid DTB | Yes | <code>fdt header</code> fails or kernel rejects blob | header/schema/basic properties valid and kernel marker appears |
| Wrong kernel/DTB pairing | Yes | compatible/driver/config mismatch | known-good matched vendor tuple |
| Kernel never enters | Yes | no earliest kernel marker but U-Boot pre-jump marker exists | kernel entry marker observed |
| Earlycon points at wrong UART | Yes | correct entry beacon elsewhere; DT mismatch | verified UART base/type/clock and early log |
| Kernel reaches <code>/init</code> but init fails | Yes | kernel log shows exec error/panic | marker from static <code>/init</code> |
| DRM/KMS turns screen black | Yes | UART boot continues after display loss | UART also stops before display driver |
| Watchdog resets/hangs system | Possible | reset reason/timing/repeatability | watchdog disabled or serviced and no reset evidence |

## 9.7 Address-layout worksheet

Fill only from target evidence:

| Object | Start | Loaded size | Reserved/decompressed size | End-exclusive | Alignment | Evidence |
|---|---:|---:|---:|---:|---:|---|
| DRAM usable bank(s) | | | | | | <code>bdinfo</code>, DT, source |
| OpenSBI/firmware | | | | | | OpenSBI log/DT reservation |
| relocated U-Boot | | | | | | <code>bdinfo</code>/map |
| U-Boot stack | | | | | | map/source |
| U-Boot malloc arena | | | | | | config/<code>bdinfo</code> |
| control/working FDT | | file totalsize | buffer plus expansion | | 8 B minimum | <code>fdt header</code> |
| kernel source | | file size | if compressed: source span | | format | load result |
| kernel destination | | image size | worst-case decompressed span | | 2 MiB RV64 entry | Image header/source |
| initramfs | | exact returned size | exact size/relocation | | | copied <code>filesize</code> |
| video/simple framebuffer | | | live scanout reservation | | | DT/U-Boot video |
| persistent breadcrumbs | | | | | | DT/source |
| other reserved memory | | | | | | <code>/reserved-memory</code> |

For every pair, prove:

\[  
[start_1,end_1) \cap [start_2,end_2) = \varnothing  
\]

Also prove each end calculation does not wrap, every range lies inside accessible RAM, the kernel entry lies in its destination, and the DTB capacity—not merely totalsize—fits runtime edits.

## 9.8 Next five RV2 actions

1. **Acquire UART safely.** The expected framing is 115200 8N1, and public material identifies a three-pin debug interface, but verify the exact revision’s pinout and voltage before wiring. Capture from power-on through failure with timestamps.
2. **Freeze the revision-3 tuple.** Hash the full device image, first 30 MiB, GPT, <code>Image</code>, every candidate DTB, both initramfs forms, <code>boot.cmd</code>, <code>boot.scr</code>, environment file, and kernel config.
3. **Decode the actual boot policy.** Extract <code>boot.scr</code>; record every load command, returned byte count, copied <code>filesize</code>, address, <code>bootargs</code>, DTB selection, and final <code>booti</code> arguments.
4. **Build a physical interval map at U-Boot.** Record <code>bdinfo</code>, <code>printenv</code> addresses, loaded sizes, FDT totalsize, U-Boot relocation, and firmware/reserved ranges. Reject every overlap before boot.
5. **Insert staged, nonpersistent markers.** U-Boot before/after each load and immediately before <code>booti</code>; then the earliest safe kernel marker or verified earlycon; then <code>/init</code>. Change only one layer per run.

Do not overwrite the original raw boot region, saved environment, partition geometry, or baseline files while establishing the observation channel.

## 9.9 A revision-3 U-Boot evidence script

Use interactively first; do not save it to the persistent environment until proven:

~~~text
# Context: U-Boot shell. Substitute actual device, partition, paths, and
# addresses from the vendor environment; this is a diagnostic pattern.
=> echo R3:A:begin
=> bdinfo
=> printenv kernel_addr_r ramdisk_addr_r fdt_addr_r bootargs

=> load mmc 0:1 ${kernel_addr_r} /boot/Image
=> setenv r3_kernel_size ${filesize}
=> echo R3:B:kernel ${kernel_addr_r} ${r3_kernel_size}

=> load mmc 0:1 ${ramdisk_addr_r} /boot/initrd.img-6.6.63-ky
=> setenv r3_initrd_size ${filesize}
=> echo R3:C:initrd ${ramdisk_addr_r} ${r3_initrd_size}

=> load mmc 0:1 ${fdt_addr_r} /boot/${fdtfile}
=> setenv r3_fdt_size ${filesize}
=> echo R3:D:fdt ${fdt_addr_r} ${r3_fdt_size}
=> fdt addr ${fdt_addr_r}
=> fdt header
=> fdt print /chosen
=> fdt print /memory

=> echo R3:E:handoff
=> booti ${kernel_addr_r} ${ramdisk_addr_r}:${r3_initrd_size} ${fdt_addr_r}
~~~

The exact memory node may be named <code>/memory@…</code>, so use <code>fdt print /</code> to find it. Whether <code>initrd.img</code> or <code>uInitrd</code> belongs with <code>booti</code> depends on the vendor script and enabled U-Boot path; inspect with <code>file</code>, <code>dumpimage -l</code>, and the shipping command rather than naming convention.

## 9.10 Validate the initramfs independently

~~~bash
# Context: Linux development host.
file initrd.img-6.6.63-ky uInitrd-6.6.63-ky
dumpimage -l uInitrd-6.6.63-ky
gzip -dc initrd.img-6.6.63-ky | cpio -it | sed -n '1,120p'

# Extract into a fresh disposable directory.
workdir="$(mktemp -d)"
( cd "$workdir" && gzip -dc /absolute/path/initrd.img-6.6.63-ky | cpio -idmu )
file "$workdir/init" "$workdir/bin/busybox"
readelf -h -l "$workdir/bin/busybox"
ls -l "$workdir/init"
~~~

Check that:

- <code>/init</code> exists and has execute bits;
- its interpreter exists if it is a script;
- BusyBox is RV64 and truly static if the initramfs lacks a dynamic loader;
- <code>/dev</code>, <code>/proc</code>, and <code>/sys</code> setup is appropriate;
- <code>/init</code> emits an unmistakable marker;
- failure ends in a visible shell or timed reboot with a reason.

---

# 10. Debugging boot chains

## 10.1 Debug by boundary, not by symptom

“Black screen” and “hang” are observations, not locations. Build a ladder:

~~~mermaid
flowchart TD
    A["Reset"] --> B["SPL marker"]
    B --> C["OpenSBI marker"]
    C --> D["U-Boot prompt"]
    D --> E["Pre-jump marker"]
    E --> F["Kernel entry marker"]
    F --> G["Early console"]
    G --> H["/init marker"]
    H --> I["PID 1 / workload"]
~~~

For every experiment record:

- last marker observed;
- first marker expected but missing;
- exact changed artifact;
- unchanged artifact hashes;
- power/reset behavior;
- full timestamped log;
- result and next discriminating experiment.

## 10.2 Observation channels

| Channel | Earliest possible stage | Strength | Limitation |
|---|---|---|---|
| UART | ROM/SPL if configured | Can span the entire chain | pinout/voltage/clock must be correct |
| JTAG | reset/any stage | register, memory, PC, breakpoints | availability, secure debug locks |
| GPIO pulse | earliest mutable code | timing-visible on logic analyzer | requires known-safe GPIO/pinmux |
| Persistent SRAM/DRAM breadcrumb | early stage | survives console failure; sometimes warm reset | region must be preserved and excluded |
| pstore/ramoops | later kernel | captures panic/log across reboot | must be configured and memory reserved |
| Network beacon | later bootloader/kernel | remote automation | depends on much hardware |
| Display | bootloader/kernel | convenient | mode switch can destroy continuity |
| Reset-reason registers | after reboot | distinguishes watchdog/power/software reset | register semantics are SoC-specific |

Hardware power/activity LEDs are not software progress beacons unless code deliberately drives them and the electrical function is verified.

## 10.3 UART acquisition discipline

Before connecting:

- verify logic voltage; do not connect a 5 V UART to a lower-voltage SoC header;
- connect ground and adapter RX to board TX first;
- usually do not connect adapter VCC;
- verify header pin order for the exact board revision;
- start with expected 115200 8N1, no flow control, then test alternatives only with evidence;
- capture raw bytes from before power-on;
- retain adapter model and terminal settings in the manifest.

Example host capture:

~~~bash
# Context: Linux host; device name is an example.
picocom --baud 115200 --databits 8 --parity n --stopbits 1 \
  --flow n --logfile rv2-r3-uart.log /dev/ttyUSB0
~~~

## 10.4 U-Boot pre-handoff checks

~~~text
# Context: U-Boot shell; read-only unless a load command reads into RAM.
=> version
=> bdinfo
=> printenv
=> part list mmc 0
=> ls mmc 0:1 /boot
=> fdt addr ${fdt_addr_r}
=> fdt header
=> fdt print /chosen
=> crc32 ${kernel_addr_r} ${r3_kernel_size}
=> crc32 ${ramdisk_addr_r} ${r3_initrd_size}
=> crc32 ${fdt_addr_r} ${r3_fdt_size}
~~~

CRC32 is useful for repeatability/corruption isolation, not security. Compare it across loads and against a host-computed value using the same bytes/polynomial/tool.

Build an interval table:

| Object | Start | End-exclusive | Alignment | Source of truth |
|---|---:|---:|---:|---|
| OpenSBI/resident firmware | acquire | acquire | platform | DT/U-Boot/OpenSBI log |
| relocated U-Boot | acquire | compute | platform | <code>bdinfo</code> |
| U-Boot malloc/stack/FDT/video | acquire | compute | platform | <code>bdinfo</code>/source |
| Linux Image | <code>kernel_addr_r</code> | start + exact file size/decompressed span | 2 MiB RV64 entry | env/load/header |
| initramfs | <code>ramdisk_addr_r</code> | start + copied size | page/helpful | load result |
| working DTB | <code>fdt_addr_r</code> | start + capacity | 8 bytes | FDT header/buffer |
| persistent trace | acquire | compute | platform | DTS/source |

Perform every addition with overflow checking. Remember that a compressed kernel requires source, destination, and decompression-workspace analysis.

## 10.5 Linux early diagnostics

Useful kernel configuration/command-line tools, subject to driver support:

- <code>CONFIG_SERIAL_EARLYCON=y</code>;
- correct <code>earlycon=…</code> derived from binding/platform, not guessed;
- <code>console=…</code> for the normal driver;
- <code>ignore_loglevel</code>;
- <code>loglevel=8</code>;
- <code>initcall_debug</code>;
- <code>early_ioremap_debug</code> for specific mapping problems;
- <code>panic=…</code> and <code>panic_on_warn</code> only when recovery behavior is planned;
- <code>init=/bin/sh</code> or <code>rdinit=/bin/sh</code> to isolate user space;
- <code>nokaslr</code> during address-sensitive debugging when supported/appropriate;
- <code>maxcpus=1</code> to isolate secondary-hart issues;
- subsystem-specific dynamic debug later in boot.

The current project only proves that earlycon support is configured; it does not prove the DT/command-line selects the correct UART.

## 10.6 Earliest kernel-entry beacon

A temporary marker placed before normal console initialization can distinguish branch/entry failure from console configuration failure. It must:

- use a verified already-initialized UART or safe scratch register;
- preserve all architecturally required entry registers;
- avoid clobbering boot data;
- not depend on uninitialized stack/global state;
- be visibly unique;
- be removed or guarded after diagnosis.

Source-level placement must be selected in the exact vendor kernel’s RISC-V head/entry assembly. Review disassembly to ensure the marker precedes the suspected failure and respects relocation/MMU state. This is board modification work, not a generic command-line option.

## 10.7 Binary and source tracing

~~~bash
# Context: Linux development host.
riscv64-linux-gnu-readelf -h -l Image-or-ELF
riscv64-linux-gnu-nm -n vmlinux | sed -n '1,120p'
riscv64-linux-gnu-objdump -drS vmlinux | less

# U-Boot source checkout at the exact shipping tag.
rg -n 'Starting kernel|do_booti|booti_start|boot_jump_linux|cleanup_before_linux' .
rg -n 'fdt_addr_r|ramdisk_addr_r|kernel_addr_r|bootcmd' \
  configs board include arch
~~~

For a raw Linux <code>Image</code>, use <code>vmlinux</code> and the architecture image-header definition to correlate entry/placement; do not expect <code>readelf</code> to parse a raw Image as ELF.

## 10.8 JTAG strategy

With hardware debug:

1. Halt at U-Boot’s final architecture-specific boot function.
2. Record entry, <code>a0</code>, <code>a1</code>, <code>satp</code>, privilege state, and PC.
3. Validate DTB magic at <code>a1</code>.
4. Set a hardware breakpoint at the kernel physical entry.
5. Single-step the transfer.
6. If the breakpoint hits, move to the earliest kernel labels.
7. If it does not, inspect exception/cause/trap state and instruction bytes.
8. Avoid software breakpoints in soon-to-be-copied or cache-sensitive image memory.

## 10.9 Fault taxonomy

| Category | Typical signature | Best first discriminator |
|---|---|---|
| Media/layout | reset before banner, inconsistent reads | raw-region/GPT hash and ROM/SPL log |
| DRAM | random corruption, size-sensitive failure | training log and bounded memory test |
| Packaging | FIT node/hash/config errors | <code>dumpimage -l</code>, exact ITS |
| Environment/script | wrong filename/address/size | decoded script and echo markers |
| Placement | late loader error or silent corruption | interval map and hashes |
| Handoff state | immediate trap/no kernel marker | JTAG registers and entry breakpoint |
| DT | early panic/no console/device probe fault | FDT checks, schema, known-good matched DTB |
| Initramfs | exec error/panic after kernel init | archive/file/readelf and <code>rdinit=/bin/sh</code> |
| Display only | black HDMI while UART continues | simultaneous UART capture |
| Watchdog/power | periodic reset | reset reason and timing |

## 10.10 Experimental hygiene

- Preserve one known-good medium.
- Never change boot region, kernel, DTB, initramfs, command line, and environment in one run.
- Hash every input and output.
- Keep QEMU runs separated by QEMU, OpenSBI, U-Boot, Linux, machine arguments, and DTB hash.
- Do not claim QEMU virt validates RV2 clocks, PMU, USB, HDMI, pinmux, firmware layout, or physical timing.
- Stop after a new boundary is proven and update the hypothesis table before changing more.

## 10.11 U-Boot command diagnostics and justified conclusions

| Command/action | Use | Success proves | Failure narrows to |
|---|---|---|---|
| <code>version</code> | build identity | running banner/build text | not binary hash or source provenance |
| <code>bdinfo</code> | DRAM/relocation/board state | U-Boot’s current view | DT/Linux may still disagree |
| <code>dm tree</code> | bound/probed devices | DM topology/state | missing driver, DT, provider, or probe error |
| <code>mmc/usb/nvme/scsi</code> info/list | controller/media discovery | that U-Boot path works | not Linux driver path |
| <code>part list</code>, <code>ls</code> | partition/filesystem | parser can read selected media | not file integrity |
| <code>md</code> | inspect RAM bytes | bytes at address now | source, reservation, or future stability |
| <code>cmp</code> | compare ranges | current equality over length | authenticity |
| <code>crc32</code> | repeatability | same corruption-detection digest | security |
| <code>iminfo</code>/<code>dumpimage</code> host-side | image header/config | parseable supported image | authorized unless signature required |
| <code>load</code> + immediate <code>filesize</code> | load a file | returned byte count in RAM | correct destination/no overlap |
| <code>fdt header/print</code> | blob structure/properties | readable current working FDT | semantic hardware correctness |
| <code>booti</code> error | late path diagnostics | parser/preparation reached | kernel entry only with later evidence |

<code>mw</code>, arbitrary <code>cp</code>, and memory loads can overwrite executing firmware or evidence. Use only in a proven disposable RAM range. Persistent media writes/erase/save commands require a separate reviewed procedure.

Header clues:

~~~text
ELF              7f 45 4c 46
FDT/FIT          d0 0d fe ed
gzip             1f 8b
xz               fd 37 7a 58 5a 00
zstd             28 b5 2f fd
cpio newc ASCII   30 37 30 37 30 31
~~~

A FIT shares FDT magic; structure/content distinguishes it from a hardware DTB.

## 10.12 Debug builds, logs, relocation, and trace

- Enable U-Boot logging/debug symbols in a disposable build; select categories/levels narrowly to avoid SPL overflow and timing changes.
- Use <code>CONFIG_DEBUG_UART</code> only with verified early UART constants.
- Retain ELF and map files for SPL and proper; raw binaries are insufficient for symbolization.
- Relocated U-Boot breakpoints need the runtime relocation offset. Derive it from <code>bdinfo</code>/symbols rather than setting only link-address breakpoints.
- Where supported, use U-Boot trace/log buffers but reserve them from later images and record their timing overhead.
- In QEMU, stop before the handoff, inspect <code>a0/a1/satp</code>, DTB magic, entry bytes, and single-step the final indirect branch.
- With OpenOCD/JTAG, verify hart selection and reset behavior; the debugger may itself alter halt/reset/cache timing.

Negative tests should be deliberate and recoverable: bad DTB magic, truncated FIT, wrong signature, missing initramfs size, one disabled DT provider, or a forced boot-count failure. Never inject faults into the only recovery image.

---

# 11. Security and reliability

## 11.1 Four different guarantees

| Mechanism | Question answered | What it does not prove |
|---|---|---|
| Checksum/CRC | Were bytes accidentally changed? | Who authorized them |
| Cryptographic hash | Do bytes match this digest? | Whether the digest itself is trusted |
| Signature verification | Did an authorized key sign the covered content? | Freshness unless version/rollback policy is signed |
| Measured boot | What did the platform observe/load? | Whether loading should have been allowed |

Secure boot usually means **verified boot with an immutable or strongly protected root key**, plus rollback prevention and controlled recovery. Measured boot extends digests into a protected log/PCR-like mechanism so a remote or local verifier can attest to the loaded sequence.

## 11.2 Chain of trust

~~~mermaid
flowchart TD
    R["ROM root key / immutable digest"] --> S["Verify SPL"]
    S --> F["Verify privileged firmware"]
    F --> U["Verify U-Boot and control DT"]
    U --> O["Verify OS configuration"]
    O --> K["Verify kernel, DTB, initramfs"]
    K --> V["Verify root filesystem / policy"]
~~~

Every arrow needs:

- a protected verification implementation;
- an authenticated length and load address;
- an allowed key and algorithm;
- failure behavior;
- version/rollback state;
- a key-rotation/revocation path;
- fault-injection assumptions.

Authenticating the kernel while accepting an unsigned DTB or command line can still permit security policy changes: disabling an IOMMU, altering reserved memory, changing console/init, exposing debug devices, or passing a malicious initramfs.

## 11.3 FIT verified boot

A FIT can contain multiple images and configurations. A simplified illustrative source:

~~~dts
/dts-v1/;

/ {
    description = "Signed OS bundle";
    #address-cells = <2>;

    images {
        kernel-1 {
            description = "Linux Image";
            data = /incbin/("Image");
            type = "kernel";
            arch = "riscv";
            os = "linux";
            compression = "none";
            load = <0x0 0x80400000>;
            entry = <0x0 0x80400000>;
            hash-1 {
                algo = "sha256";
            };
        };

        fdt-1 {
            data = /incbin/("board.dtb");
            type = "flat_dt";
            arch = "riscv";
            compression = "none";
            hash-1 {
                algo = "sha256";
            };
        };
    };

    configurations {
        default = "conf-1";
        conf-1 {
            kernel = "kernel-1";
            fdt = "fdt-1";
            signature-1 {
                algo = "sha256,rsa2048";
                key-name-hint = "release";
                sign-images = "kernel", "fdt";
            };
        };
    };
};
~~~

**Teaching example:** <code>0x80400000</code> is a 2 MiB-aligned QEMU-teaching destination, not an RV2 address. A real bundle uses reviewed 64-bit load/entry values and a complete interval map. The important security property is that the chosen **configuration** binds the intended kernel and FDT. Signing individual components without signing their authorized combination can permit mix-and-match.

Typical host operations:

~~~bash
# Context: isolated signing/build host; options vary by U-Boot release.
mkimage -f os.its unsigned.itb
mkimage -f os.its -k keys/ -K u-boot-control.dtb -r signed.itb
dumpimage -l signed.itb
~~~

Protect private keys outside ordinary build workers. Review what <code>-r</code> marks required and which public key is embedded. Test wrong-key, corrupted-image, missing-signature, and unauthorized-configuration failures. See [U-Boot FIT signature verification](https://docs.u-boot.org/en/v2025.01/usage/fit/signature.html).

## 11.4 Root-key placement

FIT public keys are commonly stored in U-Boot’s **control DT**, not in the untrusted OS DTB. The control DT or its digest must itself be authenticated by a preceding stage. If an attacker can replace U-Boot plus the key tree, FIT verification adds no trust.

Production key hierarchy:

- offline root key authorizes intermediate/release keys;
- release key signs firmware/OS manifests;
- device or product-family trust anchor is fused or ROM-protected;
- revocation list and minimum-version state are authenticated;
- development keys are unmistakably separate and rejected in production lifecycle state.

## 11.5 Rollback protection

A signature says “authorized,” not “new enough.” Include a monotonically increasing security version in signed metadata and compare it to protected state:

~~~text
if signature_invalid:
    reject
if image_security_version < protected_minimum:
    reject_as_rollback
if boot_succeeds_and_update_is_committed:
    advance_protected_minimum_according_to_policy
~~~

Do not advance irreversible state before a newly installed image demonstrates boot success, or a power loss can brick both slots. Design key rotation and emergency recovery before fusing production policy.

## 11.6 Measured boot

U-Boot can measure boot components into a TPM/event log when configured. A useful event includes the digest plus enough metadata to distinguish:

- stage and component type;
- version/build identity;
- selected FIT configuration;
- DTB and command line;
- secure-boot policy;
- failure/recovery mode.

Remote attestation policy must understand legitimate update transitions. A measurement log without protected extend semantics is only a debug log. See [U-Boot measured boot](https://docs.u-boot.org/en/latest/usage/measured_boot.html).

## 11.7 Environment and shell hardening

Threats include:

- interrupting autoboot to gain a shell;
- changing <code>bootargs</code> to <code>init=/bin/sh</code>;
- loading unsigned images over USB/network;
- editing persistent environment;
- invoking memory read/write commands;
- booting an alternate slot or removable device;
- extracting secrets from RAM or flash.

Mitigations:

- verified configurations whose policy includes command line and DTB;
- authenticated/encrypted environment if mutable policy is necessary;
- disable or password-gate interactive shell, recognizing that weak password schemes are not a hardware trust boundary;
- remove unused commands, protocols, parsers, and boot devices;
- lock debug according to lifecycle;
- enforce required signatures in code, not mutable scripts;
- protect environment offsets from filesystem/update overlap;
- zero sensitive memory before untrusted handoff where required;
- rate-limit/restrict recovery and use signed recovery payloads.

Maintain a separate service/manufacturing mode with auditable activation rather than shipping an unrestricted production shell.

## 11.8 A/B update state machine

~~~mermaid
stateDiagram-v2
    [*] --> StableA
    StableA --> TrialB: install signed B
    TrialB --> StableB: OS reports success
    TrialB --> StableA: attempts exceed limit
    StableB --> TrialA: later update
    TrialA --> StableA: OS reports success
    TrialA --> StableB: attempts exceed limit
~~~

State must survive sudden power loss:

- active slot;
- trial slot;
- attempts remaining or boot count;
- image version and validity;
- successful-boot flag;
- update transaction generation/checksum.

U-Boot’s boot-count facility can use <code>bootcount</code>, <code>bootlimit</code>, and <code>altbootcmd</code> when <code>CONFIG_BOOTCOUNT_LIMIT</code> and a backend are configured. User space marks successful boot by resetting/committing state. See [U-Boot boot count](https://docs.u-boot.org/en/latest/api/bootcount.html). The backend’s power-loss atomicity and wear behavior are part of the design.

## 11.9 Watchdogs

A boot watchdog must be:

- started at a deliberate stage;
- serviced only by code making bounded progress;
- handed off or reconfigured explicitly;
- compatible with lengthy signature checks, media retries, and DRAM training;
- recorded in reset-reason diagnostics;
- tested in every stage and recovery path.

Blindly kicking a watchdog in a timer loop turns a hang into a permanent hang. A stage should service it at progress checkpoints with an upper-bound design.

The current RV2 baseline says Linux watchdog support is not enabled in the recorded configuration. That says nothing about a Boot ROM, SPL, OpenSBI, PMIC, or external watchdog. Determine reset reasons rather than infer.

## 11.10 Power-loss-safe updates

Never update the only bootable copy in place. A robust sequence:

1. Write inactive image and metadata.
2. Flush media according to its real durability semantics.
3. Read back and verify.
4. Atomically mark the inactive slot as a trial.
5. Reboot with limited attempts.
6. Let the OS run health checks.
7. Atomically commit success.
8. Retain a signed recovery image outside normal update scope.

NAND/eMMC/SPI NOR have different erase, bad-block, wear, reliable-write, and boot-partition behavior. “<code>sync</code> returned” is not a complete storage-failure model.

## 11.11 Recovery design

Recovery must answer:

- What triggers it: failed verification, boot count, strap, authenticated remote request?
- Which stage remains trusted?
- Which transport and parser are enabled?
- Is the recovery payload signed and rollback-checked?
- Can recovery restore partition tables/raw firmware/environment?
- Can an operator retrieve logs without secrets?
- Can recovery itself be updated safely?
- How is physical presence distinguished from remote input?

A removable “golden image” is useful only if ROM/media selection and the golden artifact are protected and tested.

## 11.12 DTB as security input

Treat the DTB as untrusted input until verified and structurally bounded. A malicious or corrupt DT can:

- describe RAM over firmware or MMIO;
- hide or expose reserved regions;
- redirect console;
- alter IOMMU/DMA relationships;
- enable debug devices;
- cause integer/offset parser faults;
- select unexpected drivers;
- change <code>/chosen</code> boot policy.

Validate total size before copying; use hardened libfdt; sign the authorized tree/configuration; apply only authenticated overlays; revalidate after fixups; never allow an unbounded <code>fdt resize</code> into unknown memory.

## 11.13 Reproducibility and supply chain

Archive per release:

- source commit/submodules/patches;
- toolchain/container digest;
- configuration and generated headers;
- DT source and schema warnings;
- binary map, sizes, section layout;
- full artifact hashes and signatures;
- SBOM and license record;
- signing provenance without private material;
- test evidence and hardware revisions;
- recovery artifacts and operator procedure.

Reproducible output improves forensic confidence, but reproducibility does not itself prove the source or toolchain is trustworthy.

## 11.14 Production review questions

- Is the first mutable byte authenticated?
- Are lengths, destinations, and entry points covered by the signature?
- Are kernel, DTB, initramfs, and command line bound into one authorized configuration?
- Can an old signed vulnerable image boot?
- Can saved environment disable verification?
- What happens after three corrupt boots?
- Which immutable recovery path remains?
- Can power fail after each write in the update state machine?
- Is UART/JTAG accessible in production, and is that intentional?
- Are security failures distinguishable from media/DRAM failures without leaking secrets?

## 11.15 Threat model

| Adversary/capability | Target | Required controls |
|---|---|---|
| corrupt media/power loss | availability/integrity | checksums, redundant metadata, A/B, atomic state, recovery |
| physical removable-media attacker | boot policy | ROM-rooted verification, boot-order lock, signed recovery |
| console access | environment/shell/memory | locked autoboot, restricted commands, immutable verification policy |
| network attacker | PXE/TFTP/update | authenticated transport and signed payload; freshness/replay policy |
| supply-chain/build compromise | release artifacts | pinned/reproducible builds, review, SBOM, isolated signing, provenance |
| stolen old signing key/image | rollback | revocation and protected minimum security version |
| DMA-capable device | secrets/firmware/kernel memory | IOMMU/firewall/teardown, restricted preboot drivers |
| invasive/fault attacker | verification branches/keys | lifecycle debug control, hardened crypto, redundant checks, hardware countermeasures |

Assets include signing keys, device secrets, firmware integrity, boot availability, rollback counters, measured-boot state, customer data, and diagnostic logs.

## 11.16 UEFI Secure Boot, shim, TPM, and encryption

UEFI Secure Boot authenticates EFI executables against firmware trust databases/policy. Platform Key and key-exchange/signature databases govern enrollment and allowed/revoked images; exact ownership policy is platform-specific. In many Linux deployments, firmware accepts a signed **shim** EFI application; shim applies its embedded/vendor/Machine Owner Key policy to GRUB or another next stage and supports revocation metadata such as SBAT. GRUB/kernel policy must remain aligned—accepting an unsigned module, command line, or kernel can reopen the chain. See [UEFI 2.11](https://uefi.org/specs/UEFI/2.11/) and the [shim source](https://github.com/rhboot/shim).

TPM measured boot extends digests into PCRs and records an event log. A verifier replays the log and compares PCRs. PCR policy can seal a disk key to an approved boot state, but updates require a planned policy transition/recovery key.

Disk encryption provides **confidentiality** of stored data; verified boot provides **authenticity/integrity of executable policy**. One does not imply the other:

- an authentic loader may unlock attacker-modified unverified user data;
- an encrypted disk may boot malicious signed/rollback firmware that captures the key;
- key entry via an untrusted console can be intercepted;
- keys remain exposed in RAM/devices after unlock unless lifecycle/teardown is designed.

## 11.17 Upstream capability versus integrator responsibility

| Upstream can provide | Integrator must decide/prove |
|---|---|
| FIT parsing, hashes/signatures | immutable trust anchor, required-signature policy, key custody |
| measured-boot hooks | TPM wiring, event policy, attestation verifier |
| environment backends | authentication, redundancy, offsets, corruption recovery |
| boot count backends | success definition, atomic update, wear, fallback |
| watchdog drivers | stage timeouts, servicing discipline, handoff |
| standard boot/recovery commands | allowed devices/protocols and production shell policy |
| crypto algorithms | approved algorithms, side-channel/fault threat, rotation/revocation |
| update building blocks | end-to-end power-loss-safe transaction and fleet rollback |

Before release, perform CVE monitoring for U-Boot, OpenSBI/TF-A, crypto/compression/filesystem/network libraries, toolchain, UEFI/shim/GRUB where present, and vendor binaries. Maintain a tested emergency-signing and revocation process. Redundant environment copies need sequence/checksum/selection logic and fault injection; “two copies” alone is not atomic.

At handoff, stop DMA-capable devices not intentionally inherited, close firmware mappings/domains where applicable, reserve persistent firmware memory, and scrub keys no longer needed. Document which stage owns every device and secret before and after the branch.

---

# 12. GRUB 2 and other boot technologies

## 12.1 These projects occupy different layers

OpenSBI and TF-A are primarily privileged runtime/secure firmware; coreboot initializes hardware; UEFI defines a firmware interface; GRUB/systemd-boot are OS loaders/managers; U-Boot may cover hardware initialization through OS launch; kexec launches a new kernel from a running kernel. Comparing them as interchangeable “bootloaders” hides the real architecture.

## 12.2 Comparison matrix

| Technology | Typical predecessor | Main role | Typical successor/handoff | Strong fit |
|---|---|---|---|---|
| U-Boot | ROM/SPL/TF-A/OpenSBI | Embedded init, policy, media, native/EFI OS boot | Native Linux registers+DT, EFI app | SoCs, appliances, recovery |
| GRUB 2 | BIOS or UEFI | Filesystem/menu/module-rich OS loader | x86 Linux boot protocol, EFI, Multiboot | PCs, servers, multiboot |
| UEFI/EDK II | SEC/PEI/DXE platform firmware | Standard protocols, drivers, boot/runtime services | EFI application; <code>ExitBootServices</code> | Standardized server/client firmware |
| systemd-boot | UEFI | Minimal EFI boot manager | EFI stub/UKI | Simple UEFI Linux fleets |
| UKI/systemd-stub | UEFI manager | Signed combined kernel/initrd/cmdline metadata | Linux EFI stub transition | Measured/verified Linux deployment |
| barebox | ROM/earlier firmware | Embedded bootloader with Linux-like APIs | Native Linux + DT | Embedded development/maintenance |
| coreboot | reset/ROM | Fast hardware initialization | Payload: UEFI, SeaBIOS, LinuxBoot, GRUB | Open x86/selected SoC firmware |
| LinuxBoot/u-root | coreboot/UEFI or firmware | Linux kernel as firmware + Go user-space loader | kexec to production kernel | Servers, flexible network/storage policy |
| TF-A | Arm ROM/BL stages | EL3 secure runtime and trusted boot | BL33 U-Boot/UEFI/kernel | Armv8 secure firmware |
| OpenSBI | RISC-V ROM/SPL | M-mode runtime SBI | S-mode U-Boot/UEFI/Linux | RISC-V platforms |
| iPXE | PXE ROM/UEFI/U-Boot/network | Network boot client/scripting | OS loader/kernel/another iPXE | Datacenter provisioning |
| Syslinux/Extlinux | BIOS/UEFI/filesystem; config also read by U-Boot | Simple boot menu/config | Linux | Removable media/simple fleets |
| MCUboot | MCU ROM/first stage | Signed image selection/swap/rollback | RTOS/application | Microcontrollers |
| Android boot + AVB | vendor bootloader | Android image packaging and verified boot | Android Linux kernel | Android devices |
| kexec/Petitboot | Running Linux | In-kernel next-kernel loading | Architecture kexec entry | OpenPOWER/LinuxBoot/reboot speed |
| zIPL | IBM Z IPL mechanisms | Prepare boot records and Linux parameters | Linux on IBM Z | IBM Z |

## 12.3 GRUB 2

GRUB separates a small platform-specific core from loadable modules. It understands many filesystems, partition schemes, disk abstractions, configuration language constructs, cryptographic/security features, and OS protocols. On x86 it can be entered through BIOS or as an EFI application.

Typical UEFI GRUB chain:

1. UEFI Boot Manager loads <code>grubx64.efi</code>.
2. GRUB locates modules/configuration and storage.
3. It selects and loads Linux/initrd, constructs command line and boot parameters.
4. It obtains the current UEFI memory map.
5. It exits boot services at the required boundary.
6. It enters Linux through the applicable x86/EFI path.

GRUB is strong when rich disk/filesystem/menu policy is more important than tiny footprint. It is generally not the component that trains embedded DRAM. See the [GNU GRUB manual](https://www.gnu.org/software/grub/manual/grub/grub.html); the current manual is identified as 2.14 by the accessible documentation mirror.

## 12.4 UEFI and EDK II

UEFI standardizes:

- loaded-image and device-path models;
- block/file/network protocols;
- Boot Manager variables and boot options;
- memory allocation and memory map;
- boot and runtime services;
- configuration tables such as ACPI and DT;
- Secure Boot signature databases.

EDK II is a major open-source implementation. U-Boot can also provide an EFI implementation and execute EFI applications through <code>bootefi</code>. See [UEFI 2.11](https://uefi.org/specs/UEFI/2.11/) and [U-Boot bootefi](https://docs.u-boot.org/en/v2024.04/usage/cmd/bootefi.html).

The difficult UEFI handoff is memory-map convergence:

~~~text
GetMemoryMap -> allocate/finish work -> GetMemoryMap again -> ExitBootServices(map_key)
~~~

An allocation between the final map and exit changes the key. A robust loader retries the specified sequence and stops using boot services only after successful exit.

## 12.5 systemd-boot and Unified Kernel Images

systemd-boot is a small UEFI boot manager that reads boot-loader entries and launches EFI executables. A Unified Kernel Image packages the Linux EFI stub/kernel with initrd, command line and metadata into one PE file that can be signed and measured. This narrows mix-and-match compared with loose files, provided signature and command-line policy are enforced. See [systemd-boot](https://www.freedesktop.org/software/systemd/man/systemd-boot.html), [systemd-stub](https://www.freedesktop.org/software/systemd/man/systemd-stub.html), and [ukify](https://www.freedesktop.org/software/systemd/man/ukify.html).

## 12.6 coreboot, LinuxBoot, and payloads

coreboot focuses on hardware initialization and then launches a payload. Payload choices include SeaBIOS, EDK II, GRUB, and Linux-based environments. LinuxBoot uses a Linux kernel for firmware device support and a small user space—often u-root/systemboot—to locate, verify, and <code>kexec</code> the production kernel. This reuses Linux drivers but moves a large kernel into the trusted/update surface. See [coreboot documentation](https://doc.coreboot.org/), [coreboot payloads](https://doc.coreboot.org/payloads.html), and the [LinuxBoot book](https://book.linuxboot.org/coreboot.u-root.systemboot/index.html).

## 12.7 barebox

barebox is an embedded bootloader with Linux-inspired driver and device abstractions, shell/environment capabilities, Devicetree support, and native Linux boot. Its design can feel familiar to kernel developers and is suitable for maintainable embedded recovery. The platform support and production ecosystem still determine feasibility. See [barebox Linux boot documentation](https://www.barebox.org/doc/latest/user/booting-linux.html).

## 12.8 Network boot: iPXE

iPXE adds robust network protocols and scripting and can chainload from existing PXE/UEFI/U-Boot environments. It is useful for installation, stateless fleets, and recovery, but expands dependency on DHCP/DNS/HTTP/TLS infrastructure and credential/time policy. See [iPXE documentation](https://ipxe.org/docs) and [chainloading guide](https://ipxe.org/howto/chainloading).

## 12.9 MCUboot and Android AVB

MCUboot targets microcontrollers and focuses on signed image slots, swaps/overwrites, boot trailers, and rollback behavior under tight flash/RAM constraints. It is not a general Linux filesystem bootloader. See [MCUboot documentation](https://docs.mcuboot.com/) and [design](https://docs.mcuboot.com/design.html).

Android Verified Boot binds Android boot/system partitions into a verified chain with rollback metadata and device-state policy. Modern Android packaging includes versioned boot/vendor_boot structures and AVB metadata. See [Android Verified Boot](https://source.android.com/docs/security/features/verifiedboot/avb) and the [AVB source README](https://android.googlesource.com/platform/external/avb/+/master/README.md).

## 12.10 kexec and Petitboot

<code>kexec</code> lets a running Linux kernel load and transfer to another kernel without returning through all platform firmware. It can shorten reboot paths and reuse Linux drivers for discovery. However:

- devices and CPUs must be quiesced;
- crash-kernel and normal-kexec paths differ;
- secure boot may require signed-image enforcement;
- firmware initialization is skipped, so stale hardware state matters;
- a kexec boot is not equivalent to a cold-boot performance sample.

Petitboot uses a Linux environment to discover boot options and typically kexecs the selected host kernel. On OpenPOWER, that environment is integrated with OPAL/skiboot.

## 12.11 Choosing an architecture

Ask in this order:

1. Who initializes silicon and DRAM?
2. Which privileged runtime must remain?
3. Is the platform contract native DT, ACPI/UEFI, OPAL, SBI, or another ABI?
4. Which media/filesystems/network protocols are required?
5. What is the immutable root of trust?
6. What update/recovery availability is required?
7. What footprint and boot-time budget applies?
8. Which drivers and board ports are maintained upstream?
9. Which operator interface is acceptable in production?
10. How will the final handoff be observed and tested?

Use the smallest composition that meets those requirements and has a sustainable maintenance/security community. “Smallest code” is not always “smallest lifecycle risk.”

## 12.12 Detailed comparison

| Technology/category | Common platforms | Firmware assumptions | FS/network and configuration | DTB/ACPI handling | Secure boot | Kernel handoff | Footprint/update/recovery | Ideal use |
|---|---|---|---|---|---|---|---|---|
| U-Boot, multi-role loader | Arm/RISC-V/Power/embedded x86 | ROM/xPL or can supply xPL; optional TF-A/OpenSBI | broad block FS, USB/net/PXE; env/hush/bootstd | control + working DT, fixups; EFI/ACPI-capable paths | FIT verified/measured boot, platform root required | native Linux, EFI, ELF and others | configurable; A/B/recovery integrator-designed | SoC boot/recovery and mixed protocols |
| GRUB 2, OS loader | BIOS/UEFI PCs/servers, selected others | initialized BIOS/UEFI/platform | rich modular FS/disk/net; grub.cfg shell | ACPI/EFI; DT on supported platforms | UEFI Secure Boot/shim and signed-module policy | Linux protocol/EFI, Multiboot | larger modular core; config/package updates | rich multiboot and enterprise OS selection |
| UEFI/EDK II, firmware interface | client/server Arm/x86/RISC-V | SEC/PEI/DXE platform port | protocols/drivers; NVRAM Boot#### | ACPI and/or DT configuration tables | Secure Boot databases, measured boot | EFI application then ExitBootServices | large standardized firmware; capsules/recovery platform-specific | standardized hardware/OS boundary |
| Linux EFI stub/UKI | UEFI systems | working UEFI | UKI combines loose inputs; manager selects | ACPI/DT from firmware plus embedded metadata | PE signing and measured sections | stub internal transition | one signed artifact; fleet-friendly | controlled Linux-only UEFI |
| systemd-boot | UEFI | EFI FS/variables | loader entries, no general shell | passes firmware context | relies on UEFI/UKI signatures | starts EFI executable | small manager; simple fallback | simple Linux fleets |
| Syslinux/Extlinux | BIOS/UEFI/removable; config parsed by U-Boot too | platform-specific loader present | simple FS/menu config, network variants | limited/native context; U-Boot supplies DT | signature story integration-specific | Linux protocol | small/simple | install media and simple configs |
| barebox | embedded SoCs | ROM/earlier stage, board port | embedded FS/net/shell | DT-centric, fixups | signed-image features depend on integration | native Linux | moderate; maintenance/recovery focus | embedded Linux development/product |
| coreboot + payload | x86/selected SoCs | replaces proprietary init stages where supported | payload-dependent | ACPI/DT generated for payload/OS | verified boot/vboot designs platform-specific | through payload | fast init; dual-region recovery common | open hardware initialization |
| LinuxBoot/u-root | servers | coreboot/UEFI/other loads Linux firmware kernel | Linux drivers + Go tooling/network | ACPI/DT via Linux | signed firmware kernel and next payload policy | kexec | relatively large but driver-rich | server provisioning/recovery |
| OpenSBI, runtime firmware | RISC-V | M-mode entry/platform port | no general OS FS policy | consumes/fixes/passes FDT | verified boot belongs to surrounding chain | S/HS/VS payload with SBI | small resident firmware | RISC-V privileged services |
| TF-A, secure/runtime firmware | Armv8 | ROM/BL stages/platform port | not general boot-manager policy | passes platform data to BL33 | Trusted Board Boot/CoT | BL33 at configured EL/security | compact privileged components | Arm secure-world/runtime boundary |
| iPXE, network loader | BIOS/UEFI/PXE/U-Boot chainload | network hardware/protocol environment | DHCP, TFTP, HTTP(S), iSCSI and scripts | hands through platform protocol | signed binaries/TLS/payload policy | chainload/OS-specific | network-dependent recovery | provisioning/stateless boot |
| kexec/Petitboot | running Linux, notably OpenPOWER | firmware already initialized | Linux drivers/FS/net; discovered menu | running kernel supplies DT/firmware data | kernel-enforced signed kexec policy | architecture kexec | skips cold firmware; rescue Linux | flexible server OS discovery |
| MCUboot | MCUs | MCU ROM/flash | no general FS; slot metadata | none/RTOS-specific | signed images, rollback counters | RTOS/app vector/header | very small; swap/overwrite/revert | constrained microcontrollers |
| Android loader/AVB | Android SoCs | vendor secure firmware/bootloader | Android partition formats, slots | vendor DT/DTBO handling | AVB chain/rollback indices | Android Linux protocol | tightly coupled OTA/A-B | Android devices |
| Hostboot/skiboot/OPAL | OpenPOWER servers | BMC/SBE/PNOR platform chain | skiroot/Petitboot uses Linux drivers | OPAL DT/runtime interface | platform secure boot/measured boot design | Petitboot kexec; Linux OPAL calls | serviceable server firmware | OpenPOWER |
| IPL/zIPL | IBM Z | IBM Z IPL/hypervisor/device records | zIPL prepares disk; platform boot menus | architecture parameter mechanisms | platform-specific secure operations | IBM Z Linux entry | enterprise IPL/recovery processes | IBM Z Linux |

U-Boot can itself expose EFI Boot Services and launch GRUB or another EFI application. In that composition, U-Boot owns hardware/DT/EFI implementation, GRUB owns OS menu/filesystem policy, and Linux consumes the EFI/native contract. Both components’ secure-boot policies must compose.

## 12.13 GRUB 2 deeper path

### BIOS

Legacy BIOS reads a small boot sector. Because that sector cannot contain GRUB’s filesystem/module logic, installation arranges a **core image** in an embedding area or a platform-supported partition/location. The core includes enough modules to find the GRUB prefix and load normal mode. Disk layout changes can therefore invalidate assumptions if GRUB is not reinstalled through supported tooling.

### UEFI

Firmware loads GRUB as a PE/COFF EFI application from the EFI System Partition or configured device path. GRUB uses EFI protocols for devices/memory, loads modules/configuration, then enters Linux via the supported EFI or native loader path and obeys the final memory-map/ExitBootServices rules.

### Runtime model

- platform/core image establishes module loading;
- <code>normal</code> mode reads <code>grub.cfg</code> and presents menu/shell;
- filesystem, RAID/LVM, crypto, video, network, Linux, Multiboot, and other capabilities are modules;
- <code>linux</code>/<code>linuxefi</code>-style commands and <code>initrd</code> behavior are platform/build/version dependent;
- selected command line and initrd are incorporated into the target boot protocol;
- Multiboot kernels receive a tagged information structure, not Linux boot_params.

### Secure Boot and shim

Firmware may validate shim, which then validates GRUB according to its embedded/MOK/SBAT policy. GRUB must restrict unsigned modules/configuration pathways and validate the kernel according to distribution policy; Linux may enforce lockdown. A signed first executable plus an unrestricted GRUB shell/module path is not a complete chain. Review the exact distribution patch set, because Secure Boot integration is not described fully by vanilla configuration alone.

---

# 13. Required knowledge and a 12-week learning plan

## 13.1 Competency matrix

| Domain | Foundation | Working | Senior/staff |
|---|---|---|---|
| Boot architecture | name stages | trace one board | design boundaries, recovery, ownership |
| RISC-V | integer ABI | CSRs/SBI/entry | privilege, HSM, fences, PMU, errata |
| C/assembly | freestanding basics | startup/linker | audit undefined behavior and state transitions |
| Linker/ELF | sections/symbols | maps/relocations | packaging, XIP/relocation/security review |
| U-Boot | commands/env | build/source trace | xPL/DM/bootstd/FIT/port/upstream |
| Devicetree | DTS syntax | bindings/libfdt | ABI/schema/topology/review |
| Linux early boot | command line | head/setup/initcalls | architecture entry and early-MMU debug |
| Firmware APIs | know SBI/UEFI | call/trace extensions | choose/runtime/security integration |
| Storage/update | partitions/images | A/B implementation | power-failure and fleet lifecycle |
| Security | hashes/signatures | verified boot | threat model, keys, rollback, attestation |
| Debug | serial logs | JTAG/QEMU/bisect | experiment design and cross-layer RCA |
| Performance | run benchmark | control noise | firmware/boot contamination and PMU validity |

## 13.2 Prerequisite proof matrix

| Topic | Why it matters | Minimum depth | Recommended exercise | Proof of competence |
|---|---|---|---|---|
| C, volatile/MMIO, UB, freestanding | loaders have no runtime safety net | object lifetime, integer overflow, volatile limits, barriers | write polled UART + bounded parser | compiler/disassembly review under optimization |
| RISC-V assembly/ABI/CSR/traps/PMP/MMU/cache/atomics | exact entry and privilege transition | psABI, M/S/U, trap CSRs, satp, fences, LR/SC | startup + trap + SBI call in QEMU | explain register/state at each instruction |
| Linker/ELF/relocations/maps/formats | placement is behavior | VMA/LMA, sections, symbols, program headers | custom linker + objcopy + map audit | predict raw layout and relocation |
| reset/clock/power/pinctrl/DRAM | earlier stages create usable hardware | dependency/reset sequence and DRAM boundary | trace one SoC from source/manual | reviewed stage ownership diagram |
| UART/timer/IRQ/DMA/PCIe/USB/storage | observation and boot media | MMIO, IRQ topology, DMA ownership, block/partition/FS | bring up two QEMU devices and trace DT | diagnose one provider/probe failure |
| SBI/OpenSBI/Linux/EFI/Multiboot | successor protocol must be exact | entry arguments, services, exit/cleanup | compare three handoffs | executable contract tables/tests |
| DTSpec/bindings/dtc/libfdt/schema | non-discoverable hardware ABI | cells/phandles/ranges/IRQ/providers/fixups | create/overlay/schema-check tree | zero unexplained relevant warnings |
| U-Boot/Linux Kconfig/Make/Git | builds are dependency graphs | defconfig, fragments, cross build, bisect | reproduce pinned build and one bisect | hashes/config/log and clean reproduction |
| GDB/binutils/QEMU/JTAG/serial/logic analyzer | prove exact boundary | symbols, relocation, breakpoints, electrical safety | breakpoint at final jump and GPIO/UART timeline | register/memory capture with inference |
| hashes/signatures/PKI/TPM/update | production authorization/availability | threat model, FIT/PE, keys, PCRs, rollback, A/B | signed FIT + power-fail model | negative tests fail closed and recovery works |

## 13.3 Twelve-week plan

Assume 8–12 focused hours per week and access to QEMU plus, from week 7, the RV2 and a safe UART adapter.

### Week 1 — Reset-to-user-space map

- Read Chapters 1–2.
- Draw the chain for QEMU virt, the RV2, x86 UEFI, and OpenPOWER.
- For every arrow, write privilege, arguments, memory owner, and observation.
- Complete Labs 1 and 2.

**Exit criterion:** explain why U-Boot, OpenSBI, and Linux are distinct and which remains resident.

### Week 2 — RISC-V ABI and privileged state

- Study the RISC-V calling convention, M/S/U modes, <code>mstatus/sstatus</code>, <code>satp</code>, traps, <code>fence.i</code>, and SBI base/HSM/time/reset.
- Disassemble a tiny freestanding ELF.
- Complete Lab 3.

**Exit criterion:** write and review the exact Linux entry register/state table from memory, then verify it against the kernel docs.

### Week 3 — U-Boot initialization and driver model

- Build U-Boot sandbox and a RISC-V QEMU target.
- Trace <code>start.S</code>, <code>board_init_f</code>, relocation, <code>board_init_r</code>, <code>main_loop</code>.
- Explore device/uclass binding and probing.
- Complete Labs 4 and 5.

### Week 4 — Images, environment, and boot methods

- Compare raw Image, legacy uImage, FIT, EFI application, and script.
- Decode an environment and boot script.
- Trace <code>booti</code> into architecture handoff.
- Complete Labs 6 and 9.

### Week 5 — Devicetree engineering

- Read DTSpec basics/flattened format and matching Linux bindings.
- Write, compile, decompile, query, overlay, and schema-check trees.
- Distinguish control and working FDT.
- Complete Labs 2, 3, and 12.

### Week 6 — Minimal launcher

- Implement the Chapter 7 loader.
- Add an interval map and machine-readable error codes.
- Boot a kernel with built-in initramfs on QEMU.
- Complete Lab 10.

### Week 7 — RV2 evidence acquisition

- Verify UART voltage/pinout.
- Acquire raw region, partition layout, full hashes, scripts, DTBs, configs, and UART log.
- Do not alter the vendor boot region.
- Complete Lab 14.

### Week 8 — RV2 kernel handoff

- Decode the shipping script.
- Construct an address/size/reservation map.
- Reproduce loads interactively and copy <code>filesize</code> immediately.
- Establish pre-jump and earliest-kernel markers.
- Complete Labs 7 and 8 adapted to the safe baseline.

### Week 9 — Porting/build reproducibility

- Rebuild the pinned vendor U-Boot/OpenSBI tuple.
- Inspect ITS/FIT and source anchors.
- Change a harmless banner in a disposable QEMU/vendor build before hardware.
- Document artifact provenance.

### Week 10 — Verified boot and recovery

- Generate test-only keys, sign a FIT, verify success and failure.
- Design A/B state transitions and inject power failures in a model/test harness.
- Complete Labs 11 and 13.

### Week 11 — Cross-platform and production review

- Compare GRUB/UEFI/coreboot/LinuxBoot/barebox/Petitboot/zIPL.
- Review key management, rollback, watchdog, environment, debug, and recovery.
- Produce a one-page architecture decision record.

### Week 12 — Performance and capstone

- Measure cold versus warm/kexec boot separately.
- Capture firmware versions, frequency, CPU topology, command line, interrupts, and thermal state.
- Run Labs 15 and 16.
- Present an RV2 root-cause report that clearly separates observations, hypotheses, and next experiment.

Weekly accountability summary:

| Week | Required lab/output | Deliverable | Acceptance |
|---:|---|---|---|
| 1 | Labs 1–2 | four-chain contract map | every arrow has mode/input/output/evidence |
| 2 | Lab 3 + entry disassembly | RISC-V entry note | matches normative register/state contract |
| 3 | Labs 4–5 | U-Boot init/DM trace | bind/probe and relocation explained |
| 4 | Labs 6/9 | booti source-to-wire trace | sizes/reservations/final jump linked |
| 5 | Labs 12/18/19 | DT baseline/overlay/schema dossier | control/working trees distinguished |
| 6 | Lab 10 | mini-loader artifact/map/log | kernel first marker plus negative test |
| 7 | Lab 14 | RV2 evidence manifest | no target mutation; complete hashes/log |
| 8 | Labs 7–8 adapted safely | RV2 handoff worksheet | one new boundary proven |
| 9 | Lab 17/vendor rebuild | reproducible build record | source/config/toolchain/artifacts pinned |
| 10 | Labs 11/13 | signed FIT + A/B model | invalid images fail; power cuts recover |
| 11 | Lab 20/design review | architecture decision record | layer/security/recovery trade-offs defended |
| 12 | Labs 15–16 | capstone RCA/performance report | evidence and uncertainty review-ready |

---

# 14. Practical labs

Every lab uses a disposable directory or copy. Record tool versions and SHA-256 hashes.

**Execution status:** these are reproducible procedures and illustrative expected patterns. No lab is claimed as executed during handbook generation; only the pre-existing project observations explicitly labeled in Chapter 9 are treated as observations.

## Lab 1 — Artifact classification

**Objective:** distinguish ELF, raw Image, uImage, FIT, DTB, cpio/initramfs, and boot script.

**Steps:**

~~~bash
# Context: Linux host, copies of artifacts.
file *
sha256sum *
readelf -h candidate.elf
dumpimage -l candidate.itb
fdtget -t s candidate.dtb / compatible
gzip -t initramfs.cpio.gz
~~~

Use <code>fdtdump</code>/<code>fdtget</code> on a known DTB node rather than relying on the illustrative root query.  
**Evidence:** classification table with magic/header, parser, payload, and intended command.  
**Failure injection:** rename extensions; prove classification still uses content.

## Lab 2 — Inspect QEMU’s generated DTB

**Objective:** learn a complete generated tree and version dependence.

**Steps:** run QEMU with <code>-machine virt,dumpdtb=…</code>, decompile with <code>dtc -s</code>, identify memory, CPUs, interrupt controllers, UART, virtio, chosen, and reserved memory. Repeat with different <code>-m</code>, <code>-smp</code>, and one extra device.  
**Evidence:** normalized diffs and command lines.  
**Pass:** explain every changed property.

## Lab 3 — DTS round-trip and corruption

**Objective:** distinguish syntax, structure, and schema.

**Steps:** compile a teaching DTS; round-trip it; query strings/cells; flip one byte in a copy; truncate another copy; run <code>dtc</code>/<code>fdtdump</code>.  
**Safety:** corrupt copies only.  
**Evidence:** tool errors and header changes.  
**Pass:** explain why a structurally valid DT can still be semantically wrong.

## Lab 4 — U-Boot sandbox initialization

**Objective:** inspect driver model without hardware.

**Steps:**

~~~bash
# Context: upstream U-Boot source on Linux host.
make O=out-sandbox sandbox_defconfig
make O=out-sandbox -j"$(nproc)"
./out-sandbox/u-boot -T
~~~

At the prompt use <code>version</code>, <code>bdinfo</code>, <code>dm tree</code>, <code>env print</code>, <code>help bootflow</code>, and test commands.  
**Evidence:** map uclass → driver → device for three devices.  
**Pass:** distinguish bind from probe.

## Lab 5 — Standard boot discovery

**Objective:** understand bootdev, bootmeth, and bootflow.

Create a disposable disk image/filesystem with an extlinux configuration supported by the chosen U-Boot sandbox/QEMU target. Run <code>bootflow scan -lb</code>, inspect candidates, intentionally break a filename, and observe state/error changes.  
**Evidence:** bootflow state table.  
**Pass:** identify the method that parsed the configuration and all loaded objects.

## Lab 6 — QEMU U-Boot <code>booti</code>

**Objective:** prove raw Image + initramfs + DTB handoff.

Build/use a known QEMU RV64 kernel and static BusyBox initramfs. Load each to non-overlapping addresses through a supported virtual block/network path, copy initramfs <code>filesize</code>, inspect the FDT, and run <code>booti</code>.  
**Evidence:** U-Boot load sizes, interval map, “Starting kernel,” early Linux, and <code>/init</code> markers.  
**Failure injection:** omit <code>:size</code>, use <code>-</code>, and supply an invalid DTB copy.  
**Pass:** attribute each failure to the correct layer.

## Lab 7 — Initramfs contract

**Objective:** prove user-mode transition independently of real storage.

Construct an initramfs with static BusyBox and an <code>/init</code> that mounts <code>proc/sys/dev</code>, prints a unique marker, displays <code>/proc/cmdline</code>, and opens a shell. Validate permissions, architecture, and archive contents.  
**Failure injection:** remove execute bit; use a missing interpreter; use a dynamically linked BusyBox without loader.  
**Evidence:** three distinct kernel exec errors/outcomes.

## Lab 8 — Memory-overlap experiment

**Objective:** make placement failure visible.

On QEMU only, map all U-Boot and image ranges. Boot a valid layout, then move a disposable DTB or initramfs to deliberately overlap another disposable object by a controlled amount. Never overwrite U-Boot or firmware intentionally on hardware.  
**Evidence:** before/after hashes and observed parser/kernel failure.  
**Pass:** detect overlap from arithmetic before executing it.

## Lab 9 — Trace <code>booti</code> in source

**Objective:** connect shell command to architecture branch.

In an exact U-Boot tag:

~~~bash
# Context: source checkout.
rg -n 'U_BOOT_CMD.*booti|do_booti|booti_start|boot_jump_linux|BOOTM_STATE_GO' .
git grep -n 'Starting kernel'
~~~

Create a call graph including image setup, decompression, LMB, FDT/initrd preparation, cleanup, and branch. Compare current upstream with vendor 2022.10ky.  
**Evidence:** two source maps with commit IDs.  
**Pass:** name behaviors that cannot be backported by assumption.

## Lab 10 — Build the minimal RISC-V launcher

**Objective:** replace U-Boot proper’s final policy in QEMU while retaining OpenSBI.

Implement Chapter 7, build with a pinned cross-toolchain, inspect the map, package as OpenSBI FW_PAYLOAD, and boot a built-in-initramfs kernel. Add progress bytes <code>A</code> before copy, <code>B</code> after copy, and <code>C</code> immediately before branch.  
**Failure injection:** invalid FDT magic; misaligned kernel destination in a disposable branch.  
**Evidence:** UART and JTAG/QEMU PC if available.  
**Pass:** state the last proven boundary for each case.

## Lab 11 — Signed FIT

**Objective:** demonstrate authenticity and configuration binding.

Generate disposable test keys, create kernel/DTB/initramfs FIT configurations, embed the test public key into a disposable control DT, and enable required signature verification. Boot the valid image; corrupt each component; substitute a wrong DT; try an unsigned config.  
**Evidence:** verification logs and key fingerprint.  
**Cleanup:** delete test private keys if not needed; never reuse them.  
**Pass:** every unauthorized combination fails closed.

## Lab 12 — Overlay and fixup

**Objective:** distinguish base topology, overlay, and runtime fixup.

Create a base teaching DT with a disabled device and an overlay that enables/augments it. Apply with <code>fdtoverlay</code> and U-Boot <code>fdt apply</code>. Add a <code>/chosen</code> property at runtime.  
**Failure injection:** wrong target path and insufficient FDT expansion space.  
**Evidence:** normalized tree at each step.  
**Pass:** identify which changes affect U-Boot DM versus only Linux.

## Lab 13 — Boot-count/A-B model

**Objective:** test fallback as a state machine.

Use U-Boot sandbox or a small host model to represent slots A/B, trial state, boot count, boot limit, success commit, and signed version. Inject process termination/power loss after every state write.  
**Evidence:** transition coverage table and invariant that at least one authorized slot remains.  
**Pass:** no injected point produces an unrecoverable ambiguous state.

## Lab 14 — RV2 offline evidence inventory

**Objective:** create a non-mutating revision-3 dossier.

Acquire full hashes, first-30-MiB image, partition dump, <code>/boot</code> listing with sizes, scripts, environment, DTB decompilations, kernel config, U-Boot/OpenSBI versions, and UART settings.  
**Do not:** write the device, save environment, or substitute an artifact.  
**Evidence:** manifest and dependency graph.  
**Pass:** another engineer can identify every byte used by the boot attempt.

## Lab 15 — Boot timeline

**Objective:** measure stage latency without mixing clocks.

Collect timestamps from ROM/SPL/U-Boot logs where available, U-Boot timer commands, Linux <code>printk</code> timestamps, and user-space monotonic time. Mark clock resets and calibrations; do not subtract timestamps from unrelated time domains without synchronization.  
**Evidence:** stage intervals with uncertainty.  
**Pass:** distinguish cold boot, warm reset, and kexec.

## Lab 16 — Benchmark contamination audit

**Objective:** show how boot configuration changes performance results.

Run a small syscall/interrupt/IPC benchmark under two controlled configurations—for example default versus verbose debug/initcall logging, or cold versus warm cache—while recording kernel, DTB, command line, CPU frequency/governor, affinity, interrupts, temperature, and repetitions.  
**Evidence:** raw samples, median/tails, confidence/bootstrap method, and confounder table.  
**Pass:** explain which observed difference is attributable and which remains ambiguous.

## Lab 17 — Build U-Boot for QEMU RISC-V and trace autoboot

**Objective:** cover a real RISC-V U-Boot build and command loop.

~~~bash
# Context: pinned upstream U-Boot source.
make O=out-qemu ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- \
  qemu-riscv64_smode_defconfig
make O=out-qemu ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- \
  -j"$(nproc)"
~~~

Package/run it with the OpenSBI method documented by that exact U-Boot board configuration. Interrupt autoboot, capture <code>version</code>, <code>bdinfo</code>, <code>printenv bootcmd bootdelay</code>, <code>dm tree</code>, and <code>bootflow scan -lb</code>.  
**Expected pattern:** OpenSBI banner followed by a U-Boot banner/prompt; exact text is version-dependent.  
**Failure variants:** wrong OpenSBI mode, wrong payload artifact, missing virtual block device.  
**Cleanup:** remove only the named <code>out-qemu</code> build directory after archiving map/config/hashes.  
**Pass:** identify who loaded U-Boot and its entry privilege/FDT.

## Lab 18 — Control FDT versus working FDT

**Objective:** prove the two-tree distinction and edit only a copy.

At a compatible U-Boot prompt select the control tree with <code>fdt addr -c</code>, inspect it with <code>fdt print /</code>, then select the loaded working tree again with <code>fdt addr ${fdt_addr_r}</code>. Compare <code>fdtcontroladdr</code> with <code>fdt_addr_r</code>. Move the working blob, resize it, change <code>/chosen/bootargs</code> and <code>stdout-path</code>, then show <code>dm tree</code> is unchanged.  
**Failure variants:** insufficient expansion space; invalid alias; accidental control-tree selection.  
**Pass:** Linux receives the edit while U-Boot DM topology stays unchanged.  
**Cleanup:** reset/reload; do not <code>saveenv</code>.

## Lab 19 — Schema-check and prune QEMU DT

**Objective:** run validation and find a dependency boundary.

Use QEMU’s generated DT as observation, then work from the matching Linux QEMU virt DTS/bindings in a kernel tree. Run <code>make ARCH=riscv dtbs_check W=1</code>. Disable one leaf virtio device, then a provider in separate copies; boot each with a known kernel/initramfs.  
**Expected:** leaf removal removes that device; provider removal causes a predictable earlier failure/warning.  
**Do not:** pass a hand-edited incomplete teaching DTS as if it were QEMU’s canonical tree.  
**Pass:** correlate schema output, normalized diff, and last boot marker.

## Lab 20 — Native, EFI, and GRUB/EFI comparison

**Objective:** observe three policy paths converging on kernel entry.

On QEMU or another disposable virtual platform:

1. Boot the same kernel/initramfs through U-Boot native <code>booti</code>.
2. Boot its EFI-stub/UKI form through U-Boot <code>bootefi</code> or UEFI.
3. Boot through GRUB as an EFI application.

Record firmware/loader versions, image form, command line, initramfs hash, DT/ACPI source, memory-map exit, and earliest kernel marker.  
**Failure variants:** invalid PE signature in a test trust setup; changed command line; missing initrd.  
**Pass:** explain the different predecessor contracts without claiming identical hardware state or boot time.  
**Cleanup:** delete disposable NVRAM/disk images and test keys only after archiving evidence.

---

# 15. Boot choices and performance measurement

## 15.1 Boot firmware can contaminate a benchmark

Even after Linux takes control, earlier choices can affect:

- DRAM frequency/training/interleaving;
- cache and branch-predictor warm state;
- CPU frequency and voltage;
- enabled harts and topology;
- interrupt routing;
- reserved memory and contiguous allocation;
- IOMMU/DMA state;
- PMU counter availability;
- timer frequency;
- firmware call latency;
- watchdog and management interrupts;
- console logging and framebuffer;
- entropy/KASLR layout;
- thermal state.

Therefore a benchmark report must identify the boot tuple, not merely the kernel version.

## 15.2 RISC-V SBI overhead

Linux may invoke SBI for timers, IPIs, remote fences, hart lifecycle, reset, PMU, or debug console depending on platform and kernel. Costs include:

- S-mode to M-mode trap;
- firmware dispatch and validation;
- platform MMIO or interprocessor action;
- return to S-mode;
- cache/TLB synchronization.

The SBI PMU extension can virtualize or expose hardware/firmware counters, but availability and event mappings are platform-specific. Discover the extension and counter descriptors; record OpenSBI version and DT PMU description. See the [SBI PMU extension](https://github.com/riscv-non-isa/riscv-sbi-doc/blob/master/src/ext-pmu.adoc).

For privilege-transition experiments, distinguish:

- user → supervisor syscall;
- supervisor → machine SBI call;
- user → supervisor → machine composite path;
- interrupt/trap entry versus synchronous call;
- local versus remote fence/IPI.

## 15.3 Measurement tuple

Record:

~~~text
board + revision + RAM
power source and thermal conditions
Boot ROM/SPL/OpenSBI/U-Boot identities and hashes
raw firmware-region hash and partition layout
kernel commit/config/Image hash
DTB and initramfs hashes
bootargs
online CPUs, affinity, IRQ affinity
frequency/governor/idle states
timer and PMU source
root filesystem/storage
console/log level
KASLR and mitigations
warm/cold/kexec classification
workload binary/toolchain hash
sample count and statistical method
~~~

## 15.4 Specific workload controls

| Measurement | Boot/firmware confounders | Controls |
|---|---|---|
| Syscall latency | tracing, mitigations, frequency, migration, console | pin CPU, pre-fault, record config, disable unintended tracing |
| Interrupt latency | IRQ routing, coalescing, SBI/IPI, idle depth | pin IRQ/task, record controller/timer, separate idle cases |
| Privilege switch | SBI path, counter access, trap delegation | identify transition path and counter privilege |
| Memory footprint | DT reserved ranges, CMA, firmware carve-outs, initramfs | report physical memory map and allocator state |
| IPC | topology/cache sharing, scheduler, mitigations, page size | pin peers, record cache/topology and transport |
| Boot time | media cache, DRAM training, retries, logging | separate stage clocks and cold/warm/kexec |

## 15.5 Cold boot versus warm reset versus kexec

| Mode | Reinitializes ROM/DRAM? | Firmware path | Suitable conclusion |
|---|---:|---|---|
| Power-cycle cold boot | Usually yes | full | field availability and complete boot time |
| Warm reset | Platform-dependent | partial/full | reset-path behavior only |
| kexec | No | skips most firmware | OS-to-OS transition and update/recovery speed |

Never combine samples across these modes without labeling and modeling the difference.

## 15.6 Firmware timing instrumentation

Preferred hierarchy:

1. a stable always-on counter documented across stages;
2. stage-local monotonic timers with synchronization markers;
3. external GPIO/logic-analyzer pulses;
4. timestamped UART bytes with transmission delay accounted for;
5. display observations only as coarse markers.

UART at 115200 transmits roughly 11.52 kbytes/s with ten bits per 8N1 character. Verbose logging changes boot time; either measure with fixed logs or use compact binary markers/external pins.

## 15.7 Experimental protocol

1. Freeze and hash the boot tuple.
2. Choose one boot mode and thermal precondition.
3. Verify CPU/IRQ affinity and frequency after every boot.
4. Warm or flush caches according to a written protocol.
5. Run randomized/interleaved treatments when comparing configurations.
6. Retain raw samples; report median and tail distributions, not only averages.
7. Repeat across power cycles to capture firmware/DRAM variation.
8. Attribute only effects larger than noise and not entangled with another changed artifact.

---

# Appendix A. Architecture handoff quick reference

This table is a navigation aid, not a replacement for the linked architecture document.

| Architecture/path | Entry arguments | MMU/paging | CPU/interrupt highlights | Hardware description |
|---|---|---|---|---|
| RISC-V native Linux | <code>a0=hartid</code>, <code>a1=DTB PA</code> | <code>satp=0</code> | ordered HSM preferred; firmware memory reserved | DTB |
| AArch64 native Linux | <code>x0=DTB PA</code>, <code>x1..x3=0</code> | off | interrupts masked; DMA quiesced | DTB |
| x86 32-bit protocol | <code>ESI=boot_params</code> | paging off | protected mode and protocol state | boot_params + E820/ACPI |
| x86 64-bit protocol | <code>RSI=boot_params</code> | long mode/page tables as specified | protocol-defined segment/register state | boot_params + E820/ACPI |
| EFI-stub path | EFI image entry parameters initially | UEFI-defined until stub transition | final memory map then ExitBootServices | EFI tables; ACPI or DT |
| PowerPC direct FDT | commonly <code>r3=FDT</code>, other registers per path | platform-specific | multiple historical protocols | FDT or Open Firmware/OPAL path |
| Custom kernel | whatever the versioned ABI defines | explicitly defined | explicitly defined | DT, ACPI, boot-info, or custom |

For RISC-V, also confirm the Linux Image header and 2 MiB RV64 alignment. For x86, check the protocol version fields supported by both loader and kernel.

---

# Appendix B. U-Boot source-navigation map

## B.1 Current upstream

| Question | Start here | Follow into |
|---|---|---|
| Where does the CPU enter? | architecture <code>start.S</code> | low-level init, linker script |
| What runs before relocation? | <code>common/board_f.c</code> | <code>init_sequence_f</code>, board/arch hooks |
| What runs after relocation? | <code>common/board_r.c</code> | <code>init_sequence_r</code>, main loop |
| How is autoboot selected? | <code>common/main.c</code>, autoboot code | <code>bootcmd</code>, bootstd |
| How does standard boot find OSes? | <code>boot/bootstd-uclass.c</code> and bootmeth/bootdev code | extlinux, EFI, script methods |
| What does <code>booti</code> call? | <code>cmd/booti.c</code> | bootm image, LMB, architecture <code>bootm.c</code> |
| Where is RISC-V final jump? | <code>arch/riscv/lib/bootm.c</code> | cleanup, SMP/hart code |
| How are images reserved? | LMB and bootm code | architecture memory reservations |
| Where do DT fixups occur? | <code>boot/image-fdt.c</code>, architecture/board hooks | libfdt, <code>ft_board_setup</code> |
| How does DM bind/probe? | <code>drivers/core/</code> | uclass and selected driver |
| Where is environment loaded? | <code>env/</code> | configured storage backend |
| How is SPL built/loaded? | <code>common/spl/</code> | board boot-device loader, FIT |
| How is an image assembled? | <code>tools/binman/</code>, board DTS/ITS | entries, external firmware |

Use [current <code>cmd/booti.c</code>](https://github.com/u-boot/u-boot/blob/master/cmd/booti.c) and [current RISC-V <code>bootm.c</code>](https://github.com/u-boot/u-boot/blob/master/arch/riscv/lib/bootm.c) as entry points, but pin a commit before citing line-level behavior.

## B.2 RV2 vendor tree

Start with:

~~~text
configs/x1_defconfig
board/ky/x1/
board/ky/x1/x1.env
include/configs/x1.h
arch/riscv/dts/x1_orangepi-rv2.dts
uboot-opensbi.its
cmd/booti.c
arch/riscv/lib/bootm.c
common/spl/
lib/lmb.c
~~~

Then:

~~~bash
# Context: exact vendor U-Boot checkout.
git rev-parse HEAD
git status --short
git show --stat --oneline 89bff4a7e4cadfb5f130edb1ec44c39bff20a427
rg -n 'x1_orangepi|fw_dynamic|u-boot-opensbi|booti|Starting kernel' .
rg -n 'kernel_addr_r|ramdisk_addr_r|fdt_addr_r|fdtfile|bootcmd' \
  configs board include arch
~~~

Create a patch-series inventory relative to the nearest upstream tag. Vendor behavior lives in the delta, not only in files whose names contain <code>x1</code>.

---

# Appendix C. Command reference by execution context

## C.1 Linux host: files and images

~~~bash
file ARTIFACT
sha256sum ARTIFACT
hexdump -C -n 128 ARTIFACT
readelf -h -l ELF
objdump -drS ELF
nm -n ELF
dumpimage -l IMAGE
dtc -I dtb -O dts -s -o out.dts in.dtb
dtc -I dts -O dtb -o out.dtb in.dts
fdtdump in.dtb
fdtget -t s in.dtb / compatible
fdtput -t s copied.dtb /chosen handbook-marker "enabled"
fdtoverlay -i base.dtb -o merged.dtb overlay.dtbo
~~~

## C.2 Linux host: storage, read-only acquisition

~~~bash
lsblk -o NAME,PATH,SIZE,MODEL,SERIAL,TYPE,FSTYPE,MOUNTPOINTS
sudo fdisk -l /dev/sdX
sudo sfdisk --dump /dev/sdX
sudo dd if=/dev/sdX of=device.img bs=4M iflag=fullblock status=progress
sha256sum device.img
~~~

Always verify <code>/dev/sdX</code>. A reversed <code>dd</code> destroys the source.

## C.3 U-Boot shell: inspection

~~~text
version
bdinfo
printenv
env info
help booti
help bootflow
dm tree
mmc list
part list mmc 0
ls mmc 0:1 /
fdt addr ${fdt_addr_r}
fdt header
fdt print /
crc32 ADDRESS LENGTH
~~~

Commands such as <code>saveenv</code>, <code>env erase</code>, <code>mmc write</code>, <code>sf erase</code>, and <code>mw</code> are persistent or memory-destructive and are deliberately absent from the inspection set.

## C.4 QEMU RISC-V

~~~bash
qemu-system-riscv64 -machine virt -m 512M -smp 1 -nographic -bios default
qemu-system-riscv64 -machine virt,dumpdtb=virt.dtb -m 512M -smp 1 -nographic

# Pause at reset and expose a GDB server.
qemu-system-riscv64 -machine virt -m 512M -smp 1 -nographic \
  -bios FIRMWARE.elf -S -gdb tcp::1234
~~~

Record the QEMU version and every machine argument. <code>-bios default</code> contents depend on packaging/version.

## C.5 GDB

~~~bash
riscv64-linux-gnu-gdb loader.elf
(gdb) target remote :1234
(gdb) info registers
(gdb) x/8i $pc
(gdb) p/x $a0
(gdb) p/x $a1
(gdb) x/10wx $a1
(gdb) hbreak *KERNEL_ENTRY
(gdb) continue
~~~

Hardware-specific debug must account for address translation, secure debug locks, and multi-hart selection.

---

# Appendix D. Failure-boundary worksheet

Copy this table for each run:

| Field | Value |
|---|---|
| Experiment ID/date/operator | |
| Board/revision/RAM/serial | |
| Power/reset method | |
| Raw boot-region hash/layout | |
| SPL/OpenSBI/U-Boot versions | |
| Kernel commit/config/Image hash | |
| DTB path/hash | |
| initramfs path/hash | |
| Script/environment hash | |
| Exact command line | |
| Kernel/initrd/DTB addresses and sizes | |
| Firmware/U-Boot reservations | |
| Observation channel/settings | |
| Last marker observed | |
| First marker missing | |
| One changed variable | |
| Result | |
| Strongest justified conclusion | |
| Next discriminating test | |

Hypothesis record:

| Hypothesis | Evidence for | Evidence against | Test | Expected outcomes | Status |
|---|---|---|---|---|---|
| | | | | | |

Use “unknown,” not a guessed value. The worksheet is successful when a different engineer can reproduce both the artifact tuple and the inference.

---

# Appendix E. Glossary

| Term | Meaning in this handbook |
|---|---|
| ABI | Binary contract: registers, memory structures, calling convention, state |
| ACPI | Table/method-based platform description common on PCs/servers |
| AVB | Android Verified Boot |
| BL31/BL33 | TF-A EL3 runtime / non-secure payload designations |
| Boot device | Physical/logical source from which a stage loads data |
| Boot method | Policy/parser used to identify an OS description |
| Bootflow | U-Boot standard-boot representation of a boot candidate |
| Boot ROM | Immutable SoC code beginning at/after reset |
| Boot Services | UEFI services available before successful exit |
| bootargs | Command line passed to Linux, often through DT <code>/chosen</code> |
| bootcount | Persistent or semi-persistent failed-attempt counter |
| bootefi | U-Boot command/path that starts an EFI application |
| booti | U-Boot native flat/compressed Linux Image boot command |
| bootm | U-Boot image/OS boot state-machine family |
| BSS | Zero-initialized program data section |
| Chain of trust | Verification relationship from protected root to later content |
| Control FDT | DT used internally by U-Boot driver model |
| CRC | Error-detection checksum, not authentication |
| CSR | RISC-V control and status register |
| DT | Devicetree, the abstract hardware-description data |
| DTB/FDT | Flattened binary Devicetree blob |
| DTBO | Compiled Devicetree overlay |
| DTS/DTSI | Devicetree source/include |
| dtc | Devicetree compiler |
| DXE | UEFI driver execution environment phase |
| ELF | Executable and Linkable Format |
| EL | Arm exception level |
| EFI stub | Linux code that makes the kernel an EFI application |
| Environment | U-Boot variables and scripts, optionally persistent |
| FIT | Flattened Image Tree, a DT-format image/configuration container |
| FW_DYNAMIC | OpenSBI firmware whose next-stage parameters are supplied at run time |
| FW_JUMP | OpenSBI firmware with a fixed next-stage jump address |
| FW_PAYLOAD | OpenSBI firmware containing its next-stage payload |
| GD | U-Boot global-data structure used especially during early init |
| GPT | GUID Partition Table |
| Hart | RISC-V hardware thread |
| HSM | SBI hart-state-management extension |
| IPL | Initial program load; prominent IBM terminology |
| initramfs | Root filesystem archive unpacked into RAM for early user space |
| ITS/ITB | FIT source / compiled FIT blob |
| KASLR | Kernel address-space-layout randomization |
| kexec | Linux facility for loading and entering another kernel |
| libfdt | Library for reading and editing flattened Devicetrees |
| LMB | U-Boot logical memory block allocator/reservation tracker |
| M-mode | RISC-V machine privilege mode |
| Measured boot | Recording cryptographic measurements into protected state/log |
| MMIO | Memory-mapped device I/O |
| OPAL | OpenPOWER Abstraction Layer runtime interface |
| OpenSBI | Open-source implementation of RISC-V SBI |
| Overlay | Fragment applied to a base Devicetree |
| PE/COFF | Executable format used by UEFI applications |
| Petitboot | Linux-based boot environment/manager used notably on OpenPOWER |
| phandle | Numeric reference from one DT node/property to another |
| PMP | RISC-V physical memory protection |
| PNOR | Platform NOR flash used in OpenPOWER systems |
| PSCI | Arm power-state coordination interface |
| Relocation | Moving U-Boot to its selected DRAM execution address and fixing references |
| Reserved memory | Physical ranges the OS must not allocate normally |
| Rollback | Booting an older, still-signed but disallowed vulnerable version |
| S-mode | RISC-V supervisor privilege mode |
| SBI | RISC-V Supervisor Binary Interface |
| Secure boot | Enforcement that only authorized code/configuration executes |
| SPL | Small U-Boot program-loader build used before U-Boot proper |
| Standard boot | U-Boot bootdev/bootmeth/bootflow framework |
| TPL/VPL/xPL | Additional/generic U-Boot program-loader phases |
| TF-A | Trusted Firmware-A |
| U-Boot proper | Full U-Boot stage, usually relocated in DRAM |
| UEFI | Unified Extensible Firmware Interface |
| uImage | Legacy U-Boot image with header |
| UKI | Unified Kernel Image |
| Verified boot | Signature/policy enforcement before execution |
| Working FDT | Mutable DT prepared for the next OS |
| zIPL | IBM Z Linux boot loader preparation tool |

---

# Appendix F. Primary-source map

## F.1 U-Boot

- [Board initialization flow](https://docs.u-boot.org/en/latest/develop/init.html)
- [Driver-model design](https://docs.u-boot.org/en/latest/develop/driver-model/design.html)
- [Devicetree control](https://docs.u-boot.org/en/latest/develop/devicetree/control.html)
- [Live Devicetree](https://docs.u-boot.org/en/v2026.01/develop/driver-model/livetree.html)
- [OF-platdata](https://docs.u-boot.org/en/v2026.04/develop/driver-model/of-plat.html)
- [fdt command](https://docs.u-boot.org/en/v2026.04/usage/cmd/fdt.html)
- [booti command](https://docs.u-boot.org/en/latest/usage/cmd/booti.html)
- [Standard boot overview](https://docs.u-boot.org/en/latest/develop/bootstd/overview.html)
- [Environment](https://docs.u-boot.org/en/latest/usage/environment.html)
- [Logical memory block design](https://docs.u-boot.org/en/latest/develop/lmb.html)
- [Binman](https://docs.u-boot.org/en/latest/develop/package/binman.html)
- [Verified boot](https://docs.u-boot.org/en/latest/usage/fit/verified-boot.html)
- [FIT signatures](https://docs.u-boot.org/en/v2025.01/usage/fit/signature.html)
- [Measured boot](https://docs.u-boot.org/en/latest/usage/measured_boot.html)
- [Boot count](https://docs.u-boot.org/en/latest/api/bootcount.html)
- [bootefi](https://docs.u-boot.org/en/v2024.04/usage/cmd/bootefi.html)
- [Upstream U-Boot source](https://github.com/u-boot/u-boot)

## F.2 Linux and Devicetree

- [RISC-V boot requirements](https://docs.kernel.org/arch/riscv/boot.html)
- [AArch64 booting](https://docs.kernel.org/arch/arm64/booting.html)
- [x86 boot protocol](https://docs.kernel.org/arch/x86/boot.html)
- [PowerPC booting](https://docs.kernel.org/arch/powerpc/booting.html)
- [Linux EFI stub](https://docs.kernel.org/arch/arm/uefi.html)
- [Devicetree schema authoring/validation](https://docs.kernel.org/devicetree/bindings/writing-schema.html)
- [DTSpec basics](https://devicetree-specification.readthedocs.io/en/latest/chapter2-devicetree-basics.html)
- [DTSpec required nodes](https://devicetree-specification.readthedocs.io/en/latest/chapter3-devicenodes.html)
- [DTSpec flattened format](https://devicetree-specification.readthedocs.io/en/latest/chapter5-flattened-format.html)
- [DTC/libfdt release archive](https://www.kernel.org/pub/software/utils/dtc/)
- [dtschema package/releases](https://pypi.org/project/dtschema/)
- [Linux SoC DT maintenance guidance](https://docs.kernel.org/process/maintainer-soc.html)

## F.3 RISC-V

- [OpenSBI firmware types](https://github.com/riscv-software-src/opensbi/blob/master/docs/firmware/fw.md)
- [RISC-V SBI specification](https://github.com/riscv-non-isa/riscv-sbi-doc/blob/master/riscv-sbi.adoc)
- [SBI PMU extension](https://github.com/riscv-non-isa/riscv-sbi-doc/blob/master/src/ext-pmu.adoc)
- [RISC-V privileged architecture snapshot](https://docs.riscv.org/reference/isa/v20260120/priv/priv-preface.html)
- [RISC-V machine-level ISA](https://docs.riscv.org/reference/isa/v20260120/priv/machine.html)
- [RISC-V ELF psABI](https://github.com/riscv-non-isa/riscv-elf-psabi-doc/blob/master/riscv-cc.adoc)

## F.4 UEFI, GRUB, and alternatives

- [UEFI 2.11](https://uefi.org/specs/UEFI/2.11/)
- [UEFI Boot Services](https://uefi.org/specs/UEFI/2.11/07_Services_Boot_Services.html)
- [GNU GRUB manual](https://www.gnu.org/software/grub/manual/grub/grub.html)
- [Multiboot2 specification](https://www.gnu.org/software/grub/manual/multiboot2/multiboot.html)
- [EDK II](https://github.com/tianocore/edk2)
- [systemd-boot](https://www.freedesktop.org/software/systemd/man/systemd-boot.html)
- [systemd-stub](https://www.freedesktop.org/software/systemd/man/systemd-stub.html)
- [coreboot](https://doc.coreboot.org/)
- [barebox](https://www.barebox.org/doc/latest/user/booting-linux.html)
- [LinuxBoot](https://book.linuxboot.org/coreboot.u-root.systemboot/index.html)
- [Trusted Firmware-A](https://trustedfirmware-a.readthedocs.io/en/stable/design/firmware-design.html)
- [TF-A Trusted Board Boot](https://trustedfirmware-a.readthedocs.io/en/latest/design/trusted-board-boot.html)
- [iPXE](https://ipxe.org/docs)
- [MCUboot](https://docs.mcuboot.com/)
- [Android Verified Boot](https://source.android.com/docs/security/features/verifiedboot/avb)
- [Syslinux project](https://wiki.syslinux.org/wiki/index.php?title=The_Syslinux_Project)

## F.5 IBM public boot ecosystems

- [OpenPOWER Hostboot](https://github.com/open-power/hostboot)
- [OPAL/skiboot and skiroot/Petitboot](https://github.com/open-power/skiboot)
- [IBM Linux boot/IPL](https://www.ibm.com/docs/en/linux-on-systems?topic=shutdown-booting-linux)
- [IBM zIPL disk preparation](https://www.ibm.com/docs/en/linux-on-systems?topic=ipl-disk-preparation)

## F.6 Orange Pi RV2

- [Official Orange Pi RV2 product/support page](https://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/service-and-support/Orange-Pi-RV2.html)
- [Orange Pi RV2 wiki](https://www.orangepi.org/orangepiwiki/index.php/Orange_Pi_RV2)
- [Orange Pi build repository](https://github.com/orangepi-xunlong/orangepi-build/blob/next/README.md)
- [Orange Pi vendor U-Boot repository](https://github.com/orangepi-xunlong/u-boot-orangepi)
- [meta-riscv RV2 U-Boot recipe](https://github.com/riscv/meta-riscv/blob/master/recipes-bsp/u-boot/u-boot-orangepi.bb)
- [External RV2 boot log from another unit](https://dmesgd.nycbug.org/dmesgd?do=view&id=8920) — corroboration only

Source discipline:

- Specifications and upstream manuals define contracts.
- Exact source commits define implementation.
- Vendor repositories/recipes define integration.
- Target captures define the current board.
- Other-board logs and secondary articles generate hypotheses, not target facts.

---

# Appendix G. Request-to-chapter coverage matrix

| Requested topic | Where covered |
|---|---|
| How control reaches U-Boot | Chapters 1–3 |
| What is before U-Boot | Chapter 2 |
| U-Boot’s boot roles | Chapter 3 |
| U-Boot init/source/runtime internals | Chapter 3 and Appendix B |
| Loading and passing control to Linux | Chapter 4 |
| Exact register/state contracts | Chapter 4 and Appendix A |
| How DTB is used/generated | Chapter 5 |
| Control FDT versus OS FDT | Chapter 5 |
| Building a minimal DTB | Chapters 5–6 |
| RV2-specific minimal DT method | Chapter 6 |
| Writing a boot stage/custom kernel loader | Chapter 7 |
| Knowledge required | Chapters 7 and 13 |
| Building/porting U-Boot | Chapter 8 |
| Orange Pi RV2 revision-3 application | Chapter 9 |
| Failure isolation and black screen | Chapters 9–10 |
| Security, verified/measured boot, rollback | Chapter 11 |
| GRUB 2 and alternatives | Chapter 12 |
| IBM-relevant public boot chains | Chapters 2 and 12 |
| Study plan | Chapter 13 |
| Hands-on labs | Chapter 14 |
| Performance/benchmark effects | Chapter 15 |
| Commands/glossary/sources | Appendices C, E, F |

---

# Appendix H. Image/header inspection table

| Artifact | Identifying structure/magic | Preferred inspection | Common trap |
|---|---|---|---|
| ELF | leading bytes <code>7f 45 4c 46</code> | <code>readelf -h -l</code>, <code>objdump</code> | confusing ELF VMA/entry with raw storage offset |
| DTB or FIT | big-endian <code>d0 0d fe ed</code> | <code>fdt header</code>, <code>fdtdump</code>, <code>dumpimage -l</code> | both share FDT container magic |
| legacy uImage/uInitrd | big-endian magic <code>27 05 19 56</code> plus header | <code>dumpimage -l</code>, <code>iminfo</code> | CRC is not authentication |
| raw RISC-V Linux Image | executable instructions plus architecture Image header containing offset/size/flags/version/magic fields | matching kernel <code>arch/riscv/kernel/head.S</code>/<code>asm/image.h</code>, <code>hexdump</code>, U-Boot <code>booti</code> | it is not ELF and its first bytes may also encode EFI <code>MZ</code> when configured |
| PE/COFF EFI | DOS <code>MZ</code> plus PE signature at header-directed offset | <code>objdump -x</code>, <code>pesign</code>/<code>sbverify</code> where available | leading MZ alone is insufficient |
| gzip | <code>1f 8b</code> | <code>gzip -t</code>, <code>file</code> | concatenated streams/size assumptions |
| xz | <code>fd 37 7a 58 5a 00</code> | <code>xz -t</code> | decompression workspace/worst-case output |
| zstd | <code>28 b5 2f fd</code> for standard frame | <code>zstd -t</code> | skippable frames and size bounds |
| cpio newc | ASCII <code>070701</code> or CRC variant <code>070702</code> | <code>cpio -it</code> after decompression | archive may be compressed; modes/interpreters matter |
| Android boot image | versioned Android header/magic | matching AOSP tooling/docs | field layout changes by header version |
| U-Boot script image | legacy/FIT-style header plus script payload | <code>dumpimage -l</code>/<code>-p</code> | binary strings output is not a reliable decode |

Never identify a format from an extension alone. Parse lengths before reading fields and bind the parser version to the producer.

---

# Appendix I. Entry and DT profile checklists

## I.1 RISC-V Linux entry

- [ ] Entry physical address is derived from the loaded Image and 2 MiB-aligned for RV64.
- [ ] Kernel destination and full image/decompression span are inside usable RAM.
- [ ] <code>a0</code> is the boot hart ID.
- [ ] <code>a1</code> is an 8-byte-aligned physical DTB pointer.
- [ ] FDT header/offsets/totalsize validate in its containing buffer.
- [ ] <code>satp=0</code>; required fences/instruction visibility are complete.
- [ ] Entry is in S-mode under resident SBI firmware unless an M-mode kernel was intentionally built.
- [ ] S-mode interrupts are disabled/normalized and delegation matches firmware policy.
- [ ] Non-boot harts are stopped/managed according to HSM or the chosen legacy protocol.
- [ ] OpenSBI and every persistent firmware/loader range are reserved.
- [ ] DMA-capable devices are quiesced or deliberately inherited.
- [ ] DT memory banks, chosen, initrd end-exclusive bounds, and reservations are correct.
- [ ] The image, initramfs, working DTB, U-Boot, firmware, stack, malloc, framebuffer, and trace buffers do not overlap.

## I.2 DT Profile A

- [ ] root compatible/model/cells
- [ ] CPU/hart and CPU interrupt-controller nodes
- [ ] timebase and correct timer/SBI description
- [ ] actual memory banks
- [ ] reserved firmware/secure/framebuffer memory
- [ ] interrupt-controller topology
- [ ] UART and every provider
- [ ] chosen stdout/bootargs/initrd bounds
- [ ] initramfs with working <code>/init</code>

## I.3 DT Profile B

- [ ] all Profile A items
- [ ] exact USB/storage controller and physical port path
- [ ] clocks/resets/power/regulators/pins/PHY/role-switch
- [ ] DMA/IOMMU/coherency
- [ ] block/partition/filesystem kernel support and root command line

## I.4 DT Profile C

- [ ] all Profile A items
- [ ] exact measurement interrupt/timer/PMU path
- [ ] CPU/cache/topology for selected harts
- [ ] stable OPP/frequency/power policy
- [ ] no unintended CMA/framebuffer/background device
- [ ] measurement-specific device/provider path

---

# Appendix J. U-Boot environment and load-address worksheet

| Variable/object | Value | Size/capacity | End-exclusive | Source | Saved or transient? |
|---|---:|---:|---:|---|---|
| <code>kernel_addr_r</code> | | | | | |
| kernel decompression destination | | | | | |
| <code>ramdisk_addr_r</code> | | | | | |
| copied initramfs size variable | | | | | |
| <code>fdt_addr_r</code> | | | | | |
| DTB expansion capacity | | | | | |
| <code>scriptaddr</code> | | | | | |
| <code>pxefile_addr_r</code> | | | | | |
| <code>loadaddr</code> | | | | | |
| <code>fdtcontroladdr</code> | | | | | |
| U-Boot relocation | | | | <code>bdinfo</code> | |
| stack/malloc/video | | | | source/<code>bdinfo</code> | |
| firmware/reserved memory | | | | DT/OpenSBI | |

Script audit:

- [ ] <code>filesize</code> copied immediately after every load that needs it later.
- [ ] Every command’s error stops or takes a deliberate fallback.
- [ ] No variable is recursively expanded in an unintended phase.
- [ ] Saved environment is identified and compared with compiled defaults.
- [ ] <code>fdt_high</code>/<code>initrd_high</code> behavior is understood, not cargo-culted.
- [ ] Device/partition/path case and filesystem are exact.
- [ ] Network-provided configuration and payload trust are explicit.
- [ ] Final command and all arguments are printed in diagnostic builds.

---

# Appendix K. Boot failure decision tree

~~~mermaid
flowchart TD
    A["Any reset/SPL marker?"] -->|No| B["ROM, media header, raw region, power"]
    A -->|Yes| C["U-Boot prompt/pre-jump marker?"]
    C -->|No| D["SPL, DRAM, OpenSBI, U-Boot package"]
    C -->|Yes| E["Kernel entry marker?"]
    E -->|No| F["address, overlap, privilege, satp, FDT, fence"]
    E -->|Yes| G["Earlycon/normal console?"]
    G -->|No| H["UART/DT, MMU, trap, timer, early memory"]
    G -->|Yes| I["/init marker?"]
    I -->|No| J["initramfs, root, driver, exec"]
    I -->|Yes| K["PID 1/service/application policy"]
~~~

At every leaf, choose one test that separates two hypotheses. Do not rewrite multiple stages in response to one missing marker.

---

# Appendix L. Upstream/vendor audit and production readiness

## L.1 Vendor-port delta

- [ ] exact vendor commits, branches, submodules, binary blobs, and toolchain recorded;
- [ ] nearest upstream base identified;
- [ ] patch series classified: SoC, board, policy, security, workaround, debug;
- [ ] upstream equivalents/backports reviewed for semantic—not textual—compatibility;
- [ ] Kconfig/defconfig and generated <code>.config</code> diffed;
- [ ] DTS/bindings diffed against matching Linux/U-Boot trees;
- [ ] bootm/booti/LMB/FIT/EFI/environment changes traced;
- [ ] ROM/binman/ITS offsets/load/entry values audited;
- [ ] known CVEs and unsupported parsers/protocols inventoried;
- [ ] rebuild artifact hashes compared with deployed binaries;
- [ ] a path to remove or maintain every delta exists.

## L.2 Production boot

- [ ] immutable root authenticates the first mutable stage;
- [ ] chain authenticates code, lengths, addresses, DT, initramfs, command line, and configuration selection;
- [ ] rollback counter and key revocation/rotation are tested;
- [ ] measured-boot event log and verifier policy are versioned if used;
- [ ] production shell/debug/network/removable-media policy matches threat model;
- [ ] environment is protected, redundant where needed, and power-fail tested;
- [ ] A/B slot state and successful-boot commit are atomic;
- [ ] watchdog timeouts and reset reasons are observable at every stage;
- [ ] signed recovery remains independently bootable;
- [ ] DMA and secrets are quiesced/scrubbed at ownership changes;
- [ ] reproducible build/SBOM/provenance/signing records are retained;
- [ ] security-update and field-forensics procedures are rehearsed.

---

# Appendix M. Claims requiring RV2 hardware confirmation

These must not be copied from QEMU or another RV2 unit:

- Boot ROM boot order, exact headers, SRAM limits, recovery triggers, and authentication.
- Byte-level sub-layout and redundancy inside sectors 0–61,439.
- Exact target OpenSBI version, SBI extensions, PMP domains, firmware reservation, and next-stage address.
- U-Boot binary hash, relocation, malloc/stack/video ranges, saved-environment backend and offsets.
- Ky X1 MMIO map used by this board revision.
- 4 GiB DRAM bank layout, holes, training, frequency, and retained-state behavior.
- UART pins, voltage, controller instance, base, clock, reset, pinctrl, and working earlycon string.
- Interrupt topology and whether the vendor platform uses legacy PLIC/CLINT-like blocks, ACLINT, or AIA components.
- Every clock/reset/power/regulator/IOMMU/DMA relationship in the vendor DT.
- USB boot/data port controller and PHY route.
- HDMI/simple-framebuffer handoff and the cause of the black transition.
- Exact DTB selected by <code>boot.scr</code> and its full hash.
- Exact initramfs form/size passed by the successful scripted path.
- Whether U-Boot reaches the final branch, Linux reaches entry, earlycon runs, or <code>/init</code> executes.
- Reset cause/watchdog behavior.
- PMU/counter mappings and timing accuracy for the planned performance experiments.

Smallest safe confirmation methods are, in order: preserve/hashes; decode source/script/DTB; continuous UART; nonpersistent U-Boot inspection; earliest kernel marker; JTAG/register capture; one-change hardware experiment.

---

# Appendix N. Chapter summaries and self-checks

## N.1 Chapter 1

**Summary:** Boot is a chain of contracts with distinct initialization, runtime firmware, loader, kernel, and user-space roles.

- Can you name the privilege, input, output, and evidence at every arrow in the RV2 diagram?
- Why are OpenSBI, U-Boot, UEFI Boot Manager, GRUB, and Linux not interchangeable names?

## N.2 Chapter 2

**Summary:** Pre-U-Boot stages solve ROM/SRAM, power/clock/pin, DRAM, authentication, privilege, and media problems.

- Which responsibilities move into your project if you replace the vendor SPL?
- How would you distinguish FW_DYNAMIC from FW_PAYLOAD in source and a binary package?

## N.3 Chapter 3

**Summary:** U-Boot is a configured framework whose relocation, driver model, environment, boot method, image format, and reservations determine behavior.

- Why can a correct default environment be ignored at runtime?
- Trace <code>booti</code> from command registration to the architecture jump in two exact tags.

## N.4 Chapter 4

**Summary:** Linux entry is an architecture ABI; on RV64 it requires aligned physical entry, <code>a0/a1</code>, <code>satp=0</code>, correct hart/reservation state, and a valid DTB.

- What does “Starting kernel” prove and not prove?
- How do RISC-V <code>a0/a1</code>, AArch64 <code>x0</code>, and x86 <code>boot_params</code> differ?

## N.5 Chapter 5

**Summary:** Devicetree describes hardware and boot data; U-Boot’s control tree and mutable OS working tree have different consumers.

- Name all four meanings of “generate a DTB.”
- Why can a blob pass dtc yet still hang Linux?

## N.6 Chapter 6

**Summary:** A minimal RV2 DT starts from an exact known-good tree and removes leaves only after dependency, schema, boot, and rollback evidence.

- What differs among Profiles A, B, and C?
- Which devices may remain required in the U-Boot control DT but not the Linux working DT?

## N.7 Chapter 7

**Summary:** A custom launcher is an ABI, placement, validation, state-normalization, observability, security, and recovery project—not merely an indirect call.

- When would you choose Tracks A, B, or C?
- Which obligations remain outside <code>handoff.S</code> before the final <code>jr</code>?

## N.8 Chapter 8

**Summary:** A sustainable U-Boot port progresses from documented ROM/DRAM boundaries through drivers and boot protocol to reproducible packaging and upstream-quality maintenance.

- Which artifact is actually flashable for the selected board and how do you prove it?
- How is debug UART different from the normal driver-model serial console?

## N.9 Chapter 9

**Summary:** The revision-3 evidence supports a preserved 30 MiB vendor region and later visible progress, while the Linux handoff boundary remains unknown.

- Which exact observation would prove kernel entry?
- Why can the same black HDMI symptom result from pre-jump failure and successful DRM takeover?

## N.10 Chapter 10

**Summary:** Debugging should identify the last observed and first missing marker, then run one discriminating test with a frozen artifact tuple.

- What does a stable CRC prove, and why is it not authentication?
- How do you use relocation information before setting a U-Boot breakpoint?

## N.11 Chapter 11

**Summary:** Production boot requires an end-to-end root of trust, rollback, measured evidence where needed, atomic updates, watchdog discipline, recovery, and a maintained threat model.

- Why must DTB, initramfs, command line, and configuration selection be authenticated with the kernel?
- At which power-loss points can an A/B design lose all bootable slots?

## N.12 Chapter 12

**Summary:** Boot technologies occupy different layers; choose a composition by initialization, firmware ABI, policy, security, recovery, footprint, and maintenance needs.

- When does U-Boot launch GRUB as an EFI application, and who owns each contract?
- Why is kexec timing incomparable to cold boot?

## N.13 Chapter 13

**Summary:** Senior competence combines ISA/ABI, SoC, toolchain/linking, U-Boot/Linux/DT, security, debugging, and experiment design.

- Which competency is your current bottleneck and what artifact will prove it is closed?
- Can another engineer reproduce your build without your shell history?

## N.14 Chapter 14

**Summary:** The labs progress from artifact/DT inspection to handoff, signed boot, failure injection, RV2 evidence capture, and cross-path comparison.

- Which lab establishes a negative test for every trusted transition?
- Which labs are generic/QEMU-only and cannot validate RV2 hardware?

## N.15 Chapter 15

**Summary:** Firmware and boot state can persist as frequency, topology, counter, reservation, cache, interrupt, thermal, and device confounders.

- What tuple must accompany a syscall/interrupt/IPC result?
- How will you separate cold boot, warm reset, and kexec samples?

---

# What the reader can now do — senior-staff competency checklist

A senior technical staff member should be able to:

- [ ] Draw a board’s chain from reset through PID 1 with privilege and ownership at each arrow.
- [ ] Separate Boot ROM, xPL/SPL, privileged runtime, U-Boot proper, kernel, initramfs, and user space.
- [ ] Trace current and vendor U-Boot initialization, relocation, driver binding, standard boot, image preparation, and architecture jump.
- [ ] Explain exactly what RISC-V Linux requires in <code>a0</code>, <code>a1</code>, <code>satp</code>, alignment, hart state, and reserved memory.
- [ ] Compare that contract with AArch64, x86 boot_params/EFI, and PowerPC paths.
- [ ] Build a complete physical-memory interval map and reject overlap/overflow before execution.
- [ ] Distinguish U-Boot’s control FDT from the working OS FDT.
- [ ] Read/write DTS, decode cells/phandles, use libfdt, apply overlays, and run schema validation.
- [ ] Explain why a DTB cannot generally be discovered from arbitrary hardware.
- [ ] Reduce a DT from a known-good baseline without deleting provider dependencies or inventing addresses.
- [ ] Classify and inspect raw Image, uImage, FIT, EFI, script, DTB, and initramfs artifacts.
- [ ] Design a versioned custom-kernel ABI and write a minimal freestanding launcher.
- [ ] Port U-Boot in staged milestones from UART through DRAM/media to OS boot.
- [ ] Design a chain of trust covering configuration, DTB, initramfs, command line, and rollback.
- [ ] Design power-loss-safe A/B updates, boot success, watchdog, and recovery.
- [ ] Choose among U-Boot, UEFI/GRUB, coreboot/LinuxBoot, barebox, Petitboot/kexec, MCUboot, and AVB by layer and lifecycle.
- [ ] Use UART/JTAG/QEMU/persistent breadcrumbs to prove a boundary rather than infer from a screen.
- [ ] Produce a root-cause report that separates observation, external corroboration, hypothesis, and conclusion.
- [ ] Control firmware/boot state when measuring syscall, interrupt, privilege, memory, and IPC performance.
- [ ] Preserve reproducible source, toolchain, config, hash, signature, and test provenance.

## Immediate RV2 execution checklist

1. Capture continuous, timestamped 115200 8N1 UART after verifying the exact pinout and voltage.
2. Acquire complete hashes and a byte-for-byte copy of the corrected first 30 MiB plus partition metadata.
3. Decode the shipping script/environment and record exact load path, address, returned size, and final command.
4. Create the U-Boot physical interval map and validate the selected DTB/initramfs offline and at the prompt.
5. Add one marker per boundary—pre-<code>booti</code>, earliest Linux, and <code>/init</code>—without changing the revision-3 boot region.

## Closing principle

Boot engineering becomes tractable when every transition is treated as a versioned contract and every diagnosis stops at the strongest observed boundary. U-Boot is powerful because it spans hardware, policy, formats, security, and OS protocols; that breadth is also why exact configuration, exact source, exact artifacts, and disciplined evidence matter more than any memorized command.
