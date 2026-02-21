# Goal

To measure the no. of cycles it takes for a dependent load to complete.

# Concept

When we do `while (1) pnt = *pnt` each `*pnt` access will have to wait for the previous load to get completed. Thus the _**LSU**_ will have to wait for the previous load to get completed and then issue the next load with the procured address.

# Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>
#include <unistd.h>
#include <sys/syscall.h>
#include <linux/perf_event.h>
#include <sys/mman.h>

// 1. Setup a dummy perf event to "unlock" the hardware counter for this process
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
#define L10  curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr; curr=(void**)*curr;
#define L100 L10 L10 L10 L10 L10 L10 L10 L10 L10 L10
#define L1000 L100 L100 L100 L100 L100 L100 L100 L100 L100 L100

int main() {
    int fd = open_perf_event();
    if (fd < 0) {
        perror("perf_event_open failed. Run as sudo?");
        return 1;
    }

    // 2. Pointer Chase Setup (16KB for L1)
    size_t size = 16 * 1024;
    void** nodes = aligned_alloc(64, size);
    for (int i = 0; i < (size/8) - 1; i++) nodes[i] = &nodes[i+1];
    nodes[(size/8)-1] = nodes[0];

    register void** curr = nodes[0];
    uint64_t start, end;

    // 3. The Measurement
    // Because the perf 'fd' is open, the kernel ensures scounteren is set for us!
    asm volatile ("fence; rdcycle %0" : "=r"(start) :: "memory");

    for (int i = 0; i < 1000000; i++) {
        // 10 dependent loads
        //curr = (void**)*curr; curr = (void**)*curr; curr = (void**)*curr; curr = (void**)*curr; curr = (void**)*curr;
        //curr = (void**)*curr; curr = (void**)*curr; curr = (void**)*curr; curr = (void**)*curr; curr = (void**)*curr;
        L10
	asm volatile("" : "+r"(curr));
    }

    asm volatile ("fence; rdcycle %0" : "=r"(end) :: "memory");

    printf("Total Cycles: %lu\n", end - start);
    printf("Cycles per load: %f\n", (double)(end - start) / 10000000);

    close(fd);
    return 0;
}
```

**Explanations :**
- You require perf to make use of the SBI to enable the PMU counters. Without the counters won't update as SBI PMU Extension pauses these counters to save energy. Read more at [[Enabling rdcycle read]].
- `asm volatile("" : "+r"(curr));` ensures that the compiler doesn't optimizes away the `curr` loads in the L100 micro.
- `asm volatile ("fence; rdcycle %0" : "=r"(start) :: "memory");`  
  `_1_ :` is for the assembly instructions.  
  `:_2_:` is for output location defining  
  `1:2:_3_:` _3_ is for input location defining  
  `1:2:3:_4_);` _4_ is for Special clobbers: "memory" acts as a compiler memory barrier telling the compiler that memory may have changed.  
  `fence;` here ensures that all the load and stores are completed before executing this line. Ensuring that there is no other relocation happening which disturbs the intended sequence of instructions.

# Hardware Outputs

```
./a.out
Total Cycles: 20274670
Cycles per load: 2.03
```
