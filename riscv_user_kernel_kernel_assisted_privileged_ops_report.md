# Building a RISC-V User-Space Linux Kernel With Kernel-Assisted Privileged Operations

## Executive summary

What you want is feasible, but only under a very specific interpretation of “privileged instructions execute in kernel mode.” A RISC‑V Linux kernel binary running as a normal Linux user process will execute all ordinary unprivileged RISC‑V instructions natively on the host core, because the guest kernel process is itself just a RISC‑V userspace program. However, any supervisor-only CSR access or supervisor-only instruction executed in U-mode will trap as an illegal instruction, and stock Linux on RISC‑V turns such user-mode illegal-instruction traps into `SIGILL` for the process rather than “executing the instruction on its behalf.”

The most important design constraint is that many supervisor instructions cannot be “literally” re-executed by the host kernel for a guest task without corrupting host state. Registers such as `stvec`, `sstatus`, `sscratch`, and `satp` describe the supervisor state of the current hart or active address-translation context; writing them in the host kernel would modify host supervisor state, not a private guest abstraction. `SRET` similarly changes privilege state and trap-return state for the executing hart; it is not a safe generic service to apply to an arbitrary guest task. So the correct architecture is: let privileged guest operations trap, decode them, and then have the host kernel perform equivalent privileged services on behalf of the guest while keeping a virtual supervisor CSR bank for the guest. Literal kernel-mode execution should be reserved for the small subset of operations whose semantics really map to safe host actions.

The strongest architectural recommendation is not to mutate OrangePi’s normal `arch/riscv` boot path into a userspace process first. The repository already contains the User-Mode Linux framework under `arch/um`, which exists precisely to make “Linux as a userspace process” work, together with host/guest interception machinery, host OS shims, and a build/link model for a kernel that is launched as a process. The clean path is therefore to add a RISC‑V UML sub-architecture to the repo and pull over the minimal RISC‑V semantics you need from `arch/riscv`, then pair that with a dedicated host kernel helper exposed via a file-descriptor-based ABI modeled after KVM-style ioctls.

A second crucial constraint is that the OrangePi `arch/riscv` tree assumes a normal RISC‑V kernel virtual-address layout and normal SBI/boot semantics. In the repo, RV64 `PAGE_OFFSET` defaults to high kernel addresses such as `0xffffffff80000000` or `0xffffffe000000000`, while `TASK_SIZE` is the low-half user range; a normal userspace process cannot simply become an unmodified kernel that expects those kernel virtual addresses to exist in its userspace address space. The same applies to SBI calls: OrangePi’s `arch/riscv/include/asm/sbi.h` emits `ecall`, but in Linux userspace on RISC‑V, `ecall` is the syscall mechanism, so leaving those paths unmodified would invoke host Linux syscalls, not a guest SBI.

Accordingly, the practical implementation plan has two layers. First, build a trap-compatible prototype that catches `SIGILL` and `SIGSEGV`, decodes the faulting instruction or memory access, invokes a host kernel helper through an fd/ioctl or custom syscall ABI, updates guest-visible state, and resumes execution by patching the signal context. Second, once the minimal kernel boots, replace the hottest privileged paths with explicit hypercalls so you avoid paying the signal/tracer cost on every `CSR`, `WFI`, `sfence.vma`, timer, or IPI operation. That gives you correctness first and performance second.

## Feasibility boundaries and architecture

The repository already contains both the normal RISC‑V Linux port and the UML framework. The normal RISC‑V port assumes standard kernel boot and execution: `head.S` and `setup_vm()` build mappings around `PAGE_OFFSET`, `trap_init()` writes real supervisor CSRs such as `sscratch`, `stvec`, and `sie`, and `tlbflush.h` and `processor.h` emit real `sfence.vma` and `wfi` instructions. Those are exactly the places that break if you try to run the tree as an ordinary userspace process with no user/kernel virtualization layer.

