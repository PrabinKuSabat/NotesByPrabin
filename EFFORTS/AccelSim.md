# Basic AccelSim workings

[[AccelSim Trials]]

## Configs available

1. KEPLER_TITAN (SM 3.5)
2. TITANX (SM 5.2)
3. RTX2060 (SM 7.5)
4. RTX2060_S (SM 7.5)
5. GV100 (SM 7.0)
6. QV100 (SM 7.0)
7. TITANV (SM 7.0)
8. A100 (SM 8.0)
9. RTX3070 (SM 8.6)

## Benchmarks Available

1. rodinia_2.0-ft
2. GPU_Microbenchmark
3. GPU_Atomic
4. Atomic_Profile
5. Atomic_Diverge
6. Deepbench_nvidia_tencore
	1. Not available (18-07)
7. Deepbench_nvidia_normal
	1. Not available (18-07)
8. sdk-4.2-scaled
	1. Not available (18-07)
9. rodinia-3.1
	1. myocyte failed (TITANV-PTX, 15-07)
10. parboil
	1. all 9 tests failed (TITANV-PTX, 15-07)
11. polybench
12. cutlass_volta
13. cutlass_ampere
14. cutlass_hopper
15. cutlass
16. ispass-2009
	1. 7/10 no error(QV100-PTX, 15 -07)
17. dragon-naive
18. dragon-cdp
19. proxy-apps-doe
20. pannotia
21. lonestargpu-2.0
22. mlperf_inference
23. mlperf_training
24. mlperf_inference_no_external_datasets
25. pytorch_examples
26. huggingface
	1. Failed executing helloworld(TITANV-PTX, 15-07)

### Available Traces

        cutlass: Compressed = 77.00 G, Uncompressed = 1.70 T
        deepbench: Compressed = 55.00 G, Uncompressed = 1.20 T
        parboil: Compressed = 8.70 G, Uncompressed = 182.00 G
        polybench: Compressed = 17.00 G, Uncompressed = 585.00 G
        rodinia_2.0-ft: Compressed = 7.20 M, Uncompressed = 162.00 M
        rodinia-3.1: Compressed = 1.80 G, Uncompressed = 56.00 G
        ubench: Compressed = 82.00 M, Uncompressed = 2.60 G

### Modifications made

#### ~/Prabin/secTry/accel-sim-framework/gpu-simulator/gpgpu-sim/src/intersim2/config.l

``` 
# added
void yyerror(const char* s);
void yyerror(const char* s){
  fprintf(stderr, "Parse error: %s\n", s);
}
```

#### ~/Prabin/secTry/accel-sim-framework/gpu-simulator/gpgpu-sim/src/intersim2/config.y

```
#added
void yyerror(const char* s);
```

### Benchmark Running Process

#### Simulating

`../job_launching/run_simulations.py -B parboil -C TITANV-PTX  -N parboil`

#### Monitoring a process

`./util/job_launching/monitor_func_test.py -N rodT2`

#### Getting Stats

`./get_stats.py -R -B ispass-2009 -C QV100-PTX | tee ../../csvStats/ispass-2009.csv`

#### Plotting

``

- [ ] 1. Tracer

```bash
# after building once only execute
source ./gpu-app-collection/src/setup_environment

# This generates traces for the benchmark (Replace Rodinia with the list of benchmarks)
## give the prper device Number after -D
./util/tracer_nvbit/run_hw_trace.py -B rodinia_2.0-ft -D 0
```

More on `ris:ArrowRightS` [[AccelSim Tracer Extended to other apps.]]

- [ ] 2. SASS Generation

```bash
# Do the following basics first
pip3 install -r requirements.txt
source ./gpu-simulator/setup_envrionment.sh

# Build with make
make -j -C ./gpu-simulator/

# Build with CMake
cmake -S ./gpu-simulator/ -B ./gpu-simulator/build

## For the next one, cast the outputs of YY_ to (char *) from (char const *) at line 1248 and 1137 wherever the yyerror() is called.
cmake --build ./gpu-simulator/build -j8

# Produces an executable in 
## ./gpu-simulator/bin/release/accel-sim.out
cmake --install ./gpu-simulator/build

# Sample Example For Acel-Sim's SASS traces-driven mode.
./util/job_launching/run_simulations.py -B rodinia_2.0-ft -C QV100-SASS -T ./hw_run/traces/device-<device-num>/<cuda-version>/ -N myTest



```

