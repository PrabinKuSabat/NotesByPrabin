```bash
root@ubuntu:~/secTry/accel-sim-framework# ./util/plotting/plot-correlation.py -c per.kernel.statsSASS.csv -H ./hw_run/device-0/12.4/
-----------------------------------------------------------------
All Card Summary:
HW Summary for NVIDIA GeForce RTX 3070 [Contains 10 Apps]:
----------------------------------------------------------------

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 3070 : [GPC Cycles]
RTX3070-SASS (10 apps, 193 kernels (2 < 1% Err, 20 under, 171 over)) [Correl=0.9775 Err=23.38%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (1 < 1% Err, 5 under, 5 over, 6 < 10% Err)) [Correl=0.998 Err=11.95% Agg_Err=19.10% RPD=11.26%,NMSE=0.46]

Plotting NVIDIA GeForce RTX 3070 : [Warp Instructions]
RTX3070-SASS (10 apps, 193 kernels (193 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.02%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (10 < 1% Err, 3 under, 0 over, 10 < 10% Err)) [Correl=1.0 Err=0.10% Agg_Err=0.13% RPD=0.10%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 3070 : [L2 Read Hits]
RTX3070-SASS (10 apps, 193 kernels (20 < 1% Err, 6 under, 167 over)) [Correl=0.1462 Err=14247.38%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (0 < 1% Err, 1 under, 9 over, 1 < 10% Err)) [Correl=-0.09267 Err=8066.46% Agg_Err=1133.00% RPD=116.66%,NMSE=29.15]

Plotting NVIDIA GeForce RTX 3070 : [L2 Reads]
RTX3070-SASS (10 apps, 193 kernels (185 < 1% Err, 2 under, 6 over)) [Correl=0.9993 Err=0.21%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (7 < 1% Err, 1 under, 2 over, 10 < 10% Err)) [Correl=0.9999 Err=1.09% Agg_Err=0.96% RPD=1.07%,NMSE=0.03]

Plotting NVIDIA GeForce RTX 3070 : [L2 Writes]
RTX3070-SASS (10 apps, 193 kernels (181 < 1% Err, 0 under, 12 over)) [Correl=0.9989 Err=1.09%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (6 < 1% Err, 0 under, 4 over, 9 < 10% Err)) [Correl=0.9994 Err=3.06% Agg_Err=1.98% RPD=2.88%,NMSE=0.04]

Plotting NVIDIA GeForce RTX 3070 : [L2 Write Hits]
RTX3070-SASS (10 apps, 193 kernels (159 < 1% Err, 25 under, 9 over)) [Correl=0.7742 Err=9.21%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (4 < 1% Err, 7 under, 1 over, 6 < 10% Err)) [Correl=0.882 Err=26.46% Agg_Err=26.91% RPD=41.45%,NMSE=0.53]

Plotting NVIDIA GeForce RTX 3070 : [L2 Write Hit Rate]
RTX3070-SASS (10 apps, 192 kernels (7 < 1% Err, 117 under, 68 over)) [Correl=0.003993 Err=27.77%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (1 < 1% Err, 10 under, 0 over, 4 < 10% Err)) [Correl=0.1515 Err=36.30% Agg_Err=39.40% RPD=52.25%,NMSE=0.53]

Plotting NVIDIA GeForce RTX 3070 : [Occupancy]
RTX3070-SASS (10 apps, 193 kernels (35 < 1% Err, 149 under, 9 over)) [Correl=0.9706 Err=4.44%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (1 < 1% Err, 7 under, 3 over, 7 < 10% Err)) [Correl=0.9736 Err=10.95% Agg_Err=13.05% RPD=9.22%,NMSE=0.24]

Plotting NVIDIA GeForce RTX 3070 : [L1D Read Hits]
RTX3070-SASS (10 apps, 193 kernels (187 < 1% Err, 1 under, 5 over)) [Correl=0.9998 Err=0.24%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (8 < 1% Err, 1 under, 2 over, 10 < 10% Err)) [Correl=0.9989 Err=1.17% Agg_Err=5.29% RPD=1.12%,NMSE=0.16]

Plotting NVIDIA GeForce RTX 3070 : [L1D Write Hits]
RTX3070-SASS (10 apps, 193 kernels (25 < 1% Err, 164 under, 4 over)) [Correl=0.9681 Err=13.74%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (3 < 1% Err, 6 under, 1 over, 5 < 10% Err)) [Correl=0.9893 Err=31.00% Agg_Err=16.10% RPD=48.21%,NMSE=0.23]

Plotting NVIDIA GeForce RTX 3070 : [L1D Read Access]
RTX3070-SASS (10 apps, 193 kernels (167 < 1% Err, 0 under, 26 over)) [Correl=0.9998 Err=0.74%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (5 < 1% Err, 0 under, 8 over, 10 < 10% Err)) [Correl=0.9987 Err=3.36% Agg_Err=3.67% RPD=3.25%,NMSE=0.10]

Plotting NVIDIA GeForce RTX 3070 : [L1D Write Access]
RTX3070-SASS (10 apps, 193 kernels (193 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.00%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (10 < 1% Err, 0 under, 0 over, 10 < 10% Err)) [Correl=1.0 Err=0.00% Agg_Err=0.00% RPD=0.00%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 3070 : [DRAM Reads]
RTX3070-SASS (10 apps, 193 kernels (0 < 1% Err, 192 under, 1 over)) [Correl=0.5003 Err=90.65%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (0 < 1% Err, 10 under, 0 over, 0 < 10% Err)) [Correl=-0.02178 Err=64.98% Agg_Err=89.62% RPD=108.32%,NMSE=2.29]

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 3070 : [DRAM Writes]
RTX3070-SASS (10 apps, 193 kernels (1 < 1% Err, 192 under, 0 over)) [Correl=nan Err=99.48%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-SASS (10 apps (0 < 1% Err, 10 under, 0 over, 0 < 10% Err)) [Correl=nan Err=100.00% Agg_Err=100.00% RPD=200.00%,NMSE=1.35]

Combined per-kernel output available at: file:///root/secTry/accel-sim-framework/util/plotting/correl-html/combined_per_kernel.html
Combined per-app output available at: file:///root/secTry/accel-sim-framework/util/plotting/correl-html/combined_per_app.html
```

