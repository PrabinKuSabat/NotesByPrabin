# Introduction

The current gpgpu version is not installable directly from the Git. As, the `MakeFile` throws up the following error :

```
home/prabin-msc/gpgpu-sim_distribution/build/gcc-/cuda-11060/release/cuda-sim/ptx_parser_decode.def: In member function ‘symbol_table* gpgpu_context::init_parser(const char*)’:
/home/prabin-msc/gpgpu-sim_distribution/build/gcc-/cuda-11060/release/cuda-sim/ptx_parser_decode.def:2:11: error: unable to find string literal operator ‘operator""end’ with ‘const char [30]’, ‘long unsigned int’ arguments
    2 | DEF(YYEOF,"YYEOF                     /* "end of file"  */")
      |           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ptx_parser.cc:115:43: note: in definition of macro ‘DEF’
  115 | #define DEF(X, Y) g_ptx_token_decode[X] = Y;
      |                                           ^
/home/prabin-msc/gpgpu-sim_distribution/build/gcc-/cuda-11060/release/cuda-sim/ptx_parser_decode.def:4:13: error: unable to find string literal operator ‘operator""invalid’ with ‘const char [28]’, ‘long unsigned int’ arguments
    4 | DEF(YYUNDEF,"YYUNDEF                 /* "invalid token"  */")
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ptx_parser.cc:115:43: note: in definition of macro ‘DEF’
  115 | #define DEF(X, Y) g_ptx_token_decode[X] = Y;
      |                                           ^
make[1]: *** [Makefile:116: /home/prabin-msc/gpgpu-sim_distribution/build/gcc-/cuda-11060/release/cuda-sim/ptx_parser.o] Error 1
make[1]: Leaving directory '/home/prabin-msc/gpgpu-sim_distribution/src/cuda-sim'
make: *** [Makefile:208: cuda-sim] Error 2
```

The following steps will help you to make properly.

# Steps

1. Open `build/gcc-/cuda-11060/release/cuda-sim/ptx_parser_decode.def`.
	- Address of same is available in the error message.
2. Remove the `"` symbols from ` /* "end of file" */ ` and ` /* "invlalid token" */ `, line number 2 and 4 respectively.
3. Now the next error message should look something like this:

	```
   gcc -DNDEBUG -D_FORTIFY_SOURCE=2 -O2 -isystem /home/prabin-msc/.conda/envs/gpgpu/include -Wall -I. -Iarbiters -Iallocators -Irouters -Inetworks -Ipower -I/home/prabin-msc/gpgpu-sim_distribution/src -I/home/prabin-msc/gpgpu-sim_distribution/src/gpgpu-sim/  -DCREATE_LIBRARY -O3 -g -fPIC -I/home/prabin-msc/.conda/envs/gpgpu/include -c /home/prabin-msc/gpgpu-sim_distribution/build/gcc-/cuda-11060/release/intersim2/lex.yy.c -o /home/prabin-msc/gpgpu-sim_distribution/build/gcc-/cuda-11060/release/intersim2/lex.yy.o
   config.l:8:6: error: conflicting types for 'yyerror'
       8 | void yyerror(char * msg);
         |      ^~~~~~~
   In file included from config.l:3:
   /home/prabin-msc/gpgpu-sim_distribution/build/gcc-/cuda-11060/release/intersim2/y.tab.h:94:6: note: previous declaration of 'yyerror' was here
      94 | void yyerror (const char *msg);
         |      ^~~~~~~
   
   ```

	Which is basically a error in the code. Just a <mark style="background: #08BFFF99;">type conflict</mark>.
4. To solve this open file `src/intersim2/config.l`.
	- Address of the same will be provided in the error message.
5. Now add `const` to the line no. 8, 46.
	- Make it `void yyerror(const char * msg);`
6. Next, open `src/intersim2/config.y
7. Add `const` to line no. 4 in the above manner.

This should allow you to _make_ the sim.

# Running the sim

1. Run `source setup_environment release`.
	- If u have used `debug` during the initial setup use `debug` instead of `release`.
2. Go to the `configs/` directory and choose one of the models.
3. Either copy your executable file to the above directory or the `.config` and `.xml` file into the same directory as the executable file.
4. Execute as normal execution.
	- Using ` $ ./executable `.

Look [`far:PaperPlane`](https://github.com/gpgpu-sim/gpgpu-sim_distribution#step-3-run) for more.

# Errors

Due to some reason `Segmentation fault (core dumped)` happens during execution.  
<mark style="background: #FFB8EBA6;"># This has to be resolved.</mark>

---

# Extra

U can use the following `.yml` code to build a conda environment, which satisfies the GPGU dependencies.

```ad-warning
title: Dependencies for AerialVision are not satisfied. 
\# It's not required to simulate.

```

```
name: gpgpu
channels:
  - nvidia/label/cuda-11.6.0
  - conda-forge
  - defaults
