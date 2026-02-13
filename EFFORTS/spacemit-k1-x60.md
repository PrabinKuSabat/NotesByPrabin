# SpaceMIT K1/X60 Chip - Comprehensive Technical Documentation

## Executive Summary

This document compiles all available technical information about the SpaceMIT K1 SoC and its X60 RISC-V processor cores, with emphasis on manufacturing details, cache architecture, and cache coherence protocols.

---

## 1. Processor Architecture Overview

### 1.1 SpaceMIT K1 SoC Specifications

**General Information:**
- **Chip Name:** SpaceMIT Key Stone™ K1
- **Architecture:** RISC-V 64GCVB
- **Standard Compliance:** RVA22 Profile
- **Manufacturing Process:** 22nm
- **Core Configuration:** 8-core (Dual-cluster, asymmetric design)
- **Total Computing Power:** 50 KDMIPS CPU + 2.0 TOPS AI
- **Operating Temperature:** -40°C to 85°C (Industrial grade)
- **TDP:** 3-5W
- **Package Options:**
  - FCCSP: 17×17mm, 0.65mm pin pitch
  - FCBGA: 19×19mm, 0.65mm pin pitch

### 1.2 SpaceMIT X60™ Core Architecture

**Core Specifications:**
- **Type:** High-performance, low-power RISC-V processor core
- **ISA:** RISC-V 64GCVB (64-bit, General, Compressed, Vector, Bit manipulation)
- **Profile:** RVA22 standard compliant
- **Pipeline:** 8-stage dual-issue in-order pipeline
- **Performance:** SPECint®2006 > 4.0/GHz per core
- **Performance vs ARM:** 30% faster than ARM Cortex-A55 (per core)
- **Power Efficiency:** 20% lower power consumption than comparable ARM chips under same workload

---

## 2. Cache Architecture (Critical Details)

### 2.1 Dual-Cluster Configuration

#### Cluster 0 (AI-Enhanced)

- **Cores:** 4× X60™ RISC-V cores
- **AI Capability:** 2.0 TOPS AI computing power
- **L1 Instruction Cache (L1-I):** 32KB per core
- **L1 Data Cache (L1-D):** 32KB per core
- **L2 Cache:** 512KB (shared across 4 cores in cluster)
- **TCM (Tightly-Coupled Memory):** 512KB dedicated for AI operations
- **Vector Width:** 256-bit (VLEN=256)
- **AI Instructions:** 16 custom AI instructions including matrix multiplication and sliding window operations

#### Cluster 1 (Standard)

- **Cores:** 4× X60™ RISC-V cores
- **AI Capability:** None (standard RISC-V cores)
- **L1 Instruction Cache (L1-I):** 32KB per core
- **L1 Data Cache (L1-D):** 32KB per core
- **L2 Cache:** 512KB (shared across 4 cores in cluster)
- **TCM:** None
- **Vector Width:** 256-bit (VLEN=256)

### 2.2 Cache Hierarchy Summary

**Total Cache Per Cluster:**
- L1 cache total: 256KB (128KB instruction + 128KB data across 4 cores)
- L2 cache: 512KB shared
- Total per cluster: 768KB + 512KB TCM (Cluster 0 only)

**System-Wide Cache:**
- Total L1 cache: 512KB (8 cores × 64KB)
- Total L2 cache: 1024KB (1MB) across both clusters
- Additional TCM: 512KB (Cluster 0 only)

---

## 3. Cache Coherence Protocols (Critical Manufacturing Detail)

### 3.1 L1 Cache Coherence: MESI Protocol

The L1 caches (both instruction and data) implement the **MESI consistency protocol**.

**MESI States:**
- **M (Modified):** Cache line is present only in this cache, has been modified, and differs from main memory (dirty)
- **E (Exclusive):** Cache line is present only in this cache and matches main memory (clean)
- **S (Shared):** Cache line may be present in multiple caches and matches main memory
- **I (Invalid):** Cache line is invalid (not present or stale)

