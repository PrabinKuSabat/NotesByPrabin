---
dg-publish: true
---

# Original Version

> [!info] Note  
> If this doesn't work. Implement the latest version of the cuda environment.

> [!warning] It's necessary  
> It's necessary to satisfy the following dependencies:  
> `sudo apt-get install -y bison flex build-essential xutils-dev`

name: accelRelease  
channels:
  - conda-forge
  - defaults  
dependencies:
  - _libgcc_mutex=0.1=conda_forge
  - _openmp_mutex=4.5=2_gnu
  - _sysroot_linux-64_curr_repodata_hack=3=h5bd9786_13
  - binutils_impl_linux-64=2.36.1=h193b22a_2
  - binutils_linux-64=2.36=hf3e587d_33
  - bison=3.8=h9c3ff4c_0
  - bzip2=1.0.8=h7f98852_4
  - ca-certificates=2022.9.24=ha878542_0
  - flex=2.6.4=h58526e2_1004
  - gcc_impl_linux-64=7.5.0=hda68d29_13
  - gcc_linux-64=7.5.0=h47867f9_33
  - gettext=0.21.1=h27087fc_0
  - gxx_impl_linux-64=7.5.0=h64c220c_13
  - gxx_linux-64=7.5.0=h555fc39_33
  - kernel-headers_linux-64=3.10.0=h4a8ded7_13
  - ld_impl_linux-64=2.36.1=hea4e1c9_2
  - libdrm-conda-x86_64=2.4.97=ha78e32a_1107
  - libffi=3.4.2=h7f98852_5
  - libgcc-ng=12.2.0=h65d4601_19
  - libglu=9.0.0=he1b5a44_1001
  - libglvnd-conda-x86_64=1.0.1=ha78e32a_1107
  - libglvnd-glx-conda-x86_64=1.0.1=ha78e32a_1107
  - libgomp=12.2.0=h65d4601_19
  - libiconv=1.17=h166bdaf_0
  - libidn2=2.3.4=h166bdaf_0
  - libnsl=2.0.0=h7f98852_0
  - libsqlite=3.40.0=h753d276_0
  - libstdcxx-ng=12.2.0=h46fd767_19
  - libunistring=0.9.10=h7f98852_0
  - libuuid=2.32.1=h7f98852_1000
  - libzlib=1.2.13=h166bdaf_4
  - m4=1.4.18=h516909a_1001
  - make=4.3=hd18ef5c_1
  - mesa-khr-devel-conda-x86_64=18.3.4=ha78e32a_1107
  - mesa-libglapi-conda-x86_64=18.3.4=ha78e32a_1107
  - mesa-libglu-conda-x86_64=9.0.0=hc958a99_1107
  - mesa-libglu-devel-conda-x86_64=9.0.0=hc958a99_1107
  - ncurses=6.3=h27087fc_1
  - openssl=3.0.7=h166bdaf_0
  - pip=22.3.1=pyhd8ed1ab_0
  - python=3.8.13=ha86cf86_0_cpython
  - readline=8.1.2=h0f457ee_0
  - setuptools=65.5.1=pyhd8ed1ab_0
  - sqlite=3.40.0=h4ff8645_0
  - sysroot_linux-64=2.17=h4a8ded7_13
  - tk=8.6.12=h27826a3_0
  - wget=1.20.3=ha35d2d1_1
  - wheel=0.38.4=pyhd8ed1ab_0
  - zlib=1.2.13=h166bdaf_4
  - zstd=1.5.2=h6239696_4
  - icu=73.1=h6a678d5_0
  - libboost=1.82.0=h109eef0_2
  - libxml2=2.13.8=hfdd30dd_0
  - xz=5.6.4=h5eee18b_1  
prefix: /home/cab-prj/.conda/envs/accelRelease

---

# Latest Version

name: accelsim  
channels:
  - conda-forge
  - defaults  
