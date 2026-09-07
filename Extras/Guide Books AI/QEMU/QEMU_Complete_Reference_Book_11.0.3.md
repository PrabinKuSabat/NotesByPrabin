# QEMU: Architecture, Operation, Command-Line Reference, and Documentation Atlas

## A practical engineering book for system architecture, operating-system, firmware, virtualization, and RISC-V work

**Baseline:** QEMU 11.0.3  
**Book edition:** 1.0 — 4 August 2026  
**Audience:** system architects, OS and firmware engineers, virtualization engineers, researchers, developers, and technical interview preparation  
**Prepared for:** enterprise engineering work, with additional RISC-V, Power, and s390x guidance  

> This is an independent technical book. It is not an IBM publication and is not endorsed by IBM or the QEMU Project.

---

## Version and completeness contract

QEMU changes quickly. This book is deliberately tied to **QEMU 11.0.3**, the latest stable point release available on the edition date. QEMU 11.1.0-rc2 was a release candidate and is not used as the normative baseline. The authoritative release status is the [QEMU download page](https://www.qemu.org/download/), and the reproducible source baseline is the [`v11.0.3` source tag](https://gitlab.com/qemu-project/qemu/-/tree/v11.0.3).

Online links in this book normally point to the QEMU Project's readable [`docs/master`](https://www.qemu.org/docs/master/) HTML. That site is a rolling manual and may describe a newer development build. When exact 11.0.3 behavior matters, consult the tagged source above, the locally installed man page, and the binary's own help output.

“Complete” has a precise meaning here:

1. Every one of the **115 top-level system-emulator options** defined by QEMU 11.0.3 is listed in Chapter 8.
2. Major suboption families—machine, CPU, accelerator, topology, memory, block, network, display, character devices, boot, debug, migration, security, and objects—are explained in enough depth to construct and review real command lines.
3. User-mode emulation and all six stable standalone tools are covered.
4. Every major official manual area is summarized. The final atlas provides an exhaustive linked index of the stable documentation tree.
5. Runtime-generated properties are not falsely presented as one universal static list. Machines, CPUs, devices, object classes, backends, and properties depend on the target binary, host, accelerator, build configuration, and selected machine. Chapter 7 shows how to obtain the exact list from the binary in use.

This book does **not** duplicate thousands of generated QMP schema entries, every register definition, or every device-specific property. Those surfaces are machine-readable, build-dependent, and versioned. Their concepts, discovery methods, important command families, and authoritative references are included.

---

## How to read this book

- Read Chapters 1–6 in order for a reliable mental model.
- Use Chapters 7–8 while writing or reviewing command lines.
- Read Chapters 9–17 by subsystem.
- Use Chapters 18–20 for architecture-specific and hands-on work.
- Use Chapters 21–22 when extending or operating QEMU at scale.
- Use Chapter 23 and Appendix E as a map of the official manuals.

Shell examples use POSIX syntax. A backslash means the command continues on the next line. Replace example paths, interfaces, ports, CPU models, firmware, and images with values valid for your environment.

---

## Table of contents

1. [What QEMU is](#1-what-qemu-is)
2. [Modes, components, and vocabulary](#2-modes-components-and-vocabulary)
3. [How system emulation works](#3-how-system-emulation-works)
4. [How hardware-accelerated virtualization works](#4-how-hardware-accelerated-virtualization-works)
5. [QEMU versus a simulator](#5-qemu-versus-a-simulator)
6. [What QEMU can be used for](#6-what-qemu-can-be-used-for)
7. [Installation, building, and capability discovery](#7-installation-building-and-capability-discovery)
8. [Complete `qemu-system-*` top-level option reference](#8-complete-qemu-system--top-level-option-reference)
9. [Machines, CPUs, memory, NUMA, and devices](#9-machines-cpus-memory-numa-and-devices)
10. [Storage, block graphs, images, and snapshots](#10-storage-block-graphs-images-and-snapshots)
11. [Networking](#11-networking)
12. [Character devices, consoles, displays, audio, USB, and TPM](#12-character-devices-consoles-displays-audio-usb-and-tpm)
13. [Boot, firmware, direct kernel loading, and bare metal](#13-boot-firmware-direct-kernel-loading-and-bare-metal)
14. [Management: HMP, QMP, QGA, and QOM](#14-management-hmp-qmp-qga-and-qom)
15. [Debugging, tracing, plugins, and deterministic execution](#15-debugging-tracing-plugins-and-deterministic-execution)
16. [Performance engineering and measurement limits](#16-performance-engineering-and-measurement-limits)
17. [Security model and hardening](#17-security-model-and-hardening)
18. [User-mode emulation](#18-user-mode-emulation)
19. [Standalone tools](#19-standalone-tools)
20. [Target architectures, IBM-relevant platforms, and RISC-V](#20-target-architectures-ibm-relevant-platforms-and-risc-v)
21. [Practical recipes and laboratories](#21-practical-recipes-and-laboratories)
22. [Migration, high availability, automation, and QEMU development](#22-migration-high-availability-automation-and-qemu-development)
23. [Official documentation atlas](#23-official-documentation-atlas)

Appendices: [A. Command-line review checklist](#appendix-a-command-line-review-checklist) · [B. Troubleshooting](#appendix-b-troubleshooting) · [C. Glossary](#appendix-c-glossary) · [D. Primary references](#appendix-d-primary-references) · [E. Exhaustive documentation page index](#appendix-e-exhaustive-documentation-page-index)

---

# 1. What QEMU is

QEMU is a generic, open-source **machine emulator and virtualizer**. Its central job is to present software with a model of a computer—or, in user mode, a model of a foreign process execution environment—so that code can run outside its native hardware context. The official definition and the two principal modes are in [About QEMU](https://www.qemu.org/docs/master/about/index.html).

QEMU is not one monolithic “virtual machine.” It is a family of target executables, libraries, device models, management protocols, and utilities:

- `qemu-system-riscv64`, `qemu-system-x86_64`, `qemu-system-s390x`, and similar binaries model complete machines.
- `qemu-riscv64`, `qemu-aarch64`, `qemu-ppc64`, and similar binaries run individual foreign-architecture user programs.
- `qemu-img`, `qemu-nbd`, and `qemu-storage-daemon` manipulate or export storage independently of a VM.
- HMP, QMP, the guest agent, and D-Bus integrations provide human and programmatic control planes.
- TCG translates guest instructions when the guest cannot run natively.
- KVM, HVF, WHPX, Xen, NVMM, MSHV, and Nitro are acceleration or virtualization integrations available on particular hosts and targets.

## 1.1 Emulation and virtualization in the same program

The word **emulation** means QEMU implements the guest-visible behavior in software. It may translate a RISC-V instruction into x86-64 host instructions, model a UART register, produce a virtual interrupt, or implement a disk controller backed by a host file.

The word **virtualization** means the guest CPU is sufficiently compatible with the host that a hypervisor can run guest instructions directly on real processors while maintaining isolation. QEMU still builds the machine, models many devices, supplies firmware, owns much of the I/O topology, and exposes management interfaces. The accelerator handles privileged CPU execution and selected interrupt or memory functions.

Therefore:

- **QEMU + TCG** can run a different guest ISA from the host ISA.
- **QEMU + KVM/HVF/WHPX/etc.** normally requires a compatible host and guest ISA and prioritizes performance.
- QEMU can emulate some components while accelerating others in the same VM.

## 1.2 QEMU, KVM, and libvirt are different layers

| Layer | Primary responsibility | Typical interface |
|---|---|---|
| QEMU | Machine/device model, VM process, block and network backends, firmware integration, emulation, management endpoints | CLI, QMP, HMP |
| KVM | Linux kernel hypervisor interface for executing compatible guest CPUs and accelerating selected platform functions | `/dev/kvm` ioctls used by QEMU |
| libvirt | Higher-level lifecycle, policy, inventory, networking, storage, and orchestration across hypervisors | XML, API, `virsh`, virt-manager |

Saying “a KVM VM” usually means a QEMU process using KVM acceleration, often managed by libvirt. KVM is not a replacement for QEMU's device model, and libvirt is not a CPU emulator.

## 1.3 The fidelity boundary

QEMU's correctness target is primarily **architectural and functional**:

- Guest instructions should update architected state correctly.
- Exceptions, privilege transitions, MMU translation, and interrupts should follow the guest architecture closely enough to run real software.
- Device registers, queues, DMA, and protocol behavior should satisfy guest drivers.
- Firmware and OS software should observe a coherent machine.

QEMU generally does not reproduce a processor's private pipeline stages, physical cache hierarchy, branch predictor, exact DRAM timing, analog behavior, or instruction-by-instruction power consumption. That boundary is essential when using QEMU for research or performance conclusions.

---

# 2. Modes, components, and vocabulary

## 2.1 System emulation

System emulation models an entire computer: CPUs, RAM, interrupt controllers, timers, buses, devices, firmware-facing interfaces, and boot paths. A guest kernel runs with its normal privilege levels and controls guest hardware. The host sees one QEMU process with vCPU and I/O threads.

Typical executable:

```bash
qemu-system-riscv64 -machine virt -m 2G -smp 4 ...
```

This mode is used for operating systems, kernels, firmware, drivers, virtual machines, embedded boards, and bare-metal programs.

## 2.2 Linux user-mode emulation

Linux user mode loads one foreign-architecture Linux executable into a host process. QEMU translates its user instructions and translates guest Linux system calls into host kernel operations. There is no guest kernel, firmware, or complete virtual machine.

```bash
qemu-riscv64 -L /usr/riscv64-linux-gnu ./foreign-program
```

It is valuable for cross-architecture build/test workflows and containers. It cannot test guest kernel behavior, guest device drivers, firmware, or a guest MMU in the same way as system mode.

## 2.3 BSD user-mode emulation

BSD user mode is a separate, less complete facility for selected BSD ABIs. Treat its supported targets and system-call coverage as experimental and verify the precise build. See [QEMU user-mode documentation](https://www.qemu.org/docs/master/user/main.html).

## 2.4 Standalone storage and support tools

QEMU's tools reuse the block layer and protocol implementations without necessarily starting a VM:

- `qemu-img`: offline image creation, inspection, conversion, checking, resizing, snapshots, bitmaps, and mapping.
- `qemu-nbd`: NBD server/client and Linux `/dev/nbdX` attachment.
- `qemu-storage-daemon`: long-running QMP-controlled block graph and export service.
- `qemu-pr-helper`: SCSI persistent-reservation helper.
- `qemu-trace-stap`: SystemTap trace utility.
- `qemu-vmsr-helper`: virtual RAPL MSR helper.

## 2.5 Core vocabulary

**Host** is the system running QEMU. **Guest** is software running inside QEMU. **Target** identifies a guest ISA/build target. **Machine** or **board** is a concrete platform model containing buses and devices. **CPU model** selects the guest-visible processor contract. **Accelerator** selects how vCPU execution is implemented. **Frontend** is a guest-visible device. **Backend** connects that device to host resources. **QOM** is QEMU's object model. **QMP** is its JSON management protocol. **HMP** is its human monitor. **QGA** is the in-guest agent. **TCG** is the dynamic translator. **softmmu** refers to system emulation's software implementation of guest address translation.

## 2.6 Frontend/backend composition

A recurring QEMU pattern is separation of guest-facing hardware from host-facing data handling:

```text
guest driver -> emulated/paravirtual frontend -> QEMU backend -> host resource
```

Examples:

| Guest frontend | Link property | Host backend |
|---|---|---|
| `virtio-net-pci` | `netdev=net0` | `-netdev user,id=net0` or TAP/vhost-user |
| `virtio-blk-pci` | `drive=disk0` | `-blockdev ...,node-name=disk0` |
| UART created by machine | chardev relation | `-chardev socket,id=con0,...` |
| `tpm-tis` | `tpmdev=tpm0` | `-tpmdev emulator,id=tpm0,...` |
| `virtio-rng-pci` | `rng=rng0` | `-object rng-random,id=rng0,...` |

IDs create explicit graph edges. A well-engineered command line names every important object and avoids reliance on ambiguous defaults.

---

# 3. How system emulation works

This chapter explains the execution path from process startup to guest instruction execution and device I/O. The stable implementation contains target-specific variations, but the model below applies broadly.

## 3.1 Process startup and machine realization

When `qemu-system-TARGET` starts, it performs roughly these stages:

1. Parse the command line and configuration files.
2. Select the machine type, accelerator, CPU model, RAM topology, and compatibility version.
3. Instantiate QOM objects for the machine, buses, devices, memory backends, and auxiliary services.
4. Realize devices: validate properties, allocate resources, connect buses, register memory-mapped I/O regions, IRQ lines, DMA relationships, and migration state.
5. Load firmware, ROMs, a direct kernel, initrd, DTB, or generic loader payloads.
6. Create vCPU execution threads and I/O contexts.
7. Expose monitors, display/console endpoints, network listeners, and other backends.
8. Reset the virtual machine into its architectural power-on state.
9. Enter the run loop, unless `-S` or managed startup keeps vCPUs paused.

“Realize” is important QEMU terminology: an object may exist before the resource-dependent step that turns it into a live device. Failures such as an unavailable bus, conflicting address, missing backend, or invalid property usually appear during realization.

## 3.2 The virtual address-space model

QEMU constructs guest-visible address spaces from **MemoryRegion** objects. Regions may represent RAM, ROM, aliases, containers, or device MMIO callbacks. A guest load/store is resolved through this topology.

For RAM under TCG, QEMU maps guest physical memory to host virtual memory. The guest's MMU translation is implemented in software using a software TLB. The high-level path is:

```text
guest virtual address
  -> guest page-table walk / translated TLB lookup
  -> guest physical address
  -> QEMU MemoryRegion dispatch
  -> RAM access or device MMIO callback
```

User mode does not reproduce a complete guest MMU: it relies much more heavily on the host process address space and host kernel.

## 3.3 TCG dynamic binary translation

TCG is QEMU's just-in-time translation engine. It does not normally interpret every guest instruction one at a time. Instead:

1. The target frontend decodes guest instructions from the current program counter.
2. It emits target-independent TCG intermediate operations for architectural effects.
3. TCG optimizes and lowers those operations to host instructions.
4. Host code is placed in a translation cache as a **translation block** (TB).
5. QEMU executes the generated host code.
6. Frequently adjacent TBs may be directly chained, reducing dispatcher overhead.
7. Translation ends at control-flow, page, exception, instrumentation, or other required boundaries.
8. If code pages or relevant translation state change, affected TBs are invalidated and later rebuilt.

A TB is a translation and caching unit, not necessarily a compiler basic block in the strict academic sense. It is also not a simulated pipeline window. Its size and boundaries are implementation details chosen for correct and efficient execution.

## 3.4 CPU state, exceptions, and interrupts

Each vCPU has a target-specific architectural state: registers, program counter, control/status registers, exception state, and MMU context. Generated code updates this state and exits to QEMU when an event requires central handling—for example:

- an instruction needs helper logic;
- an exception or fault occurs;
- an interrupt becomes pending;
- an MMIO access invokes a device;
- the TB budget expires under instruction counting;
- another thread “kicks” the vCPU;
- debugging, single-step, or watchpoint logic intervenes.

QEMU then runs target-specific exception/interrupt code, updates architectural state, and resumes at the correct guest location.

## 3.5 Multi-threaded TCG

When the guest and host memory models are compatible with QEMU's implementation, multi-threaded TCG (MTTCG) assigns one host thread to each vCPU. This can use several host cores, but it must synchronize shared translated-code structures, device state, and architectural ordering. QEMU falls back to single-threaded round-robin execution when required, including configurations such as instruction-count record/replay.

`-accel tcg,thread=multi` requests MTTCG; `thread=single` is valuable for debugging and determinism. More host threads do not guarantee linear scaling: translation, the big QEMU lock in remaining paths, shared device state, memory bandwidth, and the workload all matter. See [Multi-threaded TCG](https://www.qemu.org/docs/master/devel/multi-thread-tcg.html).

## 3.6 Device models and I/O

A device model contains guest-visible registers and state transitions. It can register MMIO or port-I/O handlers, raise/lower interrupt lines, initiate DMA, schedule timers, and expose migration state. On a guest access:

1. The CPU or bus routes the transaction to a MemoryRegion/device.
2. QEMU calls the registered read/write handler.
3. The device validates the access, changes internal state, and may schedule asynchronous work.
4. A backend performs host I/O, perhaps in the main loop, an IOThread, a thread pool, the host kernel, or an external vhost-user process.
5. Completion updates queues/registers and injects a guest interrupt if appropriate.

Traditional emulated hardware such as an e1000 NIC reproduces a hardware programming interface. Virtio is paravirtual: the guest knows it is using a virtual device and communicates through standardized shared queues, reducing emulation overhead.

## 3.7 Event loop, asynchronous I/O, and IOThreads

QEMU's main loop polls file descriptors, timers, bottom halves, and asynchronous completion sources. Block and network subsystems are nonblocking where possible. An `iothread` object supplies a separate AioContext so selected devices or block nodes can process I/O without contending entirely on the main event loop.

IOThreads improve concurrency only when the frontend/backend and workload support it. Queue mapping, host storage parallelism, cache mode, `aio=threads|native|io_uring`, vhost offload, and guest driver behavior all affect the outcome.

## 3.8 Virtual time and timers

QEMU maintains several clock domains. Device timers generally use virtual time; management and host services may use real or host clocks. With ordinary execution, QEMU tries to make guest time useful while the VM runs or pauses. With `-icount`, virtual time is derived from an instruction count and configured scale.

Instruction-count time is **not hardware cycle time**. The official option explicitly warns that complex out-of-order CPUs and cache hierarchies break any simple instruction-to-time relationship. `icount` is for deterministic control, testing, and record/replay—not microarchitectural performance prediction.

## 3.9 Firmware and boot handoff

At reset, a real machine starts from a defined reset vector or firmware entry. QEMU can follow that model by supplying BIOS/UEFI/OpenSBI/OpenBIOS or a board ROM. Alternatively, direct kernel boot places a kernel and associated metadata into target-specific locations and initializes state according to that architecture's boot protocol.

This difference matters:

- Firmware boot tests more of the production boot chain.
- Direct kernel boot is faster and easier for kernel development.
- Generic loader devices are useful for bare-metal payloads at known addresses.
- A DTB describes platform hardware on architectures that use a flattened device tree.

## 3.10 State serialization and migration

Migratable devices register versioned VMState descriptions. Live migration transfers configuration-compatible device state, vCPU state, and RAM while tracking pages dirtied during the transfer. Machine-type versioning preserves guest-visible compatibility across QEMU releases. A “latest” machine type is convenient for new VMs; an explicit versioned type is safer for fleets that require migration compatibility.

Migration is not simply copying process memory. External storage, network identity, passed-through hardware, device migratability, firmware, CPU features, and destination capabilities must form a compatible contract.

---

# 4. How hardware-accelerated virtualization works

With a hardware accelerator, QEMU retains the machine-management role but delegates compatible guest CPU execution to a hypervisor interface.

## 4.1 KVM execution path

On Linux with KVM, QEMU opens `/dev/kvm`, creates a VM, maps guest memory, creates vCPUs, configures architectural state, and asks the kernel to run each vCPU. Guest instructions execute on real host cores until an event causes a VM exit. Examples include selected privileged operations, MMIO that must reach userspace, shutdown, debug events, or interrupts requiring userspace coordination.

The path is conceptually:

```text
QEMU vCPU thread -> KVM_RUN -> guest executes on hardware
                              -> VM exit
QEMU handles exit/device work -> KVM_RUN again
```

In-kernel interrupt controllers, irqchips, and vhost data paths can reduce exits. VFIO may assign a host device using an IOMMU. These optimizations improve performance but change the trusted computing base and migration constraints.

## 4.2 CPU model as an ABI

Under acceleration, the `-cpu` selection is more than a performance switch. It is a guest-visible ABI containing instruction-set features, topology reporting, and architectural capabilities. `-cpu host` exposes a host-like model and usually maximizes local capabilities, but it can prevent migration to a different CPU. A named, versioned, or fleet-compatible model is preferable for migratable enterprise VMs.

## 4.3 Other accelerators

| Accelerator | Typical host | Role |
|---|---|---|
| KVM | Linux on Arm, MIPS, Power, RISC-V, s390x, x86; exact host/target support varies | Kernel virtualization API |
| HVF | macOS on Arm or x86 | Apple Hypervisor Framework |
| WHPX | Windows on Arm or x86 | Windows Hypervisor Platform |
| Xen | Linux control domain on Arm or x86 | Xen hypervisor integration |
| NVMM | NetBSD x86 | NetBSD virtual machine monitor |
| MSHV | Supported Linux/x86 environments | Microsoft hypervisor interface |
| Nitro | Supported AWS Nitro Enclave use | Native enclave acceleration/machine integration |
| TCG | Portable software execution | Cross-ISA or same-ISA emulation without a hardware hypervisor |

Availability is compile-, host-, target-, and privilege-dependent. Always verify with `-accel help`.

## 4.4 Device acceleration spectrum

There is a continuum rather than a binary “emulated or native” choice:

1. Fully emulated legacy device in QEMU userspace.
2. Virtio frontend with QEMU userspace backend.
3. Virtio frontend with vhost kernel backend.
4. Virtio frontend with an external vhost-user backend.
5. vDPA or related data-path acceleration.
6. VFIO device assignment with IOMMU isolation.

Moving down the list can reduce exits/copies but increases deployment dependencies, isolation review, hardware coupling, and migration complexity.

---

# 5. QEMU versus a simulator

The terms **emulator** and **simulator** overlap in ordinary language. QEMU source and help text sometimes say it “simulates” a peripheral. The useful engineering distinction is not the word itself; it is **what state and timing the tool promises to model**.

## 5.1 Functional emulator versus microarchitectural simulator

| Question | QEMU system emulation | Cycle-/timing-oriented architectural simulator |
|---|---|---|
| Main goal | Run real software with correct guest-visible behavior | Predict or study internal hardware behavior and timing |
| CPU model | ISA and architected system state | ISA plus pipelines, queues, predictors, caches, interconnect, memory timing |
| Typical output | Boot success, register/device behavior, logs, traces, functional tests | Cycles, IPC, miss rates, stalls, bandwidth, queue occupancy, power estimates |
| Speed | Generally high, especially with KVM; TCG optimized for execution | Usually much slower as model detail grows |
| Full OS/software support | Strong | Varies; detailed modes can be expensive for full workloads |
| Cross-ISA | Yes with TCG | Often, depending on simulator models |
| Cycle accurate | No | Sometimes; accuracy depends on calibration and model |
| Hardware PMU prediction | Not a general QEMU promise | Often an explicit objective |
| Best use | Bring-up, compatibility, CI, debugging, virtualization, device/firmware work | Microarchitecture research and quantitative performance prediction |

## 5.2 What QEMU models accurately enough to trust

You can normally use QEMU to reason about:

- whether software follows an implemented ISA and privilege architecture;
- whether a kernel reaches a boot milestone on the modeled board;
- exception, interrupt, MMU, syscall, and device-driver control flow;
- register-level behavior of implemented devices;
- protocol and image-format behavior;
- repeatable functional failures;
- management and migration interfaces, subject to documented compatibility.

You must validate target- and device-specific implementation quality. An unsupported register may be stubbed, an obscure hardware erratum may not exist, and a machine may deliberately model only the subset needed by common guests.

## 5.3 What QEMU does not establish

Ordinary QEMU results do not by themselves establish:

- real pipeline latency or throughput;
- real IPC improvement;
- exact cache hit/miss behavior of a physical CPU;
- branch-predictor performance;
- DRAM controller timing or contention;
- physical privilege-transition latency;
- board power, thermal behavior, or voltage/frequency effects;
- performance equivalence between a generic virtual board and a product SoC.

Host wall-clock time under TCG measures the cost of QEMU translation and modeling on that host. It is not the execution time of the emulated processor. Host wall-clock time under KVM can be meaningful for the virtualized workload on that host, but it still includes virtualization topology, scheduling, and virtual I/O choices.

## 5.4 `icount` is not cycle accuracy

`-icount shift=N` maps guest instructions to a chosen virtual-time increment. It can make event order deterministic and underpins record/replay. It does not know whether a real instruction would issue in parallel, miss in cache, mispredict a branch, wait for a TLB, or overlap memory operations. The official [TCG instruction-counting documentation](https://www.qemu.org/docs/master/devel/tcg-icount.html) explicitly separates instruction counting from cycle-accurate emulation.

## 5.5 Using QEMU in performance research correctly

QEMU is still very useful in a performance-research pipeline:

- Validate binaries, boot flows, and workload correctness before hardware runs.
- Use TCG plugins to collect architecture-neutral instruction, branch, and memory-access streams.
- Generate basic-block vectors for phase selection.
- Use deterministic replay to make functional investigations reproducible.
- Prototype ISA or device behavior before detailed timing models exist.
- Compare algorithmic event counts if the instrumentation itself is the metric.

Then use real hardware counters or a calibrated microarchitectural simulator for claims about cycles, IPC, cache behavior, privilege-switch latency, and power.

## 5.6 Decision guide

| Goal | Recommended primary tool |
|---|---|
| Boot a new Linux kernel | QEMU system emulation |
| Run an s390x or RISC-V user binary on x86 | QEMU user mode |
| Test a device driver against a modeled device | QEMU system emulation |
| Measure exact Orange Pi RV2 cache misses | The physical board's PMU, not generic QEMU `virt` |
| Predict performance of a proposed cache hierarchy | A calibrated timing simulator |
| Execute an x86 VM near natively on Linux | QEMU with KVM |
| Reproduce an interrupt-order bug | QEMU record/replay if the involved devices are supported |
| Count dynamic guest instructions or memory accesses | TCG plugin, with stated counting semantics |
| Validate production live-migration compatibility | QEMU/libvirt test environment matching fleet contracts |

---

# 6. What QEMU can be used for

## 6.1 Operating-system and kernel development

QEMU shortens the edit-build-boot-debug loop. A kernel can be loaded directly with `-kernel`, provided an initramfs with `-initrd`, given arguments with `-append`, and stopped at reset with `-S` for GDB. Serial output can be captured in CI, disk writes can be redirected to temporary overlays, and machine state can be reproduced without repeatedly flashing physical media.

Strong use cases include:

- early boot and architecture bring-up;
- scheduler, memory-management, syscall, interrupt, and driver development;
- kernel configuration validation;
- panic/crash collection;
- multi-CPU and NUMA functional testing;
- automated boot-time assertions;
- testing across CPU feature sets and machine versions.

The limitation is hardware specificity. Booting on `riscv-virt` validates the generic RISC-V kernel path and the drivers for that virtual platform; it does not prove that an Orange Pi RV2 clock, pinctrl, PCIe, cache, or vendor peripheral driver will work.

## 6.2 Firmware, bootloader, and bare-metal development

QEMU can execute BIOS, UEFI/EDK2, OpenSBI, U-Boot, OpenBIOS, board ROMs, trusted firmware, and standalone ELF/raw images where target support exists. Semihosting and the generic loader allow early code to print, read files, or exit tests before full devices are available.

Use it for reset-vector work, privilege handoff, page-table setup, interrupt-controller initialization, firmware tables, DTB construction, boot protocol conformance, and CI for bare-metal test suites. Semihosting grants guest code direct host-facing operations and must be restricted to trusted code.

## 6.3 Cross-architecture application development

User-mode QEMU can run foreign Linux tools during cross-compilation, execute test binaries in CI, support multi-architecture container images through `binfmt_misc`, and enable debugging with a remote GDB endpoint. It is faster to set up than a full guest OS and reuses the host kernel.

It is not an ABI oracle for every corner case. Signal, atomic, memory-model, clone, ioctl, and uncommon syscall behavior can differ or be incomplete. Tests that depend on a guest kernel require system mode or hardware.

## 6.4 Cloud and enterprise virtualization

QEMU is a core VM process in many virtualization stacks. With KVM and management layers it supports virtual CPU contracts, versioned machine types, block graphs, network backends, hotplug, snapshots, live migration, confidential-guest technologies, and device assignment.

Enterprise concerns include:

- stable CPU and machine ABIs;
- migration compatibility and downgrade planning;
- image provenance and backing-chain management;
- storage flush semantics and data integrity;
- QMP lifecycle integration;
- least-privilege execution and process isolation;
- monitoring, crash recovery, and fleet-wide capability probing.

## 6.5 Device-model and driver development

Developers can add a QOM device, register MMIO regions, interrupts, DMA, reset behavior, and migration state, then exercise it with qtest, firmware, or a guest driver. The educational `edu` PCI device is a useful introduction. VFIO-user and multi-process QEMU can place device emulation outside the main VM process.

## 6.6 Storage engineering

The QEMU block layer handles raw and structured images, copy-on-write chains, filters, network protocols, encryption, snapshots, dirty bitmaps, throttling, backups, mirroring, and exports. `qemu-img` serves offline workflows; `qemu-storage-daemon` exposes long-running QMP-controlled storage without vCPUs.

The block layer is powerful enough to cause serious data loss when misused. Never modify an image concurrently with a running VM unless the operation is explicitly designed as a live block job through that VM's control plane.

## 6.7 Network laboratories

Multiple QEMU processes can be connected by user networking, TAP/bridge interfaces, sockets, multicast/datagram backends, vhost-user, vDPA, or specialized backends. This supports protocol testing, routing/firewall labs, virtual appliances, packet capture, fault injection, and isolated topologies.

User-mode networking is convenient and unprivileged but behaves like a userspace NAT and is not equivalent to a layer-2 production network. TAP/bridge is more realistic but requires host configuration and security review.

## 6.8 Security research and testing

Snapshots, instrumentation, controlled devices, and deterministic replay make QEMU useful for fuzzing, exploit reproduction, malware analysis, and guest/firmware testing. However, QEMU is a large attack surface. A VM is not automatically a sufficient sandbox for hostile code. Harden the process, minimize devices and backends, avoid dangerous host sharing, patch promptly, and add OS-level isolation.

## 6.9 Continuous integration and regression testing

QEMU is well suited to scripted tests because it has headless operation, deterministic IDs, machine-readable QMP, serial logs, exit devices/semihosting, snapshots, qtest, and cross-ISA execution. Good CI treats startup success, boot milestone, shutdown reason, timeout, and artifact collection as explicit states rather than parsing an interactive terminal casually.

## 6.10 Education and architecture exploration

QEMU exposes instruction traces, registers, device trees, monitor state, and boot sequences. It is excellent for learning privilege levels, interrupts, MMIO, virtual memory, firmware handoff, device enumeration, and driver behavior. It is not a substitute for a detailed pipeline/cache simulator when the lesson is microarchitectural timing.

## 6.11 IBM-relevant uses

QEMU has important enterprise and IBM-adjacent surfaces:

- `pseries` models a PAPR logical partition environment for Power guests.
- `powernv` models bare-metal-style POWER systems and firmware/device paths.
- `s390-ccw-virtio` is the principal s390x virtual machine type.
- KVM on Power and s390x provides hardware acceleration on compatible hosts.
- XIVE, sPAPR NUMA, s390x channel I/O, protected virtualization, CPU topology, and IBM Flexible Service Interface documentation have dedicated official sections.
- QMP, migration compatibility, storage, and virtio are directly relevant to enterprise virtualization and hybrid-cloud engineering.

These models must be matched to the intended layer. `pseries` is not `powernv`; an s390x TCG guest is not performance-equivalent to a KVM guest on IBM Z hardware; and a virtual FSI interface is not a physical timing model.

# 7. Installation, building, and capability discovery

## 7.1 Choose a version intentionally

Distribution packages are convenient and security-maintained, but their version and enabled features differ. Upstream source builds give precise control. Record at least:

```bash
qemu-system-x86_64 --version
qemu-img --version
```

For a reproducible project, also record the package release or git tag, configure arguments, compiler, host kernel, firmware package versions, accelerator, machine type, CPU model, and image metadata.

## 7.2 Common package installations

The upstream [download page](https://www.qemu.org/download/) gives current platform guidance. Typical examples are:

```bash
# Debian/Ubuntu: system emulators and user-mode binaries
sudo apt update
sudo apt install qemu-system qemu-utils qemu-user qemu-user-static

# Fedora
sudo dnf install @virtualization

# Arch Linux
sudo pacman -S qemu

# macOS/Homebrew
brew install qemu
```

Package names are distribution contracts, not upstream QEMU contracts. Firmware, GUI, networking, block-driver, guest-agent, and architecture-specific binaries may be split into additional packages.

## 7.3 Build QEMU 11.0.3 from source

Obtain and verify the signed tarball or the official git tag. QEMU uses a configure front end, Meson, and Ninja. An out-of-tree build keeps generated files separate:

```bash
tar -xf qemu-11.0.3.tar.xz
mkdir qemu-11.0.3-build
cd qemu-11.0.3-build

../qemu-11.0.3/configure \
  --target-list=riscv64-softmmu,riscv64-linux-user \
  --enable-slirp \
  --enable-plugins

make -j"$(nproc)"
make check
```

Install only when required:

```bash
sudo make install
```

For development, run binaries directly from the build directory. A debug build commonly starts with `--enable-debug`; sanitizers and fuzzing have separate configure switches. Use `../qemu-11.0.3/configure --help` as the authoritative option list for the checked-out version.

Important configure families include:

| Family | Examples | Purpose |
|---|---|---|
| Target selection | `--target-list=...`, `--target-list-exclude=...` | Restrict system/user targets and build time |
| Toolchain | `--cc`, `--cxx`, `--rustc`, `--python`, `--cross-prefix` | Select compilers and build tools |
| Installation | `--prefix`, `--bindir`, `--libdir`, `--datadir`, `--firmwarepath` | Control installed layout |
| Build shape | `--static`, `--enable-modules`, `--without-default-features`, `--without-default-devices` | Change linkage and included functionality |
| Emulation | `--enable-tcg`, `--enable-tcg-interpreter`, `--enable-plugins` | Select translation/instrumentation capabilities |
| Accelerators | `--enable-kvm`, `--enable-hvf`, `--enable-whpx`, `--enable-xen`, etc. | Compile host-specific accelerator integration |
| User interfaces | `--enable-gtk`, `--enable-sdl`, `--enable-curses`, `--enable-vnc`, `--enable-spice` | Display and remote UI support |
| I/O | `--enable-slirp`, `--enable-passt`, `--enable-libiscsi`, `--enable-libssh`, `--enable-rbd`, etc. | Network and storage backends |
| Security | `--enable-gnutls`, `--enable-seccomp`, `--enable-cfi`, `--enable-stack-protector` | Cryptography and hardening |
| Diagnostics | `--enable-debug`, `--enable-asan`, `--enable-tsan`, `--enable-ubsan`, `--enable-gcov`, `--enable-fuzzing` | Development and testing |

QEMU 11.0.3's platform guide specifies Python 3.9 as the minimum and documents Rust 1.83.0 for Rust-enabled components. Actual dependencies follow selected features and the supported-host policy; see [Build environment](https://www.qemu.org/docs/master/devel/build-environment.html) and [Supported build platforms](https://www.qemu.org/docs/master/about/build-platforms.html).

## 7.4 System and user build targets

Upstream `--target-list` names system emulation targets with `-softmmu` and Linux user targets with `-linux-user`. Representative examples:

```text
riscv64-softmmu       -> qemu-system-riscv64
ppc64-softmmu         -> qemu-system-ppc64
s390x-softmmu         -> qemu-system-s390x
x86_64-softmmu        -> qemu-system-x86_64
riscv64-linux-user    -> qemu-riscv64
ppc64le-linux-user    -> qemu-ppc64le
```

The complete 11.0.3 list includes system targets for AArch64, Alpha, Arm, AVR, HPPA, x86, LoongArch, m68k, MicroBlaze, MIPS variants, OpenRISC, PowerPC, RISC-V, RX, s390x, SH-4, SPARC, TriCore, and Xtensa. User-mode support is a different matrix and includes targets such as Hexagon that do not have system emulation.

## 7.5 Runtime help is part of the API

The following commands are the only reliable way to answer “what can **this** QEMU binary do?”:

```bash
QEMU=qemu-system-riscv64

$QEMU --version
$QEMU -help
$QEMU -machine help
$QEMU -cpu help
$QEMU -accel help
$QEMU -device help
$QEMU -object help
$QEMU -chardev help
$QEMU -netdev help
$QEMU -display help
$QEMU -audiodev help
```

Then inspect a selected type:

```bash
qemu-system-riscv64 -machine virt,help
qemu-system-riscv64 -device virtio-net-device,help
qemu-system-x86_64 -device virtio-blk-pci,help
qemu-system-x86_64 -object memory-backend-file,help
```

Image options are similarly discoverable:

```bash
qemu-img --help
qemu-img create -f qcow2 -o help
qemu-img create -f raw -o help
```

## 7.6 Programmatic capability discovery

Start QEMU paused with a QMP socket and query the live schema:

```bash
qemu-system-x86_64 \
  -machine q35 \
  -S -display none \
  -qmp unix:/tmp/qemu-qmp.sock,server=on,wait=off
```

After the server greeting, issue `qmp_capabilities`. Important discovery commands include:

- `query-version`
- `query-machines`
- `query-cpu-definitions`
- `query-commands`
- `query-qmp-schema`
- `qom-list-types`
- `device-list-properties`
- `qom-list-properties`
- `query-hotpluggable-cpus`

Management software should probe capabilities instead of guessing from a version number alone. Vendor builds may backport features or disable dependencies.

## 7.7 Capture a reproducibility manifest

For experiments, archive command output and hashes alongside results:

```bash
qemu-system-riscv64 --version
qemu-system-riscv64 -machine help
qemu-system-riscv64 -cpu help
sha256sum Image rootfs.cpio.gz
qemu-img info --output=json disk.qcow2
uname -a
```

Also save the exact expanded QEMU command, environment variables that affect QEMU, kernel command line, DTB hash, firmware hash, host CPU/kernel, and whether TCG or a specific hardware accelerator was used.

---

# 8. Complete `qemu-system-*` top-level option reference

This chapter lists all top-level options defined by QEMU 11.0.3. “Build-dependent” means the option or a backend may not exist in a particular binary. “Target-dependent” means the syntax is accepted only for relevant guest architectures or machines.

## 8.1 Parsing conventions

- `id=` names an object so another option can reference it.
- A comma separates properties inside one option; repeat the top-level option to create several objects.
- Boolean values are normally `on|off`, not shell booleans.
- Size suffixes are context-dependent but commonly accept `K`, `M`, `G`, and `T` binary multiples.
- Many QAPI-style options accept dotted key/value notation or JSON.
- A literal comma inside selected legacy option values is escaped as `,,`.
- `help` is often a special value: `-machine help`, `-device TYPE,help`, and so on.
- Avoid implicit devices in production definitions. Use stable IDs and explicit frontend/backend links.

## 8.2 Standard options (1–21)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 1 | `-h`, `-help` | Display system-emulator help and exit. | Output reflects the compiled target/build. |
| 2 | `-version` | Display QEMU version and package string. | Capture it with experiment or fleet metadata. |
| 3 | `-machine` | `-machine [type=]NAME[,prop=value...]` selects the board/platform. | Use `-machine help` and `-machine NAME,help`; explicit versioned machines protect migration ABI. Common properties include `accel`, `memory-backend`, `dump-guest-core`, `mem-merge`, `nvdimm`, `hmat`, and target-specific fields. |
| 4 | `-M` | Alias of `-machine`. | Prefer `-machine` in maintained scripts for clarity. |
| 5 | `-cpu` | `-cpu MODEL[,prop=value...]` selects guest CPU features. | `-cpu help`; `host` is accelerator-dependent and may harm migration portability; `max` is commonly useful under TCG. |
| 6 | `-accel` | `-accel NAME[,prop=value...]` selects TCG, KVM, HVF, WHPX, Xen, NVMM, MSHV, Nitro, etc. | Repeat for fallback order. TCG properties include `thread`, `tb-size`, `split-wx`, `one-insn-per-tb`; KVM has properties such as `kernel-irqchip`, dirty-ring, and device path where supported. |
| 7 | `-smp` | `-smp [[cpus=]N][,maxcpus=N][,drawers=...][,books=...][,sockets=...][,dies=...][,clusters=...][,modules=...][,cores=...][,threads=...]`. | Supported topology levels are machine-specific. The supported topology product must equal `maxcpus`; initial `cpus` may be lower for hotplug. |
| 8 | `-numa` | Defines NUMA nodes, CPU assignments, distances, HMAT latency/bandwidth, and memory-side caches. | Main forms are `node`, `cpu`, `dist`, `hmat-lb`, and `hmat-cache`. It assigns resources already created by `-m`, `-smp`, and memory objects; it does not allocate them. Prefer `memdev=` over legacy `mem=`. |
| 9 | `-add-fd` | `-add-fd fd=N,set=N[,opaque=TEXT]` adds a pre-opened descriptor to an fd set. | Useful for privilege separation and `/dev/fdset/SET`; stdin/stdout/stderr are not valid members. |
| 10 | `-set` | `-set group.id.arg=value` changes a property of a named option group item. | Legacy/general indirection; explicit object definitions are usually clearer. |
| 11 | `-global` | `-global DRIVER.PROPERTY=VALUE` sets the default property for all matching devices. | Powerful and broad. Prefer per-device settings unless intentionally changing defaults, including auto-created devices. |
| 12 | `-boot` | Sets firmware boot `order`, one-time order, menu, splash, timeout, and strict behavior. | Boot letters are target-specific. Do not mix firmware boot order with per-device `bootindex` unless firmware supports the intended combination. |
| 13 | `-m` | `-m [size=]SIZE[,slots=N,maxmem=SIZE]` defines initial and hotpluggable RAM limits. | Default is historically small. NUMA/memory-backend configurations should be explicit and aligned. |
| 14 | `-mem-path` | Backs guest RAM with files created in the given path. | Legacy interface; memory backend objects give better control. |
| 15 | `-mem-prealloc` | Preallocates RAM associated with `-mem-path`. | Preallocation changes startup time, residency, and failure timing. Prefer object property `prealloc=on`. |
| 16 | `-k` | Selects keyboard layout such as `en-us`, `fr`, or `de`. | Usually unnecessary when raw keycodes are available. |
| 17 | `-audio` | Creates a default audio backend and optionally a guest sound model in one shortcut. | `-audio none` suppresses audio; use `driver=help` or `model=help`. Explicit `-audiodev` plus `-device` scales better. |
| 18 | `-audiodev` | `-audiodev DRIVER,id=ID[,prop=value...]` creates a host audio backend. | Drivers such as ALSA, PipeWire, PulseAudio, CoreAudio, DirectSound, JACK, SDL, sndio, OSS, and none are build/host-dependent. |
| 19 | `-device` | `-device DRIVER[,prop=value...]` creates a guest-visible device. | Use `-device help` and `-device DRIVER,help`; link it to backends by ID. Device availability depends on target, machine buses, and build. |
| 20 | `-name` | `-name GUEST[,process=NAME][,debug-threads=on\|off]` labels guest/process/threads. | Useful in process listings and logs; not a security identity. |
| 21 | `-uuid` | Sets the guest system UUID. | Use a stable, unique UUID for managed guests; firmware and OS software may persist it. |

## 8.3 Block-device options (22–36)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 22 | `-fda` | Attach a file as floppy drive A. | Legacy convenience option. |
| 23 | `-fdb` | Attach a file as floppy drive B. | Legacy convenience option. |
| 24 | `-hda` | Attach a file as first legacy hard disk. | Implies target defaults and format probing; avoid for robust automation. |
| 25 | `-hdb` | Attach a file as second legacy hard disk. | Same caveat as `-hda`. |
| 26 | `-hdc` | Attach a file as third legacy hard disk. | Target-dependent legacy mapping. |
| 27 | `-hdd` | Attach a file as fourth legacy hard disk. | Target-dependent legacy mapping. |
| 28 | `-cdrom` | Attach a file as a CD-ROM. | Convenience option; explicit block node and read-only device is safer. |
| 29 | `-blockdev` | Defines one block graph node using QAPI key/value or JSON syntax. | Preferred modern block interface. Separate protocol nodes (file/NBD/etc.) from format nodes (raw/qcow2/etc.), give each a `node-name`, and attach a frontend. |
| 30 | `-drive` | Legacy combined drive/backend definition: `file`, `if`, `index`, `media`, `format`, `cache`, `aio`, `discard`, `detect-zeroes`, error policies, throttling, and more. | Convenient but conflates graph layers and devices. Always specify `format=` for untrusted or controlled inputs; prefer `-blockdev` for new automation. |
| 31 | `-mtdblock` | Attach a file as an MTD flash block image. | Machine must provide a compatible MTD interface. |
| 32 | `-sd` | Attach a file as an SD card image. | Board-specific availability and geometry apply. |
| 33 | `-snapshot` | Redirect writes to temporary storage rather than the original images. | Process-lifetime snapshot mode; do not confuse it with named VM snapshots or qcow2 backing overlays. |
| 34 | `-fsdev` | Creates a host filesystem backend for 9p or related frontend use. | Common drivers include `local`, `proxy`, and `synth`; security model and host path exposure require careful review. |
| 35 | `-virtfs` | Convenience syntax that creates a filesystem backend and virtio-9p frontend together. | Useful for development; explicit `-fsdev` + `-device` gives more control. Never expose sensitive host trees to untrusted guests. |
| 36 | `-iscsi` | Sets global iSCSI parameters such as initiator name or header digest. | Most connection details belong in block driver options/URIs; credentials should use secrets. |

## 8.4 USB convenience options (37–38)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 37 | `-usb` | Enables a default USB controller on machines that support the legacy switch. | Prefer an explicit controller with `-device`. |
| 38 | `-usbdevice` | Adds a USB device using legacy convenience names. | Deprecated/legacy style; use `-device usb-...,help` and explicit buses. |

## 8.5 Display and x86-specific options (39–49)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 39 | `-display` | Selects local/remote display backend and its properties. | Common backends include `gtk`, `sdl`, `curses`, `cocoa`, `egl-headless`, `dbus`, `spice-app`, and `none`; use `-display help`. |
| 40 | `-nographic` | Disables graphical output and redirects serial/monitor behavior to the terminal. | It is more than `-display none`; it commonly multiplexes console and HMP on stdio. Use `Ctrl-a h` for key help. |
| 41 | `-spice` | Configures the SPICE remote display server. | Includes addresses, ports, TLS/SASL, ticketing, compression, streaming, agent, and GL properties; feature is build-dependent. |
| 42 | `-vga` | Selects a guest VGA model such as `std`, `cirrus`, `vmware`, `qxl`, `virtio`, or `none`. | Models are target/build-dependent; explicit `-device` is more composable. |
| 43 | `-full-screen` | Starts the graphical frontend in full-screen mode. | Frontend support varies. |
| 44 | `-g` | Sets initial display geometry/depth on targets that support this legacy form. | Target-specific; inspect target manual. |
| 45 | `-vnc` | Starts/configures QEMU's VNC server at a display/socket address. | Security options include password, TLS/X.509, SASL, and authorization. Avoid unauthenticated external listeners. |
| 46 | `-win2k-hack` | Applies a legacy Windows 2000 disk-install workaround. | x86-only historical compatibility; do not use for modern guests. |
| 47 | `-no-fd-bootchk` | Disables legacy floppy-signature boot checking. | x86-only historical behavior. |
| 48 | `-acpitable` | Adds/replaces ACPI tables from files or supplied table metadata. | x86-focused expert interface; malformed tables can prevent boot. Repeatable for multiple tables. |
| 49 | `-smbios` | Supplies SMBIOS entry data or an SMBIOS binary file. | Supports multiple structure types and fields; validate guest-visible identity and avoid accidental duplication. |

## 8.6 Network options (50–52)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 50 | `-netdev` | Creates a host-side network backend with `id=ID`. | Backends include `user`, `tap`, `bridge`, `stream`, `dgram`, `socket` legacy forms, `l2tpv3`, `vde`, `hubport`, `netmap`, `vhost-user`, `vhost-vdpa`, `af-xdp`, and `passt`, depending on build/host. Attach with `-device ...,netdev=ID`. |
| 51 | `-nic` | Convenience option that creates a NIC frontend and backend together. | Good for simple commands; specify `model`, `mac`, and backend properties to avoid defaults. `-nic none` disables default networking. |
| 52 | `-net` | Legacy network frontend/backend syntax. | Retained for compatibility; use `-netdev` + `-device` or `-nic`. |

## 8.7 Character-device option (53)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 53 | `-chardev` | Creates a named byte-stream backend: `-chardev BACKEND,id=ID,...`. | Backends include null, socket, udp, file, pipe, pty, stdio, console, serial, parallel, vc, ring buffer, braille, spice, and D-Bus variants as available. It can back serial ports, monitors, TPM emulators, virtio-serial ports, QGA, and vhost-user. |

## 8.8 TPM option (54)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 54 | `-tpmdev` | Creates a TPM backend: `passthrough` or `emulator`, with `id=ID`. | Requires a guest frontend such as `-device tpm-tis,tpmdev=ID`. Host TPM passthrough is exclusive and can affect physical TPM state; software TPM via chardev is usually safer for VMs. |

## 8.9 Boot image and kernel options (55–61)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 55 | `-bios` | Loads the named BIOS/firmware image. | Interpretation and required format are machine-specific. |
| 56 | `-pflash` | Attaches a parallel-flash image, often for split UEFI code/variable stores. | Image size must match the modeled flash. Modern explicit `-blockdev` attachment may provide better control. |
| 57 | `-kernel` | Directly loads a kernel or architecture-supported executable payload. | Load address, format, entry state, and boot protocol are target-specific. |
| 58 | `-shim` | Supplies `shim.efi` for supported kernel boot flows. | Primarily relevant to supported EFI/Secure Boot-oriented paths; target-dependent. |
| 59 | `-append` | Passes a command line to a directly loaded kernel. | Has no effect on ordinary firmware disk boot unless that machine's path explicitly consumes it. |
| 60 | `-initrd` | Loads an initial RAM disk; multiboot has a multi-module form. | Ensure format and location meet the target boot protocol. |
| 61 | `-dtb` | Supplies a device-tree binary to the direct-boot payload. | The DTB must describe the actual QEMU machine configuration, not unrelated physical hardware. |

## 8.10 Debug and expert options (62–114)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 62 | `-compat` | Controls acceptance/hiding of deprecated or unstable QMP inputs/outputs. | Policies include `accept`, `reject`, `crash`, and `hide` in the applicable direction; useful for management-interface testing. |
| 63 | `-fw_cfg` | Adds a named firmware configuration item from a file or string. | Guest firmware consumes it through `fw_cfg`; use namespaced `opt/...` names and treat contents as guest input. |
| 64 | `-serial` | Redirects or creates serial ports using a chardev shorthand. | Values include `stdio`, `file:`, `pty`, `tcp:`, `unix:`, `mon:stdio`, `none`, and `chardev:ID`. Repeat for multiple ports. |
| 65 | `-parallel` | Redirects legacy parallel ports to a chardev-like backend. | Target-dependent; `none` disables defaults. |
| 66 | `-monitor` | Opens an HMP human monitor on a character endpoint. | For automation use QMP instead. `-monitor none` disables the default monitor. |
| 67 | `-qmp` | Opens a QMP control monitor on a character endpoint. | A convenience form; `-chardev` + `-mon mode=control` offers maximum control. |
| 68 | `-qmp-pretty` | Opens QMP with pretty-printed JSON. | Human readability can help debugging; clients must still implement the QMP protocol. |
| 69 | `-mon` | Connects a named chardev to HMP (`readline`) or QMP (`control`) and optional pretty mode. | Preferred explicit monitor composition. |
| 70 | `-debugcon` | Redirects an architecture-specific debug console, commonly x86 port `0xe9`, to a chardev. | Useful for firmware/very early boot output. |
| 71 | `-pidfile` | Writes the QEMU process ID to a file. | Avoid stale-file assumptions; supervised processes and QMP are stronger lifecycle mechanisms. |
| 72 | `--preconfig` | Stops before machine initialization in a preconfiguration phase for QMP completion. | Management software queries/configures initialization-time properties, then uses `x-exit-preconfig`. Experimental surface. |
| 73 | `-S` | Start with vCPUs stopped. | Combine with `-gdb`/`-s` for reset-state debugging or managed startup. |
| 74 | `-overcommit` | Controls `mem-lock` and `cpu-pm` overcommit assumptions. | Host policy and accelerator support determine effect. |
| 75 | `-gdb` | Opens the built-in GDB remote stub on an endpoint. | Use `tcp::PORT`, Unix sockets where supported, or `none`; secure remote exposure. |
| 76 | `-s` | Shorthand for a GDB server on TCP port 1234. | Usually pair with `-S`; binds according to QEMU defaults, so review exposure. |
| 77 | `-d` | Enables selected QEMU log categories. | `-d help` lists target/build categories such as instruction, CPU, MMU, interrupt, unimplemented, guest errors, plugin, and tracing-related logs. High-volume logs strongly perturb execution. |
| 78 | `-D` | Writes QEMU log output to a named file. | Pair with `-d`; protect paths and disk capacity. |
| 79 | `-dfilter` | Restricts debug output to specified address ranges. | Reduces huge `-d` logs; syntax is a comma-separated range filter. |
| 80 | `-seed` | Sets a deterministic seed for QEMU's deterministic pseudo-random inputs. | Not a cryptographic seed and not sufficient alone for complete reproducibility. |
| 81 | `-L` | Sets the QEMU data/firmware search directory. | Useful for development builds; avoid accidentally mixing firmware versions. |
| 82 | `-enable-kvm` | Legacy shorthand enabling KVM acceleration. | Prefer `-accel kvm` or `-machine accel=kvm`. |
| 83 | `-xen-domid` | Supplies Xen domain ID. | Xen-specific. |
| 84 | `-xen-attach` | Attaches to an existing Xen domain rather than creating one. | Xen-specific expert workflow. |
| 85 | `-xen-domid-restrict` | Restricts Xen operations to the specified domain where supported. | Security-hardening control for Xen integration. |
| 86 | `-no-reboot` | Exit instead of rebooting when the guest requests reset. | Useful in automated tests. |
| 87 | `-no-shutdown` | Keep QEMU running and stopped instead of exiting after guest shutdown. | Useful for postmortem inspection; supervisor must handle the stopped process. |
| 88 | `-action` | Sets actions for guest panic, reboot, shutdown, watchdog, and related events. | Central modern replacement for several one-off action flags; available events/actions are versioned. |
| 89 | `-loadvm` | Loads a named internal VM snapshot at startup. | Snapshot must contain compatible machine/device state and usually resides in a supporting image format. |
| 90 | `-daemonize` | Detaches after successful initialization. | Prefer a service manager in modern deployments; stdio backends conflict with detachment. |
| 91 | `-option-rom` | Loads an option ROM and optional boot index. | Target/bus-specific; ROM provenance is part of the trusted boot surface. |
| 92 | `-rtc` | Selects RTC base (`utc`, `localtime`, or date), clock source, and x86 drift correction. | `base=localtime` is mainly for guests expecting local RTC. `clock=vm` is useful with deterministic instruction counting. |
| 93 | `-icount` | Configures virtual instruction time and optional record/replay: `shift`, `align`, `sleep`, `rr`, `rrfile`, `rrsnapshot`. | Deterministic facility, **not cycle accurate**; incompatible with MTTCG in relevant modes. |
| 94 | `-watchdog-action` | Chooses `reset`, `shutdown`, `poweroff`, `inject-nmi`, `pause`, `debug`, or `none` when a watchdog fires. | A watchdog device must exist. Graceful shutdown may fail precisely when the guest is unhealthy. |
| 95 | `-echr` | Changes the terminal escape character used by multiplexed console/monitor. | Numeric ASCII control value; default with `-nographic` is Ctrl-a (`0x01`). |
| 96 | `-incoming` | Prepares an incoming migration over TCP, RDMA, Unix socket, fd, file, exec, QAPI channel, or deferred setup. | Transport/build support varies. Destination machine/CPU/device/storage contract must be compatible. |
| 97 | `-only-migratable` | Rejects devices/configurations that cannot remain migratable. | Valuable guardrail for migration-capable fleets, but does not prove end-to-end destination compatibility. |
| 98 | `-nodefaults` | Prevents the machine from creating its normal optional default devices. | Essential for minimal, auditable topologies; some intrinsic board devices still exist. |
| 99 | `-prom-env` | Sets OpenBIOS NVRAM variables. | PowerPC and SPARC targets only. |
| 100 | `-semihosting` | Enables target semihosting. | Arm, m68k, Xtensa, MIPS, and RISC-V support as applicable. Trusted code only: host-file access can bypass guest isolation. |
| 101 | `-semihosting-config` | Configures enablement, `native\|gdb\|auto` target, chardev, user-space access, and repeated guest arguments. | `userspace=on` expands risk; use only with fully trusted guest code. |
| 102 | `-sandbox` | Enables/configures Linux seccomp filtering, including obsolete syscalls, privilege elevation, process spawn, and resource-control policy. | Build/host-dependent and not a complete sandbox by itself. Configure after identifying required backends. |
| 103 | `-readconfig` | Loads QEMU's legacy configuration file format. | Useful for long configurations; generated management through QMP/libvirt is often safer than hand-maintained legacy config. |
| 104 | `-no-user-config` | Prevents automatic loading of user-provided QEMU config files. | Improves reproducibility and reduces configuration injection. |
| 105 | `-trace` | Enables trace events/patterns and selects event/output files. | Backend is chosen at build time; use `-trace help`/trace-event documentation and avoid secrets in traces. |
| 106 | `-plugin` | Loads a TCG plugin shared library with repeated plugin-specific arguments. | TCG only; instrumentation changes overhead and may produce very large data. Plugins observe rather than freely mutate machine state. |
| 107 | `-qtest` | Opens QEMU's internal qtest protocol endpoint. | Intended for automated QEMU/device tests, not ordinary VM operation. |
| 108 | `-qtest-log` | Selects qtest protocol logging endpoint. | Internal/testing option paired with qtest. |
| 109 | `-run-with` | Controls process lifecycle: async teardown, chroot, exit-with-parent, and user/UID:GID drop. | POSIX/feature-dependent. Combine with external isolation; open privileged resources before dropping privileges. |
| 110 | `-msg` | Adds timestamps and/or guest name to QEMU error messages. | Helpful for multi-VM logs. |
| 111 | `-dump-vmstate` | Writes the selected machine's migration VMState schema to JSON and exits. | Compare schemas with QEMU's checker to find migration compatibility regressions. |
| 112 | `-enable-sync-profile` | Enables QEMU synchronization profiling. | Developer diagnostic; introduces overhead. |
| 113 | `-perfmap` | Generates `/tmp/perf-PID.map` mapping TCG-generated blocks for Linux `perf`. | Linux+TCG build-dependent; basic mapping, not guest hardware PMU emulation. |
| 114 | `-jitdump` | Generates a JIT dump containing TCG code/symbol/line mappings for Linux `perf`. | Linux+TCG build-dependent; potentially large and sensitive. |

## 8.11 Generic object creation (115)

| # | Option | Core syntax and purpose | Important notes |
|---:|---|---|---|
| 115 | `-object` | `-object TYPENAME,id=ID[,prop=value...]` creates a user-creatable QOM object under `/objects`. | Use `-object help` and `-object TYPE,help`. Major families include memory backends, IOThreads/thread contexts, secrets/keyring, TLS credentials/cipher suites, authorization, RNG, IOMMUFD, cryptodev, net filters/COLO, confidential-guest objects, and target-specific control objects. |

## 8.12 Frequently used object families

| Object family | Representative types | What consumes it |
|---|---|---|
| Memory | `memory-backend-ram`, `memory-backend-file`, `memory-backend-memfd`, `memory-backend-shm` | `-machine memory-backend=`, `-numa memdev=`, NVDIMM, shared-memory devices |
| I/O execution | `iothread`, `thread-context` | Block/virtio devices and host thread placement |
| Entropy | `rng-builtin`, `rng-random`, `rng-egd` | `virtio-rng-*` device |
| Secrets | `secret`, `secret_keyring` where available | Encrypted images, TLS keys, storage credentials |
| TLS | `tls-creds-x509`, `tls-creds-psk`, `tls-creds-anon`, `tls-cipher-suites` | VNC, migration, NBD, chardev/network services, firmware exposure |
| Authorization | `authz-simple`, `authz-list`, `authz-listfile`, PAM variants where built | TLS/SASL network service access control |
| Network filters | `filter-dump`, `filter-buffer`, `filter-mirror`, `filter-redirector`, `filter-rewriter`, `colo-compare` | A named `netdev`; capture, delay, redirect, COLO |
| IOMMU/control | `iommufd` | VFIO/vDPA frontends using `/dev/iommu` |
| Crypto | built-in or vhost-user cryptodev backends | `virtio-crypto-*` |
| Confidential guest | SEV/SEV-SNP, TDX, IGVM, protected-VM-related types as supported | Machine `confidential-guest-support` or encryption properties |

Object types are an extensible QOM surface. The table is a functional map, not a substitute for `-object help` on the intended build.

# 9. Machines, CPUs, memory, NUMA, and devices

## 9.1 The machine is the root contract

A machine type decides which platform exists before optional devices are added. It can define buses, interrupt controllers, timers, UARTs, firmware interfaces, flash devices, default RAM layout, hotplug rules, and compatibility behavior. A CPU that the target supports is not necessarily valid on every machine.

Discover, then inspect:

```bash
qemu-system-x86_64 -machine help
qemu-system-x86_64 -machine q35,help
qemu-system-riscv64 -machine virt,help
qemu-system-ppc64 -machine help
qemu-system-s390x -machine help
```

For long-lived VMs, distinguish:

- **Alias/current type**, such as `q35` or `pseries`: follows the build's current default behavior.
- **Versioned type**, such as an architecture-specific `...-11.0` variant where supplied: preserves older guest-visible behavior for migration/compatibility.
- **Physical board model**, such as a development board: models specific peripherals but rarely every physical characteristic.
- **Generic virtual board**, such as Arm or RISC-V `virt`: designed for virtualized/emulated guests and is not a product SoC.

## 9.2 CPU selection

The CPU model controls architected features seen by the guest.

```bash
qemu-system-x86_64 -cpu help
qemu-system-riscv64 -cpu help
qemu-system-ppc64 -cpu help
```

Common model strategies:

| Strategy | Benefit | Risk/limitation |
|---|---|---|
| Default CPU | Short command; machine chooses | Defaults may change and may omit desired features |
| `max` under TCG | Broadest implemented feature set for functional testing | Not a real product CPU and can expose an artificial combination |
| Named model | Explicit, reviewable guest ABI | May not match host or future requirement |
| `host` under KVM/HVF/etc. | High feature exposure and performance | Couples VM to host capabilities; migration becomes difficult |
| Named baseline plus explicit properties | Fleet-compatible contract | Requires careful capability design and testing |

Feature spelling is target-specific. Do not assume that a CPU model name implies microarchitectural timing, cache sizes, frequency, or PMU behavior. It primarily defines guest-visible architectural capabilities.

## 9.3 vCPU topology

`-smp` separates three ideas:

- `cpus`: processors initially online/present;
- `maxcpus`: maximum including hotpluggable CPUs;
- topology: drawers/books/sockets/dies/clusters/modules/cores/threads, as supported by the machine.

Example x86 topology:

```bash
-smp cpus=8,maxcpus=16,sockets=2,dies=1,cores=4,threads=2
```

The full supported-topology product must match `maxcpus`. Guest schedulers, licensing, NUMA policy, cache-topology reporting, and migration can all depend on this contract. Avoid inventing topology merely to reach a CPU count.

## 9.4 Memory allocation

The simplest form is:

```bash
-m 4G
```

Memory hotplug needs capacity and slots:

```bash
-m size=4G,slots=4,maxmem=12G
```

For explicit placement or special backing, create a memory object:

```bash
-object memory-backend-ram,id=ram0,size=4G,prealloc=on \
-machine memory-backend=ram0 \
-m 4G
```

File/huge-page-backed example:

```bash
-object memory-backend-file,id=ram0,size=4G,mem-path=/dev/hugepages,\
share=on,prealloc=on \
-machine memory-backend=ram0 \
-m 4G
```

Key considerations:

- `prealloc=on` moves allocation cost and failures to startup and can reduce runtime page faults.
- `share=on` is required by some external processes/vhost-user configurations but changes isolation and NUMA interactions.
- Huge pages can reduce TLB pressure on the host; availability, locking, NUMA placement, and migration must be planned.
- `dump=off` can exclude memory from host core dumps, reducing dump size and secret exposure.
- `host-nodes` with `policy=bind|preferred|interleave|default` controls host NUMA placement where supported.
- `readonly` and `rom` have different guest-write semantics; do not infer one from the other.

## 9.5 NUMA example

The following creates two guest NUMA nodes with explicit memory backends and CPU placement:

```bash
qemu-system-x86_64 \
  -machine q35 \
  -m 4G \
  -smp 4,sockets=2,cores=2,threads=1 \
  -object memory-backend-ram,id=mem0,size=2G \
  -object memory-backend-ram,id=mem1,size=2G \
  -numa node,nodeid=0,memdev=mem0 \
  -numa node,nodeid=1,memdev=mem1 \
  -numa cpu,node-id=0,socket-id=0 \
  -numa cpu,node-id=1,socket-id=1 \
  -numa dist,src=0,dst=1,val=20 \
  -numa dist,src=1,dst=0,val=20 \
  ...
```

NUMA distance describes guest-reported relative locality; it does not automatically bind host memory or threads. Align guest NUMA, host NUMA policy, vCPU affinity, IOThreads, and device locality if performance matters. HMAT can describe latency/bandwidth, but descriptive values do not make QEMU simulate those delays.

## 9.6 Device, bus, and backend graph

Devices attach to buses. A PCI device cannot be added to a board with no compatible PCI bus; a `virtio-*-device` uses MMIO transport while `virtio-*-pci` requires PCI. Use the machine and device help rather than swapping names blindly.

Example explicit graph:

```bash
-blockdev driver=file,filename=disk.qcow2,node-name=disk-file \
-blockdev driver=qcow2,file=disk-file,node-name=disk-format \
-device virtio-blk-pci,drive=disk-format,id=vda \
-netdev user,id=net0 \
-device virtio-net-pci,netdev=net0,id=nic0,mac=52:54:00:12:34:56
```

The IDs serve different namespaces:

- `node-name=` identifies a block graph node.
- `id=` identifies a device, backend, or QOM object.
- link properties such as `drive=`, `netdev=`, `chardev=`, `memdev=`, and `tpmdev=` refer to those names.

## 9.7 Virtio

Virtio defines paravirtual devices using shared virtqueues. The guest driver posts descriptors; backend code consumes them and signals completion. Transports include PCI, MMIO, CCW on s390x, and target-specific variants.

Common families include:

- block and SCSI;
- network;
- console/serial;
- balloon and memory-related devices;
- RNG;
- GPU;
- input;
- filesystem (`virtiofs` via external daemon in common deployments, and legacy 9p devices);
- crypto, sound, IOMMU, and others depending on target/build.

Virtio is faster than register-heavy legacy emulation because it is designed for virtualization. It still has a frontend, transport, queue topology, interrupt mechanism, and backend whose details affect performance and security.

## 9.8 Hotplug

Hotplug is a coordinated state transition involving QEMU, firmware interfaces, ACPI or device tree conventions, guest drivers, and management software. Typical sequence:

1. Reserve capacity at boot (`maxcpus`, memory slots, PCIe root ports, etc.).
2. Create required backend objects/nodes.
3. Add the guest-visible device through QMP `device_add` or corresponding block commands.
4. Wait for guest/firmware acknowledgement where defined.
5. For removal, ask the guest to detach, observe the event, then remove backends.

Do not equate `device_del` acceptance with immediate physical removal. Some devices cannot be unplugged, and forced removal can lose data.

## 9.9 Pass-through and external device models

**VFIO** assigns a host device or mediated function using IOMMU isolation. **vfio-user** places a device model behind a userspace protocol. **vhost-user** moves a virtio backend into another process. **Multi-process QEMU** separates supported device emulation into a remote process.

Review:

- IOMMU group isolation and DMA permissions;
- reset behavior and ownership;
- interrupt remapping;
- host driver unbinding;
- migration support;
- peer process authentication and socket permissions;
- failure containment and lifecycle ordering.

Pass-through is not generic hardware emulation and usually sacrifices migration portability.

---

# 10. Storage, block graphs, images, and snapshots

## 10.1 The block graph mental model

QEMU storage is a graph of nodes rather than “one disk equals one file.” A common path is:

```text
guest block device
  -> format node (qcow2/raw/LUKS/...)
  -> optional filters (throttle, copy-before-write, quorum, ...)
  -> protocol node (file, host device, NBD, iSCSI, RBD, SSH, ...)
  -> host or remote storage
```

`-blockdev` exposes this model directly. `-drive` creates several layers implicitly.

## 10.2 Explicit qcow2 attachment

```bash
qemu-system-x86_64 \
  -blockdev driver=file,filename=/var/lib/vm/os.qcow2,node-name=os-file \
  -blockdev driver=qcow2,file=os-file,node-name=os-format \
  -device virtio-blk-pci,drive=os-format,id=os-disk
```

Benefits:

- no unsafe format auto-probing;
- distinct file and format properties;
- stable names for QMP block jobs;
- easier insertion of filters or alternate protocols;
- clearer review and diagnostics.

## 10.3 Raw versus qcow2

| Property | Raw | qcow2 |
|---|---|---|
| Layout | Guest sectors map simply to file/device offsets | Clustered metadata maps guest sectors |
| Portability | Very broad | QEMU ecosystem and compatible tooling |
| Sparse files | Host filesystem dependent | Native sparse allocation |
| Backing files | No format-level chain | Yes |
| Internal snapshots | No | Yes |
| Compression | No format feature | zlib/zstd options where supported |
| Encryption | Use a separate layer such as LUKS | qcow2 supports encryption modes; modern designs favor LUKS semantics |
| Metadata overhead | Low | Higher; cache and fragmentation matter |
| Corruption surface | Simple | Rich metadata requires consistency care |

Neither format is always “faster.” Host filesystem, preallocation, workload, cache mode, queueing, storage hardware, discard, fragmentation, and safety requirements dominate.

## 10.4 Cache modes and write guarantees

Common cache modes include:

- `writeback`: host page cache may be used; guest flush/barrier correctness is essential.
- `none`: bypass host page cache where possible, often using direct I/O.
- `writethrough`: writes are reported after reaching the required host cache/storage level.
- `directsync`: direct I/O with synchronous semantics.
- `unsafe`: acknowledges writes without normal flush guarantees; data loss/corruption is expected after failures.

Cache mode is a correctness contract, not only a benchmark knob. The full path—guest filesystem, virtual controller, QEMU, host filesystem, volume manager, controller cache, drive cache, and remote storage—must honor flushes.

## 10.5 Asynchronous I/O

Depending on the protocol and host, `aio=threads`, `aio=native`, or `aio=io_uring` may be available. Native Linux AIO often needs compatible direct-I/O cache modes. `io_uring` availability depends on build and host kernel. Benchmark with the intended queue depth, cache semantics, filesystem, and failure model.

## 10.6 Discard and zero detection

- `discard=unmap` allows guest trim/discard to reach lower layers.
- `detect-zeroes=on` converts suitable zero writes into optimized zero operations.
- `detect-zeroes=unmap` may turn zeros into discard when discard is enabled.

These can reclaim space but can reveal allocation behavior, change performance, and interact with backing chains or storage guarantees. Validate each layer.

## 10.7 Image locking and format probing

QEMU normally locks images to prevent unsafe concurrent writers. `--force-share` in selected tools weakens locking for read-only inspection and may return inconsistent metadata.

Never allow uncontrolled format probing of an untrusted image when the expected format is known. A filename is not proof of format. Explicitly specify the driver and place untrusted parsing inside a hardened environment.

## 10.8 Three meanings of “snapshot”

1. **Temporary snapshot mode**: `-snapshot` redirects writes to temporary storage for the QEMU process.
2. **Internal image snapshot**: qcow2 and some formats store snapshot metadata/data inside the image.
3. **External snapshot/overlay**: a new image records changes while the previous image becomes a backing layer.
4. **VM snapshot**: captures disk state plus vCPU/device/RAM state, commonly through `savevm`/`loadvm` and supporting storage.

Use precise terminology. An external disk snapshot is not automatically an application-consistent backup, and a VM snapshot is not necessarily portable across machine versions.

## 10.9 Backing chains

An overlay reads an unallocated cluster from its backing file and stores new writes in the overlay:

```text
base.qcow2 <- update.qcow2 <- active.qcow2
```

Create an overlay safely by specifying both backing file and format:

```bash
qemu-img create -f qcow2 \
  -B qcow2 -b /images/base.qcow2 \
  /images/work.qcow2
```

Operational rules:

- Treat every backing path and format as metadata that must be preserved.
- Do not modify a backing file while descendants use it.
- Keep chains shallow enough for manageable recovery and performance.
- Use `qemu-img info --backing-chain --output=json` for inventory.
- Understand `commit`, `rebase`, and `convert` before changing relationships.
- Back up chain metadata as well as bytes.

## 10.10 Live block operations

QMP block jobs can mirror, commit, stream, back up, resize, and manipulate bitmaps while a VM runs. Their safe use requires node names, job IDs, event handling, cancellation/finalization rules, and storage coordination. The authoritative [Live Block Device Operations](https://www.qemu.org/docs/master/interop/live-block-operations.html) guide explains primitives and example chains.

## 10.11 Dirty bitmaps

A dirty bitmap records guest-written regions at a selected granularity and is central to incremental backup. A bitmap can be persistent or transient, enabled or disabled, consistent or inconsistent, and associated with a block node. Correct workflows manage bitmap lifecycle atomically with backup jobs and respond to failures before clearing or advancing the bitmap.

## 10.12 Remote storage

Build-dependent protocols include NBD, iSCSI, RBD, Gluster, NFS, SSH, HTTP(S), FTP(S), host devices, and others. Prefer structured `-blockdev` definitions over credentials embedded in URIs. Use QEMU `secret` and TLS credential objects, verify peer identity, and define reconnect/timeouts according to workload semantics.

## 10.13 Storage safety checklist

- Is the image currently open anywhere else?
- Is the format explicit?
- Are backing-file path and format correct?
- Is the operation offline or a supported live QMP job?
- Do cache/flush settings meet the durability requirement?
- Are discard and zero detection intended?
- Are secrets passed outside process listings and logs?
- Is there a tested backup and rollback path?
- Will the destination QEMU understand the image features?

---

# 11. Networking

## 11.1 Two halves of a virtual NIC

The guest-visible NIC and host network backend are separate:

```bash
-netdev user,id=net0 \
-device virtio-net-pci,netdev=net0,mac=52:54:00:12:34:56
```

The frontend determines what guest driver and queue interface are used. The backend determines connectivity and host data path.

## 11.2 Backend comparison

| Backend | Privilege/setup | Connectivity | Typical use |
|---|---|---|---|
| `user` | Unprivileged | Userspace NAT; outbound easy; inbound via forwarding | Development, CI, quick boot |
| `passt` | External helper; usually unprivileged design | Host network integration through passt socket | Stronger modern unprivileged networking option where available |
| `tap` | Host TAP permissions/config | Layer-2 interface on host | Realistic bridges, appliances, production-like labs |
| `bridge` | Bridge helper/policy | TAP connected to named bridge | Managed layer-2 attachment |
| `stream` | Socket endpoint | Point-to-point stream link | Connect VMs/processes, passt socket |
| `dgram` | Datagram/multicast endpoint | Multi-instance virtual segment | Labs and distributed test setups |
| `socket` | Legacy socket forms | TCP/UDP/multicast | Compatibility; prefer stream/dgram forms |
| `vhost-user` | External backend and Unix socket | Fast userspace datapath | DPDK and external switch/storage stacks |
| `vhost-vdpa` | Kernel/hardware setup | vDPA data path | Hardware/offload integration |
| `af-xdp`, `netmap`, `vde`, `l2tpv3` | Specialized host dependencies | Specialized fast path or tunneling | Advanced networking |

## 11.3 User networking and port forwarding

```bash
qemu-system-x86_64 \
  -netdev user,id=net0,hostfwd=tcp:127.0.0.1:2222-:22 \
  -device virtio-net-pci,netdev=net0,mac=52:54:00:12:34:56 \
  ...
```

The guest can usually make outbound connections. Host TCP port 2222 forwards to guest port 22. Bind explicitly to loopback unless external exposure is intended. User networking is NAT-like and has protocol/ICMP/latency limitations; it is not a transparent Ethernet bridge.

## 11.4 TAP and bridge

```bash
-netdev tap,id=net0,ifname=tap0,script=no,downscript=no \
-device virtio-net-pci,netdev=net0,mac=52:54:00:12:34:56
```

Create and authorize `tap0` outside QEMU using the host's network policy, then attach it to a bridge or routing/firewall setup. Do not make QEMU run arbitrary network setup scripts as root. Namespace, capability, bridge-helper, and service-manager approaches are safer than an unrestricted privileged process.

## 11.5 Connecting QEMU instances directly

Listener:

```bash
-netdev stream,id=net0,server=on,addr.type=inet,addr.host=127.0.0.1,addr.port=1234 \
-device virtio-net-pci,netdev=net0,mac=52:54:00:00:00:01
```

Connector:

```bash
-netdev stream,id=net0,server=off,addr.type=inet,addr.host=127.0.0.1,addr.port=1234 \
-device virtio-net-pci,netdev=net0,mac=52:54:00:00:00:02
```

For several VMs, use a switch/bridge or the supported datagram/multicast forms. Give every NIC a unique locally administered MAC address.

## 11.6 Virtio-net, multiqueue, and vhost

Virtio-net multiqueue needs agreement among frontend queues, backend queues, MSI-X vectors, guest driver configuration, host queues, and CPU placement. A common pattern is target-specific and should be derived from `-device virtio-net-pci,help` and `-netdev BACKEND,help`.

`vhost=on` can move packet processing to a kernel or userspace vhost backend, reducing QEMU exits and copies. It is an acceleration path, not a substitute for guest/host queue tuning.

## 11.7 Packet capture and fault injection

The `filter-dump` object writes pcap data from a named netdev:

```bash
-object filter-dump,id=capture0,netdev=net0,file=guest.pcap
```

Other network filter objects can delay, mirror, redirect, rewrite, or compare traffic for COLO. Instrumentation changes timing and can capture credentials or personal data; secure the output.

## 11.8 Network security

- Bind management and forwarding ports to explicit addresses.
- Authenticate and encrypt remote services.
- Apply host firewall and namespace policy.
- Validate bridge helper allowlists.
- Do not reuse MAC addresses on the same layer-2 network.
- Treat vhost-user peer processes and Unix socket permissions as part of the trust boundary.
- Separate guest data networks from QMP, migration, NBD, and display management networks.

The detailed backend syntax is in [Network emulation](https://www.qemu.org/docs/master/system/devices/net.html) and [Invocation: network options](https://www.qemu.org/docs/master/system/invocation.html#network-options).

---

# 12. Character devices, consoles, displays, audio, USB, and TPM

## 12.1 Character backends

A chardev is a reusable host byte-stream endpoint. It does not create a guest device by itself.

```bash
-chardev socket,id=serial0,path=/tmp/guest-console.sock,server=on,wait=off \
-serial chardev:serial0
```

Representative backends:

| Backend | Use |
|---|---|
| `stdio` | Interactive terminal/CI pipe; only one un-multiplexed owner |
| `file` | Output capture; generally no input |
| `pty` | Dynamically allocated host pseudo-terminal |
| `socket` | Unix/TCP/Telnet/WebSocket-style connection according to properties |
| `udp` | Datagram console |
| `pipe` | Named-pipe connection |
| `ringbuf` | In-memory ring buffer readable through monitor commands |
| `null` | Discard output, no input |
| `vc` | QEMU graphical virtual console |
| `serial`/`parallel` | Physical host ports where supported |
| SPICE/D-Bus variants | Integration with remote UI/control services |

Useful generic properties include logging, timestamping, multiplexing, reconnect intervals, server/client mode, and wait behavior. Exact spelling is backend-specific.

## 12.2 `-nographic` and terminal multiplexing

For kernel/firmware work:

```bash
qemu-system-riscv64 -machine virt -nographic ...
```

Typical multiplexed keys begin with Ctrl-a:

- Ctrl-a c: switch between serial console and HMP monitor;
- Ctrl-a h: help;
- Ctrl-a x: exit QEMU;
- Ctrl-a Ctrl-a: send a literal Ctrl-a to the guest.

Exact keys are documented in [character backend multiplexer keys](https://www.qemu.org/docs/master/system/mux-chardev.html). In noninteractive CI, prefer separate chardevs for serial logs and QMP rather than multiplexing.

## 12.3 Displays

The **guest display device** (`virtio-gpu`, `qxl`, VGA models, ramfb, etc.) is distinct from the **host display backend** (`gtk`, SDL, VNC, SPICE, D-Bus, curses, none).

Examples:

```bash
# No host graphical window; serial and QMP are configured separately
-display none

# GTK with OpenGL when supported
-display gtk,gl=on

# VNC on loopback display :1 (TCP 5901)
-display none -vnc 127.0.0.1:1
```

VNC/SPICE must be secured with authentication, TLS, authorization, and network controls. `-vnc :0` can create an external unauthenticated listener depending on host defaults; never assume loopback.

## 12.4 Audio

Audio also has a frontend/backend split:

```bash
-audiodev pipewire,id=audio0 \
-device ich9-intel-hda \
-device hda-duplex,audiodev=audio0
```

For servers and deterministic tests, disable it explicitly:

```bash
-audio none
```

Audio timing, mixing, buffer sizes, input/output enablement, and host server selection are backend properties. Audio input is an external nondeterministic input and may matter for record/replay or privacy.

## 12.5 USB

Create an appropriate controller, then attach devices to a selected bus/port:

```bash
-device qemu-xhci,id=xhci \
-device usb-kbd,bus=xhci.0 \
-device usb-tablet,bus=xhci.0
```

Host USB pass-through uses host identifiers or file descriptors as supported and expands the attack surface. Device reset, reconnect, permissions, and migration are common limitations. For a stable VM definition, use explicit controller models and ports rather than `-usbdevice`.

## 12.6 TPM

Software TPM example for an x86 PC frontend:

```bash
-chardev socket,id=chrtpm,path=/tmp/swtpm.sock \
-tpmdev emulator,id=tpm0,chardev=chrtpm \
-device tpm-tis,tpmdev=tpm0
```

The external software TPM must be started and its state directory managed separately. Choose the guest interface (`tpm-tis`, `tpm-crb`, target-specific variants) according to firmware/OS support.
For example, the RISC-V `virt` documentation uses `tpm-tis-device` instead of the PCI/ISA-oriented x86 model.

Physical TPM passthrough:

```bash
-tpmdev passthrough,id=tpm0,path=/dev/tpm0 \
-device tpm-tis,tpmdev=tpm0
```

Passthrough grants the guest operations against the host TPM, normally requires exclusive access, cannot reproduce firmware initialization, and may modify physical ownership/activation state. It is unsuitable as a casual convenience option.

---

# 13. Boot, firmware, direct kernel loading, and bare metal

## 13.1 Four boot patterns

QEMU's official invocation guide identifies four broad approaches:

1. Supply firmware and let it locate the boot payload.
2. Supply firmware plus a hint or next-stage payload.
3. Directly load a kernel using QEMU's target boot protocol.
4. Manually load bytes/images at chosen guest addresses.

The right choice depends on what you are testing.

## 13.2 Firmware boot

Firmware boot is closest to deployment:

```bash
qemu-system-x86_64 \
  -machine q35 \
  -drive if=pflash,format=raw,readonly=on,file=OVMF_CODE.fd \
  -drive if=pflash,format=raw,file=OVMF_VARS.fd \
  -blockdev ... \
  -device ...
```

Keep the immutable code image separate from a per-VM writable variable store. Firmware is part of the compatibility and security contract; hash and version it.

## 13.3 Direct Linux boot

Generic form:

```bash
qemu-system-TARGET \
  -machine MACHINE \
  -kernel KERNEL_IMAGE \
  -initrd INITRAMFS \
  -append "console=GUEST_CONSOLE root=..." \
  ...
```

QEMU chooses target-specific load addresses and initial state. Kernel image format differs: x86 `bzImage`, Arm/RISC-V `Image` or supported ELF forms, and other architectures have their own protocols. The machine's target documentation is authoritative.

## 13.4 Device tree

On DT-based machines, QEMU may generate a DTB describing the selected CPUs, RAM, buses, interrupt controllers, and devices. `-dtb FILE` overrides it for direct boot. An override must stay synchronized with the virtual machine. Passing a physical Orange Pi DTB to generic `riscv-virt`, for example, does not create those physical peripherals.

Many boards provide a machine property or monitor command to dump the generated DTB. Verify using the selected machine's help or HMP `dumpdtb` where supported.

## 13.5 Generic loader

The loader device can write data or load images into the guest address space:

```bash
-device loader,file=firmware.elf,cpu-num=0
```

or write a scalar value:

```bash
-device loader,addr=0x10000000,data=0x12345678,data-len=4,data-be=on
```

The user owns the address map, endianness, reset vector, and overlap safety. This is ideal for controlled bare-metal experiments and dangerous when addresses are guessed.

## 13.6 Guest loader

`guest-loader` supports scenarios where a hypervisor or primary kernel is loaded normally and must discover additional guest kernels/initrds through a modified DTB. Its exact use is architecture- and hypervisor-specific.

## 13.7 Semihosting

Semihosting lets trusted target code request host services before a full OS I/O stack exists:

```bash
-semihosting-config enable=on,target=native,userspace=off
```

It is useful for test output, file access, arguments, and termination. It deliberately crosses the guest/host boundary and can read or corrupt host data. Never enable native semihosting for an untrusted payload.

## 13.8 Boot debugging sequence

For a silent boot:

1. Confirm the payload format and target architecture with `file`/ELF tools.
2. Confirm machine, CPU, firmware, load path, and entry protocol.
3. Select the correct guest console in both `-append` and QEMU serial configuration.
4. Add early console arguments supported by the kernel.
5. Start with `-S -gdb tcp::1234` and inspect the reset vector/entry point.
6. Add focused `-d` categories and `-D qemu.log`.
7. Dump/inspect the generated DTB where relevant.
8. Remove unrelated devices with `-nodefaults` only after a known-good boot, because defaults may include the console or boot controller.

The official [Direct Linux Boot](https://www.qemu.org/docs/master/system/linuxboot.html), [Generic Loader](https://www.qemu.org/docs/master/system/generic-loader.html), and per-target manuals define exact behavior.

# 14. Management: HMP, QMP, QGA, and QOM

## 14.1 Control-plane separation

Use separate endpoints for separate roles:

- guest serial console: guest text I/O;
- HMP: interactive human troubleshooting;
- QMP: structured lifecycle and automation;
- QGA channel: commands handled inside the guest OS;
- display protocols: graphical user interaction;
- migration/storage services: data-plane transfer.

Multiplexing is convenient for a developer terminal, but distinct Unix sockets with permissions are clearer and safer in automation.

## 14.2 HMP

The Human Monitor Protocol is a text command interface. Open it explicitly:

```bash
-chardev socket,id=hmp0,path=/run/vm/example.hmp,server=on,wait=off \
-mon chardev=hmp0,mode=readline
```

Important command families include:

- lifecycle: `stop`, `cont`, `system_reset`, `system_powerdown`, `quit`;
- state: `info status`, `info registers`, `info cpus`, `info mtree`, `info qtree`, `info pci`, `info block`, `info network`;
- memory/register inspection: `x`, `xp`, register expressions;
- devices: `device_add`, `device_del`, `drive_add`, removable-media changes;
- snapshots: `savevm`, `loadvm`, `delvm`, `info snapshots`;
- migration: `migrate`, `migrate_cancel`, `info migrate`, parameters/capabilities;
- block jobs: mirror/commit/stream/backup-related commands where exposed;
- debugging and replay: breakpoints, single stepping, replay query/seek;
- tracing: event control and status.

HMP syntax and output are designed for humans and can change. Do not build production parsers around it. The complete generated command reference is in [QEMU Monitor](https://www.qemu.org/docs/master/system/monitor.html).

## 14.3 QMP protocol handshake

QMP is a JSON message protocol. Configure a Unix socket:

```bash
-chardev socket,id=qmp0,path=/run/vm/example.qmp,server=on,wait=off \
-mon chardev=qmp0,mode=control
```

A simplified session is:

```json
<- {"QMP":{"version":{"qemu":{"major":11,"minor":0,"micro":3},"package":""},"capabilities":[]}}
-> {"execute":"qmp_capabilities","id":"cap-1"}
<- {"return":{},"id":"cap-1"}
-> {"execute":"query-status","id":"status-1"}
<- {"return":{"running":true,"singlestep":false,"status":"running"},"id":"status-1"}
```

Actual QMP JSON is normally one message per line; object member order and whitespace are not semantic. A client must:

1. read and validate the server greeting;
2. negotiate `qmp_capabilities` before ordinary commands;
3. give commands unique `id` values and correlate responses;
4. process asynchronous `event` messages at any time;
5. handle structured `error` responses without losing framing;
6. observe command-specific completion events where required;
7. reconnect/reconcile state after transport failure rather than blindly replaying mutations.

## 14.4 QMP command families

| Family | Representative operations |
|---|---|
| Discovery | `query-version`, `query-commands`, `query-qmp-schema`, `query-machines`, `query-cpu-definitions` |
| Lifecycle | `query-status`, `stop`, `cont`, `system_reset`, `system_powerdown`, `quit` |
| Devices/QOM | `device_add`, `device_del`, `qom-list`, `qom-get`, `qom-set`, `qom-list-types`, property discovery |
| Block | block-node creation/removal, media changes, jobs, dirty bitmaps, exports, throttling, snapshots |
| Migration | capabilities, parameters, start, cancel, continue, query, incoming migration |
| Memory/CPU | balloon, memory devices, hotpluggable CPUs, stats, dump guest memory |
| Display/input | VNC/SPICE queries, screenshots, input injection where supported |
| Guest agent proxying | separate QGA protocol/commands, not ordinary QMP execution |
| Debug/test | tracing, replay, qtest/developer surfaces as built |

Do not hard-code a remembered schema. Use [QMP specification](https://www.qemu.org/docs/master/interop/qmp-spec.html), the [generated QMP reference](https://www.qemu.org/docs/master/interop/qemu-qmp-ref.html), and live `query-qmp-schema`.

## 14.5 Managed startup and preconfiguration

`-S` creates a fully configured but paused machine; management can inspect it and then `cont`. `--preconfig` stops earlier so management can finish initialization-time configuration through a restricted QMP phase and exit preconfiguration. These states are different: in preconfig the machine is not fully realized for ordinary execution.

Startup automation should wait for the QMP greeting or a supervised readiness signal, not sleep for a guessed duration.

## 14.6 QOM

The QEMU Object Model supplies types, inheritance, properties, object paths, interfaces, and lifecycle. Devices are QOM objects, but so are CPUs, memory backends, IOThreads, secrets, clocks, buses, and control objects.

Useful inspection:

```text
qom-list-types
qom-list path=/machine
qom-get path=/machine property=...
device-list-properties typename=virtio-net-pci
```

QOM paths are runtime topology, while CLI `id` values are user-facing identifiers. Do not assume every property is writable after realization.

## 14.7 QEMU Guest Agent

QGA is a daemon inside the guest connected through a virtio-serial/ISA channel or platform equivalent. It gives the host a controlled guest-OS cooperation path. Common uses include:

- filesystem freeze/thaw for coordinated snapshots;
- guest shutdown/suspend;
- time query/set;
- network, filesystem, user, and OS information;
- file operations and guest command execution where enabled;
- password and vCPU operations on supported guests.

QGA is highly privileged inside many guests. Authenticate and authorize callers at the management layer, restrict the channel, and treat returned data as untrusted. QGA availability does not imply that commands are supported on every OS. See [QEMU Guest Agent](https://www.qemu.org/docs/master/interop/qemu-ga.html) and the [QGA protocol reference](https://www.qemu.org/docs/master/interop/qemu-ga-ref.html).

## 14.8 D-Bus integration

QEMU documents D-Bus control, VMState, and display interfaces for integration with desktop and management components. D-Bus is not a universal replacement for QMP; choose it when the intended frontend and process architecture use the documented interfaces.

---

# 15. Debugging, tracing, plugins, and deterministic execution

## 15.1 GDB system debugging

Start the VM stopped and expose a local GDB endpoint:

```bash
qemu-system-riscv64 \
  -machine virt \
  -S -gdb tcp:127.0.0.1:1234 \
  -nographic \
  ...
```

Connect a target-capable GDB:

```gdb
file vmlinux
target remote 127.0.0.1:1234
info registers
x/10i $pc
break start_kernel
continue
```

Use the uncompressed ELF with symbols (`vmlinux`) for debugging even when QEMU boots a compressed/raw `Image`. Symbol and runtime virtual addresses may differ before the MMU is enabled or when KASLR relocates the kernel. Disable KASLR for initial debugging or load relocated symbols correctly.

The stub supports target-dependent registers, breakpoints, watchpoints, multiple vCPUs/threads, and physical/virtual memory operations. Hardware accelerators may limit debug features. See [GDB usage](https://www.qemu.org/docs/master/system/gdb.html).

## 15.2 User-mode GDB

```bash
qemu-riscv64 -g 1234 ./program
```

Then connect a cross-GDB to `localhost:1234`. Add `,suspend=n` to supported endpoint syntax when the program should start before the debugger connects.

## 15.3 QEMU debug logging

Discover categories:

```bash
qemu-system-riscv64 -d help
```

Focused example:

```bash
-d guest_errors,unimp,cpu_reset -D qemu.log
```

Instruction/CPU/MMU logs can be enormous and slow execution by orders of magnitude. Use `-dfilter` address ranges, reproduce a short interval, and avoid treating logged execution time as performance data.

## 15.4 Trace events

QEMU subsystems define named tracepoints. Build-selected backends include log, simple, ftrace, DTrace/SystemTap, LTTng UST, syslog, or no-op forms. Typical runtime syntax:

```bash
-trace enable='virtio_*',file=qemu.trace
```

An events file can enable/disable patterns. Tracepoint names are internal developer interfaces and can change. Use the matching source/build's event list. Traces expose detailed host/guest activity and may contain addresses, filenames, or data identifiers.

## 15.5 TCG plugins

Plugins subscribe to translation and execution events and can observe instructions, memory accesses, syscalls in supported modes, vCPU idle/resume, and other defined callbacks. Example:

```bash
qemu-system-riscv64 \
  -plugin ./libinsn.so \
  -d plugin \
  ...
```

Included examples cover instruction/basic-block counts, execution logs, memory behavior, cache modeling experiments, hot blocks/pages, syscall tracing, basic-block vectors, and lockstep diagnostics.

Semantics matter:

- Translation callback does not prove a block executed.
- A translated address can appear multiple times.
- A block can exit before every translated instruction runs.
- Instruction callbacks run before execution; a following exception may prevent completion.
- Memory callbacks occur after successful access; faulting accesses may not be reported as successful memory callbacks.
- Instrumentation overhead changes host timing.

Plugins are excellent for event counts and trace generation, not automatically for physical cycle prediction. See [TCG Plugins](https://www.qemu.org/docs/master/devel/tcg-plugins.html).

## 15.6 Record/replay

Record:

```bash
qemu-system-x86_64 \
  -icount shift=auto,rr=record,rrfile=run.rr \
  -drive file=disk.qcow2,if=virtio \
  ...
```

Replay with the same VM configuration and initial disk state:

```bash
qemu-system-x86_64 \
  -icount shift=auto,rr=replay,rrfile=run.rr \
  -drive file=disk.qcow2,if=virtio \
  ...
```

Record/replay logs nondeterministic inputs and schedules supported events against instruction counts. It requires deterministic device paths, typically single-threaded TCG, and exact initial state. Unsupported/external backends can break determinism. Replay debugging can query instruction count, set replay breakpoints, and seek with snapshots. Read [Record/replay](https://www.qemu.org/docs/master/system/replay.html) before relying on a device combination.

## 15.7 qtest and qgraph

qtest starts QEMU without ordinary guest CPU execution and exposes a protocol for reading/writing device registers, clocks, IRQs, and memory. It supports fast device-model tests. qgraph describes testable machine/device graph paths so tests can instantiate valid combinations. These are QEMU developer facilities, not guest workload APIs.

## 15.8 Crash and state diagnosis

Useful artifacts include:

- serial and firmware logs;
- QEMU stderr with `-msg timestamp=on,guest-name=on`;
- focused `-d` log;
- QMP event stream and `query-status`/device queries;
- guest memory dump through QMP/HMP;
- QEMU host core dump, subject to memory dump policies and secrets;
- DTB, ACPI, SMBIOS, and `info mtree`/`info qtree` output;
- `-dump-vmstate` schema for migration failures;
- `-perfmap` or `-jitdump` for profiling QEMU's generated TCG code.

Always separate a guest crash, a QEMU assertion/segfault, host OOM/kill, backend disconnection, and management timeout; they require different evidence.

---

# 16. Performance engineering and measurement limits

## 16.1 First choose the execution mode

Performance conclusions are meaningless without naming the accelerator:

- TCG measures emulation throughput on a host and supports cross-ISA work.
- KVM/HVF/WHPX/etc. measure a virtualized workload on compatible hardware.
- Single-thread TCG and MTTCG have different scaling and determinism.
- Debug logging, tracing, plugins, sanitizers, and record/replay alter performance.

Capture `query-kvm`/accelerator state or startup logs; do not infer that KVM was used because it was installed.

## 16.2 CPU and topology

- Use a CPU model appropriate to the goal: migration baseline versus maximum local capability.
- Match vCPU count to available host capacity; oversubscription increases scheduling noise.
- Map guest topology intentionally.
- Pin vCPU and IOThreads only after understanding host NUMA and interrupt placement.
- Avoid mixing unrelated host workloads during controlled experiments.
- Report host frequency policy, SMT, turbo, isolation, and thermal state if wall time matters.

## 16.3 Memory

- Align host memory placement with vCPU NUMA nodes.
- Consider preallocation and huge pages for lower fault/TLB overhead.
- Account for KSM/memory merge; deduplication can perturb latency and memory footprint.
- Avoid host swapping.
- Separate guest used memory, QEMU resident set, virtual address space, page cache, shared mappings, and backend processes.

## 16.4 Storage

- Use explicit formats and cache modes.
- Match queues and IOThreads to the guest/device/backend.
- Compare equivalent durability semantics.
- Warm/cold cache is a test dimension, not noise to hide.
- Report image chain depth, preallocation, filesystem, storage medium, discard, and AIO backend.
- Do not benchmark through a temporary snapshot unknowingly.

## 16.5 Networking

- User NAT, TAP, vhost-net, vhost-user, and vDPA are different data paths.
- Report MTU, offloads, queue count, IRQ/vector placement, bridge/switch, and host firewall.
- Separate throughput, latency, packet rate, CPU utilization, drops, and tail latency.

## 16.6 What counters mean

| Counter source | What it measures |
|---|---|
| Host `perf` on QEMU TCG | Host work performed by QEMU/JIT/backend threads |
| TCG plugin instruction count | Defined dynamic guest instruction callbacks/events |
| Guest PMU under TCG | Whatever the target PMU model implements; not a general physical CPU model |
| Guest PMU under KVM | Virtualized/passed-through counter behavior permitted by hypervisor and host |
| Host `perf` under KVM | Host execution including QEMU and/or KVM vCPU threads, depending event/attachment |
| Physical-board PMU | Real hardware events subject to event definitions, privilege filters, multiplexing, and skid |

Never label a TCG host cycle count “RISC-V cycles.” Never derive physical privilege-switch latency from QEMU wall time. Validate counter support and event semantics.

## 16.7 A sound experimental method

1. State a functional or performance hypothesis.
2. Select QEMU only for metrics it can validly produce.
3. Lock version, machine, CPU, accelerator, firmware, kernel, rootfs, command, and host conditions.
4. Use warm-up and repeated trials; report dispersion and outliers.
5. Monitor host steal/scheduling, OOM, throttling, temperature, and background I/O.
6. Separate startup, steady state, shutdown, and instrumentation overhead.
7. Cross-check event counts with a second method where possible.
8. Validate timing claims on the intended hardware or calibrated timing model.

## 16.8 Minimal-kernel research guidance

QEMU can establish that a stripped kernel boots and remains functionally sufficient. It can compare:

- compressed/uncompressed kernel and module size;
- enabled Kconfig counts by category;
- boot milestones and instruction/event counts under a fixed QEMU configuration;
- guest memory usage and slab/process breakdown;
- required devices, drivers, syscalls, and services;
- deterministic regression behavior.

For an Orange Pi RV2 claim about IPC, cache misses, syscall latency, interrupt latency, or privilege transition time, use the board's physical PMU/timers and a controlled native kernel. QEMU `virt` is a development platform, not an Orange Pi RV2 timing model.

---

# 17. Security model and hardening

## 17.1 Threat model

Consider at least four attacker positions:

1. malicious guest code attacking an emulated device/hypervisor;
2. malicious image, firmware, DTB, ROM, or configuration attacking parsers;
3. network client attacking VNC, SPICE, QMP, migration, NBD, or chardev services;
4. compromised QEMU/backend process attacking host resources.

Confidential-computing features add a fifth concern: a guest owner may distrust the host, while the host still must contain guest-originated I/O and denial of service.

## 17.2 Minimize the machine

```bash
-nodefaults
```

Then add only required controllers and devices. Every parser, legacy device, ROM, backend, and network listener is potential attack surface. A minimal topology must still include required boot, interrupt, clock, and console paths.

## 17.3 Drop privilege and isolate the process

Where supported:

```bash
-run-with user=qemu \
-sandbox on,obsolete=deny,elevateprivileges=deny,spawn=deny,resourcecontrol=deny
```

Exact sandbox policies may block required helpers, threads, affinity, or backends. Design startup so privileged resources are opened by a trusted launcher and passed as file descriptors; then drop privileges. Add service-manager sandboxing, namespaces, cgroups, mandatory access control, read-only mounts, private temporary directories, and process separation.

`chroot` changes pathname visibility but is not a complete security boundary.

## 17.4 Secure management and data services

- Prefer Unix sockets with restrictive permissions for local QMP and chardevs.
- Bind TCP listeners to explicit management addresses.
- Use TLS credentials and authorization objects for migration, NBD, VNC, and supported channels.
- Use SASL only with a deliberate identity/authorization design.
- Firewall services and segregate management/data networks.
- Rate-limit connection setup and handle authentication failures safely.
- Treat QMP as administrator-level access: it can inspect memory, add devices, alter storage, and stop the VM.

## 17.5 Secrets

Avoid plaintext secrets in command arguments, environment variables, logs, process listings, and shell history. Use `-object secret` with protected files, keyring support, or a management system that passes secured descriptors. Review core dumps and migration streams: guest RAM and device state may contain credentials.

## 17.6 Dangerous convenience features

| Feature | Risk |
|---|---|
| Native semihosting | Trusted guest can directly access host files/services exposed by ABI |
| 9p/`-virtfs` host sharing | Guest-visible host path, symlink/permission/metadata complexity |
| Host USB/PCI/TPM pass-through | Direct physical resource access, reset/DMA/state consequences |
| Unauthenticated VNC/SPICE/QMP/NBD | Remote control, data disclosure, or image modification |
| `cache=unsafe` | Expected data loss/corruption on failures |
| Image format probing | Expands parser attack surface and can misinterpret content |
| Writable shared backing image | Cross-VM corruption and isolation failure |
| Arbitrary QEMU network scripts | Host command execution, often privileged |

## 17.7 Untrusted disk images

- Process offline images in a restricted, unprivileged environment.
- State the expected format explicitly.
- Use read-only access unless modification is intended.
- Avoid mounting untrusted guest filesystems in the host kernel; filesystem parsers are another attack surface.
- Never run `qemu-img` mutation concurrently with a VM using the image.
- Patch QEMU and relevant libraries.

## 17.8 Firmware and supply chain

Firmware, option ROMs, boot media, device models, plugins, vhost-user processes, and helper binaries are executable/trusted components. Pin versions and hashes, validate signatures/provenance, and include them in vulnerability management.

## 17.9 Confidential guests

SEV/SEV-SNP, TDX, s390 protected virtualization, IGVM, and related facilities protect selected guest state from aspects of the host according to their architecture. They do not eliminate guest-visible device interfaces, denial-of-service risk, firmware trust, attestation policy, or the need to secure management/storage/network paths. Use the target-specific confidential-guest manuals and deployment threat model.

The official [Security](https://www.qemu.org/docs/master/system/security.html), [TLS setup](https://www.qemu.org/docs/master/system/tls.html), [Secret data](https://www.qemu.org/docs/master/system/secrets.html), and [Client authorization](https://www.qemu.org/docs/master/system/authz.html) chapters are required reading for exposed services.

---

# 18. User-mode emulation

## 18.1 Execution model

Linux user mode performs these jobs:

1. Loads a foreign ELF executable and its interpreter/libraries.
2. Maps guest virtual addresses into the QEMU host process.
3. Translates guest user instructions through TCG.
4. Converts guest system-call numbers, arguments, structures, flags, and results to/from host kernel operations.
5. Translates signals and CPU exceptions.
6. Maps guest threads to host threads for supported `clone` behavior.

There is no guest kernel. Guest kernel configuration, device drivers, interrupt controllers, and firmware cannot be tested.

## 18.2 Linux user options

The exact `--help` output can contain additional build/target switches, but the documented core options are:

| Option | Purpose |
|---|---|
| `-h` | Help |
| `-L PATH` | Guest ELF interpreter/library prefix |
| `-s SIZE` | Guest stack size |
| `-cpu MODEL` | Guest CPU model/features; use `-cpu help` |
| `-E VAR=VALUE` | Set a guest environment variable; repeatable |
| `-U VAR` | Remove an environment variable; repeatable |
| `-B OFFSET` | Offset guest addresses on supported targets |
| `-R SIZE` | Reserve guest virtual address space |
| `-d ITEMS` | QEMU debug logging |
| `-g ENDPOINT` | Wait for or expose a GDB connection; `suspend=n` supported in documented forms |
| `-one-insn-per-tb` | Force one guest instruction per TB for analysis; very slow |
| `-plugin ...` / `QEMU_PLUGIN` | TCG plugin instrumentation where built |

`QEMU_STRACE=1` prints translated guest syscalls in an strace-like form. It is incomplete, and host `strace` observes QEMU's host syscalls rather than the guest ABI directly.

## 18.3 Dynamic executables and sysroots

```bash
qemu-riscv64 -L /opt/riscv/sysroot ./app
```

`-L` must contain the guest dynamic loader and libraries at their expected paths. Static executables reduce setup but do not represent dynamic-loader behavior. Verify ABI, endianness, loader name, and library versions.

## 18.4 Environment variables

```bash
qemu-aarch64 \
  -E LD_LIBRARY_PATH=/opt/app/lib \
  -U LD_PRELOAD \
  ./app
```

Do not pass host loader variables to a foreign loader accidentally. Sanitize the environment in CI.

## 18.5 Threads, atomics, and memory models

QEMU maps supported guest threads to host threads. Guest and host memory-order models may differ; TCG must enforce guest semantics, but software relying on undefined behavior or unsupported atomic details can fail. Namespace-altering `clone` flags and unusual kernel interfaces may be unsupported. Cross-check concurrency bugs on the target kernel/hardware.

## 18.6 `binfmt_misc` and containers

Linux can register QEMU user interpreters so launching a foreign ELF automatically invokes QEMU. Static user binaries are often registered for multi-architecture containers. This is convenient but hides the translator from scripts; record the QEMU version and interpreter flags, and remember the container still uses the host kernel.

## 18.7 Supported architecture matrix

QEMU 11.0.3 documents user emulation for Alpha, Arm/AArch64, Hexagon, HPPA, x86, LoongArch, m68k, MicroBlaze, MIPS ABI/endian variants, OpenRISC, PowerPC, RISC-V, s390x, SH-4, SPARC variants, and Xtensa, subject to the build. AVR, RX, and TriCore are system-only in the documented TCG matrix.

## 18.8 BSD user mode

BSD user mode has a separate option set including `-bsd FreeBSD|NetBSD|OpenBSD`, library root, stack, environment controls, and debug logging. Support is substantially narrower; consult the exact build and [user-mode manual](https://www.qemu.org/docs/master/user/main.html) before choosing it for a project.

## 18.9 When user mode is the wrong tool

Use system mode or hardware when testing:

- kernel syscalls/implementation rather than application ABI behavior;
- `/proc`, `/sys`, namespaces, cgroups, security modules, or kernel timing in a target kernel;
- device `ioctl` semantics tied to target drivers;
- firmware, privileged ISA, MMU, page tables, interrupts, or drivers;
- target-kernel signal/thread corner cases;
- physical performance.

---

# 19. Standalone tools

## 19.1 `qemu-img`

`qemu-img` performs offline image operations. The fundamental safety rule is: **do not modify an image while a VM or another process is using it**.

Global options include `-h/--help`, `-V/--version`, and `-T/--trace`. Command-specific common options include:

- `--object OBJECTDEF`: secrets/credentials;
- `--image-opts`: parse source argument as a full block option string;
- `--target-image-opts`: parse destination as full options;
- `-U/--force-share`: weaken locking for allowed read-only operations;
- `-f`/`-F`: source formats;
- `-O`: output format;
- `-o`: format-specific options;
- `-p`: progress;
- `-q`: quiet;
- `-t`/`-T`: destination/source cache modes;
- `-S`: sparse zero threshold in relevant commands.

### Complete `qemu-img` command set

| Command | Purpose and key cautions |
|---|---|
| `amend` | Change supported format metadata/options. `--force` permits selected unsafe changes. Backing relationships belong to `rebase`. |
| `bench` | Simple sequential image I/O benchmark with request count, depth, size, offset, step, AIO, cache, flush, and write controls. It benchmarks the chosen stack, not guest filesystem performance. |
| `bitmap` | Add, remove, clear, enable, disable, or merge persistent dirty bitmaps. Preserve consistency across backup workflows. |
| `check` | Check image consistency; `-r leaks\|all` attempts repair. Repair can hide or worsen corruption—make a protected copy first. Exit codes distinguish clean, internal failure, corruption, leaks, and unsupported format. |
| `commit` | Merge an overlay's changes downward into a backing image. Intermediate layers can become invalid; understand `-b` and `-d`. |
| `compare` | Compare guest-visible content across images/formats. `-s` additionally requires matching size/allocation semantics. Exit 0 means equal, 1 different. |
| `convert` | Convert one or more source images to another format; supports compression, sparse detection, backing, bitmap copy, rate limit, parallel coroutines, copy offload, and salvage. Verify destination before replacing source. |
| `create` | Create an image with format, size, options, and optional backing file/format. In QEMU 11.0.3, give backing format `-B` with `-b`; `-u` skips backing validation and is unsafe. |
| `dd` | Copy/convert using `if=`, `of=`, `bs=`, `count=`, and `skip=`. It is not GNU `dd` feature-for-feature. |
| `info` | Report virtual/actual size, format, backing chain, snapshots, bitmaps, and format-specific data. Prefer `--output=json` for tools and `--backing-chain` for inventory. |
| `map` | Report allocation/data/zero/presence extents and backing depth. Use JSON for scripts; human output is not safe to parse. |
| `measure` | Estimate required and fully allocated size for creating/converting an image. Useful for LVs, SAN LUNs, and capacity planning. |
| `snapshot` | List, create, apply, or delete internal image snapshots. This is not a live VM snapshot workflow. |
| `rebase` | Change an image's backing file. Safe mode preserves guest-visible data; `-u` only rewrites metadata and can corrupt the view if the new backing data is not equivalent. |
| `resize` | Grow or shrink virtual size. Shrinking requires `--shrink` and prior guest filesystem/partition reduction; otherwise data is lost. Growing also requires guest partition/filesystem expansion. |

Examples:

```bash
qemu-img create -f qcow2 -o compat=1.1,preallocation=metadata disk.qcow2 40G
qemu-img info --output=json --backing-chain disk.qcow2
qemu-img check -f qcow2 disk.qcow2
qemu-img convert -p -f qcow2 -O raw source.qcow2 destination.raw
qemu-img map --output=json disk.qcow2
```

Format options change by build and version:

```bash
qemu-img create -f qcow2 -o help
```

See the complete [qemu-img manual](https://www.qemu.org/docs/master/tools/qemu-img.html) and [block-driver reference](https://www.qemu.org/docs/master/system/qemu-block-drivers.html).

## 19.2 `qemu-nbd`

`qemu-nbd` exports a QEMU block image over NBD, attaches a remote export to Linux `/dev/nbdX`, or lists server exports.

### Option map

| Group | Options |
|---|---|
| Object/security | `--object`, `--tls-creds`, `--tls-authz`, `--tls-hostname` |
| Address | `-p/--port`, `-b/--bind`, `-k/--socket` |
| Image | `--image-opts`, `-f/--format`, `-o/--offset`, `-r/--read-only`, `-s/--snapshot`, `-l/--load-snapshot` |
| Metadata | `-A/--allocation-depth`, `-B/--bitmap` |
| I/O | `--cache`, `-n/--nocache`, `--aio`, `--discard`, `--detect-zeroes` |
| Mode | `-c/--connect`, `-d/--disconnect`, `-L/--list` |
| Server lifetime | `-e/--shared`, `-t/--persistent`, `--fork`, `--pid-file`, `--handshake-limit` |
| Export identity | `-x/--export-name`, `-D/--description` |
| Diagnostics | `-v/--verbose`, `-h/--help`, `-V/--version`, `-T/--trace` |

Serve a read-only qcow2 over a protected Unix socket:

```bash
qemu-nbd --socket=/run/nbd/example.sock \
  --persistent --shared=4 --read-only --format=qcow2 \
  image.qcow2
```

Attach on Linux only in a controlled environment:

```bash
sudo modprobe nbd
sudo qemu-nbd --connect=/dev/nbd0 --read-only --format=qcow2 image.qcow2
# inspect without mounting untrusted filesystems in the host kernel
sudo qemu-nbd --disconnect /dev/nbd0
```

The official manual warns against mounting filesystems from an untrusted guest image through the host kernel. See [`qemu-nbd`](https://www.qemu.org/docs/master/tools/qemu-nbd.html).

## 19.3 `qemu-storage-daemon`

The storage daemon runs the block layer and exports without a virtual machine. Its complete top-level option set is:

| Option | Purpose |
|---|---|
| `-h/--help` | Help |
| `-V/--version` | Version |
| `-T/--trace` | Tracing |
| `--blockdev BLOCKDEVDEF` | Create a block graph node; repeatable |
| `--chardev CHARDEVDEF` | Create chardev, commonly a QMP socket; repeatable |
| `--export EXPORTDEF` | Export a node as NBD, vhost-user-blk, FUSE, or VDUSE where built |
| `--monitor MONITORDEF` | Create QMP monitor attached to a chardev |
| `--nbd-server ADDRESS` | Create NBD listener for NBD exports |
| `--object OBJECTDEF` | Create secrets, TLS credentials, IOThreads, throttle groups, etc. |
| `--pidfile PATH` | Locked PID/readiness file |
| `--daemonize` | Detach after successful initialization |

NBD export example:

```bash
qemu-storage-daemon \
  --blockdev driver=file,node-name=file0,filename=disk.qcow2 \
  --blockdev driver=qcow2,node-name=disk0,file=file0 \
  --nbd-server addr.type=unix,addr.path=/run/qsd/nbd.sock \
  --export type=nbd,id=exp0,node-name=disk0,name=disk,writable=off \
  --chardev socket,id=qmp0,path=/run/qsd/qmp.sock,server=on,wait=off \
  --monitor chardev=qmp0
```

QMP controls jobs and lifecycle; use the separate [storage-daemon QMP reference](https://www.qemu.org/docs/master/interop/qemu-storage-daemon-qmp-ref.html). See the [`qemu-storage-daemon` manual](https://www.qemu.org/docs/master/tools/qemu-storage-daemon.html).

## 19.4 `qemu-pr-helper`

This helper lets a QEMU SCSI target coordinate persistent reservations with multipath or host SCSI devices through a Unix socket. Key options configure the socket path, PID file, daemonization, privilege user/group, and verbosity. It is specialized infrastructure; review [the tool manual](https://www.qemu.org/docs/master/tools/qemu-pr-helper.html) and [helper protocol](https://www.qemu.org/docs/master/interop/pr-helper.html) with the storage architecture.

## 19.5 `qemu-trace-stap`

This utility runs SystemTap scripts against QEMU's static trace probes. It can list or bind probes and pass arguments according to its manual. It requires a QEMU build and host SystemTap environment containing the expected probes. See [`qemu-trace-stap`](https://www.qemu.org/docs/master/tools/qemu-trace-stap.html).

## 19.6 `qemu-vmsr-helper`

The virtual MSR helper provides controlled host RAPL MSR access to QEMU for virtual energy reporting on supported Linux/x86 configurations. Its Unix socket, privilege, and daemon options must be secured. Virtual energy information is dependent on host support and the defined interface; it is not a portable power model. See [`qemu-vmsr-helper`](https://www.qemu.org/docs/master/tools/qemu-vmsr-helper.html) and the [RAPL MSR specification](https://www.qemu.org/docs/master/specs/rapl-msr.html).

# 20. Target architectures, IBM-relevant platforms, and RISC-V

## 20.1 Architecture support matrix

QEMU 11.0.3's documented TCG matrix is summarized below. “System” means at least one complete machine is implemented; “User” means a foreign user process ABI is implemented.

| Guest architecture | System | User | Notes |
|---|:---:|:---:|---|
| Alpha | Yes | Yes | Legacy DEC 64-bit RISC |
| Arm/AArch64 | Yes | Yes | Broad board and architectural-feature coverage |
| AVR | Yes | No | 8-bit microcontroller/Arduino-oriented models |
| Hexagon | No | Yes | Qualcomm DSP family |
| HPPA | Yes | Yes | PA-RISC systems |
| x86/x86-64 | Yes | Yes | PC, microvm, confidential guests, many accelerators |
| LoongArch64 | Yes | Yes | `virt` system model |
| m68k/ColdFire | Yes | Yes | Classic m68k and embedded ColdFire subsets |
| MicroBlaze | Yes | Yes | Xilinx soft-core variants |
| MIPS | Yes | Yes | 32/64-bit and endian/ABI variants |
| OpenRISC | Yes | Yes | `or1k` targets |
| PowerPC/Power | Yes | Yes | Embedded, PowerMac, pseries, PowerNV and others |
| RISC-V | Yes | Yes | RV32/RV64, generic and board-specific machines |
| RX | Yes | No | Renesas MCU target |
| s390x | Yes | Yes | IBM Z architecture; `s390-ccw-virtio` machine |
| SH-4 | Yes | Yes | Big/little-endian builds |
| SPARC/SPARC64 | Yes | Yes | sun4m, sun4u/sun4v/Niagara-related models |
| TriCore | Yes | No | Infineon MCU/DSP family |
| Xtensa | Yes | Yes | Configurable soft-core, endian variants |

The target binary and `-machine help` remain authoritative. Several implemented architectures have less dedicated prose documentation than others.

## 20.2 x86

`qemu-system-x86_64` and `qemu-system-i386` offer the richest PC ecosystem. Major machine families include legacy i440FX (`pc`) and PCIe-oriented Q35, plus `microvm`, Xen PVH, and Nitro Enclave-related types. Features include BIOS/UEFI, ACPI/SMBIOS, CPU models, KVM paravirtualization, Hyper-V enlightenments, SGX, SEV/SEV-SNP, TDX, and many legacy/virtio devices.

For new general-purpose VMs, Q35 plus explicit devices is usually a cleaner starting point than an implicit legacy PC. Fleet design must choose versioned machine and CPU models rather than aliases.

## 20.3 Arm

Arm system emulation covers 32- and 64-bit architectural profiles, many fixed physical boards, and the generic `virt` platform. Use `qemu-system-aarch64` for AArch64 machines; it can also run supported 32-bit configurations. Board images are not interchangeable merely because CPUs share the Arm ISA. The `virt` board is best for generic Linux/UEFI/KVM workflows; physical board models are for their documented peripheral map.

## 20.4 PowerPC and Power: IBM-relevant distinctions

The Power target includes very different systems:

### `pseries`

`pseries` models a PAPR/sPAPR logical partition style platform, including RTAS, virtual I/O, XIVE/XICS paths, NUMA behavior, and firmware conventions. It is the natural QEMU model for a virtualized Power server guest. KVM acceleration depends on a compatible Power host and kernel mode. PAPR protected execution and migration have dedicated constraints.

### `powernv8`, `powernv9`, `powernv10`, `powernv11`

PowerNV models a bare-metal-style OpenPOWER system around successive POWER generations, including firmware/BMC and platform hardware paths. It is useful for skiboot, host firmware, BMC interaction, and platform bring-up. It is not the same guest ABI as pseries and has different acceleration status and device completeness.

### Other PowerPC machines

- `ppce500`: embedded e500 platform;
- PowerMac: classic Macintosh machines;
- PReP and embedded boards;
- AmigaNG-related models.

Before selecting a Power model, decide whether the software expects an LPAR/PAPR environment, a bare-metal PowerNV environment, an embedded SoC, or a historical desktop.

## 20.5 s390x: IBM Z

`qemu-system-s390x` models 64-bit z/Architecture with one principal machine family, `s390-ccw-virtio`, versioned for compatibility. Guest devices commonly use channel I/O and virtio-ccw. Dedicated documentation covers:

- channel subsystem and CSS devices;
- 3270 terminals;
- CCW boot devices and IPL;
- PCI devices on s390x;
- VFIO-AP and VFIO-CCW;
- protected virtualization;
- drawers/books/sockets/cores topology.

KVM on an s390x host can expose CPUs up to compatible host generation according to QEMU/kernel policy. TCG implements a functional architecture subset and is not a performance model of IBM Z. Use named CPU/machine compatibility for migration.

## 20.6 IBM Flexible Service Interface and XIVE specifications

The official guest-hardware specification manual includes IBM FSI, POWER9 XIVE, sPAPR XIVE, and sPAPR NUMA mechanics. These documents describe QEMU-specific guest interfaces and are essential when writing firmware, a driver, or a device model. They do not assert electrical or physical timing accuracy.

## 20.7 RISC-V system emulation

Use:

```bash
qemu-system-riscv64   # RV64 system
qemu-system-riscv32   # RV32 system
```

RISC-V has **no universal default board** in QEMU 11.0.3; specify `-machine`. RISC-V systems vary substantially in interrupt controllers, memory maps, firmware, and peripherals. A kernel built for one board normally will not boot on another unless it contains support for both and receives correct platform description.

Documented board families include:

- generic `virt`;
- SiFive U;
- Microchip Icicle Kit;
- Shakti C;
- XiangShan Kunminghu;
- MIPS P8700-related RISC-V platform support;
- MicroBlaze V generic platform.

The complete list comes from:

```bash
qemu-system-riscv64 -machine help
qemu-system-riscv32 -machine help
```

## 20.8 The RISC-V `virt` platform

`virt` does not correspond to a physical board. In QEMU 11.0.3 it provides, subject to configuration:

- up to 512 generic RV32GC/RV64GC cores with optional extensions;
- CLINT or optional ACLINT arrangements;
- PLIC or selectable AIA/APLIC/IMSIC configurations;
- CFI parallel NOR flash;
- NS16550-compatible UART;
- Goldfish RTC;
- SiFive test device;
- eight virtio-mmio transports;
- generic PCIe host bridge;
- `fw_cfg`;
- optional RISC-V IOMMU devices.

It automatically creates a DTB unless `-dtb` overrides it. A custom DTB must match `-smp`, `-m`, interrupt devices, and firmware expectations.

## 20.9 RISC-V firmware choices

For `virt` and `sifive_u`:

- `-bios default`: load the OpenSBI firmware bundled with QEMU; this is also the default if omitted.
- `-bios none`: load no firmware; the user must arrange reset/entry payloads.
- `-bios FILE`: load the supplied firmware image.

Normal Linux path:

```text
QEMU reset ROM -> OpenSBI in M-mode -> Linux in S-mode -> user space
```

This exercises RISC-V privilege handoff and SBI calls. If testing a custom OpenSBI build, supply it explicitly and record its version/hash.

## 20.10 RISC-V Linux with initramfs

```bash
qemu-system-riscv64 \
  -machine virt \
  -cpu rv64 \
  -smp 8 \
  -m 4G \
  -nographic \
  -bios default \
  -kernel arch/riscv/boot/Image \
  -initrd rootfs.cpio.gz \
  -append "console=ttyS0 earlycon=sbi rdinit=/init"
```

Validate `-cpu rv64` on the installed build; a named CPU or `max` may be more appropriate for specific extension tests. The generated DTB describes the virtual hardware. For a disk rootfs, add an explicit virtio block device and use the matching root argument.

## 20.11 Orange Pi RV2 boundary

QEMU 11.0.3 does not document an Orange Pi RV2 machine model. Use RISC-V `virt` to validate:

- generic RV64 compilation and instruction behavior;
- OpenSBI-to-Linux handoff;
- kernel core subsystems;
- initramfs and user-space startup;
- virtio/16550/PLIC or selected virtual interrupt paths;
- debugging and deterministic functional tests.

Do not use it to claim validation of:

- the Orange Pi RV2 SoC memory map or vendor devices;
- its eight-core split-L2/no-L3 cache organization;
- physical cache misses, IPC, DRAM latency, power, or thermal behavior;
- board boot ROM, U-Boot port, DTB, pinctrl, clock, PCIe, storage, or network drivers unless an exact device model exists.

The native-board phase must use the Orange Pi kernel/DTB/firmware and real measurements. Keep a configuration delta showing which drivers exist only for `virt` and which are required only on the board.

## 20.12 RISC-V AIA and IOMMU

The `virt` machine can select:

```bash
-machine virt,aia=none
-machine virt,aia=aplic
-machine virt,aia=aplic-imsic,aia-guests=N
```

It can expose the system IOMMU or PCI device form:

```bash
-machine virt,iommu-sys=on
# or
-device riscv-iommu-pci
```

Guest kernel, firmware, and QEMU must agree on these features. Their dedicated guest-hardware specs describe QEMU's interfaces; do not infer support from the ISA string alone.

---

# 21. Practical recipes and laboratories

These are templates. Run the discovery commands first and replace all paths.

## Lab 1: Create a capability manifest

```bash
mkdir -p qemu-capabilities
qemu-system-riscv64 --version > qemu-capabilities/version.txt
qemu-system-riscv64 -machine help > qemu-capabilities/machines.txt
qemu-system-riscv64 -cpu help > qemu-capabilities/cpus.txt
qemu-system-riscv64 -accel help > qemu-capabilities/accelerators.txt
qemu-system-riscv64 -device help > qemu-capabilities/devices.txt
qemu-system-riscv64 -object help > qemu-capabilities/objects.txt
```

Goal: prove exactly which build was used before creating a command line. Add hashes and host metadata for a research manifest.

## Lab 2: Boot a minimal RISC-V kernel

```bash
qemu-system-riscv64 \
  -machine virt \
  -smp 4 \
  -m 1G \
  -nodefaults \
  -nographic \
  -kernel Image \
  -initrd rootfs.cpio.gz \
  -append "console=ttyS0 earlycon=sbi rdinit=/init panic=-1"
```

If `-nodefaults` removes something the selected build/board expects, begin without it, inspect `info qtree`, then minimize incrementally. Success criteria should include OpenSBI output, kernel entry, console initialization, rootfs mount, `/init`, and a deliberate shutdown/exit signal.

## Lab 3: Add an explicit RISC-V virtio disk

```bash
qemu-img create -f qcow2 disk.qcow2 8G

qemu-system-riscv64 \
  -machine virt \
  -m 2G -smp 4 -nographic \
  -kernel Image \
  -append "console=ttyS0 root=/dev/vda rw" \
  -blockdev driver=file,filename=disk.qcow2,node-name=disk-file \
  -blockdev driver=qcow2,file=disk-file,node-name=disk-format \
  -device virtio-blk-device,drive=disk-format,id=vda
```

On `virt`, `virtio-blk-device` uses a virtio-mmio bus. A PCI variant can be selected only when the PCI topology and guest driver support it.

## Lab 4: Kernel debugging from reset

```bash
qemu-system-riscv64 \
  -machine virt -m 1G -smp 1 \
  -S -gdb tcp:127.0.0.1:1234 \
  -nographic \
  -kernel Image \
  -initrd rootfs.cpio.gz \
  -append "console=ttyS0 nokaslr"
```

```gdb
file vmlinux
target remote 127.0.0.1:1234
break start_kernel
continue
```

Observe M-mode firmware first if bundled OpenSBI runs before Linux. A breakpoint in `start_kernel` will resolve when execution reaches the loaded kernel and symbols match.

## Lab 5: Cross-run a RISC-V user program

```bash
riscv64-linux-gnu-gcc -O2 -g hello.c -o hello
qemu-riscv64 -L /usr/riscv64-linux-gnu ./hello
QEMU_STRACE=1 qemu-riscv64 -L /usr/riscv64-linux-gnu ./hello
```

Compile statically only if a compatible static toolchain is available:

```bash
riscv64-linux-gnu-gcc -O2 -g -static hello.c -o hello-static
qemu-riscv64 ./hello-static
```

Compare guest syscall trace with host `strace` on QEMU to learn the translation boundary.

## Lab 6: Safe overlay workflow

```bash
qemu-img create -f qcow2 base.qcow2 20G
qemu-img create -f qcow2 -B qcow2 -b base.qcow2 experiment.qcow2
qemu-img info --output=json --backing-chain experiment.qcow2
```

Attach only the top image. Keep `base.qcow2` immutable while descendants exist. To discard the experiment, stop the VM and delete only the overlay after verifying the chain.

## Lab 7: QMP lifecycle

Start paused:

```bash
qemu-system-x86_64 \
  -machine q35 \
  -S -display none -nodefaults \
  -qmp unix:/tmp/lab.qmp,server=on,wait=off
```

Connect with a QMP-aware client or a line-oriented Unix-socket tool. Negotiate capabilities, then run:

```json
{"execute":"qmp_capabilities","id":"1"}
{"execute":"query-version","id":"2"}
{"execute":"query-machines","id":"3"}
{"execute":"query-status","id":"4"}
{"execute":"cont","id":"5"}
```

The VM has no boot devices in this minimal example; the lab objective is protocol framing, IDs, events, and lifecycle state.

## Lab 8: User-mode network with SSH forwarding

```bash
qemu-system-x86_64 \
  -machine q35 -m 2G -smp 2 \
  -netdev user,id=net0,hostfwd=tcp:127.0.0.1:2222-:22 \
  -device virtio-net-pci,netdev=net0,mac=52:54:00:12:34:56 \
  ...
```

After the guest starts SSH:

```bash
ssh -p 2222 user@127.0.0.1
```

The host port is intentionally loopback-only.

## Lab 9: Capture guest packets

```bash
-netdev user,id=net0 \
-device virtio-net-pci,netdev=net0,mac=52:54:00:12:34:57 \
-object filter-dump,id=pcap0,netdev=net0,file=guest.pcap
```

Open the pcap in Wireshark/tcpdump. Treat it as sensitive data.

## Lab 10: Deterministic record and replay

Use a small, supported, fully controlled VM. Create an immutable base and identical starting overlay/snapshot. Record with `rr=record`, archive the exact command and image hashes, then replay with `rr=replay`. Confirm the same console/event sequence. Add one unsupported external input at a time to identify the deterministic boundary.

## Lab 11: Inspect a Power or s390x target

Without needing boot media, enumerate the enterprise platform contracts:

```bash
qemu-system-ppc64 -machine help
qemu-system-ppc64 -cpu help
qemu-system-ppc64 -machine pseries,help
qemu-system-ppc64 -machine powernv10,help

qemu-system-s390x -machine help
qemu-system-s390x -cpu help
qemu-system-s390x -machine s390-ccw-virtio,help
```

Compare buses/devices with `-device help` and the target manuals. This makes the pseries/PowerNV/s390x distinction concrete before choosing firmware and kernels.

## Lab 12: Minimal-topology audit

Boot once with machine defaults, then enter HMP:

```text
info qtree
info mtree
info irq
info block
info network
```

Repeat with `-nodefaults` and explicitly add required devices. Diff the inventories. The result is a defensible device-minimization record; it is not proof of smaller physical hardware or faster guest execution.

---

# 22. Migration, high availability, automation, and QEMU development

## 22.1 Live migration model

Precopy migration iteratively sends RAM while the source runs, tracks dirtied pages, then pauses briefly to transfer remaining RAM and device/vCPU state. If the workload dirties memory faster than the channel can converge, downtime or total migration time grows.

Postcopy starts the guest at the destination before all RAM arrives and faults missing pages from the source. It can bound switchover but makes source/destination/network failure during migration more consequential.

Additional documented mechanisms include multifd channels, mapped-RAM formats, compression options, dirty-rate limiting, VFIO/virtio-specific state, checkpoint/restart modes, and postcopy recovery. Availability and compatibility vary by accelerator, target, devices, and release.

## 22.2 Migration compatibility checklist

- Same compatible, preferably versioned machine type.
- Compatible CPU model/features and topology.
- Destination QEMU understands all VMState versions.
- Identical or intentionally compatible firmware, ROMs, device properties, and kernel-irqchip behavior.
- Storage is shared or migrated consistently.
- Network identity and external backends exist at destination.
- Every device is migratable; pass-through often is not.
- RAM backend/sharing/encryption/confidential-guest requirements match.
- TLS/authentication and bandwidth are configured.
- Clock/time and downtime policy are defined.
- Cancellation, rollback, source shutdown, and split-brain prevention are tested.

`-only-migratable` is a useful startup guard, not an end-to-end certification.

## 22.3 QMP migration workflow

A manager typically:

1. Queries migration capabilities and parameters.
2. Configures both ends and starts destination `-incoming` or deferred incoming mode.
3. Starts migration with a URI/channel definition.
4. Consumes `query-migrate` status and migration events.
5. Handles pre-switchover/postcopy states as configured.
6. Confirms destination running and source completion.
7. Applies external fencing and storage/network ownership transitions.

Never infer success solely because the source QMP connection closed.

## 22.4 COLO and replication

COLO coordinates a primary and secondary VM with checkpoints, block replication, and network packet comparison/rewriting. It is a specialized fault-tolerance design with strict storage/network topology and failure-handling requirements. Read the [QEMU COLO documentation](https://www.qemu.org/docs/master/system/qemu-colo.html), block replication, and filter object references before evaluating it.

## 22.5 Automation principles

- Generate explicit IDs and retain a machine-readable desired-state model.
- Launch under a supervisor; avoid PID polling and arbitrary sleeps.
- Use QMP, not HMP parsing.
- Probe schema/capabilities.
- Correlate every command with `id` and consume events continuously.
- Make mutations idempotent or reconcile actual state after failure.
- Validate storage locks and socket ownership before launch.
- Redact secrets from logs and crash reports.
- Treat command-line ordering and startup dependencies as a directed graph.
- Persist machine/CPU/firmware compatibility contracts separately from transient host paths.

## 22.6 QEMU source-tree orientation

| Area | Role |
|---|---|
| `target/ARCH/` | Guest instruction decode, CPU state, MMU, exceptions, helpers |
| `tcg/` | Target-independent IR, optimization, host backends, code generation |
| `accel/` | TCG/KVM/other accelerator integration |
| `hw/` | Machines, buses, and device models grouped by subsystem/architecture |
| `system/` | System-emulator core, run state, memory setup, device tree, lifecycle |
| `block/` | Block drivers, graph and jobs |
| `net/` | Network backends and helpers |
| `chardev/` | Character backends |
| `migration/` | VM migration and VMState machinery |
| `qapi/` | QMP/QAPI schemas generating types, commands, events, visitors, docs |
| `qom/` and `include/qom/` | Object model |
| `linux-user/`, `bsd-user/` | User-mode syscall/ABI implementations |
| `tests/` | qtest, qgraph, functional, TCG, fuzz, unit and CI tests |
| `docs/` | User, interoperability, specification, and developer manuals |

## 22.7 Adding a device

A typical device-model project involves:

1. Define a QOM type and state structure.
2. Define properties and reset behavior.
3. Create MMIO/PIO regions and register callbacks.
4. Add IRQ, GPIO, DMA, clock, bus, and backend links.
5. Realize/unrealize resources with correct error handling.
6. Register migration state with versioning.
7. Add the device to a machine or make it user-creatable.
8. Write qtest/unit/functional tests and documentation.
9. Check endian, access-size, concurrency, reentrancy, and malicious-guest inputs.

The [QOM](https://www.qemu.org/docs/master/devel/qom.html), [QDev API](https://www.qemu.org/docs/master/devel/qdev-api.html), [Memory API](https://www.qemu.org/docs/master/devel/memory.html), reset, migration, and qtest manuals are the core reading path.

## 22.8 Adding or changing an ISA target

TCG target work separates:

- guest CPU state and features;
- instruction decode, often using decodetree;
- emitted TCG operations and helper calls;
- exception/MMU/interrupt semantics;
- disassembly and GDB register exposure;
- system/user ABI integration;
- translation tests and architecture test suites.

TCG IR describes architectural effects; the host backend lowers them to host instructions. Review [TCG internals](https://www.qemu.org/docs/master/devel/tcg.html), [TCG operations](https://www.qemu.org/docs/master/devel/tcg-ops.html), [decodetree](https://www.qemu.org/docs/master/devel/decodetree.html), and load/store rules.

## 22.9 QAPI and monitor changes

QAPI schemas generate C data types, visitors, command/event marshalling, and reference documentation. Management interfaces require compatibility discipline, feature/deprecation metadata, typed errors, tests, and documentation. Do not expose an HMP-only string interface when a stable management operation is intended.

## 22.10 Testing

Key suites include:

- unit tests;
- qtest and qgraph device/system tests;
- functional boot tests;
- `check-tcg` translated-code tests;
- fuzzing;
- block `iotests`/driver-specific tests as present in the source tree;
- ACPI tests;
- migration compatibility tests;
- CI across hosts, compilers, sanitizers, and cross builds.

Run the narrow test first, then broader `make check`/project-prescribed suites. A change to guest-visible behavior needs migration and versioning review, not only a passing local boot.

## 22.11 Upstream contribution

QEMU development is mailing-list centered. Read the code of conduct, coding style, maintainer map, patch submission, provenance/DCO, testing, and pull-request process. Send small reviewable patches with tests and documentation. Device and target maintainers may require architecture specifications or hardware evidence.

---

# 23. Official documentation atlas

The stable QEMU 11.0.3 documentation tree has seven principal manuals plus a glossary. This chapter explains what each contains, what should be read fully, and what can be used as reference.

## 23.1 About QEMU

Start at [About QEMU](https://www.qemu.org/docs/master/about/index.html).

| Section | Summary | Reading priority |
|---|---|---|
| Supported build platforms | Supported host architectures, accelerators, OS lifetime policy, compilers/runtimes, Windows build rules | Full for build/release owners |
| Emulation | System/user target matrix, semihosting, TCG plugins and included examples | Full for emulation/research work |
| Deprecated features | Inputs still present but scheduled for removal, with replacements | Check before every upgrade |
| Removed features | Historical removals by release | Reference during migration/upgrade |
| License | QEMU/manual GPLv2 terms and component considerations | Full for redistribution/compliance owners |

## 23.2 System Emulation User's Guide

Start at [System Emulation](https://www.qemu.org/docs/master/system/index.html).

### Read fully for ordinary engineering

- [Introduction](https://www.qemu.org/docs/master/system/introduction.html): accelerators, features, command composition.
- [Invocation](https://www.qemu.org/docs/master/system/invocation.html): canonical system CLI and all top-level options/suboptions.
- [Device Emulation](https://www.qemu.org/docs/master/system/device-emulation.html): frontend, bus, backend, pass-through, and device families.
- [Disk Images](https://www.qemu.org/docs/master/system/images.html) and [block drivers](https://www.qemu.org/docs/master/system/qemu-block-drivers.html): image semantics.
- [Direct Linux Boot](https://www.qemu.org/docs/master/system/linuxboot.html), target manual, and loader docs for kernel/firmware work.
- [QEMU Monitor](https://www.qemu.org/docs/master/system/monitor.html), [QMP spec](https://www.qemu.org/docs/master/interop/qmp-spec.html), and QMP reference for automation.
- Security, TLS, secrets, and authorization before exposing a service.

### Operational/reference sections

| Section group | What it covers |
|---|---|
| Graphical and mux keys | Keyboard grabs, console switching, terminal escape sequences |
| net_failover | Coordinating a virtio standby NIC with a passed-through primary |
| Generic/guest loader | Loading data/images at addresses and nested guest payload descriptions |
| Barrier client | Coordinated barrier protocol/tool usage |
| VNC/TLS/secrets/authz | Remote-service protection and credential objects |
| GDB | System/user target remote debugging |
| Record/replay | Deterministic inputs, snapshots, supported devices, replay debugging |
| Managed startup | Paused/preconfigured lifecycle states |
| Bootindex | Per-device firmware boot order |
| CPU hotplug | Query/add CPU object workflows |
| Persistent reservations | SCSI PR manager integration |
| Security | Threat model and deployment guidance |
| Multi-process QEMU | Out-of-process device model architecture |
| Confidential guests/IGVM | Common confidential-guest concepts and IGVM input |
| Nitro/WHPX | Accelerator/platform-specific setup |
| VM templating | Shared memory/image template optimization and constraints |
| Composable SR-IOV | SR-IOV device composition interface |
| COLO | Checkpoint-based fault tolerance |

### Device documentation

The device subtree covers virtio (GPU, PMEM, sound, vhost-user), network devices, CAN, CanoKey, CCID, CXL, eMMC, Intel igb, ivshmem, keyboard, NVMe, SCSI persistent reservations, USB/U2F, and vfio-user. Read a device page when that device appears in the intended topology; the option-level source of truth remains `-device TYPE,help` and QMP property discovery.

### Target documentation

The guide provides target chapters for Arm, AVR, LoongArch, m68k, MIPS, OpenRISC, PowerPC, RISC-V, RX, s390x, SPARC32/64, x86, and Xtensa. Other implemented system targets may have less standalone prose. Always pair the architecture chapter with the selected board page and runtime `-machine help`.

## 23.3 User Mode Emulation User's Guide

[User Mode Emulation](https://www.qemu.org/docs/master/user/index.html) explains supported OS modes, syscall translation, signals, threading, Linux options/binaries, and BSD status/options. Read it fully before relying on user mode for ABI or concurrency testing; it is short and its limitations determine result validity.

## 23.4 Tools manual

[Tools](https://www.qemu.org/docs/master/tools/index.html) contains the six stable 11.0.3 tool manuals:

1. `qemu-img`
2. `qemu-storage-daemon`
3. `qemu-nbd`
4. `qemu-pr-helper`
5. `qemu-trace-stap`
6. `qemu-vmsr-helper`

Read the full relevant tool page before a mutating storage operation. Command help and format-specific `-o help` remain mandatory because compiled drivers vary.

## 23.5 Management and interoperability

Start at [System Emulation Management and Interoperability](https://www.qemu.org/docs/master/interop/index.html).

| Section group | Summary |
|---|---|
| Barrier, dirty bitmaps, live block operations | Coordinated control and backup/block-job workflows |
| D-Bus, VMState, display | D-Bus integration contracts |
| NBD | QEMU's NBD protocol extensions and implementation behavior |
| Image formats | Parallels, Parallels XML, qcow2, and QED on-disk specifications |
| PR helper | Persistent-reservation helper wire protocol |
| QMP | JSON wire specification and generated full command/type/event reference |
| Guest agent | QGA use and generated protocol reference |
| Storage-daemon QMP | QMP subset exposed by QSD |
| vfio-user | Userspace device protocol |
| vhost-user/gpu/vDPA | Virtio backend protocols and platform bindings |
| Virtio balloon statistics | Guest statistics names/semantics |
| VNC LED pseudo-encoding | QEMU VNC extension for keyboard LED state |

Protocol and image-format specifications should be read fully by implementers. Operators can read the conceptual section and use the generated reference for exact messages.

## 23.6 Guest hardware specifications

Start at [System Emulation Guest Hardware Specifications](https://www.qemu.org/docs/master/specs/index.html). These pages define QEMU-specific interfaces presented to guest firmware/drivers, including:

- QEMU PCI IDs, serial/test devices;
- POWER XIVE and sPAPR XIVE/NUMA;
- ACPI generic event, hardware-reduced hotplug, HEST/GHES, CPU/memory/PCI/NVDIMM hotplug, and ERST;
- TPM interface;
- SEV guest-firmware interface;
- `fw_cfg`;
- IBM FSI;
- VMware PVSCSI;
- educational PCI device;
- ivshmem and pvpanic;
- SPDM, standard VGA, virtual system controller, VMCoreInfo, VM generation ID;
- RAPL MSR, Rocker switch;
- RISC-V IOMMU and AIA;
- ASPEED interrupt controller and IOMMU test device.

Driver/firmware/device-model authors should read the relevant specification in full. VM users normally need only the summary and device manual unless debugging a low-level interface.

## 23.7 Developer information

Start at [Developer Information](https://www.qemu.org/docs/master/devel/index.html). It is organized as:

| Area | Essential contents |
|---|---|
| Community process | Code of conduct, conflict resolution, maintainers, style, patch submission, provenance, stable process, pull requests, secure coding, Rust policy |
| Build system | Configure/Meson architecture, environment setup, Kconfig, documentation, QAPI generation/domain, CFI |
| Testing | Main test guide, qtest/qgraph, functional tests, ACPI tests, CI, fuzzing, blkdebug, blkverify |
| Internal APIs | Bit operations, load/store APIs, lock counters, MemoryRegion, modules, PCI, QOM/QDev APIs, UI, zoned storage |
| Subsystems | QOM, atomics/RCU, coroutines, clocks, eBPF RSS, migration, multiprocess, reset, s390 internals, tracing, UEFI vars, VFIO/IOMMUFD, monitor commands, virtio backends, crypto/LUKS, IOThreads |
| TCG | Core translator, operations, decodetree, MTTCG, instruction counting, plugins, replay |
| Codebase | Generated source-level architecture overview |

Read the community/style/testing material before sending patches. Read QOM/QDev/Memory for devices, QAPI for management interfaces, migration VMState guidance for migratable state, and TCG documents for CPU translation.

## 23.8 Glossary

The [official glossary](https://www.qemu.org/docs/master/glossary.html) defines project-specific terms such as accelerator, board, block, guest, host, machine, migration, softmmu, QMP/HMP, MTTCG, QOM, TCG, virtio, and vhost-user. Use it to resolve overloaded terminology before reading source code.

---

# Appendix A. Command-line review checklist

Before approving a QEMU command:

- [ ] QEMU version/build and target binary recorded.
- [ ] Machine type explicit; versioned when migration requires it.
- [ ] Accelerator explicit and verified at runtime.
- [ ] CPU model/features explicit and compatible with migration goal.
- [ ] vCPU topology mathematically valid.
- [ ] RAM, memory backend, huge-page/preallocation, and NUMA policy intentional.
- [ ] `-nodefaults` used or every default device inventoried.
- [ ] Every frontend/backend/object has a stable unique ID.
- [ ] Disk format explicit; no unintended probing.
- [ ] Cache, AIO, discard, error, and durability policies explicit.
- [ ] Backing chains inventoried and immutable where required.
- [ ] Network backend, MAC, queues, listeners, and firewall intentional.
- [ ] QMP separate from guest console and protected.
- [ ] VNC/SPICE/NBD/migration/chardev listeners authenticated, encrypted, and bound correctly.
- [ ] Firmware/ROM/DTB/kernel/initrd hashes and versions recorded.
- [ ] Host paths, shares, semihosting, pass-through, and helpers minimized.
- [ ] Privilege drop, sandbox, namespaces, MAC policy, cgroups, and supervisor configured.
- [ ] Secrets absent from argv/logs/environment where avoidable.
- [ ] Shutdown, watchdog, crash, timeout, and cleanup behavior defined.
- [ ] Migration, backup, and recovery tested—not merely configured.
- [ ] Performance claims match QEMU's fidelity and accelerator.

---

# Appendix B. Troubleshooting

| Symptom | Likely causes | Evidence/action |
|---|---|---|
| `No machine specified` on RISC-V | RISC-V has no default board | Add `-machine virt` or exact supported board; run `-machine help` |
| `Property ... not found` | Wrong version/type/transport | Run `TYPE,help`; verify QEMU version and machine bus |
| `No 'PCI' bus found` | PCI device on non-PCI topology | Use virtio-mmio `*-device`, add supported PCI host, or choose correct board |
| Black screen/no serial output | Wrong console, firmware/payload, display/serial routing | Use `-nographic`, correct kernel `console=`, early console, GDB at reset |
| QEMU uses TCG unexpectedly | KVM unavailable, permissions, wrong ISA, initialization failure | `-accel help`, inspect `/dev/kvm`, startup errors, explicitly `-accel kvm` so failure is visible |
| `Could not open ... Permission denied` | Service user, SELinux/AppArmor, socket/file permissions | Inspect effective UID/groups, path traversal permissions, MAC logs; avoid running all of QEMU as root |
| Image is locked | Another writer/process exists | Identify owner; do not bypass locking for mutation |
| “Image format was not specified” | Implicit probing/raw restriction | Add explicit `format=` or `-blockdev driver=...` |
| Overlay cannot find backing file | Moved relative path or missing format | `qemu-img info --backing-chain`; repair only with understood `rebase` semantics |
| VM hangs at boot after `-nodefaults` | Removed required console/controller/device | Compare `info qtree` before/after; add devices explicitly |
| Host SSH forwarding fails | Guest service/address, bind, user-network syntax/firewall | Verify guest SSH, `hostfwd`, listener address, host port, QEMU build's slirp support |
| GDB breakpoint never hits | Wrong symbols/address/KASLR/firmware stage | Use matching `vmlinux`, disable KASLR, inspect PC, wait through firmware, relocate symbols |
| Guest time drifts | Host scheduling, RTC/clock choice, icount mode | Review `-rtc`, guest clocksource, load, pause behavior; do not misuse icount as hardware timing |
| Record/replay diverges | Different initial state or unsupported nondeterminism | Hash all inputs, use single-thread TCG, supported devices/backends, identical command |
| Live migration will not converge | High dirty rate or insufficient bandwidth | Query dirty rate/migration stats; throttle, improve channel, tune multifd, or evaluate postcopy risk |
| Migration rejects CPU/device | Incompatible ABI, property, firmware, machine version | Compare machine/CPU/QMP schema/VMState; use versioned contracts and `-only-migratable` |
| Poor TCG SMP scaling | Single-thread mode, shared locks/devices, translation overhead | Verify `thread=multi`, remove icount, profile host threads, reduce contention |
| Guest PMU value looks implausible | Unsupported/virtualized event semantics | Identify TCG/KVM model and PMU support; validate on real target hardware |

---

# Appendix C. Glossary

| Term | Meaning in QEMU context |
|---|---|
| Accelerator | Mechanism that runs vCPUs: TCG or a hypervisor integration |
| AioContext | Event-loop context for asynchronous I/O; IOThreads provide separate contexts |
| Backing file | Lower image in a copy-on-write chain |
| Backend | Host-facing implementation supplying data/service to a frontend |
| BQL | Big QEMU Lock, a global lock still protecting selected shared paths |
| Chardev | Host byte-stream backend used by serial, monitor, QGA, vhost-user, etc. |
| Device | Guest-visible or internal QOM component with properties/state |
| Dirty bitmap | Granular record of regions written since a defined point |
| DTB/FDT | Binary flattened device tree describing platform hardware |
| Frontend | Guest-visible device interface |
| Guest | Software environment running inside QEMU |
| Host | System/process environment running QEMU |
| HMP | Human Monitor Protocol, text interface |
| IOThread | Dedicated QEMU I/O event-loop thread/object |
| KVM | Linux kernel virtualization interface used by QEMU |
| Machine/board | Root virtual platform model and compatibility contract |
| MemoryRegion | QEMU object representing RAM, ROM, MMIO, aliases, or containers |
| MTTCG | Multi-threaded TCG, commonly one host thread per vCPU |
| NBD | Network Block Device protocol |
| Node name | Identifier for a QEMU block graph node |
| QAPI | Schema and code-generation system for QMP types/commands/events |
| QDev | Device/bus lifecycle layer integrated with QOM |
| QEMU | Generic emulator/virtualizer and its tool family |
| QGA | QEMU Guest Agent and its protocol |
| QMP | JSON QEMU Machine Protocol |
| QOM | QEMU Object Model |
| softmmu | System emulation's software guest address-translation mode |
| Target | Guest architecture/build target, not the host |
| TB | TCG translation block containing generated host code for guest instructions |
| TCG | Tiny Code Generator, QEMU's dynamic binary translator/JIT |
| vCPU | Virtual processor execution context/thread |
| VFIO | Framework for safely assigning host devices with IOMMU support |
| virtio | Standard paravirtual device family using virtqueues |
| VMState | Versioned device/machine state serialized for migration/snapshots |
| vhost | Kernel or external backend acceleration for virtio data paths |

---

# Appendix D. Primary references

All primary technical references below are maintained by the QEMU Project unless otherwise noted.

1. [QEMU 11.0.0 release announcement](https://www.qemu.org/2026/04/22/qemu-11-0-0/)
2. [QEMU downloads and current releases](https://www.qemu.org/download/)
3. [QEMU 11.0.3 tagged source](https://gitlab.com/qemu-project/qemu/-/tree/v11.0.3)
4. [QEMU documentation root](https://www.qemu.org/docs/master/)
5. [About QEMU](https://www.qemu.org/docs/master/about/index.html)
6. [System Emulation](https://www.qemu.org/docs/master/system/index.html)
7. [System Invocation](https://www.qemu.org/docs/master/system/invocation.html)
8. [User Mode Emulation](https://www.qemu.org/docs/master/user/index.html)
9. [Tools](https://www.qemu.org/docs/master/tools/index.html)
10. [Management and Interoperability](https://www.qemu.org/docs/master/interop/index.html)
11. [Guest Hardware Specifications](https://www.qemu.org/docs/master/specs/index.html)
12. [Developer Information](https://www.qemu.org/docs/master/devel/index.html)
13. [QMP protocol specification](https://www.qemu.org/docs/master/interop/qmp-spec.html)
14. [QMP reference](https://www.qemu.org/docs/master/interop/qemu-qmp-ref.html)
15. [TCG internals](https://www.qemu.org/docs/master/devel/tcg.html)
16. [RISC-V system target](https://www.qemu.org/docs/master/system/target-riscv.html)
17. [PowerPC system target](https://www.qemu.org/docs/master/system/target-ppc.html)
18. [s390x system target](https://www.qemu.org/docs/master/system/target-s390x.html)
19. [Official glossary](https://www.qemu.org/docs/master/glossary.html)

---

# Appendix E. Exhaustive documentation page index

The following index enumerates every reStructuredText page reachable from the QEMU 11.0.3 stable documentation toctree. It is intentionally exhaustive. Every item provides both the easier-to-read rolling HTML page and the exact `v11.0.3` tagged source, so a later documentation change cannot erase the version used by this book. Index pages and generated references are included because they are part of the official tree.

## E.1 Documentation root

- [Welcome to QEMU's documentation!](https://www.qemu.org/docs/master/) — `docs/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/index.rst)

## E.2 About QEMU

- [About QEMU](https://www.qemu.org/docs/master/about/index.html) — `docs/about/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/about/index.rst)
- [Supported build platforms](https://www.qemu.org/docs/master/about/build-platforms.html) — `docs/about/build-platforms.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/about/build-platforms.rst)
- [Emulation](https://www.qemu.org/docs/master/about/emulation.html) — `docs/about/emulation.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/about/emulation.rst)
- [Deprecated features](https://www.qemu.org/docs/master/about/deprecated.html) — `docs/about/deprecated.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/about/deprecated.rst)
- [Removed features](https://www.qemu.org/docs/master/about/removed-features.html) — `docs/about/removed-features.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/about/removed-features.rst)
- [License](https://www.qemu.org/docs/master/about/license.html) — `docs/about/license.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/about/license.rst)

## E.3 System emulation

- [System Emulation](https://www.qemu.org/docs/master/system/index.html) — `docs/system/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/index.rst)
- [Introduction](https://www.qemu.org/docs/master/system/introduction.html) — `docs/system/introduction.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/introduction.rst)
- [Invocation](https://www.qemu.org/docs/master/system/invocation.html) — `docs/system/invocation.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/invocation.rst)
- [Device Emulation](https://www.qemu.org/docs/master/system/device-emulation.html) — `docs/system/device-emulation.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/device-emulation.rst)
- [VirtIO Devices](https://www.qemu.org/docs/master/system/devices/virtio/index.html) — `docs/system/devices/virtio/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/virtio/index.rst)
- [VirtIO GPU](https://www.qemu.org/docs/master/system/devices/virtio/virtio-gpu.html) — `docs/system/devices/virtio/virtio-gpu.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/virtio/virtio-gpu.rst)
- [VirtIO Persistent Memory](https://www.qemu.org/docs/master/system/devices/virtio/virtio-pmem.html) — `docs/system/devices/virtio/virtio-pmem.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/virtio/virtio-pmem.rst)
- [VirtIO Sound](https://www.qemu.org/docs/master/system/devices/virtio/virtio-snd.html) — `docs/system/devices/virtio/virtio-snd.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/virtio/virtio-snd.rst)
- [vhost-user back ends](https://www.qemu.org/docs/master/system/devices/virtio/vhost-user.html) — `docs/system/devices/virtio/vhost-user.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/virtio/vhost-user.rst)
- [vhost-user daemons in contrib](https://www.qemu.org/docs/master/system/devices/virtio/vhost-user-contrib.html) — `docs/system/devices/virtio/vhost-user-contrib.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/virtio/vhost-user-contrib.rst)
- [CAN Bus Emulation Support](https://www.qemu.org/docs/master/system/devices/can.html) — `docs/system/devices/can.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/can.rst)
- [CanoKey QEMU](https://www.qemu.org/docs/master/system/devices/canokey.html) — `docs/system/devices/canokey.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/canokey.rst)
- [Chip Card Interface Device (CCID)](https://www.qemu.org/docs/master/system/devices/ccid.html) — `docs/system/devices/ccid.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/ccid.rst)
- [Compute Express Link (CXL)](https://www.qemu.org/docs/master/system/devices/cxl.html) — `docs/system/devices/cxl.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/cxl.rst)
- [eMMC Emulation](https://www.qemu.org/docs/master/system/devices/emmc.html) — `docs/system/devices/emmc.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/emmc.rst)
- [igb](https://www.qemu.org/docs/master/system/devices/igb.html) — `docs/system/devices/igb.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/igb.rst)
- [Inter-VM Shared Memory Flat Device](https://www.qemu.org/docs/master/system/devices/ivshmem-flat.html) — `docs/system/devices/ivshmem-flat.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/ivshmem-flat.rst)
- [Inter-VM Shared Memory device](https://www.qemu.org/docs/master/system/devices/ivshmem.html) — `docs/system/devices/ivshmem.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/ivshmem.rst)
- [Sparc32 keyboard](https://www.qemu.org/docs/master/system/devices/keyboard.html) — `docs/system/devices/keyboard.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/keyboard.rst)
- [Network emulation](https://www.qemu.org/docs/master/system/devices/net.html) — `docs/system/devices/net.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/net.rst)
- [NVMe Emulation](https://www.qemu.org/docs/master/system/devices/nvme.html) — `docs/system/devices/nvme.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/nvme.rst)
- [SCSI Devices](https://www.qemu.org/docs/master/system/devices/scsi/index.html) — `docs/system/devices/scsi/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/scsi/index.rst)
- [SCSI Persistent Reservation Live Migration](https://www.qemu.org/docs/master/system/devices/scsi/migrate-pr.html) — `docs/system/devices/scsi/migrate-pr.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/scsi/migrate-pr.rst)
- [Universal Second Factor (U2F) USB Key Device](https://www.qemu.org/docs/master/system/devices/usb-u2f.html) — `docs/system/devices/usb-u2f.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/usb-u2f.rst)
- [USB emulation](https://www.qemu.org/docs/master/system/devices/usb.html) — `docs/system/devices/usb.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/usb.rst)
- [vfio-user](https://www.qemu.org/docs/master/system/devices/vfio-user.html) — `docs/system/devices/vfio-user.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/devices/vfio-user.rst)
- [Keys in the graphical frontends](https://www.qemu.org/docs/master/system/keys.html) — `docs/system/keys.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/keys.rst)
- [Keys in the character backend multiplexer](https://www.qemu.org/docs/master/system/mux-chardev.html) — `docs/system/mux-chardev.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/mux-chardev.rst)
- [QEMU Monitor](https://www.qemu.org/docs/master/system/monitor.html) — `docs/system/monitor.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/monitor.rst)
- [Disk Images](https://www.qemu.org/docs/master/system/images.html) — `docs/system/images.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/images.rst)
- [QEMU virtio-net standby (net_failover)](https://www.qemu.org/docs/master/system/virtio-net-failover.html) — `docs/system/virtio-net-failover.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/virtio-net-failover.rst)
- [Direct Linux Boot](https://www.qemu.org/docs/master/system/linuxboot.html) — `docs/system/linuxboot.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/linuxboot.rst)
- [Generic Loader](https://www.qemu.org/docs/master/system/generic-loader.html) — `docs/system/generic-loader.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/generic-loader.rst)
- [Guest Loader](https://www.qemu.org/docs/master/system/guest-loader.html) — `docs/system/guest-loader.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/guest-loader.rst)
- [QEMU Barrier Client](https://www.qemu.org/docs/master/system/barrier.html) — `docs/system/barrier.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/barrier.rst)
- [VNC security](https://www.qemu.org/docs/master/system/vnc-security.html) — `docs/system/vnc-security.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/vnc-security.rst)
- [TLS setup for network services](https://www.qemu.org/docs/master/system/tls.html) — `docs/system/tls.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/tls.rst)
- [Providing secret data to QEMU](https://www.qemu.org/docs/master/system/secrets.html) — `docs/system/secrets.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/secrets.rst)
- [Client authorization](https://www.qemu.org/docs/master/system/authz.html) — `docs/system/authz.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/authz.rst)
- [GDB usage](https://www.qemu.org/docs/master/system/gdb.html) — `docs/system/gdb.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/gdb.rst)
- [Record/replay](https://www.qemu.org/docs/master/system/replay.html) — `docs/system/replay.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/replay.rst)
- [Managed start up options](https://www.qemu.org/docs/master/system/managed-startup.html) — `docs/system/managed-startup.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/managed-startup.rst)
- [Managing device boot order with bootindex properties](https://www.qemu.org/docs/master/system/bootindex.html) — `docs/system/bootindex.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/bootindex.rst)
- [Virtual CPU hotplug](https://www.qemu.org/docs/master/system/cpu-hotplug.html) — `docs/system/cpu-hotplug.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/cpu-hotplug.rst)
- [Persistent reservation managers](https://www.qemu.org/docs/master/system/pr-manager.html) — `docs/system/pr-manager.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/pr-manager.rst)
- [QEMU System Emulator Targets](https://www.qemu.org/docs/master/system/targets.html) — `docs/system/targets.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/targets.rst)
- [Arm System emulator](https://www.qemu.org/docs/master/system/target-arm.html) — `docs/system/target-arm.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-arm.rst)
- [Analog Devices max78000 board (max78000fthr)](https://www.qemu.org/docs/master/system/arm/max78000.html) — `docs/system/arm/max78000.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/max78000.rst)
- [Arm Integrator/CP (integratorcp)](https://www.qemu.org/docs/master/system/arm/integratorcp.html) — `docs/system/arm/integratorcp.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/integratorcp.rst)
- [Arm MPS2 and MPS3 boards (mps2-an385, mps2-an386, mps2-an500, mps2-an505, mps2-an511, mps2-an521, mps3-an524, mps3-an536, mps3-an547)](https://www.qemu.org/docs/master/system/arm/mps2.html) — `docs/system/arm/mps2.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/mps2.rst)
- [Arm Musca boards (musca-a, musca-b1)](https://www.qemu.org/docs/master/system/arm/musca.html) — `docs/system/arm/musca.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/musca.rst)
- [Arm Realview boards (realview-eb, realview-eb-mpcore, realview-pb-a8, realview-pbx-a9)](https://www.qemu.org/docs/master/system/arm/realview.html) — `docs/system/arm/realview.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/realview.rst)
- [Arm Server Base System Architecture Reference board (sbsa-ref)](https://www.qemu.org/docs/master/system/arm/sbsa.html) — `docs/system/arm/sbsa.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/sbsa.rst)
- [Arm Versatile boards (versatileab, versatilepb)](https://www.qemu.org/docs/master/system/arm/versatile.html) — `docs/system/arm/versatile.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/versatile.rst)
- [Arm Versatile Express boards (vexpress-a9, vexpress-a15)](https://www.qemu.org/docs/master/system/arm/vexpress.html) — `docs/system/arm/vexpress.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/vexpress.rst)
- [Aspeed family boards (ast2500-evb, ast2600-evb, bletchley-bmc, fuji-bmc, gb200nvl-bmc, fby35-bmc, fp5280g2-bmc, g220a-bmc, palmetto-bmc, qcom-dc-scm-v1-bmc, qcom-firework-bmc, quanta-q71l-bmc, rainier-bmc, romulus-bmc, sonorapass-bmc, supermicrox11-bmc, supermicrox11spi-bmc, tiogapass-bmc, witherspoon-bmc, yosemitev2-bmc)](https://www.qemu.org/docs/master/system/arm/aspeed.html) — `docs/system/arm/aspeed.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/aspeed.rst)
- [Banana Pi BPI-M2U (bpim2u)](https://www.qemu.org/docs/master/system/arm/bananapi_m2u.html) — `docs/system/arm/bananapi_m2u.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/bananapi_m2u.rst)
- [B-L475E-IOT01A IoT Node (b-l475e-iot01a)](https://www.qemu.org/docs/master/system/arm/b-l475e-iot01a.html) — `docs/system/arm/b-l475e-iot01a.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/b-l475e-iot01a.rst)
- [Boundary Devices SABRE Lite (sabrelite)](https://www.qemu.org/docs/master/system/arm/sabrelite.html) — `docs/system/arm/sabrelite.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/sabrelite.rst)
- [Canon A1100 (canon-a1100)](https://www.qemu.org/docs/master/system/arm/digic.html) — `docs/system/arm/digic.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/digic.rst)
- [Cubietech Cubieboard (cubieboard)](https://www.qemu.org/docs/master/system/arm/cubieboard.html) — `docs/system/arm/cubieboard.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/cubieboard.rst)
- [Emcraft SmartFusion2 SOM kit (emcraft-sf2)](https://www.qemu.org/docs/master/system/arm/emcraft-sf2.html) — `docs/system/arm/emcraft-sf2.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/emcraft-sf2.rst)
- [Exynos4 boards (nuri, smdkc210)](https://www.qemu.org/docs/master/system/arm/exynos.html) — `docs/system/arm/exynos.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/exynos.rst)
- [Facebook Yosemite v3.5 Platform and CraterLake Server (fby35)](https://www.qemu.org/docs/master/system/arm/fby35.html) — `docs/system/arm/fby35.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/fby35.rst)
- [Freecom MusicPal (musicpal)](https://www.qemu.org/docs/master/system/arm/musicpal.html) — `docs/system/arm/musicpal.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/musicpal.rst)
- [Kyoto Microcomputer KZM-ARM11-01 (kzm)](https://www.qemu.org/docs/master/system/arm/kzm.html) — `docs/system/arm/kzm.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/kzm.rst)
- [Nordic nRF boards (microbit)](https://www.qemu.org/docs/master/system/arm/nrf.html) — `docs/system/arm/nrf.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/nrf.rst)
- [Nuvoton iBMC boards (kudo-bmc, mori-bmc, npcm750-evb, quanta-gbs-bmc, quanta-gsj, npcm845-evb)](https://www.qemu.org/docs/master/system/arm/nuvoton.html) — `docs/system/arm/nuvoton.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/nuvoton.rst)
- [NXP i.MX25 PDK board (imx25-pdk)](https://www.qemu.org/docs/master/system/arm/imx25-pdk.html) — `docs/system/arm/imx25-pdk.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/imx25-pdk.rst)
- [NXP MCIMX6UL-EVK (mcimx6ul-evk)](https://www.qemu.org/docs/master/system/arm/mcimx6ul-evk.html) — `docs/system/arm/mcimx6ul-evk.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/mcimx6ul-evk.rst)
- [NXP MCIMX7D Sabre (mcimx7d-sabre)](https://www.qemu.org/docs/master/system/arm/mcimx7d-sabre.html) — `docs/system/arm/mcimx7d-sabre.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/mcimx7d-sabre.rst)
- [NXP i.MX 8M Plus Evaluation Kit (imx8mp-evk)](https://www.qemu.org/docs/master/system/arm/imx8mp-evk.html) — `docs/system/arm/imx8mp-evk.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/imx8mp-evk.rst)
- [Orange Pi PC (orangepi-pc)](https://www.qemu.org/docs/master/system/arm/orangepi.html) — `docs/system/arm/orangepi.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/orangepi.rst)
- [Raspberry Pi boards (raspi0, raspi1ap, raspi2b, raspi3ap, raspi3b, raspi4b)](https://www.qemu.org/docs/master/system/arm/raspi.html) — `docs/system/arm/raspi.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/raspi.rst)
- [Sharp Zaurus SL-5500 (collie)](https://www.qemu.org/docs/master/system/arm/collie.html) — `docs/system/arm/collie.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/collie.rst)
- [Siemens SX1 (sx1, sx1-v1)](https://www.qemu.org/docs/master/system/arm/sx1.html) — `docs/system/arm/sx1.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/sx1.rst)
- [Stellaris boards (lm3s6965evb, lm3s811evb)](https://www.qemu.org/docs/master/system/arm/stellaris.html) — `docs/system/arm/stellaris.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/stellaris.rst)
- [STMicroelectronics STM32 boards (netduino2, netduinoplus2, olimex-stm32-h405, stm32vldiscovery)](https://www.qemu.org/docs/master/system/arm/stm32.html) — `docs/system/arm/stm32.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/stm32.rst)
- ['virt' generic virtual platform (virt)](https://www.qemu.org/docs/master/system/arm/virt.html) — `docs/system/arm/virt.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/virt.rst)
- [VMApple machine emulation](https://www.qemu.org/docs/master/system/arm/vmapple.html) — `docs/system/arm/vmapple.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/vmapple.rst)
- [Xen Device Emulation Backend (xenpvh)](https://www.qemu.org/docs/master/system/arm/xenpvh.html) — `docs/system/arm/xenpvh.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/xenpvh.rst)
- [AMD Versal Virt (amd-versal-virt, amd-versal2-virt)](https://www.qemu.org/docs/master/system/arm/xlnx-versal-virt.html) — `docs/system/arm/xlnx-versal-virt.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/xlnx-versal-virt.rst)
- [Xilinx Zynq board (xilinx-zynq-a9)](https://www.qemu.org/docs/master/system/arm/xlnx-zynq.html) — `docs/system/arm/xlnx-zynq.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/xlnx-zynq.rst)
- [Xilinx ZynqMP ZCU102 (xlnx-zcu102)](https://www.qemu.org/docs/master/system/arm/xlnx-zcu102.html) — `docs/system/arm/xlnx-zcu102.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/xlnx-zcu102.rst)
- [A-profile CPU architecture support](https://www.qemu.org/docs/master/system/arm/emulation.html) — `docs/system/arm/emulation.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/emulation.rst)
- [Arm CPU Features](https://www.qemu.org/docs/master/system/arm/cpu-features.html) — `docs/system/arm/cpu-features.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/arm/cpu-features.rst)
- [AVR System emulator](https://www.qemu.org/docs/master/system/target-avr.html) — `docs/system/target-avr.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-avr.rst)
- [LoongArch System emulator](https://www.qemu.org/docs/master/system/target-loongarch.html) — `docs/system/target-loongarch.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-loongarch.rst)
- [loongson3 virt generic platform (virt)](https://www.qemu.org/docs/master/system/loongarch/virt.html) — `docs/system/loongarch/virt.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/loongarch/virt.rst)
- [ColdFire System emulator](https://www.qemu.org/docs/master/system/target-m68k.html) — `docs/system/target-m68k.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-m68k.rst)
- [MIPS System emulator](https://www.qemu.org/docs/master/system/target-mips.html) — `docs/system/target-mips.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-mips.rst)
- [OpenRISC System emulator](https://www.qemu.org/docs/master/system/target-or1k.html) — `docs/system/target-or1k.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-or1k.rst)
- [Or1ksim board](https://www.qemu.org/docs/master/system/or1k/or1k-sim.html) — `docs/system/or1k/or1k-sim.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/or1k/or1k-sim.rst)
- ['virt' generic virtual platform](https://www.qemu.org/docs/master/system/or1k/virt.html) — `docs/system/or1k/virt.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/or1k/virt.rst)
- [OpenRISC 1000 CPU architecture support](https://www.qemu.org/docs/master/system/or1k/emulation.html) — `docs/system/or1k/emulation.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/or1k/emulation.rst)
- [CPU Features](https://www.qemu.org/docs/master/system/or1k/cpu-features.html) — `docs/system/or1k/cpu-features.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/or1k/cpu-features.rst)
- [PowerPC System emulator](https://www.qemu.org/docs/master/system/target-ppc.html) — `docs/system/target-ppc.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-ppc.rst)
- [AmigaNG boards (amigaone, pegasos1, pegasos2, sam460ex)](https://www.qemu.org/docs/master/system/ppc/amigang.html) — `docs/system/ppc/amigang.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/ppc/amigang.rst)
- [Embedded family boards](https://www.qemu.org/docs/master/system/ppc/embedded.html) — `docs/system/ppc/embedded.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/ppc/embedded.rst)
- [PowerMac family boards (g3beige, mac99)](https://www.qemu.org/docs/master/system/ppc/powermac.html) — `docs/system/ppc/powermac.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/ppc/powermac.rst)
- [PowerNV family boards (powernv8, powernv9, powernv10, powernv11)](https://www.qemu.org/docs/master/system/ppc/powernv.html) — `docs/system/ppc/powernv.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/ppc/powernv.rst)
- [ppce500 generic platform (ppce500)](https://www.qemu.org/docs/master/system/ppc/ppce500.html) — `docs/system/ppc/ppce500.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/ppc/ppce500.rst)
- [Prep machine (40p)](https://www.qemu.org/docs/master/system/ppc/prep.html) — `docs/system/ppc/prep.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/ppc/prep.rst)
- [pSeries family boards (pseries)](https://www.qemu.org/docs/master/system/ppc/pseries.html) — `docs/system/ppc/pseries.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/ppc/pseries.rst)
- [RISC-V System emulator](https://www.qemu.org/docs/master/system/target-riscv.html) — `docs/system/target-riscv.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-riscv.rst)
- [Microblaze-V generic board (amd-microblaze-v-generic)](https://www.qemu.org/docs/master/system/riscv/microblaze-v-generic.html) — `docs/system/riscv/microblaze-v-generic.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/riscv/microblaze-v-generic.rst)
- [Microchip PolarFire SoC Icicle Kit (microchip-icicle-kit)](https://www.qemu.org/docs/master/system/riscv/microchip-icicle-kit.html) — `docs/system/riscv/microchip-icicle-kit.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/riscv/microchip-icicle-kit.rst)
- [Boards for RISC-V Processors by MIPS](https://www.qemu.org/docs/master/system/riscv/mips.html) — `docs/system/riscv/mips.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/riscv/mips.rst)
- [Shakti C Reference Platform (shakti_c)](https://www.qemu.org/docs/master/system/riscv/shakti-c.html) — `docs/system/riscv/shakti-c.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/riscv/shakti-c.rst)
- [SiFive HiFive Unleashed (sifive_u)](https://www.qemu.org/docs/master/system/riscv/sifive_u.html) — `docs/system/riscv/sifive_u.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/riscv/sifive_u.rst)
- ['virt' Generic Virtual Platform (virt)](https://www.qemu.org/docs/master/system/riscv/virt.html) — `docs/system/riscv/virt.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/riscv/virt.rst)
- [BOSC Xiangshan Kunminghu FPGA prototype platform (xiangshan-kunminghu)](https://www.qemu.org/docs/master/system/riscv/xiangshan-kunminghu.html) — `docs/system/riscv/xiangshan-kunminghu.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/riscv/xiangshan-kunminghu.rst)
- [RX System emulator](https://www.qemu.org/docs/master/system/target-rx.html) — `docs/system/target-rx.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-rx.rst)
- [s390x System emulator](https://www.qemu.org/docs/master/system/target-s390x.html) — `docs/system/target-s390x.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-s390x.rst)
- [Adjunct Processor (AP) Device](https://www.qemu.org/docs/master/system/s390x/vfio-ap.html) — `docs/system/s390x/vfio-ap.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/vfio-ap.rst)
- [The virtual channel subsystem](https://www.qemu.org/docs/master/system/s390x/css.html) — `docs/system/s390x/css.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/css.rst)
- [3270 devices](https://www.qemu.org/docs/master/system/s390x/3270.html) — `docs/system/s390x/3270.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/3270.rst)
- [Subchannel passthrough via vfio-ccw](https://www.qemu.org/docs/master/system/s390x/vfio-ccw.html) — `docs/system/s390x/vfio-ccw.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/vfio-ccw.rst)
- [PCI devices on s390x](https://www.qemu.org/docs/master/system/s390x/pcidevices.html) — `docs/system/s390x/pcidevices.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/pcidevices.rst)
- [Boot devices on s390x](https://www.qemu.org/docs/master/system/s390x/bootdevices.html) — `docs/system/s390x/bootdevices.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/bootdevices.rst)
- [Protected Virtualization on s390x](https://www.qemu.org/docs/master/system/s390x/protvirt.html) — `docs/system/s390x/protvirt.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/protvirt.rst)
- [CPU topology on s390x](https://www.qemu.org/docs/master/system/s390x/cpu-topology.html) — `docs/system/s390x/cpu-topology.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/s390x/cpu-topology.rst)
- [Sparc32 System emulator](https://www.qemu.org/docs/master/system/target-sparc.html) — `docs/system/target-sparc.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-sparc.rst)
- [Sparc64 System emulator](https://www.qemu.org/docs/master/system/target-sparc64.html) — `docs/system/target-sparc64.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-sparc64.rst)
- [x86 System emulator](https://www.qemu.org/docs/master/system/target-i386.html) — `docs/system/target-i386.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-i386.rst)
- [i440fx PC (pc-i440fx, pc)](https://www.qemu.org/docs/master/system/i386/pc.html) — `docs/system/i386/pc.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/pc.rst)
- ['microvm' virtual platform (microvm)](https://www.qemu.org/docs/master/system/i386/microvm.html) — `docs/system/i386/microvm.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/microvm.rst)
- ['nitro-enclave' virtual machine (nitro-enclave)](https://www.qemu.org/docs/master/system/i386/nitro-enclave.html) — `docs/system/i386/nitro-enclave.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/nitro-enclave.rst)
- [Cpu](https://www.qemu.org/docs/master/system/i386/cpu.html) — `docs/system/i386/cpu.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/cpu.rst)
- [Hyper-V Enlightenments](https://www.qemu.org/docs/master/system/i386/hyperv.html) — `docs/system/i386/hyperv.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/hyperv.rst)
- [Xen HVM guest support](https://www.qemu.org/docs/master/system/i386/xen.html) — `docs/system/i386/xen.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/xen.rst)
- [Xen PVH machine (xenpvh)](https://www.qemu.org/docs/master/system/i386/xenpvh.html) — `docs/system/i386/xenpvh.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/xenpvh.rst)
- [Paravirtualized KVM features](https://www.qemu.org/docs/master/system/i386/kvm-pv.html) — `docs/system/i386/kvm-pv.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/kvm-pv.rst)
- [Software Guard eXtensions (SGX)](https://www.qemu.org/docs/master/system/i386/sgx.html) — `docs/system/i386/sgx.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/sgx.rst)
- [AMD Secure Encrypted Virtualization (SEV)](https://www.qemu.org/docs/master/system/i386/amd-memory-encryption.html) — `docs/system/i386/amd-memory-encryption.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/amd-memory-encryption.rst)
- [Intel Trusted Domain eXtension (TDX)](https://www.qemu.org/docs/master/system/i386/tdx.html) — `docs/system/i386/tdx.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/i386/tdx.rst)
- [Xtensa System emulator](https://www.qemu.org/docs/master/system/target-xtensa.html) — `docs/system/target-xtensa.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/target-xtensa.rst)
- [Security](https://www.qemu.org/docs/master/system/security.html) — `docs/system/security.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/security.rst)
- [Multi-process QEMU](https://www.qemu.org/docs/master/system/multi-process.html) — `docs/system/multi-process.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/multi-process.rst)
- [Confidential Guest Support](https://www.qemu.org/docs/master/system/confidential-guest-support.html) — `docs/system/confidential-guest-support.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/confidential-guest-support.rst)
- [Independent Guest Virtual Machine (IGVM) support](https://www.qemu.org/docs/master/system/igvm.html) — `docs/system/igvm.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/igvm.rst)
- [AWS Nitro Enclaves](https://www.qemu.org/docs/master/system/nitro.html) — `docs/system/nitro.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/nitro.rst)
- [Windows Hypervisor Platform](https://www.qemu.org/docs/master/system/whpx.html) — `docs/system/whpx.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/whpx.rst)
- [QEMU VM templating](https://www.qemu.org/docs/master/system/vm-templating.html) — `docs/system/vm-templating.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/vm-templating.rst)
- [Composable SR-IOV device](https://www.qemu.org/docs/master/system/sriov.html) — `docs/system/sriov.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/sriov.rst)
- [Qemu COLO Fault Tolerance](https://www.qemu.org/docs/master/system/qemu-colo.html) — `docs/system/qemu-colo.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/system/qemu-colo.rst)

## E.4 User-mode emulation

- [User Mode Emulation](https://www.qemu.org/docs/master/user/index.html) — `docs/user/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/user/index.rst)
- [QEMU User space emulator](https://www.qemu.org/docs/master/user/main.html) — `docs/user/main.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/user/main.rst)

## E.5 Tools

- [Tools](https://www.qemu.org/docs/master/tools/index.html) — `docs/tools/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/tools/index.rst)
- [QEMU disk image utility](https://www.qemu.org/docs/master/tools/qemu-img.html) — `docs/tools/qemu-img.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/tools/qemu-img.rst)
- [QEMU Storage Daemon](https://www.qemu.org/docs/master/tools/qemu-storage-daemon.html) — `docs/tools/qemu-storage-daemon.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/tools/qemu-storage-daemon.rst)
- [QEMU Disk Network Block Device Server](https://www.qemu.org/docs/master/tools/qemu-nbd.html) — `docs/tools/qemu-nbd.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/tools/qemu-nbd.rst)
- [QEMU persistent reservation helper](https://www.qemu.org/docs/master/tools/qemu-pr-helper.html) — `docs/tools/qemu-pr-helper.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/tools/qemu-pr-helper.rst)
- [QEMU SystemTap trace tool](https://www.qemu.org/docs/master/tools/qemu-trace-stap.html) — `docs/tools/qemu-trace-stap.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/tools/qemu-trace-stap.rst)
- [QEMU virtual RAPL MSR helper](https://www.qemu.org/docs/master/tools/qemu-vmsr-helper.html) — `docs/tools/qemu-vmsr-helper.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/tools/qemu-vmsr-helper.rst)

## E.6 Management and interoperability

- [System Emulation Management and Interoperability](https://www.qemu.org/docs/master/interop/index.html) — `docs/interop/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/index.rst)
- [Barrier client protocol](https://www.qemu.org/docs/master/interop/barrier.html) — `docs/interop/barrier.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/barrier.rst)
- [Dirty Bitmaps and Incremental Backup](https://www.qemu.org/docs/master/interop/bitmaps.html) — `docs/interop/bitmaps.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/bitmaps.rst)
- [D-Bus](https://www.qemu.org/docs/master/interop/dbus.html) — `docs/interop/dbus.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/dbus.rst)
- [D-Bus VMState](https://www.qemu.org/docs/master/interop/dbus-vmstate.html) — `docs/interop/dbus-vmstate.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/dbus-vmstate.rst)
- [D-Bus display](https://www.qemu.org/docs/master/interop/dbus-display.html) — `docs/interop/dbus-display.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/dbus-display.rst)
- [Live Block Device Operations](https://www.qemu.org/docs/master/interop/live-block-operations.html) — `docs/interop/live-block-operations.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/live-block-operations.rst)
- [QEMU NBD protocol support](https://www.qemu.org/docs/master/interop/nbd.html) — `docs/interop/nbd.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/nbd.rst)
- [Parallels Expandable Image File Format](https://www.qemu.org/docs/master/interop/parallels.html) — `docs/interop/parallels.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/parallels.rst)
- [Parallels Disk Format](https://www.qemu.org/docs/master/interop/prl-xml.html) — `docs/interop/prl-xml.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/prl-xml.rst)
- [Qcow2 Image File Format](https://www.qemu.org/docs/master/interop/qcow2.html) — `docs/interop/qcow2.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/qcow2.rst)
- [QED Image File Format Specification](https://www.qemu.org/docs/master/interop/qed_spec.html) — `docs/interop/qed_spec.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/qed_spec.rst)
- [Persistent reservation helper protocol](https://www.qemu.org/docs/master/interop/pr-helper.html) — `docs/interop/pr-helper.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/pr-helper.rst)
- [QEMU Machine Protocol Specification](https://www.qemu.org/docs/master/interop/qmp-spec.html) — `docs/interop/qmp-spec.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/qmp-spec.rst)
- [QEMU Guest Agent](https://www.qemu.org/docs/master/interop/qemu-ga.html) — `docs/interop/qemu-ga.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/qemu-ga.rst)
- [QEMU Guest Agent Protocol Reference](https://www.qemu.org/docs/master/interop/qemu-ga-ref.html) — `docs/interop/qemu-ga-ref.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/qemu-ga-ref.rst)
- [QEMU QMP Reference Manual](https://www.qemu.org/docs/master/interop/qemu-qmp-ref.html) — `docs/interop/qemu-qmp-ref.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/qemu-qmp-ref.rst)
- [QEMU Storage Daemon QMP Reference Manual](https://www.qemu.org/docs/master/interop/qemu-storage-daemon-qmp-ref.html) — `docs/interop/qemu-storage-daemon-qmp-ref.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/qemu-storage-daemon-qmp-ref.rst)
- [vfio-user Protocol Specification](https://www.qemu.org/docs/master/interop/vfio-user.html) — `docs/interop/vfio-user.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/vfio-user.rst)
- [Vhost-user Protocol](https://www.qemu.org/docs/master/interop/vhost-user.html) — `docs/interop/vhost-user.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/vhost-user.rst)
- [Vhost-user-gpu Protocol](https://www.qemu.org/docs/master/interop/vhost-user-gpu.html) — `docs/interop/vhost-user-gpu.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/vhost-user-gpu.rst)
- [Vhost-vdpa Protocol](https://www.qemu.org/docs/master/interop/vhost-vdpa.html) — `docs/interop/vhost-vdpa.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/vhost-vdpa.rst)
- [Virtio balloon memory statistics](https://www.qemu.org/docs/master/interop/virtio-balloon-stats.html) — `docs/interop/virtio-balloon-stats.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/virtio-balloon-stats.rst)
- [VNC LED state Pseudo-encoding](https://www.qemu.org/docs/master/interop/vnc-ledstate-pseudo-encoding.html) — `docs/interop/vnc-ledstate-pseudo-encoding.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/interop/vnc-ledstate-pseudo-encoding.rst)

## E.7 Guest hardware specifications

- [System Emulation Guest Hardware Specifications](https://www.qemu.org/docs/master/specs/index.html) — `docs/specs/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/index.rst)
- [PCI IDs for QEMU](https://www.qemu.org/docs/master/specs/pci-ids.html) — `docs/specs/pci-ids.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/pci-ids.rst)
- [QEMU PCI serial devices](https://www.qemu.org/docs/master/specs/pci-serial.html) — `docs/specs/pci-serial.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/pci-serial.rst)
- [QEMU PCI test device](https://www.qemu.org/docs/master/specs/pci-testdev.html) — `docs/specs/pci-testdev.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/pci-testdev.rst)
- [POWER9 XIVE interrupt controller](https://www.qemu.org/docs/master/specs/ppc-xive.html) — `docs/specs/ppc-xive.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/ppc-xive.rst)
- [XIVE for sPAPR (pseries machines)](https://www.qemu.org/docs/master/specs/ppc-spapr-xive.html) — `docs/specs/ppc-spapr-xive.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/ppc-spapr-xive.rst)
- [NUMA mechanics for sPAPR (pseries machines)](https://www.qemu.org/docs/master/specs/ppc-spapr-numa.html) — `docs/specs/ppc-spapr-numa.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/ppc-spapr-numa.rst)
- [QEMU and ACPI BIOS Generic Event Device interface](https://www.qemu.org/docs/master/specs/acpi_hw_reduced_hotplug.html) — `docs/specs/acpi_hw_reduced_hotplug.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/acpi_hw_reduced_hotplug.rst)
- [QEMU TPM Device](https://www.qemu.org/docs/master/specs/tpm.html) — `docs/specs/tpm.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/tpm.rst)
- [APEI tables generating and CPER record](https://www.qemu.org/docs/master/specs/acpi_hest_ghes.html) — `docs/specs/acpi_hest_ghes.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/acpi_hest_ghes.rst)
- [QEMU<->ACPI BIOS CPU hotplug interface](https://www.qemu.org/docs/master/specs/acpi_cpu_hotplug.html) — `docs/specs/acpi_cpu_hotplug.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/acpi_cpu_hotplug.rst)
- [QEMU<->ACPI BIOS memory hotplug interface](https://www.qemu.org/docs/master/specs/acpi_mem_hotplug.html) — `docs/specs/acpi_mem_hotplug.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/acpi_mem_hotplug.rst)
- [QEMU<->ACPI BIOS PCI hotplug interface](https://www.qemu.org/docs/master/specs/acpi_pci_hotplug.html) — `docs/specs/acpi_pci_hotplug.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/acpi_pci_hotplug.rst)
- [QEMU<->ACPI BIOS NVDIMM interface](https://www.qemu.org/docs/master/specs/acpi_nvdimm.html) — `docs/specs/acpi_nvdimm.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/acpi_nvdimm.rst)
- [ACPI ERST DEVICE](https://www.qemu.org/docs/master/specs/acpi_erst.html) — `docs/specs/acpi_erst.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/acpi_erst.rst)
- [QEMU/Guest Firmware Interface for AMD SEV and SEV-ES](https://www.qemu.org/docs/master/specs/sev-guest-firmware.html) — `docs/specs/sev-guest-firmware.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/sev-guest-firmware.rst)
- [QEMU Firmware Configuration (fw_cfg) Device](https://www.qemu.org/docs/master/specs/fw_cfg.html) — `docs/specs/fw_cfg.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/fw_cfg.rst)
- [IBM's Flexible Service Interface (FSI)](https://www.qemu.org/docs/master/specs/fsi.html) — `docs/specs/fsi.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/fsi.rst)
- [VMWare PVSCSI Device Interface](https://www.qemu.org/docs/master/specs/vmw_pvscsi-spec.html) — `docs/specs/vmw_pvscsi-spec.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/vmw_pvscsi-spec.rst)
- [EDU device](https://www.qemu.org/docs/master/specs/edu.html) — `docs/specs/edu.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/edu.rst)
- [Device Specification for Inter-VM shared memory device](https://www.qemu.org/docs/master/specs/ivshmem-spec.html) — `docs/specs/ivshmem-spec.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/ivshmem-spec.rst)
- [PVPANIC DEVICE](https://www.qemu.org/docs/master/specs/pvpanic.html) — `docs/specs/pvpanic.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/pvpanic.rst)
- [QEMU Security Protocols and Data Models (SPDM) Support](https://www.qemu.org/docs/master/specs/spdm.html) — `docs/specs/spdm.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/spdm.rst)
- [QEMU Standard VGA](https://www.qemu.org/docs/master/specs/standard-vga.html) — `docs/specs/standard-vga.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/standard-vga.rst)
- [Virtual System Controller](https://www.qemu.org/docs/master/specs/virt-ctlr.html) — `docs/specs/virt-ctlr.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/virt-ctlr.rst)
- [VMCoreInfo device](https://www.qemu.org/docs/master/specs/vmcoreinfo.html) — `docs/specs/vmcoreinfo.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/vmcoreinfo.rst)
- [Virtual Machine Generation ID Device](https://www.qemu.org/docs/master/specs/vmgenid.html) — `docs/specs/vmgenid.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/vmgenid.rst)
- [RAPL MSR support](https://www.qemu.org/docs/master/specs/rapl-msr.html) — `docs/specs/rapl-msr.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/rapl-msr.rst)
- [Rocker Network Switch Register Programming Guide](https://www.qemu.org/docs/master/specs/rocker.html) — `docs/specs/rocker.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/rocker.rst)
- [RISC-V IOMMU support for RISC-V machines](https://www.qemu.org/docs/master/specs/riscv-iommu.html) — `docs/specs/riscv-iommu.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/riscv-iommu.rst)
- [RISC-V AIA support for RISC-V machines](https://www.qemu.org/docs/master/specs/riscv-aia.html) — `docs/specs/riscv-aia.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/riscv-aia.rst)
- [ASPEED Interrupt Controller](https://www.qemu.org/docs/master/specs/aspeed-intc.html) — `docs/specs/aspeed-intc.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/aspeed-intc.rst)
- [iommu-testdev — IOMMU test device for bare-metal testing](https://www.qemu.org/docs/master/specs/iommu-testdev.html) — `docs/specs/iommu-testdev.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/specs/iommu-testdev.rst)

## E.8 Developer information

- [Developer Information](https://www.qemu.org/docs/master/devel/index.html) — `docs/devel/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/index.rst)
- [QEMU Community Processes](https://www.qemu.org/docs/master/devel/index-process.html) — `docs/devel/index-process.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/index-process.rst)
- [Code of Conduct](https://www.qemu.org/docs/master/devel/code-of-conduct.html) — `docs/devel/code-of-conduct.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/code-of-conduct.rst)
- [Conflict Resolution Policy](https://www.qemu.org/docs/master/devel/conflict-resolution.html) — `docs/devel/conflict-resolution.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/conflict-resolution.rst)
- [The Role of Maintainers](https://www.qemu.org/docs/master/devel/maintainers.html) — `docs/devel/maintainers.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/maintainers.rst)
- [QEMU Coding Style](https://www.qemu.org/docs/master/devel/style.html) — `docs/devel/style.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/style.rst)
- [Submitting a Patch](https://www.qemu.org/docs/master/devel/submitting-a-patch.html) — `docs/devel/submitting-a-patch.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/submitting-a-patch.rst)
- [Code provenance](https://www.qemu.org/docs/master/devel/code-provenance.html) — `docs/devel/code-provenance.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/code-provenance.rst)
- [Trivial Patches](https://www.qemu.org/docs/master/devel/trivial-patches.html) — `docs/devel/trivial-patches.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/trivial-patches.rst)
- [QEMU and the stable process](https://www.qemu.org/docs/master/devel/stable-process.html) — `docs/devel/stable-process.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/stable-process.rst)
- [Submitting a Pull Request](https://www.qemu.org/docs/master/devel/submitting-a-pull-request.html) — `docs/devel/submitting-a-pull-request.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/submitting-a-pull-request.rst)
- [Secure Coding Practices](https://www.qemu.org/docs/master/devel/secure-coding-practices.html) — `docs/devel/secure-coding-practices.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/secure-coding-practices.rst)
- [Rust in QEMU](https://www.qemu.org/docs/master/devel/rust.html) — `docs/devel/rust.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/rust.rst)
- [QEMU Build System](https://www.qemu.org/docs/master/devel/index-build.html) — `docs/devel/index-build.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/index-build.rst)
- [The QEMU build system architecture](https://www.qemu.org/docs/master/devel/build-system.html) — `docs/devel/build-system.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/build-system.rst)
- [Setup build environment](https://www.qemu.org/docs/master/devel/build-environment.html) — `docs/devel/build-environment.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/build-environment.rst)
- [QEMU and Kconfig](https://www.qemu.org/docs/master/devel/kconfig.html) — `docs/devel/kconfig.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/kconfig.rst)
- [QEMU Documentation](https://www.qemu.org/docs/master/devel/docs.html) — `docs/devel/docs.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/docs.rst)
- [How to use the QAPI code generator](https://www.qemu.org/docs/master/devel/qapi-code-gen.html) — `docs/devel/qapi-code-gen.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/qapi-code-gen.rst)
- [The Sphinx QAPI Domain](https://www.qemu.org/docs/master/devel/qapi-domain.html) — `docs/devel/qapi-domain.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/qapi-domain.rst)
- [Control-Flow Integrity (CFI)](https://www.qemu.org/docs/master/devel/control-flow-integrity.html) — `docs/devel/control-flow-integrity.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/control-flow-integrity.rst)
- [Testing QEMU](https://www.qemu.org/docs/master/devel/testing/index.html) — `docs/devel/testing/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/index.rst)
- [Testing in QEMU](https://www.qemu.org/docs/master/devel/testing/main.html) — `docs/devel/testing/main.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/main.rst)
- [QTest Device Emulation Testing Framework](https://www.qemu.org/docs/master/devel/testing/qtest.html) — `docs/devel/testing/qtest.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/qtest.rst)
- [Qtest Driver Framework](https://www.qemu.org/docs/master/devel/testing/qgraph.html) — `docs/devel/testing/qgraph.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/qgraph.rst)
- [Functional testing with Python](https://www.qemu.org/docs/master/devel/testing/functional.html) — `docs/devel/testing/functional.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/functional.rst)
- [ACPI/SMBIOS testing using biosbits](https://www.qemu.org/docs/master/devel/testing/acpi-bits.html) — `docs/devel/testing/acpi-bits.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/acpi-bits.rst)
- [Continuous Integration (CI)](https://www.qemu.org/docs/master/devel/testing/ci.html) — `docs/devel/testing/ci.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/ci.rst)
- [Fuzzing](https://www.qemu.org/docs/master/devel/testing/fuzzing.html) — `docs/devel/testing/fuzzing.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/fuzzing.rst)
- [Block I/O error injection using blkdebug](https://www.qemu.org/docs/master/devel/testing/blkdebug.html) — `docs/devel/testing/blkdebug.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/blkdebug.rst)
- [Block driver correctness testing with blkverify](https://www.qemu.org/docs/master/devel/testing/blkverify.html) — `docs/devel/testing/blkverify.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/testing/blkverify.rst)
- [Internal QEMU APIs](https://www.qemu.org/docs/master/devel/index-api.html) — `docs/devel/index-api.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/index-api.rst)
- [Bitwise operations](https://www.qemu.org/docs/master/devel/bitops.html) — `docs/devel/bitops.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/bitops.rst)
- [Load and Store APIs](https://www.qemu.org/docs/master/devel/loads-stores.html) — `docs/devel/loads-stores.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/loads-stores.rst)
- [Locked Counters (aka QemuLockCnt)](https://www.qemu.org/docs/master/devel/lockcnt.html) — `docs/devel/lockcnt.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/lockcnt.rst)
- [The memory API](https://www.qemu.org/docs/master/devel/memory.html) — `docs/devel/memory.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/memory.rst)
- [QEMU modules](https://www.qemu.org/docs/master/devel/modules.html) — `docs/devel/modules.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/modules.rst)
- [PCI subsystem](https://www.qemu.org/docs/master/devel/pci.html) — `docs/devel/pci.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/pci.rst)
- [QEMU Object Model (QOM) API Reference](https://www.qemu.org/docs/master/devel/qom-api.html) — `docs/devel/qom-api.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/qom-api.rst)
- [QEMU Device (qdev) API Reference](https://www.qemu.org/docs/master/devel/qdev-api.html) — `docs/devel/qdev-api.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/qdev-api.rst)
- [QEMU UI subsystem](https://www.qemu.org/docs/master/devel/ui.html) — `docs/devel/ui.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/ui.rst)
- [zoned-storage](https://www.qemu.org/docs/master/devel/zoned-storage.html) — `docs/devel/zoned-storage.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/zoned-storage.rst)
- [Internal Subsystem Information](https://www.qemu.org/docs/master/devel/index-internals.html) — `docs/devel/index-internals.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/index-internals.rst)
- [The QEMU Object Model (QOM)](https://www.qemu.org/docs/master/devel/qom.html) — `docs/devel/qom.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/qom.rst)
- [Atomic operations in QEMU](https://www.qemu.org/docs/master/devel/atomics.html) — `docs/devel/atomics.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/atomics.rst)
- [Using RCU (Read-Copy-Update) for synchronization](https://www.qemu.org/docs/master/devel/rcu.html) — `docs/devel/rcu.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/rcu.rst)
- [block-coroutine-wrapper](https://www.qemu.org/docs/master/devel/block-coroutine-wrapper.html) — `docs/devel/block-coroutine-wrapper.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/block-coroutine-wrapper.rst)
- [Modelling a clock tree in QEMU](https://www.qemu.org/docs/master/devel/clocks.html) — `docs/devel/clocks.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/clocks.rst)
- [eBPF RSS virtio-net support](https://www.qemu.org/docs/master/devel/ebpf_rss.html) — `docs/devel/ebpf_rss.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/ebpf_rss.rst)
- [Migration](https://www.qemu.org/docs/master/devel/migration/index.html) — `docs/devel/migration/index.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/index.rst)
- [Migration framework](https://www.qemu.org/docs/master/devel/migration/main.html) — `docs/devel/migration/main.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/main.rst)
- [Migration features](https://www.qemu.org/docs/master/devel/migration/features.html) — `docs/devel/migration/features.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/features.rst)
- [Postcopy](https://www.qemu.org/docs/master/devel/migration/postcopy.html) — `docs/devel/migration/postcopy.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/postcopy.rst)
- [Dirty limit](https://www.qemu.org/docs/master/devel/migration/dirty-limit.html) — `docs/devel/migration/dirty-limit.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/dirty-limit.rst)
- [VFIO device migration](https://www.qemu.org/docs/master/devel/migration/vfio.html) — `docs/devel/migration/vfio.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/vfio.rst)
- [Virtio device migration](https://www.qemu.org/docs/master/devel/migration/virtio.html) — `docs/devel/migration/virtio.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/virtio.rst)
- [Mapped-ram](https://www.qemu.org/docs/master/devel/migration/mapped-ram.html) — `docs/devel/migration/mapped-ram.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/mapped-ram.rst)
- [CheckPoint and Restart (CPR)](https://www.qemu.org/docs/master/devel/migration/CPR.html) — `docs/devel/migration/CPR.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/CPR.rst)
- [QPL Compression](https://www.qemu.org/docs/master/devel/migration/qpl-compression.html) — `docs/devel/migration/qpl-compression.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/qpl-compression.rst)
- [User Space Accelerator Development Kit (UADK) Compression](https://www.qemu.org/docs/master/devel/migration/uadk-compression.html) — `docs/devel/migration/uadk-compression.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/uadk-compression.rst)
- [QATzip Compression](https://www.qemu.org/docs/master/devel/migration/qatzip-compression.html) — `docs/devel/migration/qatzip-compression.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/qatzip-compression.rst)
- [XBZRLE (Xor Based Zero Run Length Encoding)](https://www.qemu.org/docs/master/devel/migration/xbzrle.html) — `docs/devel/migration/xbzrle.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/xbzrle.rst)
- [Backwards compatibility](https://www.qemu.org/docs/master/devel/migration/compatibility.html) — `docs/devel/migration/compatibility.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/compatibility.rst)
- [Best practices](https://www.qemu.org/docs/master/devel/migration/best-practices.html) — `docs/devel/migration/best-practices.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/migration/best-practices.rst)
- [Multi-process QEMU](https://www.qemu.org/docs/master/devel/multi-process.html) — `docs/devel/multi-process.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/multi-process.rst)
- [Reset in QEMU: the Resettable interface](https://www.qemu.org/docs/master/devel/reset.html) — `docs/devel/reset.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/reset.rst)
- [QAPI interface for S390 CPU topology](https://www.qemu.org/docs/master/devel/s390-cpu-topology.html) — `docs/devel/s390-cpu-topology.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/s390-cpu-topology.rst)
- [Booting from real channel-attached devices on s390x](https://www.qemu.org/docs/master/devel/s390-dasd-ipl.html) — `docs/devel/s390-dasd-ipl.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/s390-dasd-ipl.rst)
- [Tracing](https://www.qemu.org/docs/master/devel/tracing.html) — `docs/devel/tracing.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/tracing.rst)
- [UEFI variables](https://www.qemu.org/docs/master/devel/uefi-vars.html) — `docs/devel/uefi-vars.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/uefi-vars.rst)
- [IOMMUFD BACKEND usage with VFIO](https://www.qemu.org/docs/master/devel/vfio-iommufd.html) — `docs/devel/vfio-iommufd.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/vfio-iommufd.rst)
- [How to write monitor commands](https://www.qemu.org/docs/master/devel/writing-monitor-commands.html) — `docs/devel/writing-monitor-commands.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/writing-monitor-commands.rst)
- [Writing VirtIO backends for QEMU](https://www.qemu.org/docs/master/devel/virtio-backends.html) — `docs/devel/virtio-backends.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/virtio-backends.rst)
- [Cryptography in QEMU](https://www.qemu.org/docs/master/devel/crypto.html) — `docs/devel/crypto.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/crypto.rst)
- [LUKS volume with detached header](https://www.qemu.org/docs/master/devel/luks-detached-header.html) — `docs/devel/luks-detached-header.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/luks-detached-header.rst)
- [Using Multiple IOThread s](https://www.qemu.org/docs/master/devel/multiple-iothreads.html) — `docs/devel/multiple-iothreads.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/multiple-iothreads.rst)
- [TCG Emulation](https://www.qemu.org/docs/master/devel/index-tcg.html) — `docs/devel/index-tcg.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/index-tcg.rst)
- [Translator Internals](https://www.qemu.org/docs/master/devel/tcg.html) — `docs/devel/tcg.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/tcg.rst)
- [TCG Intermediate Representation](https://www.qemu.org/docs/master/devel/tcg-ops.html) — `docs/devel/tcg-ops.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/tcg-ops.rst)
- [Decodetree Specification](https://www.qemu.org/docs/master/devel/decodetree.html) — `docs/devel/decodetree.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/decodetree.rst)
- [Multi-threaded TCG](https://www.qemu.org/docs/master/devel/multi-thread-tcg.html) — `docs/devel/multi-thread-tcg.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/multi-thread-tcg.rst)
- [TCG Instruction Counting](https://www.qemu.org/docs/master/devel/tcg-icount.html) — `docs/devel/tcg-icount.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/tcg-icount.rst)
- [QEMU TCG Plugins](https://www.qemu.org/docs/master/devel/tcg-plugins.html) — `docs/devel/tcg-plugins.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/tcg-plugins.rst)
- [Execution Record/Replay](https://www.qemu.org/docs/master/devel/replay.html) — `docs/devel/replay.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/replay.rst)
- [Codebase](https://www.qemu.org/docs/master/devel/codebase.html) — `docs/devel/codebase.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/devel/codebase.rst)

## E.9 Glossary

- [Glossary](https://www.qemu.org/docs/master/glossary.html) — `docs/glossary.rst` · [QEMU 11.0.3 source](https://gitlab.com/qemu-project/qemu/-/blob/v11.0.3/docs/glossary.rst)

**Indexed pages: 305.**


---

## End of book

When exact behavior and this book differ, the selected QEMU binary's runtime help, QMP schema, tagged source, and corresponding official manual are authoritative—in that order for the actual deployed build.
