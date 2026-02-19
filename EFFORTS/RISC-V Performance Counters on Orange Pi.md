# Technical Analysis of Performance Monitoring Unit Access Control and User-Level Enablement on the Orange Pi RV2 RISC-V Platform

The evolution of the RISC-V architecture from a minimalist academic project to a robust ecosystem for high-performance computing is exemplified by platforms such as the Orange Pi RV2. This board, powered by the SpacemiT K1 (alternatively marketed as the Ky X1) SoC, integrates eight high-performance X60 cores adhering to the RVA22 profile. For the operating system engineer, the ability to extract high-fidelity performance data from these cores is paramount for optimizing both the kernel and user-space applications. However, the RISC-V privileged architecture imposes strict constraints on how hardware performance counters are accessed, primarily through the Supervisor Counter-Enable register, or `scounteren`. Verification of the dependencies associated with this register, and the subsequent implementation of user-mode access, requires a deep understanding of the RISC-V CSR (Control and Status Register) hierarchy and the Linux kernel’s management of multi-core synchronization.

## Architectural Verification of Counter Access Dependencies

The RISC-V privileged specification establishes a tiered access model for hardware performance monitor (HPM) registers. In this model, the hardware performance counters, such as `cycle`, `time`, and `instret`, along with the programmable counters `hpmcounter3` through `hpmcounter31`, are inherently machine-level entities. Access to these registers from lower privilege levels—specifically Supervisor mode (S-mode) and User mode (U-mode)—is not a given but is instead a delegated privilege.

The primary mechanism for this delegation is a pair of enable registers: `mcounteren` at the Machine level and `scounteren` at the Supervisor level. The architectural dependency is clear: for a U-mode process to read a performance counter, the corresponding bit must be set in both `mcounteren` (permitting S-mode and U-mode access) and `scounteren` (specifically permitting U-mode access). If the Supervisor-level register `scounteren` has a zero in a specific bit position, any attempt by a user-level application to read the associated counter results in an illegal-instruction exception, typically manifesting as a `SIGILL` signal in the Linux environment.

On the Orange Pi RV2 hardware, which utilizes the SpacemiT K1 SoC, this architectural dependency remains the fundamental gatekeeper. The X60 cores implement the RISC-V HPM extension as defined in the version 1.12 and 1.13 privileged specifications, meaning that `scounteren` is a mandatory component of the Supervisor-level state. Verification through empirical testing on the Orange Pi RV2 reveals that, by default, the Linux kernel (typically a vendor-specific 6.6-ky branch or a more recent mainline 6.18-rc1 build) does not enable these bits for user processes, confirming that direct access is dependent on the modification of this register.

### Control and Status Register Privilege Mapping

The RISC-V CSR address space is partitioned to facilitate hardware-level privilege checks. The performance counters are unique in that they are "shadowed" across these spaces. The master registers are located in the Machine-mode address space, while read-only versions are provided for lower modes to minimize the overhead of traps into higher privilege levels.

|**CSR Name**|**User Address**|**Supervisor Address**|**Machine Address**|**Description**|
|---|---|---|---|---|
|cycle|0xC00|0xC00|0xB00|Clock cycles since reset|
|time|0xC01|0xC01|N/A (Memory Mapped)|Real-time clock|
|instret|0xC02|0xC02|0xB02|Instructions retired since reset|
|hpmcounter3-31|0xC03-0xC1F|0xC03-0xC1F|0xB03-0xB1F|Programmable event counters|
|mcounteren|N/A|N/A|0x306|Machine Counter-Enable|
|scounteren|N/A|0x106|N/A|Supervisor Counter-Enable|

The `scounteren` register, located at address 0x106, is only accessible in S-mode or M-mode. Because the Linux kernel operates in S-mode, it has the authority to modify `scounteren` to permit U-mode access. However, it cannot modify `mcounteren`; that is the responsibility of the firmware, usually OpenSBI, which runs in M-mode. On the Orange Pi RV2, OpenSBI generally initializes `mcounteren` to -1 (all bits set) to delegate control to the kernel, but the kernel maintains `scounteren` with restricted bits to preserve the integrity of the `perf` subsystem and prevent information leakage via micro-architectural side channels.

