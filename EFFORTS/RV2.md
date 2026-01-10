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
1. l1 : 17.0 cycles / 1.7ns
2. l2 : 22.6 cycles / 15.9 ns
3. memory :
	- 20 MB load : 65.1 cycles / 42.4ns
	- 24MB load : 91.4 cycles