By contrast, UML is explicitly documented as “the kernel is just a process running on Linux,” and Linux on the host assists with interception so the UML kernel can handle requests. The OrangePi repo’s `arch/um` build machinery already links Linux as a host executable, pulls in `os-Linux` shims, and expects a host-specific sub-architecture under `arch/<host>/um/`. In the provided tree, that host-specific implementation exists for x86, which is why the most robust approach is to add the analogous RISC‑V host sub-architecture rather than fight the assumptions baked into `arch/riscv` boot code.

| Base approach                                       | What it gives you                                                                                                                                                | Main blockers                                                                                                     | Recommendation                           |
| --------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| Extend `arch/um` with a new RISC‑V sub-architecture | A kernel meant to run as a host process; existing userspace/host interception model; natural place for signals, ptrace, futexes, host timers, and process launch | You must write `arch/riscv/um/*` and a host helper ABI                                                            | Best long-term choice                    |
| Patch normal `arch/riscv` into a userspace mode     | Maximum reuse of the current RISC‑V port                                                                                                                         | High-half `PAGE_OFFSET`, real SBI `ecall`, real CSR writes, real `wfi`/`sfence.vma`, real trap-vector assumptions | Good only for targeted early experiments |

If your real goal is “let hardware execute supervisor-mode instructions with near-native semantics,” then a user-process design is the wrong abstraction. The RISC‑V Hypervisor extension exists specifically to virtualize supervisor-level architecture, and KVM’s fd/ioctl architecture is the standard Linux control plane for that class of virtualization. That is not the same thing as a user-mode kernel, but it is the correct answer for literal hardware-privilege execution. Your requested design remains possible, but it must be understood as a user-mode kernel with kernel-assisted privileged services, not as “S-mode in userspace.”

## Precise execution model

The correct steady-state execution model is:

1. The guest kernel binary is a native RISC‑V ELF running as a normal host Linux process or thread group.
2. All ordinary RV64 instructions that are legal in userspace execute directly on the host core.
3. Supervisor-only instructions and CSR accesses trap.
4. A userspace runtime and a host kernel helper cooperate to turn the fault into a guest-visible result.
5. Signals, eventfds, futexes, and shared memory are used to communicate interrupts, page faults, and completions.

```text
Guest kernel thread runs as host Linux task
    ├── ordinary RV64 instruction
    │       └── executes natively on hardware
    ├── supervisor CSR or S-mode instruction in U-mode
    │       └── hardware trap to host Linux
    │             └── host Linux delivers SIGILL
    │                   └── runtime decodes instruction
    │                         └── helper fd/ioctl performs service
    │                               └── runtime patches ucontext and resumes
    └── guest memory access misses host mapping
            └── SIGSEGV or userfaultfd event
                  └── runtime/helper synthesizes guest page fault or maps page
```

## How the host actually catches privileged operations

For an unmodified guest-in-userspace prototype, the main fault classes are:

- Supervisor CSR access and supervisor-only instructions become `SIGILL`.
- Host page faults caused by the guest’s virtual memory model become `SIGSEGV`, unless you use `userfaultfd`.
- Syscall-style explicit hypercalls can be routed through a custom syscall or fd/ioctl interface.

## What the host helper should do

The helper should expose a VM object and one or more VCPU objects. A good mental model is “small KVM for a user-mode RISC‑V kernel,” not “generic signal handler that knows about everything.”

Use:

- one VM fd to own guest RAM mappings, VM metadata, and interrupt/timer routing;
- one VCPU fd per guest hart to own virtual CSR state, pending interrupt state, and entry/exit state;
- shared memory for the run structure and instruction decode context;
- `eventfd` for interrupt notification and completion doorbells;
- `futex` for `WFI`-like blocking and inter-thread rendezvous.

A minimal helper ABI could have these operations:

| Object          | Operations                                                                                                           |
| --------------- | -------------------------------------------------------------------------------------------------------------------- |
| VM fd           | create/destroy VM, register guest RAM memslots, register eventfds, configure timer source, configure page-fault mode |
| VCPU fd         | get/set guest regs, get/set virtual CSR bank, run until exit, inject IRQ, request TLB sync, request MMU operation    |
| Shared run page | exit reason, decoded instruction, fault VA, scause/stval equivalents, return values for trapped operations           |

