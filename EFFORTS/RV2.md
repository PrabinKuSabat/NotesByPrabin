**CPU Name:**  
**CPU Details:** `lscpu`
- 8 cpus
- 1 thread per core.
- 1 socket
- max clock : 1600mhz
- min clock : 614.400mhz

---
**Memory Details:** `lscpu --caches`
1. L1:
	1. l1d :
		- instace size : 32K
		- total size : 256K (8 instances)
		- assoc : 4 ways
		- level : 1
		- sets 128
		- coherency size: 64
	2. l1i : 256 KiB core.
		- instace size : 32K
		- total size : 256K (8 instances)
		- assoc : 4 ways
		- level : 1
		- sets 128
		- coherency size: 64
2. L2:
	- instace size : 512K
	- total size : 1M (8 instances)
	- assoc : 16 ways
	- level : 2
	- sets : 512
	- coherency size : 64

> [!question]  
> What's all the parameters shown in gretconf -a mean?  
> POSIX, it's types and usages?

---
**Latency:**
1. l1 : 17.0 cycles / 1.7nsx
2. l2 : 22.6 cycles / 15.9 ns
3. memory :
	- 24MB load : 91.4 cycles
	- 20 MB load : 65.1 cycles / 42.4ns

---

```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

typedef struct node {
    struct node *next;
    long padding;
} Node;

static inline double now_ns() {
    struct timespec ts;
    clock_gettime(CLOCK_MONOTONIC_RAW, &ts);
    return ts.tv_sec * 1e9 + ts.tv_nsec;
}

void create_random_list(Node *list, int size) {
    for (int i = 0; i < size - 1; i++)
        list[i].next = &list[rand() % size];
    list[size - 1].next = &list[0];
}

double measure_latency(Node *list, long iterations) {
    volatile Node *p = list;

    // Warm-up
    for (int i = 0; i < 10000; i++)
        p = p->next;

    double start = now_ns();
    for (long i = 0; i < iterations; i++) {
        p = p->next;     // dependent load
    }
    double end = now_ns();

    return (end - start) / iterations;
}

int main() {
    printf("=== Orange Pi RV2 Cache Latency (ns per access) ===\n\n");

    // L1: 32 KB → 2048 nodes
    int l1_nodes = 2000;
    Node *l1 = aligned_alloc(64, l1_nodes * sizeof(Node));
    create_random_list(l1, l1_nodes);
    printf("L1  (~32 KB): %f ns\n", measure_latency(l1, 200000000));
    free(l1);

    // L2: 512 KB → 32768 nodes
    int l2_nodes = 32000;
    Node *l2 = aligned_alloc(64, l2_nodes * sizeof(Node));
    create_random_list(l2, l2_nodes);
    printf("L2 (~512 KB): %f ns\n", measure_latency(l2, 200000000));
    free(l2);

    // DRAM: 16 MB
    int mem_nodes = (16 * 1024 * 1024) / sizeof(Node);
    Node *mem = aligned_alloc(64, mem_nodes * sizeof(Node));
    create_random_list(mem, mem_nodes);
    printf("DRAM (~16 MB): %f ns\n", measure_latency(mem, 100000000));
    free(mem);

    return 0;
}
```
