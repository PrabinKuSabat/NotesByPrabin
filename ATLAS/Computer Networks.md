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
	- Broadcast
2. DHCP server offers:
	- Contains
		- Transaction ID
		- Proposed address
		- Subnet Mask
		- Lease TIme
	- Broadcast
3. DHCP Request:
	- Broadcast
4. DHCP ACK:
	- Confirmation

### NAT

**Network Address Translation**  
 Creates a realm with private addresses.
- Stores IP addresses with port numbers.

**Connection reversal used by P2P applications for NAT traversal.**

UPnP : Universal Plug and Play
- Allows user to discover and configure a nearby NAT

## ICMP

**Internet Control Message Protocol**  
Contains:
- Type
- code field
- Header and 8b of the datagram that caused error.
  
IETF : Internet Engineering Task Force

### IPV6 Format

![[image-33.png]]

#### Changes

1. Expanded Addressing Mode(128b)
	- Also introduced any cast.
2. 40-byte fixed header
	- New encoding of options allows for faster encryption
3. Flow and traffic
	- labeling of packets belonging to particular flows for which the sender requests special handling, such as a nondefault quality of service or real-time service.

#### Fields

1. Version(6b)
2. Traffic(8b)
3. Flow label(20b)
4. Payload Length(16b)
5. Next header (same as upper layer protocol)(8b)
6. Hop limit (same as TTL)
7. Source add.
8. Dest add.
9. Data

## Routing Algorithms

### Three ways to Classify

1. First : Manner of Calculating least-cost path.
	- Centralized
	- Decentralized
2. Second : Speed of updating routes
	- Static
	- Dynamic
3. Third Load sensitive
	- Load-sensitive
	- Load-insensitive

### Link-State Routing Algorithms
1. Dijkstra’s Algorithms
	- After k steps k shortest paths are know to k nodes.
	- Take the shortest route at every step.
	- O(n<sup>2</sup>)
>  Congestion sensitive routing results in oscillations.

### Distance Vector Routing Algorithms

