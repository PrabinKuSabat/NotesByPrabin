1- [ ] 1. Provide HW def file and run microbenchmarks  
	- [ ] `make -C ./GPU_Microbenchmark/`  
	- [ ] `export CUDA_VISIBLE_DEVICES=0`  
	- [ ] `./GPU_Microbenchmark/run_alll.sh | tee stats.txt`
- [x] 2. Run the tuner ✅ 2026-01-23
	- [x] `./tuner.py -s stats.txt` ✅ 2026-01-23
		- [x] copy generated folder to `gpgpu-sim/configs/tested-cfgs` and `gpu-simulator/configs/tested-cfgs` ✅ 2026-01-23
		- [x] add the name to the file `define-standard-cfgs.yml` ✅ 2026-01-23
		  Somthing like:

```ad-info 
title: Example
`#RTX4090`
RTX4090:
   base_file:"$GPGPUSIM_ROOT/configs/tested-cfgs/`<above generated folder name>`/gpgpusim.config"
```

- [x] 3. Tuner Searching ✅ 2026-01-23
	- [x] **MOST IMP:** Generating Traces for the microbenchmark suite. ✅ 2026-01-23
