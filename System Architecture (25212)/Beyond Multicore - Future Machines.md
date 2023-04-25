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

#### Building a Cluster/Supercomputer/Datacentre
- Large numbers of self contained computers in a **small form factor** (e.g. thin blades in a server rack)
- Optimised for **cooling and power** efficiency, so many can be safely and efficiently put in server racks next to many others, in a single room
![](Pasted%20image%2020230425121818.png)
- Whole racks will house 1000s of cores
- High redundancy on an individual device and a collective level, to increase fault tolerance
	- Components in devices (e.g. hard drives, memory, processors) will be identical to allow fast and easy switching
	- Devices themselves will essentially be able to act as drop-in replacements for each other, minimising downtime in the event of a fault
- Normally also contain separate units for networking and power distribution (e.g. UPS's and a network switch for each rack)
![](Pasted%20image%2020230425121840.png)
- Many of these compute racks will be joined together. This needs:
	- A network to connect them
	- Power distribution
	- Cooling
	- Most likely, **dedicated storage** (likely network attached, one or multiple storage racks)
- There will be some **frontend** node(s) that regular users will interact with
	- These contain user functions (e.g. compile programs to be run, read out results, start a procedure, etc.) and take the load of user interaction off of the compute nodes

These networks of devices can come together to make supercomputers/clusters that are essentially incomprehensible in scale. The most powerful supercomputer (as of time of writing) is the Frontier in the Oak Ridge National Laboratory in the US, with 8,730,112 total cores across all computers. It is capable of 1,685.65 PetaFLOP/s, while drawing 21.1 megawatts of power.