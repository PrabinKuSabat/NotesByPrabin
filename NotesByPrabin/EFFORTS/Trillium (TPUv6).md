 The TPUs are also a way to drive fundamental research in mixed precision, matrix and serial processing design, memory subsystems, and interconnects for AI training and inference systems.  
	 1. Mixed Precision: TPUs support the use of different numerical data types (e.g., 16-bit, 32-bit) within the same computation, allowing for optimized performance and efficiency for certain AI workloads.  
	 2. Matrix and Serial Processing Design: TPUs are designed with specialized hardware architectures that can efficiently perform the matrix-based computations that are central to many AI algorithms, as well as support serial processing of data streams.  
	 3. Memory Subsystems: The memory systems within TPUs, including the on-chip memory and interfaces to external memory, are optimized to provide high bandwidth and low latency access to data required for AI training and inference.  
	 4. Interconnects: TPUs are designed with high-speed interconnects, both within the chip and between multiple TPU chips, to enable the efficient flow of data and computations required for large-scale AI models and systems.  

# v6 VS v5

> [!important]  
> Google does not supply feeds and speeds for all TPUs uniformly, and it is all the more curious as to why. The scale of the TPU v4i pods was never disclosed and neither was its pricing. Chip sizes and process nodes are missing on the more recent TPUs and so are some basic thermals such as maximum and idle wattages.

1. TPU v6 had 4.7X higher peak performance per chip compared to the TPU v5e.
2. Twice the HBM memory capacity and twice the HBM memory bandwidth.
3. Twice the Interchip interconnect (ICI) bandwidth doubled.
4. Third generation of SparseCore, which accelerates handling the embeddings that are commonly used in ranking and recommendation algorithms.
5. Larger MXU (larger than 128x128). The TPU v1 chips used a 256×256 matrix in its cores, and we think Google may have reverted to this format and figured out a way to <mark style="background: #08BFFF99;">double pump</mark> it efficiently.  
6. Trillium devices have 67 percent more energy efficiency than the TPU v5e chips, and can be podded up into blocks of 256 devices in a loosely coupled system for running AI workloads.
7. The TPU v6 devices do not support 64-bit or 32-bit floating point math. We are certain that the TPU v6 devices will support INT8 and BF16.
	 - But there is a chance that lower eight-bit and four-bit floating point formats will also be supported with Trillium.
8. Guess is that with the TPU v6, Google is going to actually raise the price of renting one of these devices by a little more than a factor of 2X and that the resulting Trillium instances will still offer more than 2.3X better bang for the buck than the TPU v5e instances on Google cloud and 3.4X better price/performance than the TPU v5p. (By [The next Platform](https://www.nextplatform.com/2024/06/10/lots-of-questions-on-googles-trillium-tpu-v6-a-few-answers/))
	- With TPU v6e, if it exists, the inference cost could be even lower and if there is a TPU v6p the training cost could come down further.

> [!note] Double Pumping  
> **"Double Pumping"** technique suggests an innovative approach to enhance throughput without significantly altering the core architecture. Double pumping typically refers to the ability to process two data inputs in a single clock cycle, effectively doubling the output of the MMU.

# Versions of TPUs

![[Pasted image 20241209203923.png]]
