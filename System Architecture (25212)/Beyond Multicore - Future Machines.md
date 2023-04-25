As discussed before, a typical multi-core machine is structured like this:
![](Pasted%20image%2020230425115911.png)
If you want more power, then you might want a **multi-CPU** computer (with distinct processor/memory pairs) will be structured something like this:
![](Pasted%20image%2020230425120025.png)
But past a certain point, the performance required for certain tasks cannot be achieved with just one machine. This is where multi-computer systems arise:
![](Pasted%20image%2020230425120226.png)
#### Recap: Amdahl's Law
Amdahl's Law estimates the max performance of a parallel system based on the amount of parallelism that an application contains. It was intended to discourage parallel architectures.
$$\text{Speed up} = \frac{S+P}{S+(P/N)}$$
Where
$S$ = Fraction of the code which is strictly serial
$P$ = Fraction of the code which can be parallel ($S + P = 1$)
$N$ = Number of processors
See how the benefit of multiple processors wanes when even a small portion of a program is serial:
![](Pasted%20image%2020230425120620.png)
However, this law is somewhat misleading. $S$ (the proportion of code that is strictly serial) is usually a constant amount, while $P$ depends on the size of the input data. Therefore, if you want more parallelism, **increase your dataset**.
## Clusters, Datacentres and Supercomputers
All three of these terms are overloaded and commonly misused. Essentially, if a computer has lots of CPUs and lots of motherboards, someone will call it one of the three. The actual distinction *is* becoming increasingly blurred. The applications for all three generally include:
- **High Performance Computing**
	- Running one large task as quickly as possible
	- Generally done by Supercomputers, and (to some extent) clusters
- **High Throughput Computing**
	- Running as many tasks per unit of time as possible
	- Generally done by Clusters/Farms (when the task is to compute) and Datacentres (when the task is to store/retrieve data)
- **Big Data Analytics**
	- Analysing and extracting patterns from large, complex data sets
	- Would only be done by a Datacentre