**MESI Characteristics:**
- 4-state protocol
- Write-invalidate protocol
- Maintains strong consistency across all L1 caches
- Reduces unnecessary bus traffic by distinguishing between exclusive and shared clean states

### 3.2 L2 Cache Coherence: MOESI Protocol

The L2 caches (512KB per cluster) implement the **MOESI consistency protocol**.

**MOESI States:**
- **M (Modified):** Same as MESI - dirty, exclusive
- **O (Owned):** Cache line is dirty but may be shared with other caches; this cache has responsibility to write back
- **E (Exclusive):** Same as MESI - clean, exclusive
- **S (Shared):** Same as MESI - clean, possibly shared
- **I (Invalid):** Same as MESI - invalid

**MOESI Advantages over MESI:**
- The "Owned" state allows dirty cache lines to be shared between caches
- Reduces memory write-back traffic
- Owner cache can supply data to other caches without writing back to main memory first
- Better performance in multi-core scenarios with shared modified data

### 3.3 Cache Coherence Implementation Details

**Coherence Domain:**
- All 8 cores across both clusters maintain cache coherency
- L1 and L2 caches are fully coherent with each other
- Coherence maintained across cluster boundaries

**Known Issues and Solutions:**
- **L1 Cache Coherency Issue:** Early silicon had L1 cache coherency problems
- **Solution:** Set specific bits in the "ML2SETUP" CSR (Custom Control and Status Register at address 0x7F0)
- **Fix Location:** The four lower bits of ML2SETUP CSR must be configured properly
- **Status:** Issue resolved via CSR configuration (not documented in official datasheets initially)

---

## 4. Memory Subsystem

### 4.1 DRAM Interface

**LPDDR4/LPDDR4X Support:**
- **Interface Width:** 32-bit dual chip select
- **Speed:** Up to 2666 MT/s (LPDDR4X) or 2400 MT/s (LPDDR4)
- **Capacity:** Up to 16GB total
- **Bandwidth:** Up to 10.6 GB/s
- **Voltage:** Adaptive operating voltage from 0.6V to 1.05V (DVFS)

**LPDDR3 Support:**
- **Interface Width:** 32-bit dual chip select
- **Speed:** Up to 1866 MT/s
- **Capacity:** Up to 4GB total

### 4.2 Storage Interfaces

- **SPI Flash:** Supported
- **eMMC 5.1:** 8-bit interface, up to HS400 (200MHz)
- **SDIO 3.0:** SD card support, 4-bit interface, up to SDR104
- **NVMe:** Over PCIe interface

---

## 5. Vector Processing Unit

### 5.1 RISC-V Vector Extension

**Vector Specifications:**
- **Standard:** RVV 1.0 (RISC-V Vector Extension version 1.0)
- **VLEN:** 256 bits
- **Execution Width:** 128-bit × 2 (dual execution units)
- **Performance:** 2× parallel processing capability compared to ARM NEON
- **Vector Performance:** >150% of ARM NEON throughput

**Vector Use Cases:**
- DSP operations
- Machine learning inference
- SLAM (Simultaneous Localization and Mapping)
- Signal processing
- Image processing

---

## 6. AI Acceleration (Cluster 0 Only)

### 6.1 AI Computing Architecture

**AI Integration Method:**
- **Type:** Homogeneous fusion computing (CPU-AI integrated)
- **Technology:** SpaceMIT Daoyi™ AI Innovation
- **Performance:** 2.0 TOPS (tera operations per second)
- **LLM Performance:** >10 tokens/second @ 1B parameter models
- **Supported Frameworks:** TensorFlow Lite, TensorFlow, ONNX Runtime

**Custom AI Instructions:**
- 16 AI-specific RISC-V custom instructions
- Matrix multiplication operations
- Sliding window calculations
- Optimized for neural network inference

**Dedicated Resources:**
- 512KB TCM (Tightly-Coupled Memory) for AI data
- Provides low-latency, deterministic access for AI workloads

---

## 7. Microarchitecture Details

### 7.1 Pipeline Design

