Before lrc we were talking about this :  
Here is the complete verified analysis with all numbers from the CSVs.  

Full Per-Benchmark Ratio Analysis  
The four problem metrics broken down per benchmark :  
L2 Write Hit Rate — Correl=nan, Err=90.73%  
The single most important finding: Every HW value is exactly 100% across all 12 apps. On the real RTX 4090, every write to L2 is a hit — no exceptions. The sim returns 0–75% across benchmarks. The nan correlation happens because you can't compute Pearson correlation on a constant vector (zero variance on HW side). This is not a tuning issue — it is a fundamental write-policy mismatch.  
BenchmarkHW %SIM %  
All 12  
100  
0–75  
L2 Write Hits — Correl=-0.1024, Err=105.24%  
l1_lat is the killer outlier: SIM=6144 vs HW=2054 — 3× overshoot — which single-handedly flips the correlation negative. Every other benchmark is severely under (SIM≈0 vs HW=384–32768).  
GPC Cycles — Correl=0.976, Err=45.94%  
Two clusters of opposite failure:  
shared_bank_conflicts/1,2 → SIM is 2.5–3.0× OVER HW  
l1_bw_32f, shared_bw → SIM is only 0.55–0.59× of HW  
These two clusters cancel each other in correlation (hence decent Correl=0.976) but destroy the error metric.  
DRAM Reads — Correl=1.0, Err=36.37%  
Perfect shape but systematic under-count. Three distinct sub-problems:  
l1_lat/shared_bw/shared_lat → SIM=0, HW=124–220 — these are HW background/TLB noise, SIM=0 is likely correct  
MaxIops_int32/l1_shared_bw → SIM ~0.55× HW — L1 over-hits in sim, fewer DRAM misses  
l1_bw_* → SIM ~0.87–0.92× HW — sector granularity mismatch  

Verified Fix Plan (with Assurance %)  
Fix 1 — L2 Write Policy (Addresses L2 Write HitRate + Write Hits) — 92% assurance  
Root cause: Ada Lovelace RTX 4090 has 72MB L2 in write-back mode. With such a large L2, every eviction write-back finds the dirty sector in L2 = 100% write hit rate. GPGPU-Sim's RTX4090 config likely inherited an older write policy that generates write misses.  
What to check in gpgpusim.config:  
bash  
grep "cache_l2_cfg\|l2_config\|write_policy\|l2_write" \  
gpu-simulator/gpgpu-sim/configs/tested-cfgs/NVIDIA_GeForce_RTX_4090/gpgpusim.config  
  
The L2 config format is: cache_l2_cfg N:S:A:B:Wlat,E,wf:WP:Alat,E,bf where WP = write policy. It must be W (write-back) not T (write-through) or A (write-allocate-only). If the config has WP=T or a no-write-allocate policy, change to write-back and the L2 write hit rate will jump toward 100%.  
Why 92% (not 100%): the absolute count of L2 write hits also depends on eviction pressure, which depends on L2 size modeling. If L2 size in config does not match the true 72MB, hit rate will improve but may not reach 100%.  

Fix 2 — Shared Memory Bank Conflict Cycles (Addresses GPC Cycles 2.5–3× over) — 88% assurance  
Root cause: Ada SM90 redesigned shared memory bank conflict handling. The RTX 4090 has dual warp schedulers per SM with improved replay reduction. The sim is adding ~2.5–3× more stall cycles per bank-conflicted warp than hardware actually does.  
What to check:  
bash  
grep "shmem_latency\|shared_mem_lat\|bank_conflict\|shmem_warp_parts\|shmem_num_banks" \  
gpu-simulator/gpgpu-sim/configs/tested-cfgs/NVIDIA_GeForce_RTX_4090/gpgpusim.config  
  