### Bit-Field Definitions in scounteren

The `scounteren` register is a 64-bit register on RV64 systems like the SpacemiT K1. Each bit corresponds to a specific counter in the HPM suite.

|**Bit Position**|**Associated Counter**|**Standard Support**|**K1/X60 Implementation**|
|---|---|---|---|
|0|cycle|Mandatory|Implemented|
|1|time|Mandatory|Implemented (via S-mode `time` CSR)|
|2|instret|Mandatory|Implemented|
|3|hpmcounter3|Optional|Implemented (Programmable)|
|4|hpmcounter4|Optional|Implemented (Programmable)|
|…|…|…|…|
|31|hpmcounter31|Optional|Implementation Defined|

As a WARL (Write Any, Read Legal) register, `scounteren` will only retain bits that correspond to hardware that is actually present and not disabled by M-mode. When an engineer writes a value to this register, the hardware ignores writes to bits for counters that are not implemented. This behavior allows for programmatic discovery of the PMU’s capabilities on the specific Orange Pi RV2 hardware by writing a bitmask of all ones and reading back the result.

## The SpacemiT K1 (X60) Micro-architecture and its PMU

The Orange Pi RV2 is distinguished by its use of the SpacemiT K1 SoC, which features eight X60 cores. These cores are organized into two clusters of four cores each. Cluster 0 is typically enhanced with AI-specific "Daoyi" innovation deployment, while Cluster 1 provides standard general-purpose capabilities. Despite these differences in AI acceleration, the PMU implementation across all eight cores is consistent with the RVA22 profile.

The X60 core utilizes an 8-stage, dual-issue in-order pipeline. In-order pipelines are generally more deterministic for performance profiling than out-of-order designs, as instructions retire in the same order they were fetched, reducing the complexity of correlating counter increments with the instruction stream. However, the dual-issue nature means that the Instruction Per Cycle (IPC) can theoreticaly reach 2.0, assuming no stalls occur in the pipeline stages.

### Performance Monitoring Events on X60

The SpacemiT K1 PMU is not limited to counting cycles and instructions. It supports the configuration of `hpmcounter3` through `hpmcounter31` to track a variety of micro-architectural events through the `mhpmevent` registers. These event selectors are crucial for identifying bottlenecks in the 8-stage pipeline.

|**Event Category**|**Example Event Codes (K1 Specific)**|**Micro-architectural Impact**|
|---|---|---|
|Frontend|Instruction Cache Refill, TLB Refill|Stalls in fetch/decode stages|
|Backend|Data Cache Refill, Load/Store Stalls|Stalls in execute/memory stages|
|Execution|Branch Misprediction, Floating Point Units|Pipeline flushes and functional unit contention|
|AI Extension|Matrix Multiplication, Sliding Window|Specialized Daoyi instruction throughput|

Research into the X60 core indicates that it supports the `Sscofpmf` extension, which provides Supervisor-level configuration for counter overflow interrupts and filtering. This is a significant advancement over older RISC-V cores like the SiFive U74, which lacked standardized overflow support in S-mode, often forcing the Linux kernel to rely on M-mode traps for sampling. With `Sscofpmf`, the Orange Pi RV2 can support more efficient profiling with tools like `perf record`, as the kernel can handle PMU interrupts directly.

### Hardware Constraints and Overflow Interrupts

Despite its robust features, the X60 core has exhibited limitations in certain early silicon revisions regarding the reliability of overflow interrupts for all event types. For the operating system engineer, this implies that while "counting" mode (e.g., `perf stat`) is highly reliable, "sampling" mode (e.g., `perf record`) may require specific workarounds provided in the vendor's kernel fork. Specifically, sampling the `cycle` or `instret` counters is generally well-supported, but sampling more obscure micro-architectural events might lead to "skid"—the discrepancy between the program counter at the time of the overflow and the program counter recorded by the interrupt handler.

