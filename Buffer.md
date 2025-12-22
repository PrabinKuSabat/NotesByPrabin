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