- [ ] 3. Correlator

```bash 
# export the following it's necessary
# ensure that the accel-sim-framework directory path is correct
export GPUAPPS_ROOT=/home/cab-prj/Prabin/accel-sim-framework/gpu-app-collection

nano c

```

- [ ] 4. Tuner

```c
//RTX4090 HW Primary Definition File
//loveLace_RTX4090_hw_def.h <- File Name

#ifndef NV4090_HW_DEF_H
#define NV4090_HW_DEF_H

#include "./common/common.h"
#include "./common/deviceQuery.h"

// L1 Data Cache size per SM (unified L1 data / shared memory): 128 KB
#define L1_SIZE                      (128 * 1024)    // 131072 bytes  // source: NVIDIA Ada GPU Architecture Whitepaper; TechPowerUp RTX 4090 Database

// GPU clock frequency (MHz): choose the Boost clock for maximal performance
#define CLK_FREQUENCY                2520            // MHz  // source: NVIDIA Ada Lovelace GPU (Wikipedia); NVIDIA Ada GPU Architecture Whitepaper

#define ISSUE_MODEL                  issue_model::single   // single-issue per warp scheduler (each sub-core has one dispatch unit)   // source: NVIDIA Ada Lovelace Microarchitecture (Proviz Whitepaper)
#define CORE_MODEL                   core_model::subcore   // SM is divided into 4 sub-core partitions   // source: NVIDIA Ada Lovelace Microarchitecture (Proviz Whitepaper)

#define DRAM_MODEL                   dram_model::GDDR6X    // GDDR6X memory   // source: NVIDIA Ada Lovelace Microarchitecture (Wikipedia); TechPowerUp RTX 4090 Database

#define WARP_SCHEDS_PER_SM           4      // 4 warp schedulers per SM (one per sub-core partition)   // source: NVIDIA Ada Lovelace Microarchitecture (Proviz Whitepaper)

// number of SASS HMMA per 16×16 PTX WMMA for FP16→FP32 accumulate operation
#define SASS_hmma_per_PTX_wmma       1      // one HMMA SASS per 16×16 PTX WMMA (FP16→FP32)   // source: “Dissecting the NVIDIA Hopper Architecture” (arXiv microbenchmark)

// These L2 cache banking parameters are not publicly documented; placeholders given:
#define L2_BANKS_PER_MEM_CHANNEL     0      // TODO: banks per memory channel (unknown)
#define L2_BANK_WIDTH_in_BYTE        0      // TODO: bank width in bytes (unknown)

#endif // NV4090_HW_DEF_H

```

``` bash
#After adding the above .h to the directory hw_def 
#add the file name to the hw_def.h file.

make -C ./util/tuner/GPU_Microbenchmark/

export CUDA_VISIBLE_DEVICES=0 #Choose the device u want to tune to.

# Run the ubench and save output in stats.txt
./util/tuner/GPU_Microbenchmark/run_all.sh | tee stats.txt
# Run the tuner with the stats.txt from the previous step
./util/tuner/tuner.py -s stats.txt

#ensure to copy the generated folder to the gpgpu-sim and accel-sim directories
cp -r TITAN_V ../../gpu-simulator/gpgpu-sim/configs/tested-cfgs/
cp -r TITAN_V ../../gpu-simulator/configs/tested-cfgs/

# Volta
TITANV:
   base_file: "$GPGPUSIM_ROOT/configs/tested-cfgs/TITAN_V/gpgpusim.config"
```

# TUNER Correlating

[[Tuner Correlating in Accelsim]]

# Steps For Release Branch

[[Release Branch Installation]]
