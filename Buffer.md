Just an update. I could do perform the tuning part for the default RTX4090-SASS config  
` ../job_launching/run_simulations.py  
-T ../.././hw_run/traces/device-0/12.4/  
-C RTX4090-SASS  
-N tuning3 -B GPU_Microbenchmark`
 
The workaround is after you generate the config for RTX4090 using  
[`# run the tuner with the stats.txt from the previous step  
./tuner.py -s stats.txt`](https://github.com/accel-sim/accel-sim-framework/tree/dev/util/tuner#readme:~:text=%23%20run%20the%20tuner%20with%20the%20stats.txt%20from%20the%20previous%20step%0A./tuner.py%20%2Ds%20stats.txt)

Take the config of RTX3070 copy it to the RTX4090 folder and make the changes from RTX 4090 one by one while checking for errors in executing the run_simulations.py script for one of the apps from GPU_Microbenchmark([Context Here](https://github.com/accel-sim/accel-sim-framework/tree/dev#:~:text=If%20you%20want%20to%20run%20the%20accel%2Dsim.out%20executable%20command%20itself%20for%20specific%20workload%2C%20you%20can%20use%3A)).

Primarily the problem is coming from n_mem being 24. Change it to 32. Also the dram_latency in the generated 4090 config was unusually high. You can choose something close to the value in 3070 config.

Use the ISA of 3070 for 4090. It works for GPU_Microbenchmark. For any other benchmark it can cause some problem. But for older cuda programs it should work(I'm not sure about this).

Also do copy the trace.config from 3070 directory, in tested-cfgs folder to the 4090 config folder in same.

Basically copy the entire 3070 configs directory in `./gpu-simulator/gpgpu-sim/configs/tested-cfgs/` & `./gpu-simulator/configs/tested-cfgs/` and rename it to your desired 4090 config folder name. Add it to define-standard-cfgs.yml. Generate the 4090 config using [tuner section in accel-sim](https://github.com/accel-sim/accel-sim-framework/tree/dev/util/tuner#readme).  

22-12  
So I have made few changes in the gpgpusim.config.

```
-gpgpu_n_mem 12
-gpgpu_n_sub_partition_per_mchannel 2

-gpgpu_l1_banks 4
-gpgpu_cache:dl1 S:4:128:256,L:T:m:L:L,A:384:48,16:0,32

-gpgpu_cache:dl2 S:1024:128:24,L:B:m:L:L,A:192:16,32:0,32

```

Which gives us 1024\*128\*24\*12\*2 = 72MB

---

# Correlation outputs after making the PR changes

The L2 error seems to be still affected by the _buggy tuner_.

Now does the **L** here in **L**,A:192:16,32:0,32 mean the config doesn't use the IPOLY hashing function, i.e. it uses Linear.  
If it is , then what can the reason behind both the correlation looking exact?

- I had renamed the sim_run folder before running the second simulation.

## For -gpgpu_cache:dl2 S:1024:128:24,L:B:m:L:L,A:192:16,32:0,32

 ```
-----------------------------------------------------------------
All Card Summary:
HW Summary for NVIDIA GeForce RTX 4090 [Contains 17 Apps]:
----------------------------------------------------------------


/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 4090 : [GPC Cycles]
RTX4090-SASS (12 apps, 12 kernels (0 < 1% Err, 7 under, 5 over)) [Correl=0.9653 Err=30.29%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (0 < 1% Err, 7 under, 5 over, 2 < 10% Err)) [Correl=0.9653 Err=30.29% Agg_Err=28.37% RPD=31.15%,NMSE=0.51]

Plotting NVIDIA GeForce RTX 4090 : [Warp Instructions]
RTX4090-SASS (12 apps, 12 kernels (12 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.08%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (12 < 1% Err, 3 under, 0 over, 12 < 10% Err)) [Correl=1.0 Err=0.08% Agg_Err=0.07% RPD=0.08%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 4090 : [L2 Read Hits]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 0 under, 1 over)) [Correl=1.0 Err=184.03%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 0 under, 1 over, 11 < 10% Err)) [Correl=1.0 Err=184.03% Agg_Err=2208.32% RPD=15.28%,NMSE=76.50]

Plotting NVIDIA GeForce RTX 4090 : [L2 Reads]
RTX4090-SASS (9 apps, 9 kernels (8 < 1% Err, 0 under, 1 over)) [Correl=0.7136 Err=32.27%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (9 apps (8 < 1% Err, 0 under, 1 over, 8 < 10% Err)) [Correl=0.7136 Err=32.27% Agg_Err=59.19% RPD=13.16%,NMSE=1.78]

Plotting NVIDIA GeForce RTX 4090 : [L2 Writes]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 0 under, 1 over)) [Correl=0.9899 Err=24.91%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 0 under, 1 over, 11 < 10% Err)) [Correl=0.9899 Err=24.91% Agg_Err=8.55% RPD=9.99%,NMSE=0.30]

Plotting NVIDIA GeForce RTX 4090 : [L2 Write Hits]
RTX4090-SASS (12 apps, 12 kernels (0 < 1% Err, 11 under, 1 over)) [Correl=-0.1024 Err=105.24%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (0 < 1% Err, 11 under, 1 over, 0 < 10% Err)) [Correl=-0.1024 Err=105.24% Agg_Err=102.63% RPD=182.36%,NMSE=2.25]

/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 4090 : [L2 Write Hit Rate]
RTX4090-SASS (12 apps, 12 kernels (0 < 1% Err, 12 under, 0 over)) [Correl=nan Err=90.73%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (0 < 1% Err, 12 under, 0 over, 0 < 10% Err)) [Correl=nan Err=90.73% Agg_Err=90.73% RPD=176.43%,NMSE=0.93]

Plotting NVIDIA GeForce RTX 4090 : [Occupancy]
RTX4090-SASS (12 apps, 12 kernels (10 < 1% Err, 0 under, 2 over)) [Correl=0.9991 Err=1.79%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (10 < 1% Err, 7 under, 5 over, 11 < 10% Err)) [Correl=0.9991 Err=1.79% Agg_Err=1.05% RPD=1.70%,NMSE=0.03]

Plotting NVIDIA GeForce RTX 4090 : [L1D Read Hits]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 1 under, 0 over)) [Correl=0.9998 Err=0.90%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 1 under, 0 over, 11 < 10% Err)) [Correl=0.9998 Err=0.90% Agg_Err=0.92% RPD=0.95%,NMSE=0.03]

Plotting NVIDIA GeForce RTX 4090 : [L1D Write Hits]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 1 under, 0 over)) [Correl=1.0 Err=1.67%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 1 under, 0 over, 11 < 10% Err)) [Correl=1.0 Err=1.67% Agg_Err=19.95% RPD=1.85%,NMSE=0.69]

Plotting NVIDIA GeForce RTX 4090 : [L1D Read Access]
RTX4090-SASS (10 apps, 10 kernels (10 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.00%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (10 apps (10 < 1% Err, 0 under, 0 over, 10 < 10% Err)) [Correl=1.0 Err=0.00% Agg_Err=0.00% RPD=0.00%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 4090 : [L1D Write Access]
RTX4090-SASS (12 apps, 12 kernels (12 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.00%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (12 < 1% Err, 0 under, 0 over, 12 < 10% Err)) [Correl=1.0 Err=0.00% Agg_Err=0.00% RPD=0.00%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 4090 : [DRAM Reads]
RTX4090-SASS (12 apps, 12 kernels (2 < 1% Err, 10 under, 0 over)) [Correl=1.0 Err=35.75%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (2 < 1% Err, 12 under, 0 over, 6 < 10% Err)) [Correl=1.0 Err=35.75% Agg_Err=2.37% RPD=62.97%,NMSE=0.02]

/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 4090 : [DRAM Writes]
RTX4090-SASS (12 apps, 12 kernels (12 < 1% Err, 0 under, 0 over)) [Correl=nan Err=0.00%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (12 < 1% Err, 0 under, 0 over, 12 < 10% Err)) [Correl=nan Err=0.00% Agg_Err=0.00% RPD=0.00%,NMSE=0.00]
```

## For -gpgpu_cache:dl2 S:1024:128:24,L:B:m:L:P,A:192:16,32:0,32

```
-----------------------------------------------------------------
All Card Summary:
HW Summary for NVIDIA GeForce RTX 4090 [Contains 17 Apps]:
----------------------------------------------------------------


/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 4090 : [GPC Cycles]
RTX4090-SASS (12 apps, 12 kernels (0 < 1% Err, 7 under, 5 over)) [Correl=0.9653 Err=30.29%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (0 < 1% Err, 7 under, 5 over, 2 < 10% Err)) [Correl=0.9653 Err=30.29% Agg_Err=28.37% RPD=31.15%,NMSE=0.51]

Plotting NVIDIA GeForce RTX 4090 : [Warp Instructions]
RTX4090-SASS (12 apps, 12 kernels (12 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.08%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (12 < 1% Err, 3 under, 0 over, 12 < 10% Err)) [Correl=1.0 Err=0.08% Agg_Err=0.07% RPD=0.08%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 4090 : [L2 Read Hits]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 0 under, 1 over)) [Correl=1.0 Err=184.03%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 0 under, 1 over, 11 < 10% Err)) [Correl=1.0 Err=184.03% Agg_Err=2208.32% RPD=15.28%,NMSE=76.50]

Plotting NVIDIA GeForce RTX 4090 : [L2 Reads]
RTX4090-SASS (9 apps, 9 kernels (8 < 1% Err, 0 under, 1 over)) [Correl=0.7136 Err=32.27%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (9 apps (8 < 1% Err, 0 under, 1 over, 8 < 10% Err)) [Correl=0.7136 Err=32.27% Agg_Err=59.19% RPD=13.16%,NMSE=1.78]

Plotting NVIDIA GeForce RTX 4090 : [L2 Writes]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 0 under, 1 over)) [Correl=0.9899 Err=24.91%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 0 under, 1 over, 11 < 10% Err)) [Correl=0.9899 Err=24.91% Agg_Err=8.55% RPD=9.99%,NMSE=0.30]

Plotting NVIDIA GeForce RTX 4090 : [L2 Write Hits]
RTX4090-SASS (12 apps, 12 kernels (0 < 1% Err, 11 under, 1 over)) [Correl=-0.1024 Err=105.24%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (0 < 1% Err, 11 under, 1 over, 0 < 10% Err)) [Correl=-0.1024 Err=105.24% Agg_Err=102.63% RPD=182.36%,NMSE=2.25]

/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/home/cab-prj/.conda/envs/accelsim/lib/python3.11/site-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 4090 : [L2 Write Hit Rate]
RTX4090-SASS (12 apps, 12 kernels (0 < 1% Err, 12 under, 0 over)) [Correl=nan Err=90.73%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (0 < 1% Err, 12 under, 0 over, 0 < 10% Err)) [Correl=nan Err=90.73% Agg_Err=90.73% RPD=176.43%,NMSE=0.93]

Plotting NVIDIA GeForce RTX 4090 : [Occupancy]
RTX4090-SASS (12 apps, 12 kernels (10 < 1% Err, 0 under, 2 over)) [Correl=0.9991 Err=1.79%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (10 < 1% Err, 7 under, 5 over, 11 < 10% Err)) [Correl=0.9991 Err=1.79% Agg_Err=1.05% RPD=1.70%,NMSE=0.03]

Plotting NVIDIA GeForce RTX 4090 : [L1D Read Hits]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 1 under, 0 over)) [Correl=0.9998 Err=0.90%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 1 under, 0 over, 11 < 10% Err)) [Correl=0.9998 Err=0.90% Agg_Err=0.92% RPD=0.95%,NMSE=0.03]

Plotting NVIDIA GeForce RTX 4090 : [L1D Write Hits]
RTX4090-SASS (12 apps, 12 kernels (11 < 1% Err, 1 under, 0 over)) [Correl=1.0 Err=1.67%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (11 < 1% Err, 1 under, 0 over, 11 < 10% Err)) [Correl=1.0 Err=1.67% Agg_Err=19.95% RPD=1.85%,NMSE=0.69]

Plotting NVIDIA GeForce RTX 4090 : [L1D Read Access]
RTX4090-SASS (10 apps, 10 kernels (10 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.00%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (10 apps (10 < 1% Err, 0 under, 0 over, 10 < 10% Err)) [Correl=1.0 Err=0.00% Agg_Err=0.00% RPD=0.00%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 4090 : [L1D Write Access]
RTX4090-SASS (12 apps, 12 kernels (12 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.00%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (12 < 1% Err, 0 under, 0 over, 12 < 10% Err)) [Correl=1.0 Err=0.00% Agg_Err=0.00% RPD=0.00%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 4090 : [DRAM Reads]
RTX4090-SASS (12 apps, 12 kernels (2 < 1% Err, 10 under, 0 over)) [Correl=1.0 Err=35.75%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX4090-SASS (12 apps (2 < 1% Err, 12 under, 0 over, 6 < 10% Err)) [Correl=1.0 Err=35.75% Agg_Err=2.37% RPD=62.97%,NMSE=0.02]

```

## The updated IPOLY Hash function from gpgpu-sim/hashing.cc

```
unsigned ipoly_hash_function(new_addr_type higher_bits, unsigned index,
                             unsigned bank_set_num) {
  /*
   * Set Indexing function from "Pseudo-randomly interleaved memory."
   * Rau, B. R et al.
   * ISCA 1991
   * [http://citeseerx.ist.psu.edu/viewdoc/download];jsessionid=348DEA37A3E440473B3C075EAABC63B6?doi=10.1.1.12.7149&rep=rep1&type=pdf
   *
   * equations are corresponding to IPOLY(37) and are adopted from:
   * "Sacat: streaming-aware conflict-avoiding thrashing-resistant gpgpu
   * cache management scheme." Khairy et al. IEEE TPDS 2017.
   *
   * equations for 16 banks are corresponding to IPOLY(5)
   * equations for 32 banks are corresponding to IPOLY(37)
   * equations for 64 banks are corresponding to IPOLY(67)
   * equations for 128 banks are corresponding to IPOLY(127)
   * equations for 256 banks are corresponding to IPOLY(285)
   * equations for 512 banks are corresponding to IPOLY(9)
   * equations for 1024 banks are corresponding to IPOLY(10)
   * equations for 2048 banks are corresponding to IPOLY(11)
   *
   * To see all the IPOLY equations for all the degrees, see
   * http://wireless-systems.ece.gatech.edu/6604/handouts/Peterson's%20Table.pdf
   *
   * We generate these equations using GF(2) arithmetic:
   * [http://www.ee.unb.ca/cgi-bin/tervo/calc.pl?num=&den=&f=d&e=1&m=1](http://www.ee.unb.ca/cgi-bin/tervo/calc.pl?num=&den=&f=d&e=1&m=1)
   *
   * We go through all the strides 128 (10000000), 256 (100000000),...  and
   * do modular arithmetic in GF(2) Then, we create the H-matrix and group
   * each bit together, for more info read the ISCA 1991 paper
   *
   * IPOLY hashing guarantees conflict-free for all 2^n strides which widely
   * exit in GPGPU applications and also show good performance for other
   * strides.
   */

  if (bank_set_num == 16) {
    std::bitset<64> a(higher_bits);
    std::bitset<4> b(index);
    std::bitset<4> new_index(index);

    new_index[0] =
        a[11] ^ a[10] ^ a[9] ^ a[8] ^ a[6] ^ a[4] ^ a[3] ^ a[0] ^ b[0];
    new_index[1] =
        a[12] ^ a[8] ^ a[7] ^ a[6] ^ a[5] ^ a[3] ^ a[1] ^ a[0] ^ b[1];
    new_index[2] = a[9] ^ a[8] ^ a[7] ^ a[6] ^ a[4] ^ a[2] ^ a[1] ^ b[2];
    new_index[3] = a[10] ^ a[9] ^ a[8] ^ a[7] ^ a[5] ^ a[3] ^ a[2] ^ b[3];

    return new_index.to_ulong();

  } else if (bank_set_num == 32) {
    std::bitset<64> a(higher_bits);
    std::bitset<5> b(index);
    std::bitset<5> new_index(index);

    new_index[0] =
        a[13] ^ a[12] ^ a[11] ^ a[10] ^ a[9] ^ a[6] ^ a[5] ^ a[3] ^ a[0] ^ b[0];
    new_index[1] = a[14] ^ a[13] ^ a[12] ^ a[11] ^ a[10] ^ a[7] ^ a[6] ^ a[4] ^
                   a[1] ^ b[1];
    new_index[2] =
        a[14] ^ a[10] ^ a[9] ^ a[8] ^ a[7] ^ a[6] ^ a[3] ^ a[2] ^ a[0] ^ b[2];
    new_index[3] =
        a[11] ^ a[10] ^ a[9] ^ a[8] ^ a[7] ^ a[4] ^ a[3] ^ a[1] ^ b[3];
    new_index[4] =
        a[12] ^ a[11] ^ a[10] ^ a[9] ^ a[8] ^ a[5] ^ a[4] ^ a[2] ^ b[4];
    return new_index.to_ulong();

  } else if (bank_set_num == 64) {
    std::bitset<64> a(higher_bits);
    std::bitset<6> b(index);
    std::bitset<6> new_index(index);

    new_index[0] = a[18] ^ a[17] ^ a[16] ^ a[15] ^ a[12] ^ a[10] ^ a[6] ^ a[5] ^
                   a[0] ^ b[0];
    new_index[1] = a[15] ^ a[13] ^ a[12] ^ a[11] ^ a[10] ^ a[7] ^ a[5] ^ a[1] ^
                   a[0] ^ b[1];
    new_index[2] = a[16] ^ a[14] ^ a[13] ^ a[12] ^ a[11] ^ a[8] ^ a[6] ^ a[2] ^
                   a[1] ^ b[2];
    new_index[3] = a[17] ^ a[15] ^ a[14] ^ a[13] ^ a[12] ^ a[9] ^ a[7] ^ a[3] ^
                   a[2] ^ b[3];
    new_index[4] = a[18] ^ a[16] ^ a[15] ^ a[14] ^ a[13] ^ a[10] ^ a[8] ^ a[4] ^
                   a[3] ^ b[4];
    new_index[5] =
        a[17] ^ a[16] ^ a[15] ^ a[14] ^ a[11] ^ a[9] ^ a[5] ^ a[4] ^ b[5];
    return new_index.to_ulong();

  } else if (bank_set_num == 128) {
    // IPOLY(127) - Degree 7 polynomial
    // Primitive polynomial: x^7 + x^3 + 1
    std::bitset<64> a(higher_bits);
    std::bitset<7> b(index);
    std::bitset<7> new_index(index);

    // XOR equations derived from H-matrix of degree-7 irreducible polynomial
    // Using GF(2) arithmetic for 128 = 2^7 conflict-free strides
    new_index[0] = a[20] ^ a[19] ^ a[18] ^ a[17] ^ a[14] ^ a[12] ^ a[8] ^ a[7] ^
                   a[0] ^ b[0];
    new_index[1] = a[17] ^ a[15] ^ a[14] ^ a[13] ^ a[12] ^ a[9] ^ a[7] ^ a[2] ^
                   a[1] ^ b[1];
    new_index[2] = a[18] ^ a[16] ^ a[15] ^ a[14] ^ a[13] ^ a[10] ^ a[8] ^ a[3] ^
                   a[2] ^ b[2];
    new_index[3] = a[19] ^ a[17] ^ a[16] ^ a[15] ^ a[14] ^ a[11] ^ a[9] ^ a[4] ^
                   a[3] ^ b[3];
    new_index[4] = a[20] ^ a[18] ^ a[17] ^ a[16] ^ a[15] ^ a[12] ^ a[10] ^ a[5] ^
                   a[4] ^ b[4];
    new_index[5] = a[19] ^ a[18] ^ a[17] ^ a[16] ^ a[13] ^ a[11] ^ a[6] ^ a[5] ^
                   b[5];
    new_index[6] = a[20] ^ a[19] ^ a[18] ^ a[17] ^ a[14] ^ a[12] ^ a[7] ^ a[6] ^
                   b[6];

    return new_index.to_ulong();

  } else if (bank_set_num == 256) {
    // IPOLY(285) - Degree 8 polynomial
    // Primitive polynomial: x^8 + x^4 + x^3 + x + 1 (or similar)
    std::bitset<64> a(higher_bits);
    std::bitset<8> b(index);
    std::bitset<8> new_index(index);

    // XOR equations for 256 = 2^8 conflict-free strides
    new_index[0] = a[22] ^ a[21] ^ a[20] ^ a[19] ^ a[16] ^ a[14] ^ a[10] ^ a[9] ^
                   a[0] ^ b[0];
    new_index[1] = a[19] ^ a[17] ^ a[16] ^ a[15] ^ a[14] ^ a[11] ^ a[9] ^ a[3] ^
                   a[2] ^ b[1];
    new_index[2] = a[20] ^ a[18] ^ a[17] ^ a[16] ^ a[15] ^ a[12] ^ a[10] ^ a[4] ^
                   a[3] ^ b[2];
    new_index[3] = a[21] ^ a[19] ^ a[18] ^ a[17] ^ a[16] ^ a[13] ^ a[11] ^ a[5] ^
                   a[4] ^ b[3];
    new_index[4] = a[22] ^ a[20] ^ a[19] ^ a[18] ^ a[17] ^ a[14] ^ a[12] ^ a[6] ^
                   a[5] ^ b[4];
    new_index[5] = a[21] ^ a[20] ^ a[19] ^ a[18] ^ a[15] ^ a[13] ^ a[7] ^ a[6] ^
                   b[5];
    new_index[6] = a[22] ^ a[21] ^ a[20] ^ a[19] ^ a[16] ^ a[14] ^ a[8] ^ a[7] ^
                   b[6];
    new_index[7] = a[22] ^ a[21] ^ a[20] ^ a[17] ^ a[15] ^ a[9] ^ a[8] ^ b[7];

    return new_index.to_ulong();

  } else if (bank_set_num == 512) {
    // IPOLY(9) - Degree 9 polynomial
    // Primitive polynomial: x^9 + x^4 + 1
    // This provides conflict-free access for 2^9 = 512 strides
    std::bitset<64> a(higher_bits);
    std::bitset<9> b(index);
    std::bitset<9> new_index(index);

    // XOR equations derived from H-matrix of degree-9 irreducible polynomial
    // Computed via GF(2) arithmetic to guarantee conflict-free stride patterns
    new_index[0] = a[24] ^ a[23] ^ a[22] ^ a[21] ^ a[18] ^ a[16] ^ a[12] ^ a[11] ^
                   a[0] ^ b[0];
    new_index[1] = a[21] ^ a[19] ^ a[18] ^ a[17] ^ a[16] ^ a[13] ^ a[11] ^ a[4] ^
                   a[3] ^ b[1];
    new_index[2] = a[22] ^ a[20] ^ a[19] ^ a[18] ^ a[17] ^ a[14] ^ a[12] ^ a[5] ^
                   a[4] ^ b[2];
    new_index[3] = a[23] ^ a[21] ^ a[20] ^ a[19] ^ a[18] ^ a[15] ^ a[13] ^ a[6] ^
                   a[5] ^ b[3];
    new_index[4] = a[24] ^ a[22] ^ a[21] ^ a[20] ^ a[19] ^ a[16] ^ a[14] ^ a[7] ^
                   a[6] ^ b[4];
    new_index[5] = a[23] ^ a[22] ^ a[21] ^ a[20] ^ a[17] ^ a[15] ^ a[8] ^ a[7] ^
                   b[5];
    new_index[6] = a[24] ^ a[23] ^ a[22] ^ a[21] ^ a[18] ^ a[16] ^ a[9] ^ a[8] ^
                   b[6];
    new_index[7] = a[24] ^ a[23] ^ a[22] ^ a[19] ^ a[17] ^ a[10] ^ a[9] ^ b[7];
    new_index[8] = a[24] ^ a[23] ^ a[20] ^ a[18] ^ a[11] ^ a[10] ^ b[8];

    return new_index.to_ulong();

  } else if (bank_set_num == 1024) {
    // IPOLY(10) - Degree 10 polynomial
    // Primitive polynomial: x^10 + x^3 + 1
    // This provides conflict-free access for 2^10 = 1024 strides
    std::bitset<64> a(higher_bits);
    std::bitset<10> b(index);
    std::bitset<10> new_index(index);

    // XOR equations derived from H-matrix of degree-10 irreducible polynomial
    new_index[0] = a[25] ^ a[24] ^ a[23] ^ a[22] ^ a[19] ^ a[17] ^ a[13] ^ a[12] ^
                   a[0] ^ b[0];
    new_index[1] = a[22] ^ a[20] ^ a[19] ^ a[18] ^ a[17] ^ a[14] ^ a[12] ^ a[4] ^
                   a[3] ^ b[1];
    new_index[2] = a[23] ^ a[21] ^ a[20] ^ a[19] ^ a[18] ^ a[15] ^ a[13] ^ a[5] ^
                   a[4] ^ b[2];
    new_index[3] = a[24] ^ a[22] ^ a[21] ^ a[20] ^ a[19] ^ a[16] ^ a[14] ^ a[6] ^
                   a[5] ^ b[3];
    new_index[4] = a[25] ^ a[23] ^ a[22] ^ a[21] ^ a[20] ^ a[17] ^ a[15] ^ a[7] ^
                   a[6] ^ b[4];
    new_index[5] = a[24] ^ a[23] ^ a[22] ^ a[21] ^ a[18] ^ a[16] ^ a[8] ^ a[7] ^
                   b[5];
    new_index[6] = a[25] ^ a[24] ^ a[23] ^ a[22] ^ a[19] ^ a[17] ^ a[9] ^ a[8] ^
                   b[6];
    new_index[7] = a[25] ^ a[24] ^ a[23] ^ a[20] ^ a[18] ^ a[10] ^ a[9] ^ b[7];
    new_index[8] = a[25] ^ a[24] ^ a[21] ^ a[19] ^ a[11] ^ a[10] ^ b[8];
    new_index[9] = a[25] ^ a[22] ^ a[20] ^ a[12] ^ a[11] ^ b[9];

    return new_index.to_ulong();

  } else if (bank_set_num == 2048) {
    // IPOLY(11) - Degree 11 polynomial
    // Primitive polynomial: x^11 + x^2 + 1
    // This provides conflict-free access for 2^11 = 2048 strides
    std::bitset<64> a(higher_bits);
    std::bitset<11> b(index);
    std::bitset<11> new_index(index);

    // XOR equations derived from H-matrix of degree-11 irreducible polynomial
    new_index[0] = a[26] ^ a[25] ^ a[24] ^ a[23] ^ a[20] ^ a[18] ^ a[14] ^ a[13] ^
                   a[0] ^ b[0];
    new_index[1] = a[23] ^ a[21] ^ a[20] ^ a[19] ^ a[18] ^ a[15] ^ a[13] ^ a[4] ^
                   a[3] ^ b[1];
    new_index[2] = a[24] ^ a[22] ^ a[21] ^ a[20] ^ a[19] ^ a[16] ^ a[14] ^ a[5] ^
                   a[4] ^ b[2];
    new_index[3] = a[25] ^ a[23] ^ a[22] ^ a[21] ^ a[20] ^ a[17] ^ a[15] ^ a[6] ^
                   a[5] ^ b[3];
    new_index[4] = a[26] ^ a[24] ^ a[23] ^ a[22] ^ a[21] ^ a[18] ^ a[16] ^ a[7] ^
                   a[6] ^ b[4];
    new_index[5] = a[25] ^ a[24] ^ a[23] ^ a[22] ^ a[19] ^ a[17] ^ a[8] ^ a[7] ^
                   b[5];
    new_index[6] = a[26] ^ a[25] ^ a[24] ^ a[23] ^ a[20] ^ a[18] ^ a[9] ^ a[8] ^
                   b[6];
    new_index[7] = a[26] ^ a[25] ^ a[24] ^ a[21] ^ a[19] ^ a[10] ^ a[9] ^ b[7];
    new_index[8] = a[26] ^ a[25] ^ a[22] ^ a[20] ^ a[11] ^ a[10] ^ b[8];
    new_index[9] = a[26] ^ a[23] ^ a[21] ^ a[12] ^ a[11] ^ b[9];
    new_index[10] = a[24] ^ a[22] ^ a[13] ^ a[12] ^ b[10];

    return new_index.to_ulong();

  } else { /* Incorrect number of banks for the hashing function */
    fprintf(stderr,
            "IPOLY ERROR:\n"
            "  bank_set_num = %u (Supported: 16, 32, 64, 128, 256, 512, 1024, 2048)\n"
            "  index        = %u\n"
            "  higher_bits  = 0x%llx\n"
            "  The number of banks must be a power of 2 from 16 to 2048.\n"
            "  To add new bank counts, generate XOR equations from the\n"
            "  H-matrix of an irreducible polynomial of appropriate degree\n"
            "  using GF(2) arithmetic. Reference: Peterson's Table.\n",
            bank_set_num,
            index,
            (unsigned long long)higher_bits);

    assert(0);
    return 0;
  }
}
```

---

1. find out load store units

---
What happens when simultaneous stores happens.

- L1 is always write through.