**Pipeline Characteristics:**
- **Stages:** 8-stage pipeline
- **Issue Width:** Dual-issue (2 instructions per cycle)
- **Order:** In-order execution
- **Design Philosophy:** Balance between performance and power efficiency

### 7.2 Interrupt Architecture

**Local Interrupt Controller (CLINT):**
- Handles software interrupts
- Handles timer interrupts
- Memory-mapped module
- Per-core implementation

**Platform-Level Interrupt Controller (PLIC):**
- **Capacity:** 256 external interrupt sources
- **Arbitration:** Priority-based
- **Targets:** Both Machine mode and Supervisor mode
- **Formats:** Level and edge-triggered interrupts
- **Sharing:** Shared across both clusters

### 7.3 Virtual Memory

**SV39 Support:**
- 39-bit virtual addressing
- 3-level page table
- Standard RISC-V supervisor-mode virtual memory

**Physical Memory Protection (PMP):**
- 32 PMP entries
- Adheres to RISC-V security framework
- Supports both PMP and ePMP (enhanced PMP) extensions

---

## 8. Security Features

### 8.1 Cryptographic Acceleration

**Supported Algorithms:**
- **AES:** Advanced Encryption Standard
- **RSA:** Rivest-Shamir-Adleman public-key cryptography
- **ECC:** Elliptic Curve Cryptography
- **SHA2:** Secure Hash Algorithm 2
- **HMAC:** Hash-based Message Authentication Code
- **SM2, SM3, SM4:** Chinese national cryptography standards

**Additional Security:**
- **TRNG:** True Random Number Generator
- **Secure Boot:** Hardware-verified boot chain
- **Secure eFuse:** 4K bits of one-time programmable storage
- **Signature Verification:** For firmware and software integrity
- **Lifecycle Management:** Product security through all lifecycle stages

---

## 9. I/O and Peripheral Subsystems

### 9.1 High-Speed Interfaces

**PCIe:**
- **Configuration:** 5× PCIe 2.1 lanes (x2 + x2 + x1)
- **Speed:** 5 Gbps per lane
- **Use Cases:** NVMe SSDs, wireless modules, accelerators

**USB:**
- 1× USB 3.0 (combo with PCIe x1)
- 1× USB 2.0 Host
- 1× USB 2.0 OTG

**Ethernet:**
- 2× GMAC (Gigabit Media Access Controller)
- RGMII interface
- 10/100/1000 Mbps

### 9.2 Low-Speed Interfaces

- **SPI:** 4 controllers (1× QSPI, 1× SPI LCD, 2× standard SPI)
- **I2C:** 10 controllers (8× general + 1× HDMI + 1× power management)
- **UART:** 12 controllers (includes AP/BT/debug)
- **CAN-FD:** 2 controllers
- **PWM:** 30 channels
- **GPIO:** 128 pins total
  - 104× 1.8V I/O
  - 24× 1.8V/3.3V I/O

### 9.3 Display Subsystem

**Display Controllers:**
- 2× DPU (Display Processing Unit)
- Dual independent displays supported

**Display Outputs:**
- **MIPI-DSI:** 4-lane interface
- **HDMI 1.4:** Up to 1920×1080 @ 60fps
- **Maximum Resolution:** 1920×1440 @ 60fps per display

**Display Features:**
- Up to 4 full-size layer composition
- Maximum 8 layers (via up-down reuse)
- Command list mechanism (hardware register configuration)
- Concurrent write-back support
- AFBC (ARM Frame Buffer Compression) support
- MMU for virtual addressing with minimal page faults during rotation
- Color key and solid color support
- Dither support (error diffusion and pattern-based)
- DDR frequency dynamic switching with DFC buffer

### 9.4 Camera Subsystem

**ISP (Image Signal Processor):**
- **Configuration:** Dual-ISP
- **Maximum Resolution:** 16MP per camera @ 30fps
- **MIPI-CSI:** 8 lanes total
  - Mode 1: 4-lane + 4-lane (dual camera)
  - Mode 2: 4-lane + 2-lane (dual camera)
  - Mode 3: 4-lane + 2-lane + 2-lane (triple camera)

