# Introduction

- GPGPU-Sim was created by <mark style="background: #FFF3A3A6;">Tor Aamodt's</mark> research group at the University of British Columbia.
- GPGPU-Sim models the features of a modern graphics processor that are relevant to non-graphics applications.
- Creating benchmarks for the original GPGPU-Sim simulator was a very time consuming process and the validity of code generation for CPU run on a GPU was questioned by some.
	- These issues motivated the development an interface for directly running CUDA applications to leverage the growing number of applications being developed to use CUDA.
- subsequently added support for OpenCL and removed all SimpleScalar code.
- The interconnection network is simulated using the <mark style="background: #08BFFF99;">booksim</mark> simulator developed by Bill Dally's research group at Stanford.

## GPUWatcch

- GPUWattch (introduced in GPGPU-Sim 3.2.0) was developed by researchers at the <mark style="background: #FFF3A3A6;">University of British Columbia</mark>, the University of Texas at Austin, and the University of Wisconsin-Madison.
- GPUWattch leverages <mark style="background: #08BFFF99;">McPAT</mark>, which was developed by <mark style="background: #FFF3A3A6;">Sheng Li et al.</mark> at the University of Notre Dame, Hewlett-Packard Labs, Seoul National University, and the University of California, San Diego.

# Installing

## Dependencies

GPGPU-Sim was developed on SUSE Linux (this release was tested with SUSE version 11.3) and has been used on several other Linux platforms (both 32-bit and 64-bit systems).

In principle, GPGPU-Sim should work with any linux distribution as long as the following software dependencies are satisfied:
1. Download and install the CUDA Toolkit.
	- Version 4.0 of GPGPU-Sim has been tested with a subset of CUDA version 4.2, 5.0, 5.5, 6.0, 7.5, 8.0, 9.0, 9.1, 10, and 11

	> [!note]  
	> It is possible to have multiple versions of the CUDA toolkit installed on a single system -- just install them in different directories and set your CUDA_INSTALL_PATH environment variable to point to the version you want to use.1. gcc

2. g++ (recommended 4.5.1)
3. make
4. makedepend
5. xutils
6. bison(recommended 2.4.1)
7. flex(recommended 2.5.35)
8. zlib
9. CUDA Toolkit

**AerialVision dependencies:**
1. python-pmw
2. python-ply
3. python-numpy
4. libpng12-dev
5. python-matplotlib

**GPGPU-Sim documentation dependencies:**
1. doxygen
2. graphvi

> [!info] Optional  
> If you want to run OpenCL on the simulator, download and install NVIDIA's OpenCL driver from [http://developer.nvidia.com/opencl](http://developer.nvidia.com/opencl).
