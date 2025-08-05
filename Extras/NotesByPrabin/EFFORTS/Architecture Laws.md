# Amdahl's Law

Amdahl's Law is a fundamental principle in computer science that provides a formula to evaluate the theoretical maximum speedup of a computing task when some portion of it is improved through parallelism or other enhancements.

> [!info]  
Amdahl's Law is named after Gene Amdahl, who presented the concept in 1967 at the American Federation of Information Processing Societies conference.

The central idea of Amdahl's Law is that the speed of a task is limited by the portion of the task that remains non-parallelizable.​

![[Pasted image 20241209185953.png]]

S : Theoretical Speedup of the system  
p : portion of the task that can be parallelized  
s : speedup factor of the parallelizable portion.

# Gustafson's Law

Gustafson's Law is an important principle in parallel computing that addresses the limitations of Amdahl's Law by focusing on how workloads can scale with increased processing power.

> [!info]  
> Named after computer scientist John Gustafson

It suggests wiith more computing power, scientists and engineers can tackle larger and more complex problems, maximizing the potential of parallel processing.  

![[Pasted image 20241209201857.png]]

S : scaling speedup  
N : Number of Processors  
s : the fraction of serial workload  
P : the fraction of the parallel workload that is able to utilize the processors.

![[Pasted image 20241209202233.png]]
