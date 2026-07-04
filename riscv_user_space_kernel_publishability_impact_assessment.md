# Publishability and Impact Assessment for a RISC-V User-Space Kernel Project

## Executive summary

Your project has a credible research core, but **only if you frame it as a new RISC-V privilege-mediation substrate and not merely as “boot Linux in user space.”**

The closest prior systems already occupy much of the conceptual landscape:

- User-Mode Linux runs a Linux kernel as a host process.
- L4Linux runs Linux as an unprivileged user-level component on a microkernel.
- coLinux ran a guest Linux kernel cooperatively alongside a host kernel.
- Dune exposed privileged CPU features safely to user processes using virtualization hardware.
- Unikernel Linux links an application with Linux and runs at supervisor privilege.
- gVisor is a user-space application kernel.
- Drawbridge and Graphene are library-OS systems.

I did **not** find a primary-source paper describing the exact combination you propose:

> A RISC-V Linux kernel, built from a vendor OrangePi tree, first booted conventionally in QEMU and then re-executed as a host-side process with kernel-mediated handling of guest privileged operations on a stock Linux system.

That gap is real enough to justify research exploration, but not automatically strong enough for a top-tier paper.

The most important technical correction is this:

> You should not describe the design as literally executing the guest kernel’s privileged RISC-V instructions in the live host kernel context and “returning values.”

A publishable and technically defensible version of your idea is:

> A kernel-mediated supervisor-state service for a deprivileged RISC-V guest-kernel process, where the host kernel maintains guest-owned architectural state and performs semantically equivalent privileged actions safely, rather than blindly executing host supervisor instructions on behalf of the guest.

My honest assessment:

- **Workshop/demo paper:** realistic with a solid prototype.
- **Master’s/M.Tech thesis:** realistic and strong if executed carefully.
- **Top systems conference paper:** possible only if the contribution is sharpened into a real system design with correctness, security, and performance evaluation.
- **Merely booting a stripped kernel to a shell:** impressive engineering, but not enough as a research paper.

---

## Publication verdict

The project is **not yet publishable as stated**, but it becomes publishable if the claim is narrowed and strengthened.

Weak claim:

> “I ran a minimal RISC-V Linux kernel as a user-space process.”

Likely reviewer reaction:

> “This overlaps with UML, L4Linux, coLinux, library OSes, and gVisor-like deprivileging.”

Stronger claim:

> “We design and evaluate a kernel-mediated RISC-V supervisor execution model that allows a Linux kernel to run as a host process while preserving guest supervisor semantics, with quantified correctness, security, and performance trade-offs.”

That framing makes the novelty about **RISC-V privilege handling and mediation**, not merely about process-hosted kernels.

| Paper version | Publishability | Assessment |
|---|---:|---|
| Minimal QEMU boot of OrangePi kernel to shell | Low | Engineering milestone, not research by itself |
| QEMU boot plus user-space early boot with ad hoc host hooks | Low to medium | Interesting prototype, but incomplete/generalization concerns |
| User-space kernel boot to shell with a defined privilege-mediation ABI | Medium | Workshop/demo paper realistic |
| Full system plus correctness/security/performance evaluation | Medium to high | Competitive for a solid systems venue |
| Same, plus upstream relevance and reusable artifact | High | Strongest version for EuroSys/ATC/OSDI-style review |

---

## Core novelty assessment

### What is not novel

These ideas are already known:

- Linux kernel as a process: User-Mode Linux.
- Linux deprivileged onto another OS/microkernel: L4Linux.
- Cooperative guest kernel beside a host kernel: coLinux.
- User processes accessing privileged hardware features safely: Dune.
- User-space kernel for sandboxing: gVisor.
- Library OS with a narrow host ABI: Drawbridge and Graphene.
- Guest OS virtualization on RISC-V: RISC-V Hypervisor Extension, KVM, QEMU.

### What may be novel

Your defensible novelty is the specific combination:

1. **RISC-V-specific supervisor-state mediation**
2. **A Linux-derived guest kernel running as a host Linux process**
3. **Native execution of ordinary RISC-V instructions**
4. **Mediation of privileged operations through a host-side helper**
5. **Boot-to-shell demonstration from an OrangePi-derived kernel**
6. **Trace-based taxonomy of all mediated privileged operations during Linux boot**
7. **Differential validation against QEMU/OpenSBI/KVM-like reference behavior**

