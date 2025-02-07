---
tags:
  - CANotes
  - CA
aliases:
  - RDNA 3
---

[**RDNA 3** is a [GPU](https://en.wikipedia.org/wiki/Graphics_processing_unit "Graphics processing unit") microarchitecture designed by [AMD](https://en.wikipedia.org/wiki/AMD "AMD"), released with the [Radeon](https://en.wikipedia.org/wiki/Radeon "Radeon") [RX 7000 series](https://en.wikipedia.org/wiki/Radeon_RX_7000_series "Radeon RX 7000 series") on December 13, 2022. Alongside powering the RX 7000 series, RDNA 3 is also featured in the SoCs designed by AMD for the [Asus ROG Ally](https://en.wikipedia.org/wiki/Asus_ROG_Ally "Asus ROG Ally") and [Lenovo Legion Go](https://en.wikipedia.org/wiki/Lenovo_Legion "Lenovo Legion") consoles.  
AMD announced to investors their intention to achieve a performance-per-watt uplift of over 50% with RDNA 3 and that the upcoming architecture would be built using [[chiplet packaging]] on a 5 nm process.  
Full details for the RDNA 3 architecture were unveiled on November 3, 2022 at an event in [Las Vegas](https://en.wikipedia.org/wiki/Las_Vegas "Las Vegas").

# Chiplet Packaging

The decision to move to a chiplet-based GPU microarchitecture was led by AMD Senior Vice President [Sam Naffziger](https://en.wikipedia.org/wiki/Sam_Naffziger "Sam Naffziger") who had also lead the chiplet initiative with Ryzen and [Epyc](https://www.amd.com/en/products/processors/server/epyc.html).  
The use of smaller dies rather than one large monolithic die is beneficial for maximizing wafer yields as more dies can be fitted onto a single wafer. RDNA 3 uses two types of [[chiplets]]: the Graphics Compute Die (GCD) and Memory Cache Dies (MCDs).](<**RDNA 3**  is a  [GPU](https://en.wikipedia.org/wiki/Graphics_processing_unit)  microarchitecture designed by  [AMD](https://en.wikipedia.org/wiki/AMD) , released with the  [Radeon](https://en.wikipedia.org/wiki/Radeon)   [RX 7000 series](https://en.wikipedia.org/wiki/Radeon_RX_7000_series)  on December 13, 2022. Alongside powering the RX 7000 series, RDNA 3 is also featured in the SoCs designed by AMD for the  [Asus ROG Ally](https://en.wikipedia.org/wiki/Asus_ROG_Ally)  and  [Lenovo Legion Go](https://en.wikipedia.org/wiki/Lenovo_Legion)  consoles.

# Background

On June 9, 2022, AMD held their Financial Analyst Day where they presented a client GPU roadmap which contained mention of RDNA 3 coming in 2022 and RDNA 4 coming in 2024.AMD announced to investors their intention to achieve a performance-per-watt uplift of over 50% with RDNA 3 and that the upcoming architecture would be built using [[chiplet packaging]] on a 5 nm process.

# Architecture

## Chiplet Packaging

For the first time ever in a consumer GPU, RDNA 3 utilizes modular  [chiplets](https://en.wikipedia.org/wiki/Multi-chip_module)  rather than a single large monolithic  [die](https://en.wikipedia.org/wiki/Die_(integrated_circuit)) . The decision to move to a chiplet-based GPU microarchitecture was led by AMD Senior Vice President  [Sam Naffziger](https://en.wikipedia.org/wiki/Sam_Naffziger)  who had also lead the chiplet initiative with Ryzen and Epyc.According to Naffziger, cache and SRAM do not scale as linearly as logic does on advanced nodes like N5 in terms of density and power consumption so they can instead be fabricated on the cheaper, more mature N6 node.  
RDNA 3 uses two types of [[chiplets]]: the Graphics Compute Die (GCD) and Memory Cache Dies (MCDs). An organic package could not host the number of wires that would be needed to connect multiple dies in a GPU.  
RDNA 3's dies are instead connected using  [TSMC](https://en.wikipedia.org/wiki/TSMC) 's Integrated Fan-Out Re-Distribution Layer (InFO-RDL) packaging technique which provides a silicon bridge for high bandwidth and high density die-to-die communication.InFO allows dies to be connected without the use of a more costly silicon  [interposer](https://en.wikipedia.org/wiki/Interposer) . The chiplet interconnects in RDNA achieve cumulative bandwidth of 5.3 TB/s.

## Memory Cache Dies (MCDs)

With a respective 2.05 billion transistors, each Memory Cache Die (MCD) contains 16 MB of L3 cache. Theoretically, additional L3 cache could be added to the MCDs via AMD's 3D V-Cache die stacking technology as the MCDs contain unused  [TSV](https://en.wikipedia.org/wiki/Through-silicon_via)  connection points.

## Graphics Compute Die (GCD)

### Compute Units

RDNA 3's Compute Units (CUs) for graphics processing are organized in dual CU Work Group Processors (WGPs). Rather than including a very large number of WGPs in RDNA 3 GPUs, AMD instead focused on improving per-WGP throughput.This is done with improved  [dual-issue](https://en.wikipedia.org/wiki/Superscalar_processor)  shader ALUs with the ability to execute two instructions per cycle. It can contain up to 96 graphics Compute Units that can provide up to 61 TFLOPS of compute.  
The efficiency of running inference tasks on  [FP16](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)  execution resources is improved with Wave MMA ( [matrix](https://en.wikipedia.org/wiki/Matrix_multiplication)   [multiply–accumulate](https://en.wikipedia.org/wiki/Multiply%E2%80%93accumulate_operation) ) instructions. This results in increased inference performance compared to RDNA 2. WMMA supports FP16, [[bfloat16|BF16]], INT8, and INT4 data types.

### Ray Tracing

RDNA 3 features second generation ray-tracing accelerators. Each Compute Unit contains one ray tracing accelerator. The overall number of ray tracing accelerators is increased due to the higher number of Compute Units, though the number of ray tracing accelerators per Compute Unit has not increased over RDNA 2.

### Clock Speeds

RDNA 3 was designed to support high clock speeds. On RDNA 3, clock speeds have been decoupled with the front end operating at a 2.5 GHz frequency while the [[shaders]] operate at 2.3 GHz. The [[shaders]] operating at a lower clock speed gives up to 25% power savings according to AMD and RDNA 3's shader clock speed is still 15% faster than RDNA 2.

### Cache and Memory Subsystem

The 16-way associative L1 cache shared across a shader array is doubled in RDNA 3 to 256 KB. The L2 cache increased from 4 MB on  [RDNA 2](https://en.wikipedia.org/wiki/RDNA_2)  to 6 MB on RDNA 3. The [[L3-Infinity-Cache|L3 Infinity Cache]] has been lowered in capacity from 128 MB to 96 MB and latency has increased as it is physically present on the MCDs rather than being closer to the WGPs within the GCD. The Infinity Cache capacity was decreased due to RDNA 3 having wider a memory interface up to 384-bit whereas RDNA 2 used memory interfaces up to 256-bit. RDNA 3 having a wider 384-bit memory means that its cache hitrate does not have to be as high to still avoid bandwidth bottlenecks as there is higher memory bandwidth.  
RDNA 3 GPUs use  [GDDR6](https://en.wikipedia.org/wiki/GDDR6_SDRAM)  memory rather than faster  [GDDR6X](https://en.wikipedia.org/wiki/GDDR6X_SDRAM)  due to the latter's increased power consumption.

### Media Engine

RDNA 3 is the first RDNA architecture to have a dedicated media engine. It is built into the GCD and is based on  [VCN 4.0](https://en.wikipedia.org/wiki/Video_Core_Next)  encoding and decoding core.

### Power Efficiency

AMD claims that RDNA 3 achieves a 54% increase in performance-per-watt which is in line with their previous claims of 50% performance-per-watt increases for both RDNA and RDNA 2.>)