dependencies:
  - _libgcc_mutex=0.1=conda_forge
  - _openmp_mutex=4.5=2_gnu
  - binutils_impl_linux-64=2.39=h6ceecb4_0
  - binutils_linux-64=2.39=h5fc0e48_11
  - bison=3.8=h9c3ff4c_0
  - bzip2=1.0.8=h7f98852_4
  - ca-certificates=2022.9.24=ha878542_0
  - cudatoolkit=10.2.89=h713d32c_10
  - flex=2.6.4=h58526e2_1004
  - freeglut=3.2.2=h9c3ff4c_1
  - gcc=10.4.0=hb92f740_11
  - gcc_impl_linux-64=10.4.0=h5231bdf_19
  - gcc_linux-64=10.4.0=h9215b83_11
  - gxx_impl_linux-64=10.4.0=h5231bdf_19
  - gxx_linux-64=10.4.0=h6e491c6_11
  - kernel-headers_linux-64=2.6.32=he073ed8_15
  - ld_impl_linux-64=2.39=hc81fddc_0
  - libffi=3.4.2=h7f98852_5
  - libgcc-devel_linux-64=10.4.0=hd38fd1e_19
  - libgcc-ng=12.2.0=h65d4601_19
  - libgomp=12.2.0=h65d4601_19
  - libnsl=2.0.0=h7f98852_0
  - libsanitizer=10.4.0=h5246dfb_19
  - libsqlite=3.40.0=h753d276_0
  - libstdcxx-devel_linux-64=10.4.0=hd38fd1e_19
  - libstdcxx-ng=12.2.0=h46fd767_19
  - libuuid=2.32.1=h7f98852_1000
  - libxcb=1.13=h7f98852_1004
  - libzlib=1.2.13=h166bdaf_4
  - m4=1.4.18=h516909a_1001
  - ncurses=6.3=h27087fc_1
  - openssl=3.0.7=h166bdaf_0
  - pip=22.3.1=pyhd8ed1ab_0
  - pmw=2.0.1=py311h38be061_1007
  - pthread-stubs=0.4=h36c2ea0_1001
  - python=3.11.0=ha86cf86_0_cpython
  - python_abi=3.11=2_cp311
  - readline=8.1.2=h0f457ee_0
  - setuptools=65.5.1=pyhd8ed1ab_0
  - sysroot_linux-64=2.12=he073ed8_15
  - tk=8.6.12=h27826a3_0
  - tzdata=2022f=h191b570_0
  - wheel=0.38.4=pyhd8ed1ab_0
  - xorg-fixesproto=5.0=h7f98852_1002
  - xorg-inputproto=2.3.2=h7f98852_1002
  - xorg-kbproto=1.0.7=h7f98852_1002
  - xorg-libx11=1.7.2=h7f98852_0
  - xorg-libxau=1.0.9=h7f98852_0
  - xorg-libxdmcp=1.1.3=h7f98852_0
  - xorg-libxext=1.3.4=h7f98852_1
  - xorg-libxfixes=5.0.3=h7f98852_1004
  - xorg-libxi=1.7.10=h7f98852_0
  - xorg-makedepend=1.0.6=h27087fc_2
  - xorg-xextproto=7.3.0=h7f98852_1002
  - xorg-xproto=7.0.31=h7f98852_1007
  - xz=5.2.6=h166bdaf_0
  - zlib=1.2.13=h166bdaf_4
  - cuda-cccl=11.6.55=hf6102b2_0
  - cuda-command-line-tools=11.6.0=0
  - cuda-compiler=11.6.0=0
  - cuda-cudart=11.6.55=he381448_0
  - cuda-cudart-dev=11.6.55=h42ad0f4_0
  - cuda-cuobjdump=11.6.55=h9dd2d0c_0
  - cuda-cupti=11.6.55=h43c5c43_0
  - cuda-cuxxfilt=11.6.55=h69de05d_0
  - cuda-driver-dev=11.6.55=0
  - cuda-gdb=11.6.55=hff0b7d4_0
  - cuda-libraries=11.6.0=0
  - cuda-libraries-dev=11.6.0=0
  - cuda-memcheck=11.6.55=h0288dce_0
  - cuda-nsight=11.6.55=0
  - cuda-nsight-compute=11.6.0=0
  - cuda-nvcc=11.6.55=h5758ece_0
  - cuda-nvdisasm=11.6.55=h5556c0d_0
  - cuda-nvml-dev=11.6.55=haa9ef22_0
  - cuda-nvprof=11.6.55=h30b2dac_0
  - cuda-nvprune=11.6.55=h3791f62_0
  - cuda-nvrtc=11.6.55=hc54fff9_0
  - cuda-nvrtc-dev=11.6.55=h42ad0f4_0
  - cuda-nvtx=11.6.55=h99d0529_0
  - cuda-nvvp=11.6.58=h67ee751_0
  - cuda-samples=11.6.101=h8efea70_0
  - cuda-sanitizer-api=11.6.55=h4716e2e_0
  - cuda-toolkit=11.6.0=0
  - cuda-tools=11.6.0=0
  - cuda-visual-tools=11.6.0=0
  - gds-tools=1.2.0.100=0
  - libcublas=11.8.1.74=h1e58c10_0
  - libcublas-dev=11.8.1.74=h7a51e1f_0
  - libcufft=10.7.0.55=h563f203_0
  - libcufft-dev=10.7.0.55=h05eb8d0_0
  - libcufile=1.2.0.100=0
  - libcufile-dev=1.2.0.100=0
  - libcurand=10.2.9.55=h7c349da_0
  - libcurand-dev=10.2.9.55=hd2e71f0_0
  - libcusolver=11.3.2.55=hebb49eb_0
  - libcusparse=11.7.1.55=h9a152cf_0
  - libnpp=11.6.0.55=hdb0c674_0
  - libnpp-dev=11.6.0.55=h0163868_0
  - libnvjpeg=11.6.0.55=h6f17e28_0
  - libnvjpeg-dev=11.6.0.55=h0163868_0
  - nsight-compute=2022.1.0.12=0
prefix: /home/prabin-msc/.conda/envs/gpgpu
```

> [!note]  
> Change the `prefix` above to your environment location.

---

**For Miscellaneous refer to :** [`ris:ExternalLink`](https://github.com/gpgpu-sim/gpgpu-sim_distribution?tab=readme-ov-file#miscellaneous)