**Camera Features:**
- RAW sensor input, YUV output to DRAM
- Hardware JPEG encoder (up to 23MP)
- YUV/EXIF/JFIF format support
- AF/AE/AWB (Auto Focus/Exposure/White Balance)
- Face detection
- Digital zoom and panorama
- PDAF (Phase Detection Auto Focus)
- PIP (Picture-in-Picture)
- HDR support
- Continuous video autofocus
- Hardware 3D denoise

---

## 10. Graphics Processing Unit

### 10.1 GPU Specifications

**GPU Core:**
- **Model:** Imagination BXE-2-32
- **Clock Speed:** 819 MHz
- **Cache:** 32KB SLC (System Level Cache)
- **Architecture:** Optimized for RISC-V integration

**Graphics APIs:**
- OpenCL 3.0
- OpenGL ES 1.1/3.2
- Vulkan 1.3

**Performance:**
- Full Vulkan API support
- Hardware 3D graphics acceleration
- Optimized for embedded and edge computing workloads

---

## 11. Video Processing Unit (VPU)

### 11.1 Video Decode

**Supported Codecs:**
- H.265/HEVC
- H.264/AVC
- VP9
- VP8
- MPEG-4
- MPEG-2

**Decode Performance:**
- 4K @ 60fps

### 11.2 Video Encode

**Supported Codecs:**
- H.265/HEVC
- H.264/AVC
- VP9
- VP8

**Encode Performance:**
- 4K @ 30fps
- 1080p @ 60fps

**Simultaneous Processing:**
- Option 1: Encoding 1080p@60fps + Decoding 1080p@60fps
- Option 2: Encoding H264/H265 1080p@30fps + Decoding 4K@30fps

---

## 12. Real-Time CPU Subsystem (RCPU)

### 12.1 RCPU Specifications

**RCPU Core:**
- **Architecture:** RISC-V
- **Clock Speed:** 300 MHz
- **Purpose:** Real-time I/O and system control

**Supported Interfaces:**
- R-I2C: 1 controller
- R-I2S: 2 controllers
- R-UART: 2 controllers
- R-CAN-FD: 1 controller
- R-IR-RX: 1 controller (infrared receiver)
- R-PWM: 10 channels
- R-SPI: 1 controller

**Purpose:**
- Heterogeneous dual-system support
- Real-time I/O processing
- System management functions
- Low-latency peripheral control

---

## 13. Audio Subsystem

### 13.1 Audio Codec Specifications

**ADC (Analog-to-Digital Converter):**
- **SNR:** 90dB @ 20Hz-20kHz
- **Input:** 3× microphone inputs
- **Stereo Input Path:** For noise cancellation

**DAC (Digital-to-Analog Converter):**
- **SNR:** 95dB @ 20Hz-20kHz
- **Sampling Rates:** 8kHz to 48kHz

**Audio Output:**
- **Class-G Amplifier:** 95dB SNR, 31mW @ 32Ω, THD -90dB
- **Class-AB Amplifier:** 95dB SNR, 75mW @ 32Ω, THD -90dB
- **Line-out:** For external Class-D amplifier
- **Stereo Headphone Output**

**Audio Features:**
- Microphone bias for headphone detection
- Hook-key detection
- Quad vocoders for AMR (Adaptive Multi-Rate)
- Noise suppression
- Echo cancellation

---

## 14. Power Management

### 14.1 Dynamic Voltage and Frequency Scaling (DVFS)

**Voltage Range:**
- **Minimum:** 0.6V
- **Maximum:** 1.05V
- **Adaptive:** Based on workload and performance requirements

**Power Islands:**
- Different granularities of power domains
- Per-core power gating
- Per-cluster power gating
- Various power state levels

**Power States:**
- Multiple power states for aggressive power savings
- Fine-grained control for each CPU core and cluster

### 14.2 Power Consumption

**TDP (Thermal Design Power):**
- **Range:** 3-5W
- **Typical:** Depends on workload and clock frequency
- **Efficiency:** 80% power consumption compared to equivalent ARM chips