```
root@ubuntu:~/secTry/accel-sim-framework# ./util/plotting/plot-correlation.py -c per.kernel.statsPTX.csv -H ./hw_run/device-0/12.4/
-----------------------------------------------------------------
All Card Summary:
HW Summary for NVIDIA GeForce RTX 3070 [Contains 10 Apps]:
----------------------------------------------------------------

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 3070 : [GPC Cycles]
RTX3070-PTX (10 apps, 193 kernels (2 < 1% Err, 20 under, 171 over)) [Correl=0.9775 Err=23.38%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (1 < 1% Err, 5 under, 5 over, 6 < 10% Err)) [Correl=0.998 Err=11.95% Agg_Err=19.10% RPD=11.26%,NMSE=0.46]

Plotting NVIDIA GeForce RTX 3070 : [Warp Instructions]
RTX3070-PTX (10 apps, 193 kernels (193 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.02%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (10 < 1% Err, 3 under, 0 over, 10 < 10% Err)) [Correl=1.0 Err=0.10% Agg_Err=0.13% RPD=0.10%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 3070 : [L2 Read Hits]
RTX3070-PTX (10 apps, 193 kernels (20 < 1% Err, 6 under, 167 over)) [Correl=0.1462 Err=14247.38%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (0 < 1% Err, 1 under, 9 over, 1 < 10% Err)) [Correl=-0.09267 Err=8066.46% Agg_Err=1133.00% RPD=116.66%,NMSE=29.15]

Plotting NVIDIA GeForce RTX 3070 : [L2 Reads]
RTX3070-PTX (10 apps, 193 kernels (185 < 1% Err, 2 under, 6 over)) [Correl=0.9993 Err=0.21%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (7 < 1% Err, 1 under, 2 over, 10 < 10% Err)) [Correl=0.9999 Err=1.09% Agg_Err=0.96% RPD=1.07%,NMSE=0.03]

Plotting NVIDIA GeForce RTX 3070 : [L2 Writes]
RTX3070-PTX (10 apps, 193 kernels (181 < 1% Err, 0 under, 12 over)) [Correl=0.9989 Err=1.09%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (6 < 1% Err, 0 under, 4 over, 9 < 10% Err)) [Correl=0.9994 Err=3.06% Agg_Err=1.98% RPD=2.88%,NMSE=0.04]

Plotting NVIDIA GeForce RTX 3070 : [L2 Write Hits]
RTX3070-PTX (10 apps, 193 kernels (159 < 1% Err, 25 under, 9 over)) [Correl=0.7742 Err=9.21%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (4 < 1% Err, 7 under, 1 over, 6 < 10% Err)) [Correl=0.882 Err=26.46% Agg_Err=26.91% RPD=41.45%,NMSE=0.53]

Plotting NVIDIA GeForce RTX 3070 : [L2 Write Hit Rate]
RTX3070-PTX (10 apps, 192 kernels (7 < 1% Err, 117 under, 68 over)) [Correl=0.003993 Err=27.77%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (1 < 1% Err, 10 under, 0 over, 4 < 10% Err)) [Correl=0.1515 Err=36.30% Agg_Err=39.40% RPD=52.25%,NMSE=0.53]

Plotting NVIDIA GeForce RTX 3070 : [Occupancy]
RTX3070-PTX (10 apps, 193 kernels (35 < 1% Err, 149 under, 9 over)) [Correl=0.9706 Err=4.44%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (1 < 1% Err, 7 under, 3 over, 7 < 10% Err)) [Correl=0.9736 Err=10.95% Agg_Err=13.05% RPD=9.22%,NMSE=0.24]

Plotting NVIDIA GeForce RTX 3070 : [L1D Read Hits]
RTX3070-PTX (10 apps, 193 kernels (187 < 1% Err, 1 under, 5 over)) [Correl=0.9998 Err=0.24%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (8 < 1% Err, 1 under, 2 over, 10 < 10% Err)) [Correl=0.9989 Err=1.17% Agg_Err=5.29% RPD=1.12%,NMSE=0.16]

Plotting NVIDIA GeForce RTX 3070 : [L1D Write Hits]
RTX3070-PTX (10 apps, 193 kernels (25 < 1% Err, 164 under, 4 over)) [Correl=0.9681 Err=13.74%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (3 < 1% Err, 6 under, 1 over, 5 < 10% Err)) [Correl=0.9893 Err=31.00% Agg_Err=16.10% RPD=48.21%,NMSE=0.23]

Plotting NVIDIA GeForce RTX 3070 : [L1D Read Access]
RTX3070-PTX (10 apps, 193 kernels (167 < 1% Err, 0 under, 26 over)) [Correl=0.9998 Err=0.74%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (5 < 1% Err, 0 under, 8 over, 10 < 10% Err)) [Correl=0.9987 Err=3.36% Agg_Err=3.67% RPD=3.25%,NMSE=0.10]

Plotting NVIDIA GeForce RTX 3070 : [L1D Write Access]
RTX3070-PTX (10 apps, 193 kernels (193 < 1% Err, 0 under, 0 over)) [Correl=1.0 Err=0.00%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (10 < 1% Err, 0 under, 0 over, 10 < 10% Err)) [Correl=1.0 Err=0.00% Agg_Err=0.00% RPD=0.00%,NMSE=0.00]

Plotting NVIDIA GeForce RTX 3070 : [DRAM Reads]
RTX3070-PTX (10 apps, 193 kernels (0 < 1% Err, 192 under, 1 over)) [Correl=0.5003 Err=90.65%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (0 < 1% Err, 10 under, 0 over, 0 < 10% Err)) [Correl=-0.02178 Err=64.98% Agg_Err=89.62% RPD=108.32%,NMSE=2.29]

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3045: RuntimeWarning:

invalid value encountered in divide

/usr/local/lib/python3.10/dist-packages/numpy/lib/_function_base_impl.py:3046: RuntimeWarning:

invalid value encountered in divide

Plotting NVIDIA GeForce RTX 3070 : [DRAM Writes]
RTX3070-PTX (10 apps, 193 kernels (1 < 1% Err, 192 under, 0 over)) [Correl=nan Err=99.48%] :: 0 high error points dropped from Err calc. 0 dropped for HW too low (>0)
Per-App :: RTX3070-PTX (10 apps (0 < 1% Err, 10 under, 0 over, 0 < 10% Err)) [Correl=nan Err=100.00% Agg_Err=100.00% RPD=200.00%,NMSE=1.35]
```
