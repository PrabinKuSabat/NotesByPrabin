# Introduction

TPU v4 is the <mark style="background: #FFF3A3A6;">5<sup>th</sup> Google domain specific architecture</mark> (DSA) and its <mark style="background: #FFF3A3A6;">3<sup>rd</sup> supercomputer</mark> for such ML models.  
This paper describes three major features of TPUv4 that respond to these challenges:
1. **OCSes(Optical Circuit Switches) :** which address the scale and reliability
2. **SparseCorse or SC (Since v2):** provides support for embeddings in DLRMs (Deep Learning Recommendation Models).
3. **All-to-All communication patterns :** a requirement for embeddings.
- ~ Also shows how Google uses ML to co-optimize DNN models, OCS topology and SparseCorse.

# Reconfigurable Optical Switch

**Motivation :** Requirement of optical interconnects for TPUv3 racks, due to their spread.
- And optical links are >10x expensive than electrical links.
- Also, this may decrease the Bisection Bandwith of a 2D torus.  
**Solution :** OCS, acts like a plugboard to skip failed units.
- Also, 3D torus increases bisection bandwith .

## Optical Circuit Switching

- The Google Palomar OCS is based on 3D [[MEMS(Micro-Electro-Mechanical Systems)]] mirrors that can switch in miliseconds.
- Usage circulators have halved the no. of ports and cables.
	- As it allows to <mark style="background: #08BFFF99;">send light both ways.</mark>
- In a 3D torus, 3D cubes have the best bisection bandwith.
	- For 512 chips a 4<sup>3</sup> building block was chosen.
	- One rack contains 64 TPUv4 chips and their 16 CPU hosts.
	- ~ Accounts : 4 Chips per CPU.

## Construction of TPUv4

Links per face : 16  
Links per block : 96  
OCSes per block : 48  
Palomar OCS size : 136\*136 (128ports + 8 spares for link testing and repair)  
Total Palomar OCS used : 48
- Connect 64 4<sup>3</sup> blocks. 48 pairs of cables from each block.
- Each OCS is 128 ports => Total ports = 128 x 48.
- Total Cables from 64 blocks = 64 x 48 x 2 = 128 x 48  

![[Pasted image 20250122214445.png#+grid |fig 2]]  
![[Pasted image 20250122214510.png#+grid|Fig: 1]]