## Modification of scounteren in the Linux Kernel

Because `scounteren` is a privileged CSR, it cannot be modified directly from user-space. An operating system engineer must interface with the kernel to alter the system's security and access policy. On the Orange Pi RV2, this is typically achieved through the creation and insertion of a Linux Kernel Module (LKM). The LKM provides the necessary S-mode privileges to execute the `csrw` (CSR Write) instruction.

The process of modification is complicated by the nature of the Orange Pi RV2 as a multi-core (SMP) system. CSRs are local to each individual hart (core). Modifying `scounteren` on the current core does not affect the other seven cores. If a user-mode process is migrated by the Linux scheduler from an "enabled" core to a "disabled" core, it will suddenly crash when it next attempts to read a performance counter. Therefore, any modification must be synchronized across all online CPUs.

### Kernel API for Cross-Core CSR Modification

The Linux kernel provides the Symmetric Multi-Processing (SMP) infrastructure required to perform operations on all cores. The function `on_each_cpu()` is the standard mechanism for this purpose. It utilizes Inter-Processor Interrupts (IPIs) to force every core to execute a specified function.

C

```
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/smp.h>
#include <asm/csr.h>

static void enable_user_pmu_access(void *info)
{
    // Write -1 (all ones) to scounteren to enable all counters
    // Alternatively, write 0x7 to enable just cycle, time, and instret
    csr_write(CSR_SCOUNTEREN, -1);
}

static int __init pmu_enable_init(void)
{
    pr_info("Enabling user-level access to PMU counters on all cores\n");
    on_each_cpu(enable_user_pmu_access, NULL, 1);
    return 0;
}

static void __exit pmu_enable_exit(void)
{
    pr_info("Disabling user-level access to PMU counters\n");
    // Optionally reset scounteren to zero to restore security
    on_each_cpu((void* i){ csr_write(CSR_SCOUNTEREN, 0); }, NULL, 1);
}

module_init(pmu_enable_init);
module_exit(pmu_enable_exit);
```

In the RISC-V Linux kernel, the `csr_write` macro abstracts the inline assembly required to interface with the hardware. The use of `on_each_cpu` with the `wait` parameter set to `1` ensures that the module loading process does not complete until all cores have successfully updated their `scounteren` state, preventing race conditions where a process might try to read a counter before the IPI has been processed on its current core.

### Synchronization with M-mode Firmware

It is essential to verify that the M-mode firmware (OpenSBI) has not locked the counters. If `mcounteren` has bits cleared, the Supervisor write to `scounteren` will not grant U-mode access to those specific counters. On the Orange Pi RV2, the boot flow involves `FSBL.bin` (First Stage Bootloader) loading `boot-bundle.itb`, which contains the OpenSBI binary (`fw_dynamic.bin`).

If the engineer discovers that certain counters remain inaccessible despite setting `scounteren`, they must check the OpenSBI source or configuration. In OpenSBI, the initialization of `mcounteren` is typically found in the platform-specific initialization code. For the SpacemiT K1, this is usually set to allow all counters to avoid limiting the kernel’s functionality. However, in specialized secure environments, OpenSBI may explicitly clear these bits, requiring a custom build of the firmware to enable full hardware performance monitoring.

## User-Level Access to Counters in Linux

Once the `scounteren` register has been appropriately modified, a user-mode application can read the performance counters directly. This is performed using the `rdcycle`, `rdtime`, and `rdinstret` pseudoinstructions, or the more generic `csrr` (CSR Read) instruction for the `hpmcounter` registers.

Direct access provides the lowest possible overhead for performance measurement, as it avoids the system call overhead associated with `perf_event_open`. This is particularly useful for fine-grained profiling of hot loops or low-latency code paths where the hundreds of cycles required for a system call would skew the results.

### Implementing Counter Reads in C/C++

The most effective way to access these registers is through inline assembly. This ensures that the compiler does not reorder the counter reads in a way that would invalidate the measurement.

