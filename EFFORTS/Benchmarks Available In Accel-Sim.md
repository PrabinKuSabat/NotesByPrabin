---
dg-publish: true
---

# Benchmarks Available in the

1. [[rodinia_2.0-ft]]
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

# Chat-GPT Description

## Rodinia 2.0 (functional tests)

Rodinia is a widely used heterogeneous benchmark suite (CPU+GPU) from University of Virginia[github.com](https://github.com/yuhc/gpu-rodinia#:~:text=The%20University%20of%20Virginia%20Rodinia,both%20multicore%20CPUs%20and%20GPUs). The _rodinia_2.0-ft_ suite includes the following Rodinia 2.0 functional-test workloads:

- **backprop-rodinia-2.0-ft** – (Neural network backpropagation)
	 
- **bfs-rodinia-2.0-ft** – (Breadth-first search on a graph)
	 
- **hotspot-rodinia-2.0-ft** – (Thermal simulation of a chip)
	 
- **heartwall-rodinia-2.0-ft** – (Medical image/video processing)
	 
- **lud-rodinia-2.0-ft** – (Dense linear algebra: LU decomposition)
	 
- **nw-rodinia-2.0-ft** – (Needleman-Wunsch sequence alignment)
	 
- **nn-rodinia-2.0-ft** – (Nearest Neighbor classification)
	 
- **pathfinder-rodinia-2.0-ft** – (Dynamic programming pathfinding)
	 
- **srad_v2-rodinia-2.0-ft** – (Speckle reducing anisotropic diffusion in image processing)
	 
- **streamcluster-rodinia-2.0-ft** – (Streaming k-means clustering)

## GPU Microbenchmark

This suite includes low-level GPU microbenchmarks (cache/memory/compute). It is part of the AccelWattch collection of microbenchmarks[github.com](https://github.com/accel-sim/accel-sim-framework#:~:text=AccelWattch%20microbenchmarks%20and%20AccelWattch%20validation,and%20AccelWattch%20MICRO%2721%20Artifact%20Manual). The listed tests include:

- **l1_bw_32f**, **l1_bw_64f**, **l1_bw_128** – L1 cache bandwidth tests (32-bit, 64-bit, 128-byte blocks)
	 
- **l1_lat** – L1 cache latency test
	 
- **l2_bw_32f**, **l2_bw_64f** – L2 cache bandwidth tests (32-bit, 64-bit)
	 
- **l2_lat** – L2 cache latency test
	 
- **mem_bw** – Global memory bandwidth test
	 
- **mem_lat** – Global memory latency test
	 
- **shared_bw** – Shared memory bandwidth test
	 
- **shared_lat** – Shared memory latency test
	 
- **shared_bank_conflicts** – Shared memory bank conflict test (two kernels: one with conflicts, one conflict-free)
	 
- **MaxIops_int32** – Maximum integer operations (INT32) throughput test
	 
- **l1_shared_bw** – Combined L1/shared memory bandwidth test
	 
- **l1_bw_32f_unroll**, **l1_bw_32f_unroll_large** – Unrolled L1 bandwidth tests
	 
- **cp_async** – Asynchronous copy test (copy latency/bandwidth)

## GPU Atomic Tests

Atomic-operation benchmarks (AccelWattch microbenchmarks) evaluate atomic add performance. The suite includes:

- **atomic_add_bw** – Atomic-add bandwidth test (single thread)
	 
- **atomic_add_bw_conflict** – Atomic-add bandwidth with high contention (many threads)
	 
- **atomic_add_bw_profile** – Atomic-add profiling test
	 
- **atomic_add_bw_diverge** – Atomic-add divergence test

## Atomic Profile and Divergence Tests

These suites run the same atomic-add kernel with varying parameters:

- **Atomic_Profile:** `atomic_add_bw_profile` (multiple runs varying thread count)
	 
- **Atomic_Diverge:** `atomic_add_bw_diverge` (multiple runs with thread divergence)

## NVIDIA DeepBench (Tensor Cores)

NVIDIA’s DeepBench provides microbenchmarks for tensor-core performance. The _tencore_ variant includes:

- **conv_bench-tencore** – Convolutional layers benchmark (uses Tensor Cores)
	 
- **gemm_bench-tencore** – General matrix multiply (GEMM) benchmark (Tensor Cores)
	 
- **rnn_bench-tencore** – RNN (LSTM/GRU) benchmark (Tensor Cores)

## NVIDIA DeepBench (Normal)

The _normal_ DeepBench (CUDA FP16/FP32) suite includes:

- **conv_bench** – Convolutional layers benchmark (CUDA)
	 
- **gemm_bench** – GEMM benchmark (CUDA)
	 
- **rnn_bench** – RNN (LSTM/GRU) benchmark (CUDA)

## CUDA SDK 4.2 Examples (scaled)

Classic CUDA SDK sample kernels (scaled data sizes) from CUDA 4.2:

- **vectorAdd**
	 
- **convolutionSeparable**
	 
- **fastWalshTransform**
	 
- **scalarProd**
	 
- **transpose**
	 
- **scan**
	 
- **BlackScholes**
	 
- **histogram**
	 
- **mergeSort**
	 
- **sortingNetworks**

## Rodinia 3.1

Rodinia 3.1 (an updated version of the Rodinia suite) includes:

- **b+tree-rodinia-3.1** – B+ Tree search (graph/ search algorithm)
	 
- **backprop-rodinia-3.1** – Backpropagation neural network
	 
- **bfs-rodinia-3.1** – Breadth-first search on a graph
	 
- **dwt2d-rodinia-3.1** – 2D Discrete Wavelet Transform (image processing)
	 
- **gaussian-rodinia-3.1** – Gaussian blur (image processing)
	 
- **hotspot-rodinia-3.1** – Thermal simulation (physics)
	 
- **lavaMD-rodinia-3.1** – N-body molecular dynamics (LavaMD)
	 
- **lud-rodinia-3.1** – LU decomposition (linear algebra)
	 
- **myocyte-rodinia-3.1** – Myocyte simulation (biology)
	 
- **nn-rodinia-3.1** – Nearest neighbor classification
	 
- **nw-rodinia-3.1** – Needleman–Wunsch sequence alignment
	 
- **particlefilter_float-rodinia-3.1** – Particle filter (float version)
	 
- **particlefilter_naive-rodinia-3.1** – Particle filter (naive version)
	 
- **pathfinder-rodinia-3.1** – Dynamic pathfinding
	 
- **srad_v1-rodinia-3.1** – SRAD version 1 (image denoising)

_(Rodinia is a diverse GPU benchmark suite covering domains like graph traversal, linear algebra, image processing, etc.[github.com](https://github.com/yuhc/gpu-rodinia#:~:text=The%20University%20of%20Virginia%20Rodinia,both%20multicore%20CPUs%20and%20GPUs).)_

## Parboil

The Parboil benchmarks (Illinois/IMPACT) are throughput-oriented scientific workloads[hgpu.org](https://hgpu.org/?p=7496#:~:text=The%20Parboil%20benchmarks%20are%20a,benchmarks%20from%20throughput%20computing%20application). This suite includes:

- **parboil-sad** – Sum of Absolute Differences (stereo matching)
	 
- **parboil-sgemm** – Single-precision GEMM (matrix multiply)
	 
- **parboil-stencil** – 3D stencil computation
	 
- **parboil-cutcp** – CUTCP (bioinformatics / crystallography)
	 
- **parboil-mri-q** – MRI-Q (magnetic resonance imaging)
	 
- **parboil-histo** – Histogram
	 
- **parboil-spmv** – Sparse matrix–vector multiply
	 
- **parboil-mri-gridding** – MRI gridding (medical imaging)
	 
- **parboil-bfs** – Breadth-first search (one input graph)

## PolyBench

PolyBench is a suite of common numerical kernels (linear algebra, stencils, etc.)[sourceforge.net](https://sourceforge.net/p/polybench/wiki/Home/#:~:text=PolyBench%20is%20a%20benchmark%20suite,scheduling%20to%20prevent%20OS%20interference). Included benchmarks are:

- **polybench-2DConvolution**
	 
- **polybench-2mm** (2 matrix multiplications)
	 
- **polybench-3DConvolution**
	 
- **polybench-3mm** (3 matrix multiplications)
	 
- **polybench-atax** (matrix transpose & vector multiply)
	 
- **polybench-bicg** (biCG solver sub-kernel)
	 
- **polybench-correlation**
	 
- **polybench-covariance**
	 
- **polybench-fdtd2d** (2D finite-difference time domain)
	 
- **polybench-gemm** (matrix multiply)
	 
- **polybench-gesummv** (scalar, vector, matrix multiply)
	 
- **polybench-gramschmidt**
	 
- **polybench-mvt** (matrix-vector product & transpose)
	 
- **polybench-syr2k** (symmetric rank-2k)
	 
- **polybench-syrk** (symmetric rank-k)

## CUTLASS Benchmarks (Volta, Ampere, Hopper, etc.)

CUTLASS is NVIDIA’s CUDA template library for GEMM and related operations. The suites include architecture-specific GEMM tests:

- **cutlass_volta**: cutlass_07_volta_tensorop_gemm
	 
- **cutlass_ampere**: cutlass_14_ampere_tf32_tensorop_gemm, cutlass_15_ampere_sparse_tensorop_gemm
	 
- **cutlass_hopper**: cutlass_50_hopper_gemm_with_epilogue_swizzle, cutlass_48_hopper_warp_specialized_gemm, cutlass_54_hopper_fp8_warp_specialized_gemm
	 
- **cutlass (general)**: cutlass_perf_test_k1, cutlass_05_batched_gemm, cutlass_41_fused_multi_head_attention_backward, cutlass_41_fused_multi_head_attention_fixed_seqlen, cutlass_41_fused_multi_head_attention_variable_seqlen (GEMM and attention kernels)

## ISPASS-2009 (GPU-Sim)

The ISVLSI/GPGPU-Sim 2009 benchmarks are a collection of diverse kernels used in the original GPGPU-Sim study[github.com](https://github.com/gpgpu-sim/ispass2009-benchmarks#:~:text=). This suite includes:

- **ispass-2009-AES** (CUDA AES encryption)
	 
- **ispass-2009-BFS** (breadth-first search)
	 
- **ispass-2009-LIB** (LIBOR market model)
	 
- **ispass-2009-LPS** (3D Laplace solver)
	 
- **ispass-2009-MUM** (MUMmerGPU; genome sequence matching)
	 
- **ispass-2009-NN** (neural network for digit recognition)
	 
- **ispass-2009-NQU** (N-Queens solver)
	 
- **ispass-2009-RAY** (ray casting)
	 
- **ispass-2009-STO** (STO solver)
	 
- **ispass-2009-WP** (watershed particle? – as per original suite)

## Dragon (Naive and CDP)

These suites appear to contain graph-processing kernels (likely from a “Dragon” graph library). In _dragon-naive_ we have:

- **testBfs**, **testAmr**, **testSssp**, **testJoin** (graph BFS, adaptive multi-resolution, single-source shortest paths, relational join)  
	 In _dragon-cdp_ (CUDA Dynamic Parallelism) the tests are:
	 
- **testBfs-cdp**, **testSssp-cdp** (BFS and SSSP using CDP)

## Proxy-Apps (DOE)

Proxy-apps from DOE workloads include:

- **cns/cns_all** – (Solving 3D Poisson/Cahn–Hilliard equations)
	 
- **XSBench** – (Monte Carlo neutron cross-section benchmark)
	 
- **lulesh** – (Livermore Unstructured Lagrangian Explicit Shock Hydrodynamics)

## Pannotia (Irregular Graphs)

Pannotia is a suite of irregular graph algorithms (from ISWC 2013)【37†】, including:

- **bc** (betweenness centrality)
	 
- **color_max**, **color_maxmin** (graph coloring variants)
	 
- **fw** (Floyd–Warshall all-pairs shortest paths)
	 
- **fw_block** (blocked Floyd–Warshall)
	 
- **mis** (maximal independent set)
	 
- **pagerank** (PageRank)
	 
- **pagerank_spmv** (PageRank via sparse-matrix–vector)

## LonestarGPU 2.0

Graphs and physics kernels from the LonestarGPU collection (Georgia Tech):

- **lonestar-bfs-atomic**, **lonestar-bfs-wlw**, **lonestar-bfs-wla**, **lonestar-bfs-wlc** (variants of BFS)
	 
- **lonestar-sssp**, **lonestar-sssp-wlc**, **lonestar-sssp-wln** (variants of SSSP)
	 
- **lonestar-bh** (Barnes-Hut N-body)
	 
- **lonestar-dmr** (diffusion), **lonestar-mst** (minimum spanning tree)

## MLPerf Inference (GPU)

MLPerf inference benchmarks measure real-time inference speed. The suites include:

- **inference_mlperf_bert_test.sh** – MLPerf BERT inference test (small)[redhat.com](https://www.redhat.com/en/blog/how-to-accelerate-workloads-with-nvidia-gpus-on-red-hat-device-edge#:~:text=Speech,AMD%20Epyc%20CPU)
	 
- **inference_mlperf_bert**, **inference_mlperf_ssd** – MLPerf BERT and SSD inference (no external datasets)[redhat.com](https://www.redhat.com/en/blog/how-to-accelerate-workloads-with-nvidia-gpus-on-red-hat-device-edge#:~:text=Speech,AMD%20Epyc%20CPU)

## MLPerf Training (GPU)

MLPerf training benchmarks measure training throughput[mlcommons.org](https://mlcommons.org/benchmarks/training/#:~:text=The%20MLPerf%20Training%20benchmark%20suite,through%20the%20results%20dashboard%20below). The listed tests are:

- **training_mlperf_ssd** – SSD object-detection training
	 
- **training_mlperf_rnn** – RNN/LSTM training (speech recognition)

## PyTorch Examples

- **inference_mnist** – (PyTorch example: MNIST inference)

## HuggingFace Example

- **helloworld** – (HuggingFace Transformers demo)

**Sources:** These suites draw on well-known benchmark collections: Rodinia[github.com](https://github.com/yuhc/gpu-rodinia#:~:text=The%20University%20of%20Virginia%20Rodinia,both%20multicore%20CPUs%20and%20GPUs), Parboil[hgpu.org](https://hgpu.org/?p=7496#:~:text=The%20Parboil%20benchmarks%20are%20a,benchmarks%20from%20throughput%20computing%20application), PolyBench[sourceforge.net](https://sourceforge.net/p/polybench/wiki/Home/#:~:text=PolyBench%20is%20a%20benchmark%20suite,scheduling%20to%20prevent%20OS%20interference), CUTLASS/GEMM libraries, DOE proxy and graph benchmarks, and MLPerf training/inference benchmarks[mlcommons.org](https://mlcommons.org/benchmarks/training/#:~:text=The%20MLPerf%20Training%20benchmark%20suite,through%20the%20results%20dashboard%20below)[redhat.com](https://www.redhat.com/en/blog/how-to-accelerate-workloads-with-nvidia-gpus-on-red-hat-device-edge#:~:text=Speech,AMD%20Epyc%20CPU), among others. The Accel-Sim framework integrates these by name in its **define-all-apps** YAML (see question text).
