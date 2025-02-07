---
Status: Noting - Max 2
---

# Intorduction

**Steps involved in SGD**:
1. Forward Propagation
2. Backward Propagation
3. Weight Update  

**Inference Procedure Requirements**:
- Forward Propagation  

**Training Procedure Requirements**:
1. Loss measurement
2. Backward Propagation
3. Deviation Measurement
4. Weight Update  

**Algorithms that alter SGD**:
- momentum
- batch normalization
- Optimizers
	- Adaptive Gradient

## Differences in Architectural Requirements

| Architectural Aspects | Inference                                                       | Training                                                                                                                                             |
| --------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Parallelization       | 1. Each inference is independent.<br>2. So easily scalable.<br> | 1. Coordinates across parallel resources. Waiting for the mini batch to be processed.<br>2. Time depends on no. of cordinates processed in Parallel. |
| Computation           | Only Forward Propagation.                                       | Back-propagation requires derivatives from all computations, such as activation functions and multiplication by transposed weight matrices.          |
| Memory                | Small Activation working set in enough.                         | Needs to store intermediate values from forward and backward propagation.                                                                            |
| Programmability       | More or the less remains same.                                  | Algorithms and models are continually changing.                                                                                                      |
| Data                  | 8-bit integer can work.                                         | Requires 32-bit FP arithmetic to account for small weight updates in SGD.                                                                            |

# Domain-Specific Supercomputer

**Motivation**:  
Before 2014 training was done on clusters of CPUs. Whereas, the DNN training computation appetite appeared unlimited.

## Result

**DSA supercomputer in 2014**.
- Instead of clustering CPU hosts with DSA chips, which could have hampered scalability.

## Procedure

### **TPU Designing**

- One of the <mark style="background: #08BFFF99;">Critical Architecture</mark> feature is _chip communication_.  
  Features:
	1. Speed of the link
	2. Topology of interconnect
	3. switch type : centralized / distributed.

### **TPUv2 Traffic**  

Most traffic is [[All-reduce over weight updates]].

### **TPUv2 Interconnect**

For <mark style="background: #08BFFF99;">deadlock-free routing</mark> and enabling virtual-circuit, they preferred Distributed switch.
- Allows <mark style="background: #08BFFF99;">all-reduction to be in-built in</mark> a dimension-balanced, band-with optimal way in a [[2D-torus]] topology.
- Simplifies rack-level deployment.

> [!warning]  
> But in a multi-rack sy stem the racks must be adjacent.

### **2D-torus Topoly in TPUv2**

<mark style="background: #FFF3A3A6;">_ICI_ (Inter-Core Interconnect) per chip</mark> : 4  
<mark style="background: #FFF3A3A6;">Interconnect Speed</mark> : 496Gbits/s  
<mark style="background: #FFF3A3A6;">Bisection Bandwith</mark> : 15.9Terabits/s

> [!note] Bisection Bandwith  
> The bandwith between two halves of a networkof the worst-case split.
>
> Here :
>
> > Torus size = 16\*16 2D  
> > =>32 links  
> > . ' . 32 links \* 496Gbits/s = 15.9Terabits/s

```ad-abstract
title: Comparison
A separate Infiniband switch(used in CPU clusters) connecting 64 hosts(each with 4 DSA chips) has 64 ports and using 100Gbit/s links has only 6.4Terabits/s.

>  TPUv2 is 2.5x times faster than `fas:SortUp`

```

> [!success] ByProduct  
> Higher Bisection Bandwidth allowed Synchronous training. Which in turn eliminated the parameter servers required for Asynchronous training.
>
> Synchronous Training has two phases:
> 1. compute phase
> 2. communication phase  
> 	- The latter is the bottleneck.
>
> Allowed TPU to achive 96-99% of perfect linear scaleup.