The strongest publishable framing is:

> Kernel-mediated supervisor execution for deprivileged RISC-V kernels.

A second strong framing is:

> A correctness and tracing framework for validating RISC-V privileged behavior during Linux boot.

---

## Real-world problems addressed

### 1. Safer kernel development and debugging on RISC-V

Real problem:

- Kernel crashes on real boards waste time.
- Board reboot cycles are slow.
- Vendor kernels are messy.
- Debugging early boot on physical RISC-V boards is painful.
- Scheduler and memory-management experiments can crash the entire machine.

Your approach could allow:

- Kernel crash containment inside a host process.
- Faster edit-build-run-debug cycles.
- GDB-friendly debugging.
- Deterministic replay or tracing of privileged events.
- Safer experimentation with vendor kernel internals.

This is a valid real-world problem, especially for embedded RISC-V education and research.

### 2. Privileged-operation tracing and taxonomy

Real problem:

- Linux boot uses privileged operations in many places.
- It is hard to systematically observe how a RISC-V kernel uses CSRs, SBI calls, trap setup, page tables, and interrupt state.
- QEMU can emulate the system, but it does not automatically give a research-grade taxonomy of guest privileged behavior.

Your system could produce:

- Counts of CSR accesses.
- SBI call traces.
- Trap-entry and trap-return traces.
- `satp` transition records.
- `sfence.vma` frequency.
- Timer and interrupt injection logs.
- Boot-phase mapping: `head.S`, `setup_arch`, `mm_init`, `trap_init`, `sched_init`, `kernel_init`, `/init`.

This could become a strong measurement contribution.

### 3. Deprivileged supervisor software

Real problem:

- Running experimental privileged code directly in supervisor mode is dangerous.
- Full virtualization may be too heavy or opaque for certain research tasks.
- User-space sandboxes such as gVisor reimplement Linux-like behavior but do not boot a real Linux kernel.

Your system could provide a middle ground:

- Real Linux-derived supervisor code.
- Reduced host risk compared with direct kernel-mode experiments.
- More introspection than a traditional VM.
- A narrow mediation ABI instead of full hardware ownership.

This is useful for OS education, research prototyping, and architecture validation.

### 4. Vendor-kernel archaeology

Real problem:

- Vendor kernels such as board-specific OrangePi trees are often old, patched, poorly documented, and difficult to reason about.
- Researchers and students need a way to instrument them without risking real hardware every time.

Your approach could help:

- Boot and trace a vendor-derived kernel in controlled steps.
- Compare vendor tree behavior with upstream Linux.
- Identify board-specific assumptions.
- Decouple core kernel behavior from drivers.

### 5. RISC-V privileged architecture validation

Real problem:

- RISC-V hardware, firmware, SBI, and hypervisor support are still evolving.
- Different platforms may differ in behavior.
- Privileged architecture bugs are subtle.

Your system could become a validation harness for:

- CSR behavior.
- SBI call expectations.
- Trap/interrupt semantics.
- Timer behavior.
- Page-table state transitions.
- Supervisor-mode assumptions in Linux.

---

## What reviewers may criticize

### 1. “This is just UML again.”

This is the most likely objection.

Counter:

- UML is the closest ancestor, but your contribution must be RISC-V-specific supervisor-state mediation.
- You must show where RISC-V privilege semantics make the design different.
- You need a defined ABI and trace/correctness methodology, not only a port.

### 2. “This is slower than KVM.”

Likely true for many workloads.

Counter:

- Do not claim you are replacing KVM.
- Claim usefulness for debugging, tracing, validation, and controlled experimentation.
- Measure overhead honestly.
- Show cases where introspection is better than KVM/QEMU.

### 3. “This is insecure.”

A host kernel helper for guest privileged actions is dangerous.

Counter:

- Define a narrow ABI.
- Treat all guest requests as untrusted.
- Validate all memory ranges and state transitions.
- Add negative tests.
- Use fuzzing.
- Do not expose real host privileged state directly.