## Why ptrace is not the main design

`ptrace` remains useful for debugging and perhaps for an early prototype, but it should not be your long-term mainline execution path. It is fundamentally a tracer/tracee model with extra stops, and `PTRACE_SYSEMU`, historically used by UML to avoid executing syscalls, is documented as x86-only. On RISC‑V you would fall back to signal-stop handling or instruction stepping, which is much heavier than a dedicated helper ABI.

| Interception path                    | Strength                                                     | Weakness                                                                              | Best use                   |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------------------------------- | -------------------------- |
| `SIGILL` / `SIGSEGV` runtime handler | Easiest bootstrap, works with stock host kernel              | High per-fault overhead; instruction decode in signal path; awkward for SMP hot paths | First bring-up             |
| `ptrace` tracer                      | Strong observability and register control                    | Extra context switches; `PTRACE_SYSEMU` not available on RISC‑V                       | Debugging and diagnostics  |
| Kernel helper module via fd/ioctl    | Clean VM object model; can batch and integrate eventfd/futex | Requires host-side kernel work and ABI design                                         | Main implementation        |
| Explicit hypercall wrappers          | Lowest steady-state overhead among user-kernel options       | Requires patching or macro replacement in guest kernel                                | Fast path after boot works |

## Required changes in the OrangePi tree and host-side components

### Highest-value patch points in OrangePi

- `arch/riscv/include/asm/csr.h`: C-side CSR read/write/set/clear macros currently emit raw `csrr*`/`csrw*`. In a user-kernel build, these must become wrappers that either trap cleanly or call your helper ABI directly.
- `arch/riscv/include/asm/sbi.h`: OrangePi uses raw `ecall` for classic SBI services. In userspace on Linux, `ecall` is syscall entry, so these paths cannot remain unchanged.
- `arch/riscv/include/asm/tlbflush.h`: currently emits literal `sfence.vma`; replace with helper calls that synchronize your shadow MMU.
- `arch/riscv/include/asm/processor.h`: contains `wait_for_interrupt()` as literal `wfi`; replace with “block current VCPU thread on futex/eventfd until a virtual interrupt is pending.”
- `arch/riscv/kernel/traps.c`: study trap taxonomy and normal CSR writes at trap init.
- `arch/riscv/kernel/time.c` and `arch/riscv/kernel/smp.c`: timer and IPI assumptions must be re-hosted using timerfd/eventfd/shared memory.
- `arch/riscv/mm/init.c` and `arch/riscv/include/asm/pgtable.h`: boot memory layout and page-table semantics assume normal kernel virtual addressing.

### Recommended maintainable patch plan

- keep OrangePi’s existing `arch/um`;
- add `arch/riscv/Makefile.um`;
- add `arch/riscv/um/Makefile`;
- add `arch/riscv/um/` equivalents of existing x86 UML glue:
  - register save/restore,
  - syscall/trap shims,
  - userspace stub code,
  - signal/context conversion,
  - subarch boot support;
- add `tools/rvuml/` or similar host runtime plus selftests;
- add a host kernel helper that can be built out of tree against the running host kernel.

## Concrete guest-side patch plan

### Guest build mode

Add a config such as `CONFIG_RISCV_UML` or `CONFIG_RISCV_UKERNEL` and make it select an alternate host-process execution path. Do not let that mode compile raw `sstatus`, `satp`, `stvec`, `wfi`, `sfence.vma`, or SBI `ecall` instructions into hot paths. Gate them behind wrapper APIs.

### CSR wrapper layer

Replace direct CSR macro expansion with something like:

```c
rvuk_csr_read(csrno)
rvuk_csr_write(csrno, val)
rvuk_csr_set(csrno, mask)
rvuk_csr_clear(csrno, mask)
```

Under the user-kernel config, these should either:

