Aim : To provide a substrate for acrch. research rather than to exactly model any commercial GPU

---
# Top Level Org.
- SIMT cores connected via on-chip connection network to G-Ram. 
![[image-96.png]]

# Clock Domains
1. SIMT Core Cluster
2. Inter-connect network
3. l2 cache
4. dram clock domain

>  The existence of synchronizers betwn clock domains in assumed.
>  GPGPU useds a clock crossing buffers
>  - filled at source domain's clock rate
>  - drained at destn domain's clock rate

# SIMT C Cluster
`response FIFO` holds the packet ejected from ICNet before direction to SIMT C Inst Cache or memory pipeline(LDST unit).
ICNet request can be made by each SIMT core by it's own `injection port`. 
But `injection port buffer is shared`.
![[image-98.png]]

# SIMT C