### 4. “The OrangePi kernel is old.”

This is a real issue.

Counter:

- Use OrangePi as the required project artifact source.
- Also validate the core mechanism on a current upstream Linux RISC-V kernel.
- Keep patches small and documented.

### 5. “It does not generalize.”

Counter:

- Separate three layers clearly:
  - generic RISC-V privilege mediation,
  - Linux guest-kernel adaptations,
  - OrangePi/vendor-specific boot configuration.
- Demonstrate at least QEMU `virt`.
- Ideally demonstrate an upstream Linux RISC-V kernel plus the OrangePi-derived tree.

### 6. “It only boots, but does not run meaningful workloads.”

Counter:

- Boot-to-shell is only the first acceptance milestone.
- Add BusyBox shell scripts, basic process creation, timer tests, memory tests, selected LTP/kselftest/KUnit tests, and microbenchmarks.

### 7. “The design is not semantically correct.”

This is the deepest criticism.

Counter:

- Build a differential trace checker.
- Compare mediated privileged-event traces against QEMU/OpenSBI.
- Define guest-visible state precisely.
- Avoid vague claims like “we execute privileged instructions for the guest.”

---

## Correct technical framing

Avoid saying:

> “Privileged instructions are executed in the host kernel and values are returned.”

Say instead:

> “Privileged operations trap or are redirected into a host-mediated supervisor-state service. The service validates the request, updates guest-owned architectural state, performs safe host-side actions where needed, and returns guest-visible results.”

This distinction matters because RISC-V privileged state such as `satp`, `stvec`, `sstatus`, `sepc`, `scause`, `stval`, interrupt enable state, and trap vectors belongs to the currently running privilege context. If you literally execute guest operations in host kernel context, you risk corrupting host kernel state.

---

## Required technical contributions for a strong paper

### Contribution 1: Privilege-mediation ABI

Define a clear ABI between user-kernel runtime and host helper.

Example mediated operations:

- CSR read/write.
- SBI call.
- Trap-vector update.
- Page-table root update.
- `sfence.vma`.
- Timer programming.
- Interrupt injection.
- Guest memory mapping.
- Guest page-fault resolution.
- Console I/O.
- `wfi` handling.
- Scheduler tick injection.

The ABI should have:

- versioning,
- explicit structs,
- bounds checks,
- no raw host pointer trust,
- clear error codes,
- trace IDs,
- replay support.

### Contribution 2: Boot-to-shell prototype

Minimum functional milestone:

```text
OpenSBI/QEMU reference boot:
Linux Image → initramfs → /init → BusyBox shell

User-hosted boot:
user ELF runtime → deprivileged Linux-derived kernel → mediated privileged ops → BusyBox shell
```

A paper becomes much stronger if both paths are demonstrated.

### Contribution 3: Privileged-event taxonomy

Collect and classify:

- CSR ops,
- SBI calls,
- traps,
- page-table transitions,
- TLB flushes,
- timer operations,
- interrupt operations,
- fault classes,
- boot phase where each occurs.

This gives the paper a measurable scientific core.

### Contribution 4: Correctness methodology

Compare the user-hosted kernel against a reference.

Possible references:

- QEMU RISC-V `virt`,
- OpenSBI boot trace,
- upstream Linux behavior,
- KVM-style model where available.

Metrics:

- mismatch count,
- missing events,
- extra events,
- guest-visible CSR state divergence,
- boot-phase divergence,
- trap cause divergence.

### Contribution 5: Security model

Define:

- trusted computing base,
- untrusted guest,
- trusted host helper,
- trusted runtime or partially trusted runtime,
- memory ownership rules,
- device exposure policy,
- failure model.

Add negative tests:

- invalid CSR ID,
- invalid guest physical address,
- invalid `satp`,
- malformed SBI call,
- illegal interrupt injection,
- helper request after guest crash,
- malicious guest trying to map host memory.

### Contribution 6: Performance evaluation

Compare against:

- QEMU full emulation,
- QEMU/KVM if available,
- User-Mode Linux where comparable,
- native Linux boot baseline.

Metrics:

- boot time,
- time to first shell prompt,
- mediated operation count,
- average mediation latency,
- page-fault handling cost,
- timer interrupt latency,
- BusyBox command latency,
- context-switch overhead,
- memory mapping overhead.

---

## Suggested experiments

| Priority | Experiment | Metrics | Purpose |
|---|---|---|---|
| Highest | Boot-to-shell | success/failure, boot time, log completeness | Minimum viability |
| Highest | Privilege-event taxonomy | count and class of mediated ops | Converts prototype into research data |
| Highest | Differential correctness | mismatch rate vs QEMU/OpenSBI | Proves semantics |
| High | Mediation latency | CSR/SBI/fault round-trip time | Measures overhead |
| High | Security hardening | invalid requests rejected | Addresses reviewer concerns |
| Medium | BusyBox shell workload | command latency, script runtime | Demonstrates usability |
| Medium | kselftest/LTP subset | pass/fail count | Regression evidence |
| Medium | Scheduler microbenchmarks | hackbench, context switch latency | Relevant to your scheduler goal |
| Lower | I/O tests | fio or simple file tests | Only after storage path exists |

---

## Suggested artifact package

A publishable artifact should include:

| Artifact | Contents |
|---|---|
| Source code | host helper, user runtime, guest kernel patches |
| Build scripts | kernel, BusyBox, initramfs, QEMU, runtime |
| Kernel configs | `.config` for reference boot and user-hosted boot |
| Trace tools | event decoder, timeline generator, CSV/JSON output |
| Test harness | boot tests, regression tests, negative tests |
| Benchmarks | boot timing, mediation latency, shell workloads |
| Documentation | architecture, ABI, threat model, reproduction guide |
| Dataset | raw traces, summarized event counts, boot logs |
| License notes | Linux GPL, BusyBox GPL, source release obligations |

---

## Realistic milestone plan

### Milestone 1: Normal QEMU boot

Goal:

```text
OrangePi-derived RISC-V kernel boots on QEMU virt to BusyBox shell.
```

This is not a paper yet. It is your baseline.

### Milestone 2: Minimal kernel config

Goal:

```text
Strip drivers while preserving initramfs shell.
```

Output:

- reduced `.config`,
- boot logs,
- explanation of what was kept/removed.

### Milestone 3: Boot trace map

Goal:

```text
Instrument normal Linux boot path.
```

Trace:

- `head.S`,
- `start_kernel`,
- `setup_arch`,
- `trap_init`,
- `mm_init`,
- `sched_init`,
- `rest_init`,
- `kernel_init`,
- `/init`.

### Milestone 4: Privileged operation inventory

Goal:

```text
List all privileged operations encountered during boot.
```

This is the first strong research-data milestone.

### Milestone 5: User-space ELF skeleton

Goal:

```text
A RISC-V user ELF enters a fake kernel entry and handles SIGILL/SIGSEGV.
```

Do this before touching real Linux.

### Milestone 6: Host helper ABI

Goal:

```text
Implement minimal kernel helper for guest-owned privileged state.
```

Do not expose raw host supervisor state.

### Milestone 7: Early Linux boot in user process

Goal:

```text
Reach early start_kernel() or equivalent.
```

This is a strong demo milestone.

### Milestone 8: User-hosted shell

Goal:

```text
Reach BusyBox shell under mediated execution.
```

This is a workshop-worthy prototype if evaluated.

### Milestone 9: Evaluation and paper

Goal:

```text
Correctness + security + performance + artifact.
```

This is where full-paper potential begins.

---

## Venue recommendations

### Early-stage / idea paper

Best fit:

- HotOS
- Linux Plumbers Conference
- RISC-V Summit workshop
- systems/embedded workshops
- student research competitions
- posters/demos at systems conferences

Reason:

- Your early value is a new direction plus prototype.

### Solid systems paper

Possible later:

- EuroSys
- USENIX ATC
- ASPLOS
- OSDI
- SOSP

Required for these:

- mature prototype,
- strong evaluation,
- correctness story,
- artifact release,
- clear novelty over UML/L4Linux/Dune/gVisor.

### Security-oriented paper

Possible only if you make security central:

- USENIX Security
- NDSS workshop
- ACSAC
- RAID
- systems-security workshops