![[Pasted image 20250118183302.png#center|FIgure 1: 2D-Torus Topology | 750]]

### Designing Supercomputer Node

Following the main ideas of TPUv1, TPUv2 also has:
- A MXU (Matrix Multiply Unit) using a **[[systolic array]]** to reduce area and energy.
- **Software controlled on-chip memories**.

  > [!question] What is Software-Controlled on-chip memories?

<mark style="background: #FFF3A3A6;">Shallue et al.</mark> (arXiv:1811.03600) **examined the effect of increasing batch sizes.** Results,  
He found three regions:
1. Perfect Scaling Region :- 2x batch size => 1/2 no. of training steps
2. Diminishing Returns :- + batch size => slower reduction in training steps
3. Maximum Data Parallelism: + batch size => no benefits in parallelism.

Batch size of <mark style="background: #ABF7F7A6;">256 - 8,192</mark> scale perfectly in TPUs.

**TPUv2 uses two cores/chip.**
- Because Global wires can't be scaled down with the other transistors and local wires.
- Thus it prevents latencies generated by a large single core.
- _Why 2 not more?_ Writing programs for 2 large cores is easier than writing for many small cores.

## Floor Plan of TPUv2

![[Pasted image 20250120202022.png#center|Picture 2 : Floor Plan of TPUv2]]

**Major blocks of a Tensor Core**:
1. ICI (Inter-Core Interconnect)
2. [[High Bandwith Memory]]
	- 20x bandwith of TPUv1.
	- using <mark style="background: #08BFFF99;">interposer substrate</mark> which is 32 128-bit buses.
	- ? **Interposer substrate** are high-speed interconnects
	- 4 short stacks of DRAM chips.
	- ~ i.e. 8 128-bit buses per chip.

3. The Core Sequencer
	- fetches <mark style="background: #FFF3A3A6;">VLIW</mark> instructions
	- soft-ware-managed Instruction memory (Imem)
		- Scheduled by <mark style="background: #08BFFF99;">XLA compiler</mark>.
	- executes scalar operations using
		- 4k 32-bit scalar data memory (Smem)
		- 32 32-bit scalar registers (Sregs)
	- forwards vector instructions to VPU
4. VPU (Vector Processing Unit)
	- performs vector operations
	- 32K 128x32-bit elements(16MiB) of on-chip vector memory (Vmem)
	- 2D 128x8 32-bit elements(4KiB) of Vregs.
	- access MXU using <mark style="background: #08BFFF99;">decoupling FI-FO</mark> .
5. Transcendental unit
	- Performs the inverse square root operation.
6. MXU
	- Output : 32-bit FP
	- Input : 16-bit FP
	- Size : 128x128
	- ~ Because 4 128x128 MXUs perform 1.6x of a single 256x256, yet take same die area.
	- ~ Any smaller size results in more die area, coz of control wires.
7. Transpose Reduction Permute Unit
	- Function:  
	  128x128 matrix transposes  
	  permutation of VPU lanes

> [!abstract] VLIW  
> Size : 322-bit  
> Operations:
> 1. Two Scalar
> 2. Two vector ALU
> 3. Vector load and store
> 4. 2 slots to queue data to and from MXU
>
> In total 8-operations.

> [!question] What is Decoupling FI-FO?  
> A **decoupling FIFO** is a type of FIFO buffer used to separate (decouple) the operations of two processing units, such as the VPU and MXU.
>
> Its purpose is to allow the two units to operate independently, even if their speeds or workloads differ.
>
> **Flow Control:**  
> The FIFO manages flow control by signaling to the VPU when it is full (to stop producing) or to the MXU when it is empty (to pause consumption).

## TPUv3

**Changes** (compared to TPUv2) :
1. <mark style="background: #08BFFF99;">1.35x</mark>
	- Clock rate , 940MHz vs 700.
	- ICI band-width
	- memory bandwidth , 900GB/s/Chip vs 700.
2. <mark style="background: #08BFFF99;">2x MXUs</mark> , i.e. 4 MXUs
3. => <mark style="background: #08BFFF99;">2.7x performance</mark> , 123 TeraFlops/Chip vs 46.
4. Liquid cooling, allows for <mark style="background: #08BFFF99;">1.6x more power</mark>, i.e. 450 watts/chip vs 280.
5. made for <mark style="background: #08BFFF99;">1024 chips</mark> supercomputer.
6. 6% larger die with the same technology, i.e. <mark style="background: #08BFFF99;">>12nm</mark> .
	- ~ Idea about major challenges faced during TPUv2 helped in better designing.

### Designing DSA supercomputer arithmetic

As peak <mark style="background: #08BFFF99;">performance is >8x</mark> when using 16-bit vs 32-bit.

**Issues with IEEE fp16** :
- Conversion from 8-bit exponent to 5-bit before feeding to MXU lead to <mark style="background: #FFF3A3A6;">underflow of small update values</mark>.

So, a novel <mark style="background: #FFB8EBA6;">Brain floating format (bf16)</mark> with 8-bit expo. was introduced.

```ad-tip
title: Energy Saver

As size of an FP multipier scales with the sqaure of the mantissa width.
The bf16 multiplier is half the size and energy of a fp16 multiplier.

As: 8<sup>2</sup> / 11<sup>2</sup> = 0.5.
```

# Designing a DSA Supercomputer Compiler

- TPUv2/v3s also use TF, with the new system XLA (for accelerated liner algebra).  
- XLA handles the TPU-dependent mapping.
- TF programs priotize multi-dimensional arrays as they :
	1. Operate on multi-dimensional arrays explicitly.
		- Not loop based (implicit).
	2. have known memory aliasing behavior.
	3. Use Explicit, analyzable and bounded data access patterns.
- ? The above three features allow XLA to correctly and safely transforms codes for TPUs.
- XLA manages all memory transfers, including code overlays and [[Direct Memory Access]] pushes.
- XLA also uses standard VLIW compilation techniques to keep compute units busy. i.e.,
	- loop unrolling
	- instruction scheduling
	- software pipelining
- XLA uses [[operation fusion]] to reduce memory traffic, achieving speedup improvements from 1.8 to 6.3 over different models/workloads.
- XLA used only 96 ops. Where these ops are generally fusioned.
	- Ex: Like 63 ops were compiled into one for CNN1,
		- ~ a internal image recognition model similar to resnet-101.

# GPU vs TPU Architecure

| TPU                                                             | GPU                                                                                                                                                            | Subject                    |
| --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- |
| Built-in through ICI and supported by all-reduce operations.    | NVLink inside a chassis and host-controlled InfiniBand networks connect multiple chassis together.                                                             | Multi-Chip Parallelization |
| Utilizes bf16 FP arithmetic that halves the die are and energy. | Utilizes IEEEfp16 FP mulipliers.<br>Use of reduced-precision systolic arrays, may require software for loss scaling plus it adds to extra die area and energy. | Arithmetic                 |
| Dual-core, in-order and software managed memory.                | Latency-tolerant many core machines, with very large register files (20MiB).                                                                                   | Architecture               |
| 32MiB scratchpad memories, software controlled.                 | 6 MiB cache and 7.5 MiB scratchpad memory controlled by software.                                                                                              | Storage                    |
| Use DMA controllers for DRAM accesses.                          | Uses coalescing hardware.                                                                                                                                      | Memory Access              |

![[Pasted image 20250121162527.png#center |Figure 3 : Adjusted Comparison of GPU and TPU]]  
**From the figure** :
- GPUs have 2x the die size.
- 1.3x-1.6x power consumption.
- 1.6x-2.9x cloud price.

```ad-abstract
title: Thottethodi and Vijaykumar35 concluded that when compared to TPUs:
“[GPUs] incur high overhead in performance, area, and energy due to heavy multithreading which is unnecessary for DNNs which have prefetchable, sequential memory accesses. The systolic organization [of TPUs] … capture[s] DNNs’ data reuse while being simple by avoiding multithreading.”

```

> [!question] Some unknowns!  
> How reduced-precision systollic arrays help?  
> What are NVLink and InfiniBand?  
> What is coalescing hardware?

# Performance Evaluation

- 96-99% of <mark style="background: #08BFFF99;">perfect linear speedup</mark> upto 1024 chips.  
![[Pasted image 20250121185022.png#center|Figure 4 : TPUv3 vs Volta]]  
**Google's workloads :**
1. MLP0 : Unpublished
2. MLP1 : RankBrain <mark style="background: #CACFD9A6;">(Only Articles available, yet to go through!)</mark>
3. CNN0 : AlphaZero
4. CNN1 : Internal
5. RNN0 : RNMT+
6. RNN! : Improved LAS

- Geometric mean speedup of the six production applications was 1.8 for TPUv3 and 0.4 for Volta.
	- Primarily coz of diff. in fp32 and bf16.

## Bullets

1. **Improv. of v3 over v2 :**
- 2.7x peak compute
- Only 1.35x memory bandwith, ICI bandwith and clock rate.
- Surprisingly it din't result in any bottleneck, as workload improv. were 1.6x to 2.3x.
1. One upside of using the same arithmetic for training and inference is that ML experts don’t need to do extra workcalled quantization—to ensure the same accuracy of the DNN model.
2. One upside of using the same arithmetic for training and inference is that ML experts don’t need to do extra workcalled quantization—to ensure the same accuracy of the DNN model.
3. Only ResNet-50 from MLPerf 0.6 can scale beyond 1,000 TPUs and GPUs.
4. Ying et al. published a ResNet-50 results on TPUv3 that delivered 77% of perfect linear scaleup at 1,024 chips but the TPUv3 version for MLPerf 0.6 only runs at 52%. The difference is in MLPerf’s ground rules.
	- For MLPerf 0.6, NVIDIA ran ResNet-50 on a cluster of 96 DGX-2H each with 16 Voltas connected via Infiniband switches at 41% of linear scaleup for 1,536 chips.
5. CNN1 is an image recognition DNN much like ResNet101. It scales much better on TPUs because Google’s internal image datasets are much larger than what ResNet50 uses (Imagenet).
6. Traditional supercomputers measure performance using the high-performance computing (HPC) benchmark <mark style="background: #08BFFF99;">Linpack</mark>.
	- @ Look into Linpack.
7. Recent DNN successes led to an explosion in their development. Most designs focus on inference; far fewer, including the TPUv2/v3, target training.
8. Cerebras uses a whole silicon wafer to build their system, essentially treating 84 large “dies” as a single unit Each “die” has 220MB of SRAM along with about 5k cores, yielding a total of 18GB of on-chip memory and 400k cores that collectively use 15 kilowatts.
9. TPU supercomputers with 256–1,024 chips running a production application have 5x–10x performance/ Watt of the #1 traditional supercomputer on the Green500 list running Linpack and 24x–44x of the #4 supercomputer on the Top500 list.

# Other Neural Networks focusing on training

1. Cerebras uses a whole silicon wafer to build their system, essentially treating 84 large “dies” as a single unit Each “die” has 220MB of SRAM along with about 5k cores, yielding a total of 18GB of on-chip memory and 400k cores that collectively use 15 kilowatts.
2. GraphCore, there is no DRAM in the system, so they target small batch sizes to reduce memory needs. The GraphCore15 GC2 chip holds 1,216 Intelligence Processing Units that support seven threads, each of which has a peak performance of 100GFLOPS/s or 122TFLOPS/s per chip, almost identical to the peak performance of TPUv3 and Volta. It relies on the 300MB onchip SRAM for memory, with two GC2 chips per PCIe board.
3. The Habana Gaudi38 has eight VLIW SIMD cores, four stacks of HBM2 memory, bf16 arithmetic, and eight 100Gbit/sec Ethernet links to connect many chips together to form larger systems.
4. Wave Computing’s28 Dataflow Processing Unit chip has 16k processors, 8k arithmetic units, 16MB of on-chip memory, and novelty relies on asynchronous logic instead of a clock. It has external DRAM, offering both Hybrid Memory Cube and DDR4 ports.
	- As of February 2020, none of the five training startups has reported training accuracy or timeto-solution.
5. DianNao family of architectures (one of which trains)7 and ScaleDeep;37 to our knowledge, neither has been fabricated.
6. Anton systems33 showed two order-of-magnitude speedups over traditional supercomputers on molecular dynamics workloads.
