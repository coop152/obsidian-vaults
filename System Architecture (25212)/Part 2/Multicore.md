## Moore's Law
Moore conjectured (in 1965) that every two years, the number of transistors in a processor would double. This translates to a doubling of processor performance roughly every 18 months. This conjecture was driven mainly by the shrinkage of circuits; as technology developed a transistors got shorter, meaning more can fit in the same space, connections within the chip got shorter, and capacitance got lower.
This "law" has been true for about 50 years, but it is coming to an end - the number of transistors is still doubling, but it gets harder to accomplish every year. Performance cannot keep pace by just shrinking the circuits.
![](Pasted%20image%2020230418112129.png)
## Why not just make a single core faster
- Caches can minimise the impact of memory accesses
- Pipelines and superscalar architectures can increase the amount of parallelism supported by the processor, maximising use of the hardware
- Out-of-order execution and Multithreading can increase the amount of independent instructions per cycle
- And other mechanisms (e.g. branch prediction, forwarding, register renaming) can minimise the impact of the hazards which are introduced by these methods

But we have seen that these methods all have limited scalability:
- Scaling a chip physically beyond a certain point is not practical
- Hardware, power and cost increase more than linearly...
- ... but performance increases less than linearly, and is limited by the applications that are run on the processor
- **Power Wall** - As power density increases, cooling becomes a serious problem
- **ILP Wall** - Some applications have limited parallelism
- **Memory wall** - Memory does not get faster at the same rate as processors
- **Unreliability** - Smaller transistors have less predictable characteristics
- **Design complexity** - At some point, architectural innovation hits upon the complexity of the designs, causing problems

#### The power wall
The power dissipation (that is, as heat) depends on clock rate, capacitive load and voltage:
$$\text{Power} = \text{CapacitiveLoad} \times \text{Voltage}^2 \times \text{Frequency}$$
Higher clock frequency means more power dissipation. Lower voltage reduces the dynamic power consumption, but it increases the static power leakage from the transistors, which is becoming the main contributor to power consumption due to the rapid shrinking of transistors.
Without extreme cooling, microprocessors cannot move past this wall. Extreme cooling isn't practical for many devices such as laptops or phones, and even for larger devices such as desktops or servers it is unreasonable to expect every device to be equipped with expensive cooling.
#### The ILP Wall
When executing one thread, the implicit parallelism is limited in many applications. Many of the previous mechanisms we have studied are reliant on this, which poses a problem for performance. One way to express this is **Amdahl's Law**, which estimates a parallel system's **maximum performance** based on the available parallelism of an application:
$$\text{Speedup} = \frac{S + P}{S + (P/N)}$$
Where
$S$ is the fraction of code which is serial
$P$ is the fraction of code which can be parallel (that is, $S + P = 1$)
$N$ is the number of processors
This formula was originally given as a way to discourage the development of parallel architectures, as it demonstrates the poor scaling of them when even a small percentage of instructions are serial:
![](Pasted%20image%2020230418114124.png)
#### The memory wall
From the mid 80's to the mid 2000's, CPU speed was increasing over 50% per year. However, memory was lagging behind at only 10% per year.
![](Pasted%20image%2020230418114224.png)
This posed a problem in that processor utilisation was being increasingly capped by the need to fetch data from memory. In memory intensive applications, CPU utilisation may be as low as 10%.
![](Pasted%20image%2020230418114337.png)
## A solution
Put more "cores" (CPUs) on a single chip, and use these cores in parallel to achieve higher performance. This is simpler to design than adding more complexity to a single processor, and it introduces a tempting idea: if you need more computing power, just add more cores. But is it that easy?
## Putting the cores together
How do we assemble these CPUs into one cohesive unit? For memory, we could:
- Have independent processor/memory pairs (**Distributed memory**)
	- Each core has its own independent memory
	- Communication/synchronisation must be coded explicitly
	- An example is Intel's 48-core single-chip cloud computer (2009)
- Have **shared memory**
	- This is generally accepted as the right answer
	- All processors have the same view of memory
	- Communication/synchronisation is implicit - makes programming easier
	- An example is any modern traditional x86 chip (Intel Xeon, Core, Pentium, AMD Ryzen, Epyc)
	- But when considering more than a few cores, shared memory becomes difficult

How to divy up work? We could:
- Have a small number of cores, used for **separate tasks**
	- e.g. the OS runs on one core, the virus scanner runs on another, the browser runs on yet another
	- Essentially, allow multicore to replace multitasking
