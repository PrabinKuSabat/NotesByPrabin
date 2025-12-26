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
![[image-100.png]]


## Fetch and Decode
1. Instruction Buffer
	- Statistically partitioned so that all warps running on SIMT core have dedicated storage to place inst. 
	- 2 I-B entries / 1 warp
	- I-B entry :
		- 1 valid bit
		- 1 ready bit -> ready to be issued to execution pipeline.
			- simulator handles this during the schedule and issue stage using scoreboard logic and availability of hardware resources.
		- 1 decoded instruction
	- **Empty initially**
2. Warp
	- Can fetch if no instruction in I-B.
	- Eligible warps access the I-C in round robin order.
	- By default 2 consecutive inst. are fetched.
		- Valid bit in the I-B is activated for this warp only
			- <mark style="background: #FFF3A3A6;">Until all fetched inst. are issued to the execution pipeline.</mark>
3. Inst. Cache
	- read-only
	- non-blocking set-associative cache 
	- models both FIFO and LRU with no-miss or no-fill allocation policies.
	- In both cases of hit and miss the RR fetch scheduler moves to the next warp.
		- Hit : Decode
		- Miss : IC fetch request 
	- warp will need to access the IC for collecting missing data 
		- no access to IC while miss pending
4. *Kernel is considered done when all thread blocks are dispatched.*
	- Thread block is considered done once all warps have finished all operations.
	- Warp is considered done once all warps if all its threads have finished without outstanding store or pending writes to local registers.
5. Decode
	- The recent inst. are decoded and stored in respective entry in the I-B.