- issue an explicit helper call; or
- intentionally trigger `SIGILL` only in debug mode, so the runtime can validate decode/emulation correctness.

This layer is the single most important refactoring because it lets you patch the guest kernel systematically rather than instruction-by-instruction.

### SBI replacement layer

Everything in `arch/riscv/include/asm/sbi.h` must become a host-helper ABI when in user-kernel mode. In your repo, that includes timer setup, console, IPIs, remote fence operations, and shutdown. These are already packaged as a clean abstraction in the OrangePi code; rebind that abstraction instead of searching call sites ad hoc.

### Trap-return and idle layer

`SRET` must become a runtime operation that restores guest-visible privilege/trap state and resumes at guest `sepc`; `WFI` must become a blocking wait on a host synchronization primitive. QEMU’s RISC‑V `helper_sret()` is a good code-reading reference for the state transitions that must happen on `SRET`, even though your implementation target is different.

### MMU indirection layer

`satp` writes and `sfence.vma` must stop being literal instructions and become requests into a shadow-MMU subsystem. For bring-up, support only a small subset: one address-translation mode, no ASID recycling optimizations, and a global fence implementation even when narrower fences are requested. The RISC‑V spec allows over-fencing, which is useful for the first implementation.

## Host helper ABI design

A compact, extensible ABI can look like this:

| Request | Purpose |
|---|---|
| `RVUK_VM_CREATE` | Create VM object and return VM fd |
| `RVUK_VM_SET_MEMSLOT` | Bind a guest physical range to a userspace backing store |
| `RVUK_VCPU_CREATE` | Create a VCPU object and return VCPU fd |
| `RVUK_VCPU_RUN` | Enter guest thread until a trap/exit occurs |
| `RVUK_VCPU_GET_STATE` / `RVUK_VCPU_SET_STATE` | Transfer regs and virtual CSR bank |
| `RVUK_VCPU_CSR_OP` | Fast-path CSR reads/writes for patched guest code |
| `RVUK_VCPU_MMU_OP` | `satp`, `sfence.vma`, map/unmap, shootdown |
| `RVUK_VCPU_IRQ_INJECT` | Inject timer/software/external interrupts |
| `RVUK_VM_ATTACH_EVENTFD` | Eventfd for interrupts and completions |
| `RVUK_VM_SET_TIMERFD` | Timer emulation binding |

Modeling the interface around file descriptors is aligned with Linux API guidance and mirrors the KVM control model.

## Memory model

The cleanest memory model is:

- Guest physical memory is backed by one or more host mappings, preferably stable memslots conceptually similar to KVM.
- Guest virtual memory is not driven by hardware `satp`; instead, maintain a shadow host mapping or software MMU cache that translates guest virtual addresses into host virtual addresses.
- Guest page tables live in guest RAM in normal RISC‑V format, exactly as Linux expects.
- The host helper interprets `satp`/page-table changes and makes corresponding host-side mapping state visible to the userspace guest process.

The first implementation should be conservative. Every guest page-table write can either:

- write-protect page-table pages and trap writes so the helper updates shadow mappings; or
- lazily walk guest page tables after a `SIGSEGV`/`userfaultfd` event and synthesize the mapping then.

`userfaultfd` is preferable to pure `SIGSEGV` once the design stabilizes because it is intended for userland-controlled page-fault handling.

A key architectural insight is that `satp` cannot be forwarded literally. `satp` selects active supervisor address translation. In your design, it updates a virtual root page-table pointer plus associated metadata, and `SFENCE.VMA` becomes a request to synchronize the shadow MMU.

## Trap and exception flow

For the user-kernel design, the host-visible fault classes should map to guest-visible RISC‑V trap classes like this:

- host `SIGILL` from attempted supervisor instruction or CSR access → guest illegal-instruction trap;
- host `SIGSEGV` or `userfaultfd` miss on guest access → guest page fault or access fault;
- helper-injected asynchronous eventfd wakeup → guest pending interrupt;
- explicit helper call return → synthetic completion of a privileged operation.