- If we want increased performance on a single application, we need to move to **parallel programming**
	- Shared memory - OpenMP
	- Message passing - MPI
	- Independent threads - pthreads
	- And many others
- General purpose parallel programming is known to be hard. It is generally agreed upon that new approaches are needed, but there is no agreed upon method as of yet.
#### Problems
- We do not know how to engineer extensible memory systems
- We do not know how to write general purpose parallel programs
	- They are difficult to debug and the methods required do not fit with existing serial processor designs
- The power, ILP and memory walls still apply within a single core. The problem is not eliminated, there is just another layer of problems added on top

## Example Architectures
This is the traditional structure of a single core computer:
![](Pasted%20image%2020230418120248.png)
Along with being single-core, it also uses some older concepts such as the north/south bridges. A multicore system may look something like this:
![](Pasted%20image%2020230418120411.png)
Note that the caches are layered, with L1 being core-specific and split between data and instructions, L2 being core specific but holding both types of data and L3 being a shared cache. The memory controller handles memory accesses.

## Data Coherency and Consistency
When discussing memory access, it is important to make the distinction between the two ways that multicores may be used:
- **Independent processes** - e.g. single threaded applications running as separate operating system processes
	- Normally these do not share any data, as they as separate virtual memory spaces
	- When they do, communication is explicit through I/O (storage, network) or other carefully managed means (messages)
- **Threads** - parallel parts of the same application which **share the same memory**. This is where problems arise:
	- Memory coherency: ensure that changes are seen everywhere, on all cores
	- Memory consistency: ensure that memory operations are ordered correctly

## Memory Coherence
- What is the coherence problem?
	- Consider a core writing to a location in its L1 cache.
	- In situations where other cores can access the same shared memory, those cores may hold copies of that same data - these will be immediately out of date.
- The core can either
	- Write through to main memory
	- Copy back only when the cache line is rejected
- In either case, the fact that any core can have its own copy means that it is not sufficient just to update main memory
	- We need to force every core with a copy to get the updated value
	- This can become very expensive
- And memory coherence is not the only problem.

## Memory Consistency
- **Consistency** is the model presented to the programmer of when changes are seen.
- Multiple copies of some data are consistent if:
	- A read operation returns the same value from all of the copies, and
	- A write operation updates all of the copies before any other operation takes place.
- Sometimes we need to read and then update a variable in one **atomic** operation.
	- No other thread may read or write that variable while this happens

#### Example
Imagine we want to count the number of threads that are alive within a given process. Every thread runs this code:
```C
count = count + 1;
```
What behaviour did we expect?
![](Pasted%20image%2020230418121742.png)
This is perfectly possible of occurring, but there is another possibility which is problematic:
![](Pasted%20image%2020230418121825.png)
This is what might happen when you lack **memory consistency**.
## Sequential Consistency
- Memory operations **appear** to execute one at a time.
- Every thread sees write operations in the **same order**.
- Operations within a single thread **appear** to execute in the order described by the program.

Note the word "appear"; they do not actually need to be done in this order, if there is a way to do them out-of-order but maintain consistency.
- Sequential Consistency provides the behaviour that most software developers expect (that is, the data they manipulate is manipulated in the order that they laid out)
- But, it is not the most stringent memory model
- Computer architectures and some programming languages (like Java) use relaxed consistency models; the compiler has to insert special instructions to maintain the semantics of the program:
	- **Fence** - Complete all memory accesses before this instruction before doing any of the ones after it (the ones before may be out of order among themselves)
	- **Barrier** - All threads need to reach this barrier before any may continue execution.
	- **Lock** - Only one thread can proceed in the following atomic section of code.

![](Pasted%20image%2020230418122633.png)
![](Pasted%20image%2020230418122734.png)
![](Pasted%20image%2020230418122803.png)
Note that locks require special support from the architecture. Consider this:
![](Pasted%20image%2020230418122925.png)
There needs to be a way to check that some flag is set and change it in a single instruction, without allowing another thread to also check the flag before it is changed. This is called a **CAS** (Compare And Swap) instruction:
![](Pasted%20image%2020230418123053.png)
Some other instances of hardware support for locks include:
![](Pasted%20image%2020230418123131.png)
The ISA might also use **transactional memory**:
![](Pasted%20image%2020230418123227.png)
