From [TheNextPlatform](https://www.nextplatform.com/2017/05/22/hood-googles-tpu2-machine-learning-clusters/)

1. TPU is a coprocessor that connects to a processor motherboard via two PCI-Express 3.0 x8 edge connectors for an aggregate of 16 GB/s of bi-directional bandwidth.
2. TPU2 performs at a peak of 45 tera floating point operations per second (teraflops), presumably for FP16 operations.
	- TPU consumes up to 40 watts, well within PCI-Express power delivery specs, and delivers 92 tera-operations (TOPS) for 8-bit integer operations or 23 TOPS for 16-bit integer operations.
3. Google has designed its TPU2 for use in a four-rack _stamp_, which Google calls a _pod_.
4. Each of the four TPU2 board quadrants shares board power distribution.
5. We believe that four TPU2 board quadrants also share network connections via a simple network switch.
6. It looks like each board quadrant is a separate subsystem and the four subsystems are not otherwise connected to each other on the board.
7. The IBM BlueLink specification defines eight 200 Gb/sec signal lanes in each direction (16 lanes total) for a minimal 25 GB/s configuration (called a “sub-link”).
8. That gives two choices – either 10 Gb/sec Ethernet or 100 Gb/sec Intel Omni-Path Architecture (OPA). Two 100 Gbps OPA links can be combined for an aggregate bi-directional bandwidth of 25 GB/s, which matches BlueLink speeds, so we think it is Omni-Path.
9. Google’s TPU2 rack stamp has bilateral symmetry. (See Figure 2)

> [!note]  
> A stamp is a standard rack configuration (from half-rack to many racks) for a related set of workloads. Stamps help make purchase, installation, and deployment easier and less costly for large datacenter owners.

> [!important] Distance between TPU and Linking CPU  
> None of these copper cables, BlueLink or OPA, can be run much over 3 meters or 10 feet at maximum signal rate. That binds the interconnect topology linking CPU and TPU2 boards together by a 3m physical spanning distance.

![[Pasted image 20241209213050.png |Top View of TPU2 board]]  
**Fig 1** Top View of TPU2 board

```image-layout-a
![[Pasted image 20241209213532.png]]
![[Pasted image 20241209213544.png]]
```

**Fig 2** Point number 9.
