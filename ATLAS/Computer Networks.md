# Chapter 4 
## Router Parts
1. Input port
2. Switching Fabric
3. Output port
4. Router Processor
## Switching
1. By memory
2. By bus
3. By crossbar interconnects

## Output
### Queuing
#### Buffering Size:
B = RTT * C/√N - Modern Theory (N = TCP Flows in avg.)
B = RTT ⭈ C - Previous Theory

#### Packet Scheduler
- FCFS
- Weighted Fair Queuing(WFQ)

>  Can also happen at Input port.
>  - Head of Line blocking
### Dropping
- AQM(Active Queue Management Algorithms Collection)
Drop-tail : Simply drop
Early Random Detection ( widely studied  based on weighted  average of the length of the output queue