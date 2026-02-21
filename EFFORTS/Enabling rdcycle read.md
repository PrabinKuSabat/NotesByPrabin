# Issue
The kernel drivers stops all counters - including the `cycle` CSR  - when no perf events are actively tracking them. *The proof of the same has been given below* [[#Proof ->]].The hardware register is physically paused to save energy and thus results in a constant cycle number read over time.

## Why perf Works?
Invoking `perf_event_open()` explicitly request the `PERF_COUNT_HW_CPU_CYCLES` hardware event. This action tells the kernel to issue an `SBI_EXT_PMU_COUNTER_START` command, which wakes up the **PMU** and allows the `cycle `register to increment. Consequently, allowing the `rdcycle` assembly to read a advancing value. 

# Solutions
## User-Space Dummy Perf Workaround
The simplest solution is to keep the hardware counter active by opening a dummy `perf`event in user space right before invoking the custom system call / the assembly rdcycle.  

## Proof ->
This simple program without the `perf_event_open()` reads the same `CSR` Values: 
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>
#include <unistd.h>
#include <sys/syscall.h>
#include <linux/perf_event.h>
#include <sys/mman.h>


int main(){
    uint64_t temp;
    for(int i = 0; i < 5; i++){
        asm volatile("csrr %0, 0XC00" : "=r" (temp) :: "memory");
        printf("Cycles %d :: %lu\n", i , temp);
    }

    return EXIT_SUCCESS;
}
```

**Gives : 
```bash
Cycles 0 :: 9223372036875522923
Cycles 1 :: 9223372036875522923
Cycles 2 :: 9223372036875522923
Cycles 3 :: 9223372036875522923
Cycles 4 :: 9223372036875522923
```

**Whereas the same code with** `perf_event_open()` :
```c
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>
#include <unistd.h>
#include <sys/syscall.h>
#include <linux/perf_event.h>
#include <sys/mman.h>

int open_perf_event() {
    struct perf_event_attr attr = {
        .type = PERF_TYPE_HARDWARE,
        .config = PERF_COUNT_HW_CPU_CYCLES,
        .size = sizeof(struct perf_event_attr),
        .disabled = 0,
        .exclude_kernel = 1,
        .exclude_hv = 1,
    };
    return syscall(__NR_perf_event_open, &attr, 0, -1, -1, 0);
}

int main(){
    int fd = open_perf_event();
    if (fd < 0) {
        perror("perf_event_open failed. Run as sudo?");
        return 1;
    }
    uint64_t temp;
    for(int i = 0; i < 5; i++){
        asm volatile("csrr %0, 0XC00" : "=r" (temp) :: "memory");
        printf("Cycles %d :: %lu\n", i , temp);
    }

    return EXIT_SUCCESS;
}
```
**Gives** :
```bash
Cycles 0 :: 9223372036854787000
Cycles 1 :: 9223372036855117439
Cycles 2 :: 9223372036855146005
Cycles 3 :: 9223372036855157477
Cycles 4 :: 9223372036855165788 
```

**Similarly with the System Call latency test** :
```c
#include <linux/kernel.h>
#include <linux/syscalls.h>
#include <linux/slab.h>
#include <linux/irqflags.h>
#include <linux/uaccess.h>   /* copy_to_user */
#include <asm/io.h>

/* Constants for the micro-benchmark */
#define L1_SIZE   (32 * 1024)
#define PASSES    100

/**
 * rdcycle64 - Reads the RISC-V cycle CSR
 * Returns the current value of the hardware cycle counter.
 */
static inline u64 rdcycle64(void)
{
    u64 val;
    asm volatile("rdcycle %0" : "=r"(val) :: "memory");
    return val;
}

/**
 * SYSCALL_DEFINE1(riscv_read_mstatus, …)
 * * Performs a timed memory-load benchmark against an L1-sized buffer.
 * @out_cycles: Userspace pointer to store the resulting 64-bit cycle count.
 */
SYSCALL_DEFINE1(riscv_read_mstatus, u64 __user *, out_cycles)
{
    u64 *array;
    size_t len = L1_SIZE / sizeof(u64);
    volatile u64 tmp = 0;
    u64 start, end, cycles;
    unsigned long flags;
    int i;

    /* Basic sanity check for userspace pointer */
    if (!out_cycles)
        return -EINVAL;

    /* Allocate buffer matching L1 Cache size */
    array = kmalloc(L1_SIZE, GFP_KERNEL);
    if (!array)
        return -ENOMEM;

    /* Initialize array data */
    for (i = 0; i < (int)len; i++)
        array[i] = (u64)(i + 1);

    /* * Warm L1 Cache:
     * Access the data multiple times before measurement to ensure 
     * it is loaded into the L1 cache.
     */
    for (i = 0; i < 16; i++)
        for (size_t j = 0; j < len; j++)
            tmp += array[j];

    /* Ordering barriers to prevent compiler/CPU reordering */
    asm volatile("" ::: "memory");
    asm volatile("fence" ::: "memory");

    /* * Reduce system noise:
     * Disable preemption and local interrupts to ensure the 
     * measurement isn't interrupted by other tasks.
     */
    preempt_disable();
    local_irq_save(flags);

    /* START MEASUREMENT */
    start = rdcycle64();

    for (u64 p = 0; p < PASSES; p++) {
        /* Loop unrolled by 8 to maximize load throughput */
        for (size_t k = 0; k < len; k += 8) {
            tmp += array[k + 0];
            tmp += array[k + 1];
            tmp += array[k + 2];
            tmp += array[k + 3];
            tmp += array[k + 4];
            tmp += array[k + 5];
            tmp += array[k + 6];
            tmp += array[k + 7];
        }
    }

    end = rdcycle64();
    /* END MEASUREMENT */

    /* Ensure memory operations complete before ending the timing */
    asm volatile("fence" ::: "memory");
    asm volatile("" ::: "memory");

    /* Restore system state */
    local_irq_restore(flags);
    preempt_enable();

    /* Clean up memory */
    kfree(array);

    cycles = end - start;

    /* Copy the 64-bit result to userland; return 0 on success, -EFAULT on failure */
    if (copy_to_user(out_cycles, &cycles, sizeof(cycles)))
        return -EFAULT;

    return 0;
}
```

and when the above is called without the `perf` in ***user space gives you zero cycles***. But with `perf` :
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/syscall.h>
#include <stdint.h>
#include <errno.h>
#include <string.h>
#include <linux/perf_event.h>

// Replace this with the actual number you assigned in the kernel
// (e.g., __NR_riscv_read_mstatus)
#define SYS_RISCV_READ_MSTATUS 264

int open_perf_event(){
    struct perf_event_attr attr = {
        .type = PERF_TYPE_HARDWARE,
        .config = PERF_COUNT_HW_CPU_CYCLES,
        .size = sizeof(struct perf_event_attr),
        .disabled = 0,
        .exclude_kernel = 1,
        .exclude_hv = 1,
    };
    return syscall(__NR_perf_event_open, &attr, 0, -1, -1, 0);
}

int main() {
    uint64_t cycle_count = 0;
    long result;

    int fd = open_perf_event();
    if(fd < 0){
        perror("perf_event_open failed.");
        return 1;
    }
    printf("Calling custom RISC-V syscall (Number: %d)…\n", SYS_RISCV_READ_MSTATUS);

    // Call the syscall: syscall(number, argument1)
    result = syscall(SYS_RISCV_READ_MSTATUS, &cycle_count);

    if (result == 0) {
        printf("Success!\n");
        printf("Total cycles for L1 memory operations: %lu\n", cycle_count);
        printf("Total cycles per load %lu\n", cycle_count/(4*1024*100));
        
        // Basic calculation for cycles per pass
        // Note: The kernel code defines PASSES as 100
        printf("Average cycles per pass: %lu\n", cycle_count / 100);
    } else {
        fprintf(stderr, "Syscall failed: %s (Error No: %d)\n", strerror(errno), errno);
        if (errno == ENOSYS) {
            printf("Hint: The kernel doesn't recognize this syscall number.\n");
        }
    }

    return 0;
}
```

gives :
```
Calling custom RISC-V syscall (Number: 264)…
Success!
Total cycles for L1 memory operations: 3853814
Total cycles per load 9
Average cycles per pass: 38538
```
---