dependencies:
  - _libgcc_mutex=0.1=conda_forge
  - _openmp_mutex=4.5=2_gnu
  - binutils_impl_linux-64=2.39=h6ceecb4_0
  - binutils_linux-64=2.39=h5fc0e48_11
  - bison=3.8=h9c3ff4c_0
  - bzip2=1.0.8=h7f98852_4
  - c-ares=1.18.1=h7f98852_0
  - ca-certificates=2022.9.24=ha878542_0
  - cmake=3.25.0=h816a3e0_0
  - expat=2.5.0=h27087fc_0
  - flex=2.6.4=h58526e2_1004
  - gcc_impl_linux-64=12.2.0=hcc96c02_19
  - gcc_linux-64=12.2.0=h4798a0e_11
  - gettext=0.21.1=h27087fc_0
  - gxx_impl_linux-64=12.2.0=hcc96c02_19
  - gxx_linux-64=12.2.0=hb41e900_11
  - icu=70.1=h27087fc_0
  - kernel-headers_linux-64=2.6.32=he073ed8_15
  - keyutils=1.6.1=h166bdaf_0
  - krb5=1.19.3=h08a2579_0
  - ld_impl_linux-64=2.39=hc81fddc_0
  - libcurl=7.86.0=h2283fc2_1
  - libdrm-cos6-x86_64=2.4.65=h9b0a68f_1105
  - libedit=3.1.20191231=he28a2e2_2
  - libev=4.33=h516909a_1
  - libffi=3.4.2=h7f98852_5
  - libgcc-devel_linux-64=12.2.0=h3b97bd3_19
  - libgcc-ng=12.2.0=h65d4601_19
  - libglu=9.0.0=he1b5a44_1001
  - libgomp=12.2.0=h65d4601_19
  - libiconv=1.17=h166bdaf_0
  - libidn2=2.3.4=h166bdaf_0
  - libnghttp2=1.47.0=hff17c54_1
  - libnsl=2.0.0=h7f98852_0
  - libsanitizer=12.2.0=h46fd767_19
  - libsqlite=3.40.0=h753d276_0
  - libssh2=1.10.0=hf14f497_3
  - libstdcxx-devel_linux-64=12.2.0=h3b97bd3_19
  - libstdcxx-ng=12.2.0=h46fd767_19
  - libunistring=0.9.10=h7f98852_0
  - libuuid=2.32.1=h7f98852_1000
  - libuv=1.44.2=h166bdaf_0
  - libx11-common-cos6-x86_64=1.6.4=h9b0a68f_1105
  - libx11-cos6-x86_64=1.6.4=h9b0a68f_1105
  - libxml2=2.10.3=h7463322_0
  - libzlib=1.2.13=h166bdaf_4
  - m4=1.4.18=h516909a_1001
  - mesa-dri-drivers-cos6-x86_64=11.0.7=h9b0a68f_1105
  - mesa-dri1-drivers-cos6-x86_64=7.11=h9b0a68f_1105
  - mesa-libgl-cos6-x86_64=11.0.7=h9b0a68f_1105
  - mesa-libgl-devel-cos6-x86_64=11.0.7=h9b0a68f_1105
  - ncurses=6.3=h27087fc_1
  - openssl=3.0.7=h166bdaf_0
  - pip=22.3.1=pyhd8ed1ab_0
  - psutil=5.9.4=py311hd4cff14_0
  - python=3.11.0=ha86cf86_0_cpython
  - python_abi=3.11=2_cp311
  - readline=8.1.2=h0f457ee_0
  - rhash=1.4.3=h166bdaf_0
  - setuptools=65.5.1=pyhd8ed1ab_0
  - sysroot_linux-64=2.12=he073ed8_15
  - tk=8.6.12=h27826a3_0
  - tzdata=2022f=h191b570_0
  - wget=1.20.3=ha35d2d1_1
  - wheel=0.38.4=pyhd8ed1ab_0
  - xorg-makedepend=1.0.6=h27087fc_2
  - xz=5.2.6=h166bdaf_0
  - zlib=1.2.13=h166bdaf_4
  - zstd=1.5.2=h6239696_4
  - cudatoolkit=11.0.221=h6bb024c_0  
prefix: /home/cab-prj/.conda/envs/accelsim