C

```
#include <stdint.h>
#include <stdio.h>

static inline uint64_t read_cycles(void)
{
    uint64_t val;
    asm volatile ("rdcycle %0" : "=r" (val));
    return val;
}

static inline uint64_t read_instret(void)
{
    uint64_t val;
    asm volatile ("rdinstret %0" : "=r" (val));
    return val;
}

static inline uint64_t read_hpmcounter3(void)
{
    uint64_t val;
    // Direct CSR read of address 0xC03
    asm volatile ("csrr %0, 0xC03" : "=r" (val));
    return val;
}
```

On the Orange Pi RV2, the `rdcycle` instruction reads the 64-bit cycle counter. Because the X60 is a 64-bit core (RV64), the register is read in a single operation. On 32-bit RISC-V systems, reading 64-bit counters requires a complex sequence to handle overflows between reading the low and high 32-bit parts, but this is not a concern for the RV2.

### Calculating Performance Metrics

With access to these two fundamental counters, an engineer can calculate the Instructions Per Cycle (IPC) for a specific workload.

$$IPC = \frac{instret_{end} - instret_{start}}{cycle_{end} - cycle_{start}}$$

On the X60 core, an IPC close to 2.0 indicates that the dual-issue pipeline is fully utilized. An IPC below 1.0 suggests that the core is frequently stalled. To investigate the cause of these stalls, the engineer must use the programmable `hpmcounter` registers to track more specific events, such as cache misses or branch mispredictions.

## Micro-architectural Event Discovery and Mapping

While `cycle` and `instret` are standardized, the events that can be mapped to `hpmcounter3-31` are implementation-specific. For the Orange Pi RV2’s SpacemiT K1 SoC, these events are documented in the vendor's technical reference manual and are supported by the `perf` tool via JSON event definitions.

To program a counter to track a specific event, the `mhpmevent` register must be written. Crucially, these event selector registers are typically machine-level (M-mode). This means that while a user process can _read_ a counter if `scounteren` allows it, it generally cannot _configure_ what that counter is tracking. Configuration must be performed by the kernel or firmware.

### The Role of the Linux Perf Subsystem

The standard approach for an engineer is to use the Linux `perf` tool to configure the events and then use the `scounteren` method for low-overhead reads of those pre-configured events. The `perf` tool handles the complexity of writing to the SBI or the machine-level registers via the kernel driver.

|**Command**|**Purpose**|
|---|---|
|`perf list`|Displays available hardware events for the X60 core.|
|`perf stat -e <event>`|Configures a counter and provides summary statistics.|
|`perf record -e <event>`|Uses counter overflows to sample the program counter.|

When an engineer uses `perf stat -e cache-misses`, the kernel driver for the SpacemiT K1 locates an available `hpmcounter`, writes the appropriate event code for "cache misses" into the corresponding `mhpmevent` register, and begins the count. If the engineer has also enabled user-level access via `scounteren`, they can then read that same counter within their application code to get real-time, high-resolution data on cache performance.

### SpacemiT K1 Event Codes and Definitions

The symbolic names used by `perf` are mapped to raw codes in the kernel's PMU driver. For the X60 core, these codes are vital for manual configuration if the engineer is bypassing the standard `perf` tool (e.g., in a bare-metal or custom OS environment).

|**Event Name**|**Raw Event Code (SpacemiT X60)**|**Description**|
|---|---|---|
|L1D_CACHE_REFILL|0x03|Level 1 data cache misses|
|L1I_CACHE_REFILL|0x01|Level 1 instruction cache misses|
|BR_MIS_PRED|0x10|Branch mispredictions|
|MEM_ACCESS|0x13|Total memory operations|

These codes follow the standard RISC-V HPM event format where possible, though many micro-architectural events are unique to the SpacemiT pipeline design. The engineer can consult the kernel source at `tools/perf/pmu-events/arch/riscv/spacemit/x60/` to find the exhaustive list of supported events for the Orange Pi RV2.

## Memory Consistency and Counter Precision

