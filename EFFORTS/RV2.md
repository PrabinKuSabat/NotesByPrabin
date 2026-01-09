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






