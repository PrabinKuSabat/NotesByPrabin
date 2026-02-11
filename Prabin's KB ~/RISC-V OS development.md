# Hardware Available

Currently the hardware existing with me is a single board MPU by OrangePi, i.e. the [OrangePi RV2](http://www.orangepi.org/html/hardWare/computerAndMicrocontrollers/details/Orange-Pi-RV2.html). Which uses the KyX1 cpu. A 8-core RISC-V AI CPU with 2 TOPS of converged AI Power. It's based on RVA22s64 ISA with RVV1.0 RISC-V vector extension.

# The Problem

The rapid evolution of the RISC-V ecosystem has enabled organizations and research groups to design **highly customized processor architectures and SoCs.** However, there exists a significant gap between hardware design completion and systematic, *research-grade* performance validation. Current validation workflows often require extensive manual setup, kernel modification, toolchain configuration, benchmarking integration, and performance counter instrumentation. This process is *time-consuming, error-prone, and demands deep systems expertise.*

**There is a clear need for a lightweight, developer-centric operating system that enables rapid hardware performance validation with minimal setup overhead.** Such a system should:

- Provide immediate access to low-level architectural features (CSR registers, privilege modes, performance counters).
- Enable accurate latency and throughput measurement in supervisor mode.
- Minimize measurement distortion caused by unnecessary software layers.
- Integrate kernel-level instrumentation with user-space reporting.
- Offer built-in benchmarking, profiling, and metric collection capabilities at research-grade precision.
- Be portable across diverse RISC-V implementations with minimal reconfiguration.

The objective of SOS-V (Sai OS for RISC-V) is to design and develop a streamlined, industry-ready validation platform that follows an “install-and-validate” philosophy. The system should allow hardware developers to deploy the OS on a new RISC-V board and immediately begin structured performance characterization without extensive environment setup.

**In essence, the problem we aim to solve is:**

How can we reduce the friction between custom RISC-V hardware deployment and rigorous performance validation by building a lightweight, measurement-oriented operating system optimized for developer productivity?
 