When using a `sigaction` handler, do not rely on getting faulting instruction bits from the architecture. The RISC‑V privileged spec says `stval` may contain faulting instruction bits on illegal instructions, but that is optional. Safer design: read instruction bytes from the saved PC and decode them yourself.

## Which CSRs and instructions to forward

For a minimal kernel built from your OrangePi source, prioritize this set:

| Category | Forward or virtualize |
|---|---|
| Trap state | `sstatus`, `sie`, `sip`, `stvec`, `sscratch`, `sepc`, `scause`, `stval` |
| Address translation | `satp`, `sfence.vma` |
| Idle/return | `wfi`, `sret` |
| Counters and timing | `time` / `timeh` if you want controlled virtual time, plus timer-setting API |
| SBI-style services | `set_timer`, `send_ipi`, remote fence, shutdown |
| Leave native | ordinary ALU/branch/load/store/AMO instructions legal in U-mode |

Semantic policy:

- Virtualize, do not literally execute: `stvec`, `sscratch`, `sepc`, `scause`, `stval`, `sstatus`, `satp`, `sret`, `wfi`.
- Translate into host service: timer programming, IPI injection, guest memory mapping, TLB synchronization.
- Possibly execute host-side hardware action as part of service: local/remote TLB invalidation, timerfd programming, eventfd signaling.
- Never let the guest directly mutate host supervisor trap vectors or host supervisor status.

## Synchronization and concurrency

Use one host thread per guest hart. All guest harts map into the same VM object, using shared-memory control structures and futexes/eventfds. `clone()` with `CLONE_VM` is the right host primitive if not using pthreads.

The OrangePi guest SMP code already gives a semantic blueprint: it tracks per-CPU pending bits and uses SBI to send IPIs. In your port, replace `sbi_send_ipi()` with a host helper request that marks pending interrupt bits in shared state and signals the destination hart’s eventfd or wake futex.

## Security and isolation

A host helper that executes privileged services for an unprivileged process is security-sensitive. Design it as a least-authority object-capability interface:

- the fd is the authority;
- requests are validated against that VM/VCPU object;
- no request may directly reach host-global kernel state;
- do not allow arbitrary host-privileged operations through the helper;
- keep the helper small and auditable;
- disable or tightly gate module loading inside the guest prototype;
- sandbox the userspace runtime with seccomp once bring-up works.

## Performance strategy

Slow path:

```text
hardware trap → host signal delivery → userspace decode → helper ioctl/syscall → resume
```

This is acceptable for bring-up and bad for hot paths. Use staged optimization:

- Bring-up: accept signals for privileged operations.
- Boot optimization: patch SBI sites, `WFI`, `SRET`, `satp`, `sfence.vma`, and high-frequency CSR accesses to explicit hypercalls.
- Steady-state optimization: batch MMU operations, use eventfd/timerfd rather than signal-based timers, and keep shared run structures hot in cache.

For memory management, first use over-fencing and lazy refinement. `SFENCE.VMA` can be conservatively implemented as a global synchronization operation initially.

## Debugging and testing plan

Testing layers:

1. KUnit for CSR virtualization, trap classification, and MMU state transitions.
2. kselftest under `tools/testing/selftests/` for ABI and runtime tests.
3. syzkaller descriptions for helper ioctl/syscall fuzzing.
4. interactive debug mode using ptrace/GDB for privileged exits.

Minimal acceptance matrix:

| Test stage | Target outcome |
|---|---|
| Hello-path | enter guest entry point, run native unprivileged instructions, exit cleanly |
| CSR path | trapped `csrw/csrr*` operations are decoded, virtualized, and resumed correctly |
| Trap return | synthetic `SRET` resumes at correct PC with correct guest-visible status |
| MMU path | guest `satp` switch plus `sfence.vma` yields correct shadow mapping behavior |
| Timer path | guest timer interrupt fires and is observed by correct hart |
| SMP path | IPI wakeup and virtual `WFI` behavior work across two harts |
| Stress path | concurrent mapping changes and interrupt storms do not deadlock or corrupt state |

