---
dg-publish: true
---

# Notes on the GPU-Simulator Accel-Sim

> [!info] Disclaimer  
> The notes are not complete. They will be under constant changes (until the accel-sim development stops 😿.)

> [!success] Link to the Research Paper  
> The research paper can be find at [LINK.](https://par.nsf.gov/servlets/purl/10302226)

---

## [[Accel-sim Walk Around.excalidraw|Walk-through For Accel-Sim]]

## [[Configs available In AccelSim|Available Architecture Configs]]

## [[Benchmarks Available In Accel-Sim|In-built Benchmarks]]

## [[AccelSim Trials|Benchmarks Trials]]

## [[Available Traces|Provided Traces]]

## [[Special Permissions Required]]

---

## Modifications made

> [!important] Define the function yyerror  
> ~/Prabin/secTry/accel-sim-framework/gpu-simulator/gpgpu-sim/src/intersim2/config.l
> ``` 
> # added
> void yyerror(const char* s);
> void yyerror(const char* s){
>   fprintf(stderr, "Parse error: %s\n", s);
> }
> ```

> [!important] Declare the function yyerror  
> ~/Prabin/secTry/accel-sim-framework/gpu-simulator/gpgpu-sim/src/intersim2/config.y
> ```
> # added
> void yyerror(const char* s);
> ```

- Add sm89 support to compiled files
	- _Yet to complete_

For newer Architectures:
- Modify gpgusim.config files after creating
	- gpgpu_n_mem 32
	- gpgpu_memory_partition_indexing 64
	- -gpgpu_cache:dl2 S:512:128:24,L:B:m:L:P,A:192:4,32:F,32  
	  0 was replace with F in **:F,32**.

---

## Benchmark Running Process

_**The following are examples.**_

### Simulating

`../job_launching/run_simulations.py -B parboil -C TITANV-PTX  -N parboil`

### Monitoring a process

`./util/job_launching/monitor_func_test.py -N rodT2`

### Getting Stats

`./get_stats.py -R -B ispass-2009 -C QV100-PTX | tee ../../csvStats/ispass-2009.csv`

### Plotting

> [!warning] Yet to be done

---

## [[Things to try out]]

## TUNER Correlating

[[Tuner Correlating in Accelsim]]

---

> [!warning] This section is not complete.

## Steps For Release Branch

[[Release Branch Installation]]
