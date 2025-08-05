# Introduction

## Overview

GPUWattch was collaboratively developed by researchers at <mark style="background: #FFF3A3A6;">UTAustin, UWisconsin, and UBC.</mark>

New GPU power model that offers flexibility, adaptability, and stability.
- <mark style="background: #CACFD9A6;">Flexibility</mark> is achieved by using a bottom-up methodology and abstracting parameters from the microarchitectural components as model inputs.
- <mark style="background: #CACFD9A6;">Adaptability</mark> ensures that both program and microarchitectural level interactions are captured during execution, thereby enabling new power-management techniques specifically targeted at GPUs.
- <mark style="background: #CACFD9A6;">Stability</mark> is examined by validating the power model against measurements of two commercial GPUs comprehensively through the leakage power, average dynamic power, and dynamic power trace.  

The measured error is within <mark style="background: #08BFFF99;">9.7%</mark> and <mark style="background: #08BFFF99;">13.6%</mark> across our evaluated benchmark suite for the two target GPUs (<mark style="background: #ABF7F7A6;">GTX 480</mark> and <mark style="background: #ABF7F7A6;">Quadro FX 5600</mark> respectively) and the model accurately tracks the relative power consumption trend over time.

The power model modifies and extends the <mark style="background: #08BFFF99;">McPAT CPU power model</mark> simulator to model the power of contemporary GPGPUs and drive the modified McPAT version with a cycle-accurate simulator, GPGPU-Sim.

## McPAT

McPAT is an architectural modeling tool for chip multiprocessors (CMP) The main focus of McPAT is accurate power and area modeling, and a <mark style="background: #CACFD9A6;">target clock rate</mark> is used as a design constraint.  
McPAT <mark style="background: #CACFD9A6;">performs automatic extensive search</mark> to find optimal designs that satisfy the target clock frequency.
