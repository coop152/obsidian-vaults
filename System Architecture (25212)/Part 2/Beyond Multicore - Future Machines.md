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
All three of these terms are overloaded and commonly misused. Essentially, if a computer has lots of CPUs and lots of motherboards, someone will call it one of the three (though the actual distinction *is* becoming increasingly blurred.) The applications for all three generally include:
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
	- Most likely, **dedicated storage** (likely network attached, with one or multiple storage racks)
- There will be some **frontend** node(s) that regular users will interact with
	- These contain user functions (e.g. compile programs to be run, read out results, start a procedure, etc.) and take the load of user interaction off of the compute nodes

These networks of devices can come together to make supercomputers/clusters that are essentially incomprehensible in scale. The most powerful supercomputer (as of time of writing) is the Frontier in the Oak Ridge National Laboratory in the US, with 8,730,112 total cores across all computers. It is theoretically capable (Rpeak) of 1,685.65 PetaFLOP/s, while drawing 21.1 megawatts of power, though real world performance (Rmax) is closer to "just" 1,102.00 PetaFLOP/s.
![](Pasted%20image%2020230425123224.png)
Trends in supercomputers show:
- Clusters have dominated the space for a while
- Intel dominates when it comes to CPUs (although AMD's CPUs are currently in the most powerful supercomputer as of 2023)
- Most of the machines are actually used in industry, though a large percentage is still for research and academics
- NVIDIA dominates when it comes to co-processors

#### Examples
**Xeon Phi**:
- A many-core processor
	- 57 4-way multithreaded x86 general-purpose cores (228 hardware threads)
	- 512-bit SIMD operations with AVX-512
- Higher performance than a standard processor (obviously)
	- Peak performance **1.2 TeraFLOP/s**
	- Power dissipation **300W**
	- Efficiency of **4GFLOP/s/Watt**
- Moving data from/to memory is **very expensive**
	- Large chunks of data need to be moved to start executing
	- The most recent PCIe versions alleviate this (PCIe 5.0 has max bandwidth of up to 128GB/s)
- Programmed using **well known parallel programming models**, nothing specialised
	- e.g. MPI, OpenMP, autovectorisation...
![](Pasted%20image%2020230425124950.png)
- Uses a **bidirectional ring** interconnect, which connects all cores, PCIe devices, memory controllers...
	- Due to this, communications can quickly become a bottleneck
	- Newer architectures from Intel implement a 2D mesh instead
- Achieves cache coherency through a **distributed directory (TD)**
	- This is necessary to support the large number of cores
- In multi-computer environments, the local machine's RAM will need to be updated with the host machine's RAM through PCIe, which can be a big bottleneck

## Recap: Single Instruction Multiple Data (SIMD)
![](Pasted%20image%2020230425125156.png)
## General Purpose Computing with GPUs (GPGPU)
![](Pasted%20image%2020230425125704.png)
- GPU architectures are based on long arrays of cores executing the same instructions over large series of data. It's like SIMD, but even more aggressive (which would not come as a surprise considering Intel's first vector extension, MMX, was specifically made for tasks that GPUs commonly execute nowadays such as video playback and 3D rendering)
- GPUs offer the best raw performance and efficiency. For example, the NVIDIA Volta V100 offers:
	- Peak performance of more than 7 TFLOP/s
	- Relatively low power usage of 300W
	- Extremely high power efficiency of more than 25 GFLOP/s/Watt

However, there are limitations which stop GPUs from being used for every application.
- Moving data from/to memory is **very** expensive
	- Like with the Xeon Phi, large chunks of data need to be moved to start execution
	- Again, newer versions of PCIe alleviate this
	- More modern solutions will use custom high-performance interconnects (100s of GB/s)
- The memory accessed by all the cores within an array needs to be consecutive (i.e. you need to have a flat buffer of data)
	- If your data has any indirection at all (e.g. pointers) this becomes difficult
- All of the cores execute the same instruction
	- e.g. You are forced to execute control operations (`if`/`else`) on every core
- Programming a GPU differs significantly from the standard sequential models used in CPUs
	- Cuda, OpenCL, and others
- Due to these limitations, certain workloads simply cannot be ported to a GPU (at least, not without destroying the efficiency)