# 26-08 : Naveen

Hi Prabin ,  
Your choice of Accel-sim is a good choice . Under the hood Accel-sim uses GPGPUsim to simulate GPU  
 Did you get access to a 3070 ? Vast.ai or SalaryCloud is cheap . @Dr. R. Raghunatha Sarma, Assistant Professor, DMACS, SSSIHL  sir please take a look at this .   
  
I did some research using ChatGPT on latest papers on Accel-sim :   

## Recent Papers on Accel-Sim

### 1. _Analyzing and Improving Hardware Modeling of Accel-Sim_ (CAMS 2023)

- **Authors:** Rodrigo Huerta, Mojtaba Abaie Shoushtary, Antonio González
	 
- **Key Focus:** Deep analysis of Accel‑Sim’s modeling—improving the front‑end, result bus, and memory pipeline, proposing cost-effective design enhancements.
	 
- **Details:** Offers a more realistic modeling of GPU internals, addressing hardware fidelity and pointing out areas for further improvement.  
	 [Futur+15arXiv+15Accel-Sim+15](https://arxiv.org/abs/2401.10082?utm_source=chatgpt.com)[Semiconductor Engineering](https://semiengineering.com/analysis-of-accel-sim-gpgpu-simulator-and-model-improvements/?utm_source=chatgpt.com)

### 2. _Parallelizing a Modern GPU Simulator_ (CAMS 2024 / arXiv Feb 2025)

- **Authors:** Rodrigo Huerta, Antonio González
	 
- **Key Focus:** Introduces parallel execution in Accel‑Sim using OpenMP, achieving:
	 
	 - **5.8× average speed-up**
		  
	 - **Up to 14× speed-up** in some workloads
		  
	 - Deterministic multi-threading (no loss in simulation accuracy)
		  
	 - Drastically reduced simulation times—from over five days to under 12 hours for heavy workloads  
		  [ResearchGate+6arXiv+6arXiv+6](https://arxiv.org/html/2502.14691v2?utm_source=chatgpt.com)

### 3. _MAccel-Sim: A Multi-GPU Simulator for Architectural Exploration_ (Poster, IISWC 2024)

- **Authors:** Christin Bose, Cesar Avalos, Junrui Pan, Mahmoud Khairy, Tim Rogers
	 
- **Key Focus:** Proposes **MAccel‑Sim**, an extension of Accel‑Sim tailored for **multi‑GPU setups**. This addresses limitations in modeling multi-GPU workloads and seeks to enhance simulation performance and usability.  
	 [arXiv+15Purdue Engineering+15Accel-Sim+15](https://engineering.purdue.edu/tgrogers/publication/bose-iiswc-poster-2024/?utm_source=chatgpt.com)

### 4. _Integrating Per-Stream Stat Tracking into Accel-Sim_ (arXiv Apr 2023)

- **Authors:** Shichen Qiao, Xin Su, Matthew D. Sinclair
	 
- **Key Focus:** Adds capability to track **statistics per CUDA stream**—previously, stats were aggregated across all streams. This extension allows more granular analysis of kernels and avoids misleading conclusions.  
	 [arXiv](https://arxiv.org/abs/2304.11136?utm_source=chatgpt.com)  
		
	 Papers 2 and 3 sound interesting to me . You can reach out to the authors or check if they can share their implementations publicly or with you .

# 27-08 : Naveen
@Prabin Sabat   @Dr. R. Raghunatha Sarma, Assistant Professor, DMACS, SSSIHL  Please check  [https://vast.ai/](https://vast.ai/) seems extremely reasonable, mostly around 50$ for 1 month usage which is extremely cheap . You could even rent it for a short period of time . SaladCloud needs you to deploy everything as a container which might be good for you to learn as a technology.   
@Prabin Sabat document every little system detail starting from nvidia-smi output, lscpu, os details . Maybe ask ChatGPT to write a bash script for these and have them as part of your output logs  . @Saketh Cherukuri This holds especially well for you where you log the python packages and their versions.

# 27-08 : Raghunatha Sir

Yes. We will look into this. I have added Dr srinath also this thread.

Raghu