## Step-by-step implementation roadmap

### Milestone A: RISC‑V UML subarch compiles and launches

Deliverable:

- A native RISC‑V host process that enters guest code, preserves registers, and exits under host control.

Work:

- Add `arch/riscv/Makefile.um`.
- Add `arch/riscv/um/Makefile`.
- Add register definitions and signal-context mapping.
- Build a minimal host-process executable from the kernel tree.

### Milestone B: privileged instruction trap path works

Deliverable:

- `SIGILL` decode;
- helper request;
- state patch;
- resume loop for at least:
  - `sstatus`,
  - `stvec`,
  - `sscratch`,
  - `wfi`,
  - one SBI operation.

### Milestone C: single-hart minimal guest kernel boots with timer

Deliverable:

- clock/timer path works through helper instead of SBI.

Work:

- Replace `sbi_set_timer()`;
- add timerfd or hrtimer-backed helper;
- inject virtual timer interrupt into guest CSR state.

### Milestone D: shadow MMU supports `satp` and `sfence.vma`

Deliverable:

- page faults and TLB synchronization semantics are correct for one address space.

Work:

- implement a guest page table walker;
- add shadow mapping cache;
- initially make all fences global.

### Milestone E: two-hart SMP with virtual IPIs

Deliverable:

- `WFI` sleep;
- timer wakeup;
- cross-hart interrupt delivery.

Work:

- one host thread per guest hart;
- eventfd/futex wakeup path;
- replace `sbi_send_ipi()`.

### Milestone F: fast-path conversion and hardening

Deliverable:

- frequent operations become explicit hypercalls;
- selftests exist;
- helper ABI is fuzzable.

Work:

- patch `csr.h` and SBI wrappers;
- add kselftests;
- add syzkaller descriptions;
- audit privilege boundaries.

## Prioritized reading list

### Must read

1. RISC‑V Privileged ISA  
	Focus: supervisor CSRs, `satp`, `stvec`, `sstatus`, `SRET`, `SFENCE.VMA`.

2. RISC‑V SBI specification and OpenSBI  
	OrangePi’s RISC‑V port uses SBI abstractions; your helper conceptually replaces them.

3. Linux User-Mode Linux documentation and source  
	Study:
	- `arch/um`
	- `arch/x86/um`
	- `arch/um/os-Linux`

4. OrangePi RISC‑V files:
	- `arch/riscv/include/asm/csr.h`
	- `arch/riscv/include/asm/sbi.h`
	- `arch/riscv/kernel/traps.c`
	- `arch/riscv/kernel/time.c`
	- `arch/riscv/kernel/smp.c`
	- `arch/riscv/mm/init.c`
	- `arch/riscv/include/asm/pgtable.h`
	- `arch/riscv/include/asm/tlbflush.h`
	- `arch/riscv/include/asm/processor.h`

### High priority

1. Linux API design docs for syscalls/ioctls and KVM API.
2. Linux signal, ptrace, userfaultfd, eventfd, futex, timerfd man pages.
3. QEMU RISC‑V source:
	- `target/riscv/csr.c`
	- `target/riscv/op_helper.c`

### Background papers

1. Jeff Dike, User Mode Linux.
2. Exokernel: An Operating System Architecture for Application-Level Resource Management.
3. Drawbridge: A Library OS for Windows.
4. Graphene Library OS papers.

## Source links

