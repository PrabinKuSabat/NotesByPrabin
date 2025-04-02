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

#### Buffering Size

B = RTT * C/√N - Modern Theory (N = TCP Flows in avg.)  
B = RTT ⭈ C - Previous Theory

#### Packet Scheduler

- FCFS
- Weighted Fair Queuing(WFQ)

> Can also happen at Input port.
>  - Head of Line blocking

### Dropping

- AQM(Active Queue Management Algorithms Collection)  
Drop-tail : Simply drop  
Early Random Detection ( widely studied based on weighted average of the length of the output queue.

## IPv4

![[image-20.png]]

### Total 14 items in the datagram format

> Each line represents 32 bits.  

Line 1 -
1. IP Version Number(4)
2. Header Size(4b)
3. Type of service(8b)
4. Datagram Length(16b)  
Line 2 -
5. Identifier(16b)
6. Flags(2b)
7. Fragment Offset(13b)  
Line 3 -
8. TTL(8b)
9. Upper-Layer Protocol(8b)
10. Header Checksum(16b)  
Single Lines (each 32b) -
11. Source IP
12. Desti. IP
13. Options
14. Data

## IPV4 Addressing

CIDR(pr. CIDER) - Classless Interdomain Routing  
Classful Addressing - 8, 16, 24 different sizes for addressing  
IP broadcast: 255.255.255.255

> IP addresses are managed under the authority of the Internet Corporation for Assigned Names and Numbers (ICANN)  

### DHCP
**Dynamic Host Configuration Protocol (DHCP)**
- Plug and play protocol  
DHCP relay agent(a router):
- Works as a DHCP in it's absence
#### 4 Steps 
1. DHCP server discovery:
	- Port 67
2. DHCP server offers
	- Transaction ID
	- 