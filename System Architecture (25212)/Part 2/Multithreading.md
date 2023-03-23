## How else have we improved processor performance?
- Minimising memory access impact - **caches**
- Increasing clock frequency - **pipelining**
	- Maximising pipeline utilisation - **branch prediction** and **forwarding**
- Running instructions in parallel - **superscalar**
- Maximising instruction issues - **dynamic scheduling** and **out-of-order execution**

But the amount of parallelism we can exploit is limited by the programs we are running. Some areas of these programs may exhibit great potential for parallelism, with many independent instructions, but other parts may be essentially sequential with lots of data dependencies.
In the latter case, where can we find additional independent instructions? Look for more work in **different processes**.
Hardware Multithreading allows several threads to share **a single processor**; this is distinct from software multithreading, which either depends on multiple processors or on scheduling/interleaving many threads on a single processor, where no two threads are truly running at the same time. Hardware multithreading is **truly running multiple threads at the same time on the same processor**.
**Software multithreading**:
![](Pasted%20image%2020230323141633.png)
The slowness of software multithreading is apparent: storing the Process Control Block (PCB) upon every context switch is a costly action which needs to be done extremely frequently to maintain the illusion of parallelism. PCBs store lots of information about a process/thread:
![](Pasted%20image%2020230323141823.png)

## Hardware Multithreading
We want to allow multiple threads to share a single processor. This will utilise our hardware resources more efficiently, and using hardware we can make thread switching very fast (even instantaneous). To do this, we need to replicate the hardware that stores the state of each thread; this includes:
- Registers (including the program counter)
- the TLB

Virtual memory can be used to share memory along threads, though it does introduce some synchronisation challenges.