---

## 15. Debug and Development

### 15.1 Debug Architecture

**Debug Standard:**
- **RISC-V Debug Specification:** Version 0.13.2

**Debug Interfaces:**
- 2× JTAG ports (CPU subsystem and MCU subsystem)
- Multiple UART ports for debug output

**Debug Features:**
- **Program Buffer Mode:** Standard JTAG access through CPU
- **System Bus Access (SBA):** Direct on-chip resource access bypassing CPU
- **CPU Register Snapshot:** Captured on watchdog reboot
- **I/O Register Snapshot:** Captured on watchdog reboot

**Watchdog:**
- Separate watchdog timers for CPU and MCU subsystems

### 15.2 Performance Monitoring

**Performance Monitoring Unit (PMU):**
- RISC-V Performance PMU support
- Hardware performance counters
- Event sampling and counting
- Critical for performance analysis and optimization

---

## 16. Operating System Support

### 16.1 Supported Operating Systems

**Official Support:**
- **Bianbu OS:** SpaceMIT's official Linux distribution
- **Mainstream Linux:** Ubuntu, Debian, Fedora, and other distributions
- **RTOS:** Real-time operating systems

**Bootloader:**
- U-Boot (SpaceMIT fork with proprietary blobs)
- Oreboot (open-source alternative, in development)

### 16.2 Boot System

**Boot Sources:**
- SPI-NAND flash
- SPI-NOR flash
- eMMC
- SD card

**Boot ROM:**
- 128KB boot ROM

---

## 17. Manufacturing and Silicon Details

### 17.1 Process Technology

**Manufacturing Process:** 22nm

**Process Characteristics:**
- Mature 22nm node
- Balance between cost, power efficiency, and performance
- Not cutting-edge but proven and cost-effective for embedded/edge applications

### 17.2 Known Silicon Issues

**L1 Cache Coherency Bug:**
- **Issue:** Early silicon had L1 cache coherency problems between cores
- **Symptom:** Data inconsistency in multi-threaded workloads
- **Root Cause:** Hardware bug in L1 cache coherence protocol implementation
- **Workaround:** Configure ML2SETUP CSR (address 0x7F0)
- **Fix:** Set the four lower bits of ML2SETUP CSR correctly
- **Documentation Status:** Not documented in official datasheets initially; discovered by community

**Impact:**
- Required software workaround in bootloader/kernel
- Affects all K1/M1 silicon (no hardware revision known)
- Must be configured at boot time for stable multi-core operation

### 17.3 Chip Variants

**K1 Series:**
- Consumer/embedded focus
- Full feature set

**M1 Series:**
- Similar to K1
- Potentially different clock speeds or feature configurations
- Same X60 core architecture

---

## 18. Devices Using SpaceMIT K1

### 18.1 Available Boards and Systems

**Single-Board Computers:**
1. **Banana Pi BPI-F3**
	- Mainline Linux support
	- Extensive documentation
	- Active community
	- Price: ~$54-80 USD (depending on RAM configuration)

2. **Milk-V Jupiter**
	- Mini-ITX form factor
	- 8GB RAM
	- ATX-style system

3. **Bit-Brick K1**
	- Available in 4GB and 8GB configurations
	- Development kit includes power supply, SD card, UART debugger
	- Price: $54.79 (4GB) / $68.52 (8GB) basic kit

**Laptops:**  
4. **DeepComputing DC-ROMA Laptop II**  
	- RISC-V laptop  
	- Mobile form factor

**Tablets:**  
5. **DeepComputing DC-ROMA PAD II**  
	- RISC-V tablet  
	- PostmarketOS support (testing stage)

**Development Systems:**  
6. **SpaceMIT MUSE Book**  
	- Official development platform

**Others:**  
7. **Sipeed Lichee Pi 3A**  
8. **Orange Pi RV2** (uses Ky X1 variant, not K1)

---

## 19. Comparison with Other Architectures

### 19.1 Performance vs ARM Cortex-A55