- RISC‑V Privileged ISA CSRs: <https://docs.riscv.org/reference/isa/v20260120/priv/priv-csrs.html>
- RISC‑V Supervisor ISA: <https://docs.riscv.org/reference/isa/v20260120/priv/supervisor.html>
- RISC‑V Hypervisor extension: <https://www.five-embeddev.com/riscv-priv-isa-manual/latest-latex/hypervisor>
- RISC‑V SBI specification: <https://github.com/riscv-non-isa/riscv-sbi-doc>
- OpenSBI: <https://github.com/riscv-software-src/opensbi>
- OpenSBI trap code: <https://raw.githubusercontent.com/riscv-software-src/opensbi/master/lib/sbi/sbi_trap.c>
- Linux UML documentation: <https://docs.kernel.org/virt/uml/user_mode_linux_howto_v2.html>
- Upstream Linux UML Kconfig: <https://raw.githubusercontent.com/torvalds/linux/master/arch/um/Kconfig>
- OrangePi Linux repo: <https://github.com/orangepi-xunlong/linux-orangepi>
- OrangePi RISC‑V csr.h: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/include/asm/csr.h>
- OrangePi RISC‑V sbi.h: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/include/asm/sbi.h>
- OrangePi RISC‑V traps.c: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/kernel/traps.c>
- OrangePi RISC‑V time.c: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/kernel/time.c>
- OrangePi RISC‑V smp.c: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/kernel/smp.c>
- OrangePi RISC‑V mm/init.c: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/mm/init.c>
- OrangePi RISC‑V pgtable.h: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/include/asm/pgtable.h>
- OrangePi RISC‑V tlbflush.h: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/include/asm/tlbflush.h>
- OrangePi RISC‑V processor.h: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/riscv/include/asm/processor.h>
- OrangePi UML Kconfig: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/um/Kconfig>
- OrangePi UML Makefile: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/um/Makefile>
- OrangePi x86 UML Makefile.um: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/x86/Makefile.um>
- OrangePi x86 UML Makefile: <https://raw.githubusercontent.com/orangepi-xunlong/linux-orangepi/orange-pi-5.4/arch/x86/um/Makefile>
- KVM API: <https://docs.kernel.org/virt/kvm/api.html>
- ioctl API design: <https://docs.kernel.org/driver-api/ioctl.html>
- Adding Linux syscalls: <https://docs.kernel.org/process/adding-syscalls.html>
- sigaction man page: <https://man7.org/linux/man-pages/man2/sigaction.2.html>
- signal man page: <https://man7.org/linux/man-pages/man7/signal.7.html>
- ptrace man page: <https://man7.org/linux/man-pages/man2/ptrace.2.html>
- userfaultfd docs: <https://docs.kernel.org/admin-guide/mm/userfaultfd.html>
- userfaultfd man page: <https://man7.org/linux/man-pages/man2/userfaultfd.2.html>
- eventfd man page: <https://man7.org/linux/man-pages/man2/eventfd.2.html>
- futex man page: <https://man7.org/linux/man-pages/man2/futex.2.html>
- timerfd_create man page: <https://man7.org/linux/man-pages/man2/timerfd_create.2.html>
- seccomp filter docs: <https://docs.kernel.org/userspace-api/seccomp_filter.html>
- KUnit docs: <https://docs.kernel.org/dev-tools/kunit/index.html>
- kselftest docs: <https://docs.kernel.org/dev-tools/kselftest.html>
- syzkaller: <https://github.com/google/syzkaller>
- QEMU RISC‑V csr.c: <https://raw.githubusercontent.com/qemu/qemu/master/target/riscv/csr.c>
- QEMU RISC‑V op_helper.c: <https://raw.githubusercontent.com/qemu/qemu/master/target/riscv/op_helper.c>
- User Mode Linux paper/book PDF: <https://ptgmedia.pearsoncmg.com/images/9780131865051/downloads/013865056_Dike_book.pdf>
- USENIX User-Mode Linux: <https://www.usenix.org/conference/als-01/user-mode-linux>
- Exokernel PDF: <https://pdos.csail.mit.edu/6.828/2008/readings/engler95exokernel.pdf>
- Drawbridge paper: <https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/asplos2011-drawbridge.pdf>
- Graphene paper: <https://www.cs.unc.edu/~porter/pubs/tsai14graphene.pdf>
- Graphene thesis: <https://www.chiachetsai.com/files/Graphene-Thesis-Chia-Che-Tsai.pdf>