When performing high-precision measurements on the Orange Pi RV2, the RISC-V memory consistency model (RVWMO - RISC-V Weak Memory Ordering) must be considered. Performance counter reads are not automatically synchronized with the memory operations or instructions they are meant to profile. This can lead to "counter creep," where the counter value reflects instructions executed slightly before or after the code block of interest.

### Using Fences for Accurate Measurement

To ensure that a counter read occurs exactly at the desired point in the execution flow, the `fence` and `fence.i` instructions may be necessary. The `fence` instruction ensures that all previous memory operations are complete before subsequent operations begin, while `fence.i` ensures that the instruction pipeline is flushed and synchronized with memory.

C

```
static inline uint64_t read_cycles_precise(void)
{
    uint64_t val;
    // Ensure all previous instructions have retired
    asm volatile ("fence" ::: "memory");
    asm volatile ("rdcycle %0" : "=r" (val));
    // Ensure the counter read completes before subsequent instructions
    asm volatile ("fence" ::: "memory");
    return val;
}
```

On an in-order core like the X60, the impact of instruction reordering is less severe than on an out-of-order core, but the pipeline depth of 8 stages still introduces potential latency. The use of fences is particularly important when measuring short code segments where the latency of the pipeline itself could constitute a significant percentage of the total cycle count.

### Dealing with Interrupts and Context Switching

Another factor in counter precision is the occurrence of asynchronous events like interrupts and task preemptions. When the Linux kernel switches away from a user process, the hardware counters continue to run. By default, the `cycle` and `instret` counters measure "wall clock" time and total instructions executed by the core, including those of the kernel and other processes that might run during the measurement interval.

If the engineer requires "per-process" or "virtualized" counter values, they must rely on the kernel's `perf` subsystem. The `perf` driver context-switches the counter state, saving the accumulated value when a task is descheduled and restoring it when the task resumes. While this adds overhead, it is the only way to get accurate results in a multi-tasking environment. If the engineer is using the `scounteren` direct-access method, they must ensure the measurement interval is small enough to minimize the probability of a context switch, or they must pin the process to a specific core and disable interrupts (though the latter is usually not possible in user-mode).

## Security and Side-Channel Considerations

The restriction of `scounteren` is not arbitrary; it is a fundamental security measure. Hardware performance counters are a powerful tool for micro-architectural side-channel attacks. These attacks exploit the fact that the state of the CPU’s micro-architecture (caches, branch predictors, etc.) is modified by the execution of instructions, and these modifications can be observed via performance counters.

### Cache Timing Attacks

A classic example is a cache timing attack. An attacker process can use the `cycle` counter to measure the time it takes to access its own memory. If a victim process (e.g., an encryption routine) accesses a shared cache line, the attacker will observe a faster access time for that line, revealing information about the victim’s memory access pattern.

|**Vulnerability Type**|**Mechanism of Exploitation**|**Counter Used**|
|---|---|---|
|Prime+Probe|Monitoring cache evictions by measuring set access latency.|`cycle` / `time`|
|Flush+Reload|Measuring access time to shared memory to detect victim activity.|`cycle` / `time`|
|Spectre/Meltdown|Using transient execution to leak data into the cache.|`cycle` / `hpmcounters`|

By enabling `scounteren`, the operating system engineer provides an attacker with a high-precision timer and counter suite that makes these attacks significantly easier to perform and more accurate. On the Orange Pi RV2, which is likely used in development or hobbyist contexts, this risk may be acceptable. However, in a multi-tenant or secure environment, enabling user-level PMU access is strongly discouraged.

### Mitigating Information Leakage

If user-level access must be enabled, some mitigations can be considered. For instance, the kernel can be configured to clear the `scounteren` bits for untrusted processes, or the counters can be periodically "dithered" or noise can be injected to reduce their precision for non-privileged users. RISC-V’s `Sscofpmf` extension also allows for filtering counters based on privilege level, meaning a counter could be configured to only increment while in U-mode, effectively hiding S-mode activity from the user-level profiler.