**Single-Core Performance:**
- **SpaceMIT X60:** 30% faster than ARM Cortex-A55
- **SPECint®2006:** >4.0/GHz per core

**Power Efficiency:**
- 20% lower power consumption than ARM A55 under equivalent workload

**Vector Performance:**
- RVV 1.0 with VLEN=256 provides ~2× parallelism vs ARM NEON
- Vector performance >150% of ARM NEON

### 19.2 Microarchitecture Comparison

**SpaceMIT X60:**
- 8-stage dual-issue in-order
- 256-bit vector units
- MESI (L1) / MOESI (L2) coherence

**ARM Cortex-A55:**
- 8-stage in-order
- Single-issue or dual-issue (implementation-dependent)
- NEON 128-bit SIMD
- MESI or ACE coherence

**Advantages of X60:**
- Wider vector units (256-bit vs 128-bit)
- Custom AI instructions
- MOESI L2 cache for better shared dirty data handling
- Lower power consumption

---

## 20. Technical Resources and Documentation

### 20.1 Official Documentation

**Primary Sources:**
1. **K1 Brief (English):**
	- URL: <https://cdn-resource.spacemit.com/file/chip/K1/K1_brief_en.pdf>
	- Contents: High-level overview, features, specifications

2. **SpaceMIT K1 Datasheet (via Banana Pi):**
	- URL: <https://docs.banana-pi.org/en/BPI-F3/SpacemiT_K1_datasheet>
	- Contents: Detailed specifications, cache coherence protocols, subsystem details

3. **SpaceMIT K1 Brief (via Banana Pi):**
	- URL: <https://docs.banana-pi.org/en/BPI-F3/SpacemiT_K1>
	- Contents: Quick reference, key specifications

4. **Bit-Brick K1 Documentation:**
	- URL: <https://docs.bit-brick.com/docs/k1/hardware/processor-brief>
	- Contents: Processor overview, hardware specifications

### 20.2 Community Resources

**Developer Resources:**
1. **Works with RISC-V Guide:**
	- Hardware compatibility information
	- Community-contributed documentation

2. **PostmarketOS Wiki:**
	- URL: <https://wiki.postmarketos.org/wiki/SpacemiT_Key_Stone_K1>
	- Linux mainline support status
	- Device compatibility matrix

3. **Reddit Communities:**
	- r/RISCV: Active discussions on K1/X60 architecture
	- r/spacemit_riscv: Dedicated SpaceMIT community

4. **EEVblog Forum:**
	- Hardware hacking discussions
	- ML2SETUP CSR cache coherency workaround documented here

### 20.3 Academic and Technical Papers

**Performance Analysis:**
- Various papers on RISC-V performance optimization mention SpaceMIT X60
- PMU profiling methodologies for RISC-V platforms
- Vector extension benchmarking (RVV 1.0 VLEN=256)

---

## 21. Software Ecosystem

### 21.1 Compiler Support

**GCC:**
- RISC-V GCC with RVA22 profile support
- Vector extension support (RVV 1.0)
- SpaceMIT-specific tuning available

**LLVM/Clang:**
- RISC-V backend support
- Used for vector intrinsics development
- Version 20+ recommended for optimal RVV support

### 21.2 AI/ML Frameworks

**Supported Frameworks:**
- TensorFlow Lite
- TensorFlow
- ONNX Runtime
- Mainstream AI inference engines

**Integration Method:**
- CPU-AI fusion computing model
- Zero-cost AI deployment (follows CPU programming paradigms)
- TCM for low-latency AI data access

---

## 22. Application Domains

### 22.1 Target Markets

**Primary Applications:**
1. **AI Edge Computing**
	- Local LLM inference (0.5B-1B parameter models)
	- Computer vision
	- Natural language processing

2. **AI-Integrated Hardware**
	- Smart cameras
	- IoT gateways
	- Edge servers

3. **Intelligent Robotics**
	- SLAM (with vector acceleration)
	- Sensor fusion
	- Real-time control

4. **Industrial Main Control**
	- Factory automation
	- Process control
	- HMI (Human-Machine Interface)