Required:

- threat model,
- attack-surface reduction,
- fuzzing,
- exploit containment,
- rigorous helper ABI validation.

### Journals

Possible later:

- ACM Transactions on Computer Systems
- ACM Transactions on Architecture and Code Optimization
- IEEE Transactions on Computers
- Journal of Systems Architecture
- ACM SIGOPS Operating Systems Review for shorter/system notes

---

## Related work table

| System / paper | Direct link | Relevance | Gap relative to your project |
|---|---|---|---|
| User-Mode Linux | https://www.kernel.org/doc/html/v6.6/virt/uml/user_mode_linux_howto_v2.html | Linux kernel as host process | Not a RISC-V supervisor-state mediation design |
| User-Mode Linux paper | https://www.usenix.org/conference/als-01/user-mode-linux | Historical Linux-as-process research | Older, not RISC-V-focused |
| Jeff Dike UML book PDF | https://ptgmedia.pearsoncmg.com/images/9780131865051/downloads/013865056_Dike_book.pdf | Practical UML engineering | Historical and x86-era |
| L4Linux | https://l4linux.org/ | Linux as user-level app on L4Re | Microkernel substrate, not stock Linux host helper |
| L4Linux overview | https://os.inf.tu-dresden.de/L4/LinuxOnL4/overview.shtml | Deprivileged Linux model | Uses L4 APIs, not your RISC-V ABI |
| coLinux paper | https://www.kernel.org/doc/ols/2004/ols2004v1-pages-23-32.pdf | Cooperative guest Linux with host | Cautionary security/stability example |
| Dune | https://www.usenix.org/conference/osdi12/technical-sessions/presentation/belay | Safe user-level privileged CPU access | Not a booted Linux guest kernel |
| Dune PDF | https://www.usenix.org/system/files/conference/osdi12/osdi12-final-117.pdf | Key prior work | x86 virtualization hardware focus |
| Unikernel Linux | https://arxiv.org/abs/2206.00789 | Linux linked with application | Opposite direction: app enters kernel world |
| gVisor | https://gvisor.dev/docs/ | User-space application kernel | Reimplements Linux interface, does not boot Linux kernel |
| gVisor security model | https://gvisor.dev/docs/architecture_guide/security/ | Sandboxing model | Useful comparison for security framing |
| Drawbridge PDF | https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/asplos2011-drawbridge.pdf | Library OS with narrow host ABI | Not a Linux-kernel guest |
| Graphene PDF | https://www.cs.unc.edu/~porter/pubs/tsai14graphene.pdf | Multiprocess library OS | Not a real Linux kernel boot |
| RISC-V privileged ISA | https://docs.riscv.org/reference/isa/v20260120/priv/priv-index.html | Mandatory privilege semantics | Spec, not system |
| RISC-V CSR privilege rules | https://docs.riscv.org/reference/isa/v20260120/priv/priv-csrs.html | CSR illegal-instruction behavior | Supports your trap/mediation rationale |
| RISC-V hypervisor extension | https://docs.riscv.org/reference/isa/v20260120/priv/hypervisor.html | Guest OS virtualization model | Hardware virtualization baseline |
| RISC-V virtualization paper | https://arxiv.org/abs/2103.14951 | RISC-V virtualization evaluation | Baseline for hardware-assisted path |
| RISC-V hypervisor processor paper | https://arxiv.org/abs/2406.17796 | H-extension implementation | Shows hardware path reviewers will compare against |
| KVM API docs | https://docs.kernel.org/virt/kvm/api.html | VM/VCPU fd-based ABI model | Useful ABI inspiration |
| userfaultfd docs | https://docs.kernel.org/admin-guide/mm/userfaultfd.html | User-space page-fault handling | Useful future memory mechanism |
| Linux RISC-V boot docs | https://docs.kernel.org/arch/riscv/boot.html | Kernel boot entry rules | Needed for baseline |
| QEMU RISC-V virt docs | https://www.qemu.org/docs/master/system/riscv/virt.html | QEMU boot platform | Needed for baseline |
| OpenSBI | https://github.com/riscv-software-src/opensbi | RISC-V firmware/SBI implementation | Reference for SBI behavior |
| RISC-V SBI spec | https://github.com/riscv-non-isa/riscv-sbi-doc | SBI interface | Needed for mediation design |
| OrangePi kernel repo | https://github.com/orangepi-xunlong/linux-orangepi | Your source base | Vendor-tree risk |

