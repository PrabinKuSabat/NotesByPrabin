# Issue
The kernel drivers stops all counters - including the `cycle` CSR  - when no perf events are actively tracking them. *The proof of the same has been given below.* 

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




