- l1, l2 and dram latency mesaurement.  
  find some benchmark
- l2 latency btwn core clusters(4cores)
- 64B cache line in l2.  
  any byte in the line will have same latency
- 32byte  
  what's the bus width from the l2 and l1 to the core?
- Does the l2 directly send data to core?  
  critical data flow!!
- Bus width amount is been send but only the required amount of portion is required and remaining is thrown away.
- Reduce bus width to 8byte instead of 32byte to save power if edge computer.
- shutdown pages in dram to save energy.
- 32kb 8 ways, line: 64 bytes
- ![[image-104.png]]
- quad word (highest),word(32 bit), double word
- all cache lines same for all level of caches 64bytes.
- mainframe cache line is 256byte
- no software has locality more than 64byte.
- 32k size, line size :256byte 8 ways, 16 congurent
- ibm 128byte  
  intel 64byte  
  intel softwares is run slowly in ibm because of line size the cache looks small even with the same size.
- is it possible  
  hash function in data and we create 8 byte nodes, reversing the graph in random.  
  graph size is large than 32kbyte  
  node size is  
- if we have 32 byte cache line we have 512 graph nodes in the cache whereas 256 byte gives us only 128 graph nodes which makes it a bit slow.
- Graph reversing bi
