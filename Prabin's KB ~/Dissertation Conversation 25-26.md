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

# 28-08 : ME

As [@Naveen M](mailto:naveen.miriyalu@gmail.com)  Sir mentioned, I will be reviewing the four research papers. Three of them were already on my reading list, except for _MAccel-Sim_.

I am currently going through the Vast.ai documentation, and once I complete it, I will prepare and share a detailed report.

[@Dr. R. Raghunatha Sarma, Assistant Professor, DMACS, SSSIHL](mailto:rraghunathasarma@sssihl.edu.in)  Sir — after reviewing the documentation, I will send a report with specific details regarding the resources required and their associated costs.  
  
Also, from now on the [Prabin's KB ~](https://prabins.vercel.app/) website will be having my notes.  
The [Dissertation To-Do](https://prabins.vercel.app/Excalidraw/Dissertation%20Status.excalidraw/)  page on the homepage will track the project’s progress and the tasks I am working on.

# 28-08 : Naveen

[@Prabin Sabat](mailto:prabinkusabat@gmail.com)  Put your primary efforts on getting an initial correlation with a 3070 .   

1. Get onto [vast.ai](http://vast.ai/) quick if they have a trial offer
2. Get to know access and any other quick info etc (Ask ChatGPT) . ChatGPT wont make you dumb. You just need to offload dumb work to it and use your mindspace for something better .  
3. If your suffering due to process hurdles in the institute (Do let me know) . If its just a financial hurdle , I could spin up an instance and give you access . 
4. You need to be quick especially with the amount of progress you have done with understanding the setup etc . 
5. We can then build a similar config for 4090 and we have a local access for the same

# 29-08 : ME

**Sairam Sir,**

For our project, I recommend selecting the Ubuntu 22.04 VM template for our instance. The documentation for setting this up is available [here]. This setup will give us full control over the system environment, which is ideal for our needs.

## Key Use Cases

- **Custom ML Framework Setups**: With this setup, we can run multiple ML frameworks simultaneously while controlling CUDA versions. This flexibility is crucial for ensuring compatibility with both legacy projects and newer frameworks.
	 
- **Distributed Training Systems**: We can configure full Kubernetes clusters for distributed machine learning, enabling efficient training of large models across multiple nodes.

## Pricing (RTX3070)

I am considering systems with 700GB of storage (with a minimum of 130GB required to run the instance). Below are some options based on different regions and configurations:

## **Asia Region:**

- **vCPUs**: 16 (AMD Ryzen 7 5700x)
	 
- **RAM**: 64GB
	 
- **Max CUDA**: 12.8
	 
- **TFLOPS**: 19.8
	 
- **DLPerf**: 16.4 (Performance Matrix)
	 
- **Cost**: $0.320/hr (reserved)
	 
- **Download Speed (Shared)**: 485 Mbps

## **Global Region (Minimum cost):**

- **vCPUs**: 12 (Intel i5 11th Gen)
	 
- **RAM**: 64GB
	 
- **Max CUDA**: 13
	 
- **TFLOPS**: 19.8
	 
- **DLPerf**: 16.3 (Performance Matrix)
	 
- **Cost**: $0.263/hr (reserved)
	 
- **Download Speed (Shared)**: 1558 Mbps

## **Global Region (2nd Min cost):**

- **vCPUs**: 14 (Intel Xeon E5-2680 v4)
	 
- **RAM**: 129GB
	 
- **Max CUDA**: 12.9
	 
- **TFLOPS**: 19.8
	 
- **DLPerf**: 16.3 (Performance Matrix)
	 
- **Cost**: $0.301/hr (reserved)
	 
- **Download Speed (Shared)**: 884 Mbps

## **Global Region (3rd Min cost):**

- **vCPUs**: 44 (Intel Xeon E5-2699 v4)
	 
- **RAM**: 258GB
	 
- **Max CUDA**: 12.9
	 
- **TFLOPS**: 19.8
	 
- **DLPerf**: 16.5 (Performance Matrix)
	 
- **Cost**: $0.314/hr (reserved)
	 
- **Download Speed (Shared)**: 7097 Mbps

Please note that the disk size may need modification to meet our exact requirements, and I will update the pricing accordingly once I confirm the final disk size. The cost may vary with the disk configuration.

Access to all these systems will be primarily through SSH.

# 29-08 : Raghunatha

Please provide complete details of requirement and the company names. We will get the access.

# 01-09 : ME

Sairam Sir.  

The cloud systems that we are considering  are from **Vast.ai**.

Currently, the benchmarks we need to run in order to establish a proper correlation with the hardware are:  
**Rodinia-3.1, GPU_Microbenchmark, SDK-4.2-scaled, Parboil, Polybench, Cutlass, and DeepBench (NVIDIA Tensor Core).**

These benchmarks are recommended by the authors of _Accel-Sim_, who also mention a requirement of **70 GB RAM**.

Before proceeding with cloud experiments, I would like to run them locally on our server to estimate the time required to generate the necessary data.

As the CIE week has already begun, I kindly request one week to complete these runs and report back with the time estimates and the exact hardware requirements.

Regarding the meeting, I will meet you today, Sir, and will reach out to you over the phone for coordination.

# 12-09 : ME

**Sairam Sir.**  
The servers have been down since Tuesday, due to which we have not been able to make progress on any of the implementation work.