The key parameter is gpgpu_shmem_warp_parts — this controls how many sub-groups a conflicting warp is split into for serialized replay. Ada hardware handles this more efficiently:  
If gpgpu_shmem_warp_parts 1 → try increasing to match Ampere baseline  
Also verify gpgpu_shmem_access_latency — Ada SM90 is measured at ~19 cycles for conflict-free shared memory vs 23–26 cycles on older architectures. The sim may be using a too-high value.  
Why 88%: bank conflict cycle modeling in GPGPU-Sim involves multiple interacting parameters; a single knob rarely fixes it perfectly.  

Fix 3 — L1/Shared Memory Bandwidth & Latency (Addresses GPC Cycles 0.55–0.79× under) — 75% assurance  
Root cause: l1_bw_32f (0.59×) and shared_bw (0.55×) run faster in sim than HW — meaning the sim completes memory ops with fewer stalls. Ada SM90 has a known quirk: the 32-bit L1 read path has reduced effective bandwidth vs 128-bit (32-byte transactions) due to sector-level tracking overhead. The sim models peak bandwidth regardless of access width.  
What to check:  
bash  
grep "l1_latency\|gpgpu_l1_latency\|l1_cache_latency\|icnt_shmem_latency" \  
gpu-simulator/gpgpu-sim/configs/tested-cfgs/NVIDIA_GeForce_RTX_4090/gpgpusim.config  
  
gpgpu_l1_latency should be ~28–32 cycles for Ada (HW measured at 28–30 cycles for L1D hit on RTX4090)  
If currently set to lower (e.g., 20 cycles), increasing it will raise l1_lat/shared_bw/l1_bw_32f cycle counts toward HW  
For l1_bw_32f specifically, the 32-bit path issue: check if gpgpu_cache:dl1 line size is set to 128B. On Ada with 32B sectors, a 32f load fills 1 sector but the pipeline stall is per-transaction, not per-sector  
Why 75%: latency tuning for Ada requires microbenchmark-driven iteration; a single value change rarely closes the full 40% gap without re-running benchmarks.  

Fix 4 — DRAM Read Under-count (l1_bw_* at 0.87–0.92×) — 70% assurance  
Root cause: The sim counts DRAM transactions at 32B sector granularity but may aggregate them differently from the HW NV performance counters. The l1_bw_32f ratio drops to 0.87 (not 0.92 like l1_bw_128) because 32-bit accesses generate more fragmented sector requests.  
What to check:  
bash  
grep "gpgpu_n_mem\|gpgpu_dram_buswidth\|gpgpu_dram_burst_length\|dram_latency" \  
gpu-simulator/gpgpu-sim/configs/tested-cfgs/NVIDIA_GeForce_RTX_4090/gpgpusim.config  
  
RTX 4090 hardware specs:  
gpgpu_n_mem = 24 (384-bit bus = 12 x32b controllers × 2)  
gpgpu_dram_buswidth = 16 (16 bytes per controller per cycle)  
gpgpu_dram_burst_length = 2 for GDDR6X  
Why 70%: some of the DRAM under-count for latency benchmarks (l1_lat SIM=0 vs HW=196) is genuine HW background noise and cannot be fixed by config alone.  

Summary Priority Table  
PriorityFixProblemAssuranceEffort  
1  
L2 write policy → write-back  
L2 HitRate 0→100%, L2 hits 0→correct  
92%  
1 line in config  
2  
Shared mem bank conflict latency  
GPC cycles 2.5–3× over on bank-conflict  
88%  
2–3 config params  
3  
L1/Shared latency increase  
GPC cycles 0.55–0.79× under  
75%  
2–3 config params + re-run  
4  
DRAM partition/sector count  
DRAM reads 0.87–0.92×  
70%  
3–4 config params  
Recommended next step: Pull the RTX4090 gpgpusim.config and I'll audit it line-by-line against known Ada Lovelace HW specs and show you the exact before/after diff for all 4 fixes.  
  
Do you think this is still required?  
Then plan out a implementation that we will run either together or in separate messages

# Section 5: Scene Description and Variables**

[[Case Study - Mathematical Modelling Presentation Notes Section 5]]
[[]]
