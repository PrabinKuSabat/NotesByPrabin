---
dg-publish: true
---

# GPU Microbenchmark

This suite includes low-level GPU microbenchmarks (cache/memory/compute). It is part of the AccelWattch collection of microbenchmarks[github.com](https://github.com/accel-sim/accel-sim-framework#:~:text=AccelWattch%20microbenchmarks%20and%20AccelWattch%20validation,and%20AccelWattch%20MICRO%2721%20Artifact%20Manual). The listed tests include:

- **l1_bw_32f**, **l1_bw_64f**, **l1_bw_128** – L1 cache bandwidth tests (32-bit, 64-bit, 128-byte blocks)  
	 ✅
- **l1_lat** – L1 cache latency test
	 
- **l2_bw_32f**, **l2_bw_64f** – L2 cache bandwidth tests (32-bit, 64-bit)  
	 ✅
- **l2_lat** – L2 cache latency test  
	 ✅
- **mem_bw** – Global memory bandwidth test  
	 ✅
- **mem_lat** – Global memory latency test  
	 ✅
- **shared_bw** – Shared memory bandwidth test
	 
- **shared_lat** – Shared memory latency test  
	 ✅
- **shared_bank_conflicts** – Shared memory bank conflict test (two kernels: one with conflicts, one conflict-free)  
	 ✅
- **MaxIops_int32** – Maximum integer operations (INT32) throughput test  
	 ❌
- **l1_shared_bw** – Combined L1/shared memory bandwidth test
	 
- **l1_bw_32f_unroll**, **l1_bw_32f_unroll_large** – Unrolled L1 bandwidth tests  
	 ✅
- **cp_async** – Asynchronous copy test (copy latency/bandwidth)  
	 ❌
---