---

## Suggested paper title options

### Stronger titles

1. **Mediating Supervisor Execution for User-Hosted RISC-V Kernels**
2. **A Kernel-Mediated RISC-V Supervisor Substrate for Deprivileged Linux**
3. **Booting a Linux-Derived RISC-V Kernel as a Host Process**
4. **Tracing and Mediating RISC-V Privileged State During Linux Boot**
5. **Toward User-Hosted RISC-V Kernels with Safe Privilege Mediation**

### Weaker titles to avoid

1. “Running OrangePi Kernel in User Space”
2. “Virtualizing Privileged Instructions in Linux”
3. “Linux Kernel as a User Program”
4. “Executing Kernel Privileged Instructions from User Mode”

These sound either too generic, technically inaccurate, or already solved.

---

## Recommended first paper scope

A good first paper should **not** try to claim a full production VM.

Recommended first scope:

> We present a prototype framework for user-hosted RISC-V Linux kernel execution using host-mediated supervisor-state operations. The system boots an OrangePi-derived minimal Linux kernel to a BusyBox shell, records a complete taxonomy of privileged operations during boot, and validates guest-visible behavior against a QEMU/OpenSBI reference execution.

Core sections:

1. Introduction
2. Motivation: RISC-V kernel debugging and privilege tracing
3. Background: RISC-V privilege, Linux boot, UML/L4Linux/Dune
4. Design goals
5. Architecture
6. Privilege-mediation ABI
7. Implementation
8. Boot-to-shell case study
9. Privileged-event taxonomy
10. Correctness validation
11. Security analysis
12. Performance evaluation
13. Limitations
14. Related work
15. Conclusion

---

## Strongest research questions

1. Can a RISC-V Linux kernel be deprivileged into a host Linux process while preserving enough supervisor semantics to boot to userspace?
2. What privileged operations does Linux actually execute during early RISC-V boot, and how frequently?
3. Can a small host-mediated ABI reproduce guest-visible supervisor behavior without exposing unsafe host kernel state?
4. What is the cost of mediating RISC-V privileged operations compared with QEMU/KVM/UML-like baselines?
5. Can privilege-event traces be used to debug, validate, and compare RISC-V vendor kernels?
6. Can this model improve kernel-development safety and reproducibility for embedded RISC-V systems?

---

## Honest negative assessment

You should proceed, but with realistic expectations.

### Reasons this may fail as research

- It may become only an engineering port.
- Performance may be poor.
- The helper ABI may be too dangerous.
- The OrangePi kernel may be too old and vendor-specific.
- Boot-to-shell may require so many Linux changes that the result no longer looks general.
- Reviewers may say UML/L4Linux already solved the conceptual problem.
- If you do not produce measurements, reviewers will reject it as anecdotal.

### Reasons it can still succeed

- RISC-V privilege mediation is timely.
- Vendor RISC-V kernel debugging is a real problem.
- There is a clear gap between UML, KVM, gVisor, Dune, and your proposed mechanism.
- A boot-to-shell prototype plus event taxonomy is a concrete contribution.
- A correctness framework would make the work much stronger.
- Educational and research tooling value is real even if performance is not competitive.

---

## Final verdict

Yes, you can publish if you move ahead seriously, but **not merely because the kernel boots**.

A publishable contribution needs at least:

1. a clean design,
2. a working prototype,
3. a defined privilege-mediation ABI,
4. measured privileged-event traces,
5. differential correctness checking,
6. security analysis,
7. performance comparison,
8. reproducible artifacts.

The most defensible research claim is:

> A RISC-V deprivileged Linux/User-Mode-Linux-style kernel with kernel-assisted privileged-operation handling, designed as a safe privilege-mediation substrate rather than literal host execution of guest privileged instructions.

The blunt bottom line:

> This is a promising research direction, but only if you treat it as a systems research project with measurement and correctness, not as a one-off boot hack.