5. **Cloud Computing**
	- Edge cloud nodes
	- Distributed computing
	- Micro-datacenters

6. **Open-Source Harmony OS**
	- HarmonyOS Next support
	- Embedded Linux
	- RTOS applications

### 22.2 Unique Selling Points

**For Edge AI:**
- Native 2.0 TOPS AI without separate NPU
- Unified CPU-AI programming model
- TCM for deterministic AI workload latency

**For Embedded Linux:**
- Mainline kernel support (in progress)
- Rich peripheral set
- Industrial temperature range

**For RISC-V Development:**
- RVA22 compliance (modern RISC-V profile)
- RVV 1.0 with VLEN=256 (wide vectors)
- Available development boards under $100

---

## 23. Future Roadmap and Related Products

### 23.1 SpaceMIT Product Line

**X-Core Series:**
1. **X60™ Core:** Current generation (featured in K1/M1)
2. **X100™ Core:** Next-generation core (details limited)

**Key Stone Series:**
1. **K1:** Current 8-core SoC (documented here)
2. **K3:** Higher-performance variant (8 large cores, different configuration)

**Power Stone Series:**
1. **P1:** Alternative SoC variant (details limited)

### 23.2 RISC-V IP Portfolio

**SpaceMIT RISC-V IP:**
1. **IOMMU:** I/O Memory Management Unit
2. **AIA:** Advanced Interrupt Architecture
3. **IOPMP:** I/O Physical Memory Protection

---

## 24. Critical Technical Insights for Developers

### 24.1 Cache Coherence Programming Model

**Implications for Software:**
- L1 MESI coherence ensures strong consistency
- No need for explicit cache synchronization in most cases
- L2 MOESI allows efficient shared dirty data between cores
- TCM bypasses cache coherency (must be managed explicitly)

**Best Practices:**
1. Use TCM for AI workloads that don't require coherency
2. Be aware of ML2SETUP CSR configuration requirement
3. L2 cache is shared per-cluster (4 cores), not globally
4. Inter-cluster communication may have higher latency

### 24.2 Performance Optimization

**Memory Bandwidth:**
- Peak theoretical: 10.6 GB/s (LPDDR4X @ 2666 MT/s)
- Shared between 8 cores, GPU, ISP, VPU
- Bandwidth-sensitive workloads should be carefully scheduled

**Vector Optimization:**
- VLEN=256 is double ARM NEON width
- 128-bit × 2 execution units provide high throughput
- Intrinsics via LLVM/Clang 20+ recommended
- NTT (Number Theoretic Transform) benefits significantly from RVV

**Cache Efficiency:**
- 32KB L1-D per core (moderate size)
- Keep hot data within 32KB for best performance
- L2 is shared per cluster (avoid false sharing)
- 512KB L2 per 4 cores = 128KB per core average

### 24.3 Known Limitations

**Hardware Limitations:**
1. **L1 Cache Bug:** Requires ML2SETUP CSR configuration
2. **22nm Process:** Modest clock speeds (~1.6-2.0 GHz)
3. **In-Order Pipeline:** May stall on long-latency operations
4. **No L3 Cache:** All cores share 1MB L2 total

**Software Limitations:**
1. **Mainline Support:** Still in early stages (as of 2024-2025)
2. **GPU Drivers:** Vulkan 1.3 support varies (IMG BXE-2-32 driver maturity)
3. **Documentation:** Some CSRs undocumented (e.g., ML2SETUP)

---

## 25. Conclusion

The SpaceMIT K1 with X60 cores represents a significant entry in the RISC-V application processor market, particularly for edge AI and embedded Linux applications. Key differentiators include:

**Strengths:**
- Modern RVA22 RISC-V architecture
- Wide 256-bit vector units (RVV 1.0)
- Integrated 2.0 TOPS AI capability
- MOESI cache coherence for efficient multi-core operation
- Rich peripheral set (PCIe, USB 3.0, dual GMAC, etc.)
- Industrial temperature range (-40°C to 85°C)
- Affordable development platforms (<$100)