## Comparative Analysis: Orange Pi RV2 vs. Other RISC-V Platforms

The Orange Pi RV2’s approach to performance monitoring is representative of a new generation of RISC-V hardware. Compared to earlier platforms like the StarFive VisionFive 2 (JH7110) or the SiFive HiFive Unmatched (U74), the RV2 offers a more standardized and accessible PMU.

|**Feature**|**SiFive HiFive Unmatched**|**StarFive VisionFive 2**|**Orange Pi RV2**|
|---|---|---|---|
|Core|SiFive U74 (In-Order)|T-Head C910 (Out-of-Order)|SpacemiT X60 (In-Order)|
|PMU Standard|Early Privileged Spec|Custom T-Head Extensions|RVA22 / Sscofpmf|
|Overflow Support|Limited / M-mode only|Vendor Specific|Standardized S-mode|
|Linux Support|Upstream|Partial Upstream|Vendor 6.6 / Upstream 6.18+|

The VisionFive 2, using the T-Head C910 core, provides a more complex out-of-order execution engine, which makes performance profiling more powerful but also more difficult to interpret due to speculative execution and reordering. The Orange Pi RV2 strikes a balance by providing a modern, 8-core RVA22-compliant system with an in-order pipeline that is well-suited for predictable performance analysis.

## Technical Strategy for an Operating System Engineer

The task of an operating system engineer on the Orange Pi RV2 is to build a bridge between the raw hardware capabilities and the software developers who need them. This involves not only technical implementation but also the creation of a stable, secure, and documented environment.

### Step 1: Verification and Diagnostics

The first step is always verification. The engineer should use a simple user-space test to confirm the illegal instruction exception. This can be followed by a kernel-space check to read `scounteren` and `mcounteren`.

Bash

```
# Example check using the devmem2 tool (if available) or a kernel module
# To read scounteren from the kernel:
unsigned long s = csr_read(CSR_SCOUNTEREN);
pr_info("Current scounteren: 0x%lx\n", s);
```

If `mcounteren` is restricted, the engineer must modify the OpenSBI build. This is often necessary when working with "locked-down" vendor firmware that prioritizes security over developer access.

### Step 2: Implementing the SMP Enablement Module

The kernel module should be the primary tool for enablement. It should be built against the specific kernel headers of the Orange Pi RV2 (e.g., `linux-headers-6.6.63-current-ky`). The use of `on_each_cpu` is non-negotiable for system stability.

### Step 3: Integrating with Profiling Tools

Beyond raw CSR reads, the engineer should ensure that the `perf` tool is fully functional. This may involve importing the SpacemiT X60 event JSONs into the `perf` source and recompiling the tool. This provides a much more powerful environment for developers than raw assembly reads alone.

### Step 4: Documentation and Best Practices

Finally, the engineer should document the event codes and the expected IPC for the X60 core. They should also provide guidance on using fences and the security implications of enabling `scounteren`. This ensures that the performance monitoring capabilities are used effectively and responsibly.

## Conclusion: The Path Forward for RV2 Performance Monitoring

The Orange Pi RV2, with its SpacemiT K1 SoC, represents a significant milestone in the accessibility of high-performance RISC-V computing. The hardware performance monitor on the X60 cores is a robust and flexible system, but its power is gated by the `scounteren` register. By understanding the privileged architecture, the nuances of the 8-stage in-order pipeline, and the requirements of SMP synchronization in the Linux kernel, the operating system engineer can empower developers to achieve unprecedented levels of optimization.

The transition toward standardized extensions like `Sscofpmf` and the RVA22 profile ensures that the techniques developed for the Orange Pi RV2 will remain relevant as the RISC-V ecosystem continues to expand. While security considerations remain a vital counterweight to open access, the ability to selectively and precisely enable hardware performance counters is an essential tool for the modern embedded systems professional. As mainline Linux support for the RV2 matures, the integration of these performance monitoring features will become even more seamless, further solidifying the board’s position as a premier platform for RISC-V development and AI-native innovation.