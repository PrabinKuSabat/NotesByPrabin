- [ ] 1. Provide HW def file and run microbenchmarks
	- [ ] `make -C ./GPU_Microbenchmark/`
	- [ ] `export CUDA_VISIBLE_DEVICES=0`
	- [ ] `./GPU_Microbenchmark/run_alll.sh | tee stats.txt`
- [ ] 2. Run the tuner
	- [ ] `./tuner.py -s stats.txt`
		- [ ] copy generated folder to `gpgpu-sim/configs/tested-cfgs` and `gpu-simulator/configs/tested-cfgs`
		- [ ] add the name to the file `define-standard-cfgs.yml`
		  Somthing like:
```ad-info 
title: Example
`#RTX4090`
RTX4090:
   base_file:"$GPGPUSIM_ROOT/configs/tested-cfgs/`<above generated folder name>`/gpgpusim.config"
```

- [ ] 3. Tuner Searching
	- [ ] **MOST IMP:** Generating Traces for the microbenchmark suite. 