**Considerations:**
- 22nm process limits clock speeds vs cutting-edge chips
- L1 cache coherency requires workaround via undocumented CSR
- Software ecosystem still maturing
- GPU driver support varies

**Best Use Cases:**
- Edge AI inference (LLMs, computer vision)
- Embedded Linux development
- RISC-V research and education
- Industrial control systems
- Robotics with SLAM and sensor fusion

---

## Document Metadata

- **Compilation Date:** February 13, 2026
- **Primary Sources:** SpaceMIT official documentation, Banana Pi datasheets, community resources
- **Focus Areas:** Cache coherence (MESI/MOESI), manufacturing details (22nm), architecture specifications
- **Target Audience:** Systems programmers, kernel developers, hardware researchers

---

## Appendix A: Glossary

- **AIA:** Advanced Interrupt Architecture
- **CLINT:** Core-Local Interrupt Controller
- **CSR:** Control and Status Register
- **DVFS:** Dynamic Voltage and Frequency Scaling
- **ISP:** Image Signal Processor
- **MESI:** Modified-Exclusive-Shared-Invalid (cache coherence protocol)
- **MOESI:** Modified-Owned-Exclusive-Shared-Invalid (cache coherence protocol)
- **NTT:** Number Theoretic Transform
- **PLIC:** Platform-Level Interrupt Controller
- **PMP:** Physical Memory Protection
- **RVA22:** RISC-V Application Profile 2022
- **RVV:** RISC-V Vector Extension
- **SBA:** System Bus Access
- **SLAM:** Simultaneous Localization and Mapping
- **TCM:** Tightly-Coupled Memory
- **TDP:** Thermal Design Power
- **TOPS:** Tera Operations Per Second
- **VLEN:** Vector Length (in bits)
- **VPU:** Video Processing Unit

---

## Appendix B: Cache Coherence Deep Dive

### MESI Protocol State Transitions (L1 Cache)

```
Initial State: INVALID

Read Miss:
  INVALID → EXCLUSIVE (if no other cache has it)
  INVALID → SHARED (if other caches have it)

Write Hit:
  EXCLUSIVE → MODIFIED
  SHARED → MODIFIED (invalidates others)
  MODIFIED → MODIFIED

Write Miss:
  INVALID → MODIFIED (invalidates/fetches)

Read Hit:
  No state change (remains EXCLUSIVE, SHARED, or MODIFIED)

External Read:
  MODIFIED → SHARED (write back to memory)
  EXCLUSIVE → SHARED

External Write:
  Any state → INVALID
```

### MOESI Protocol State Transitions (L2 Cache)

```
Initial State: INVALID

Read Miss:
  INVALID → EXCLUSIVE (if no other cache has it, clean)
  INVALID → SHARED (if other caches have clean copy)
  INVALID → OWNED (if another cache has dirty copy and shares it)

Write Hit:
  EXCLUSIVE → MODIFIED
  OWNED → MODIFIED
  SHARED → MODIFIED (invalidates others)
  MODIFIED → MODIFIED

Write Miss:
  INVALID → MODIFIED (invalidates/fetches)

Read Hit:
  No state change

External Read:
  MODIFIED → OWNED (keep dirty copy, share data, defer writeback)
  EXCLUSIVE → SHARED

External Write:
  Any state → INVALID
```

**Key MOESI Advantage:**  
The OWNED state allows a cache to hold a dirty copy while sharing it with other caches, avoiding immediate write-back to DRAM. This significantly reduces memory traffic in multi-core shared-write scenarios.

---

## Appendix C: Relevant CSR Addresses

**Known Custom CSRs:**
- **0x7F0 (ML2SETUP):** L2 cache and coherency configuration
  - Bits [3:0]: L1 cache coherency enable (must be set for proper multi-core operation)
  - Other bits: Undocumented in official materials

**Standard RISC-V CSRs:**
- Performance counters, timers, and interrupt controllers follow standard RISC-V specifications
- 32 PMP entries accessible via standard PMP CSRs

---

**END OF DOCUMENT**
