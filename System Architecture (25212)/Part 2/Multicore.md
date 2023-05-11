## Moore's Law
Moore conjectured (in 1965) that every two years, the number of transistors in a processor would double. This translates to a doubling of processor performance roughly every 18 months. This conjecture was driven mainly by the shrinkage of circuits; as technology developed transistors got shorter, meaning more could fit in the same space, connections within the chip got shorter, and capacitance got lower.
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
Along with being single-core, it also uses some older concepts such as the north/south bridges. A modern multicore system may look something like this:
![](Pasted%20image%2020230418120411.png)
Note that the caches are layered, with L1 being core-specific and split between data and instructions, L2 being core specific but holding both types of data and L3 being a shared cache. The memory controller handles memory accesses.

## Data Coherency and Consistency
When discussing memory access, it is important to make the distinction between the two ways that multiple cores may be used:
- **Independent processes** - e.g. single threaded applications running as separate operating system processes
	- Normally these do not share any data, as they as separate virtual memory spaces
	- When they do, communication is explicit through I/O (storage, network) or other carefully managed means (messaging)
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
## Coherence Protocols
#### Snooping protocols
Each individual cache listens continuously for any activity regarding its own cached addresses (for example, if another core has written to that address.) This allows the cache to keep *itself* coherent. This requires a **broadcast** communications structure; that is, communication must be visible to all, even if it is not intended for them. This typically takes the form of a bus.

**Write Update:**
1. A core wants to write to memory.
2. The core grabs the bus for a cycle and broadcasts both the address and the new data, while also writing to its own cache.
3. Every other cache snoops on this, and updates their copy (if they have one).
4. Main memory **does not** need to be written to.

**Write Invalidate:**
1. A core wants to write to memory.
2. The core grabs the bus for a cycle and broadcasts a "Write Invalidate" message containing the address it is writing to.
3. Every other cache snoops on this, and invalidates their copy of that data (if they have one).
4. The core then **writes to main memory**.
5. Any other cores that try to read at that location will now get a cache miss and read from the up-to-date main memory.

Update might seem preferable at first, as it doesn't need to write to main memory. However, there are significant downsides compared to write invalidate:
- The messages are long
	- Address + data, invalidate only needs the address
- What if you repeatedly write the same memory location without reading? (common)
	- Update sends a useless update for each write, invalidate only needs to send one message
- What if you write to subsequent words in the same cache line without reading? (very common)
	- Update sends many useless updates, invalidate sends just one message

In real applications, Invalidate has been shown as the preferable method. Bus bandwidth is a precious commodity on multicore chips, and Update simply uses too much.

A further optimisation for either of these methods is to store if a cache entry is **shared**; that is, if any other cores also hold it. A core can entirely avoid sending certain messages using this information, which saves on precious bandwidth. Another problem that can be solved is that the previously given description of an invalidate protocol requires writing back to memory, which is slow. We would prefer to use **copy back** (recap: only copy from cache to memory when a dirty line is displaced).
A concrete implementation of Invalidate with both of these optimisations is the **MESI protocol**.
## MESI Protocol
Also known as the **Illinois protocol**, this is a practical multicore invalidate protocol which attempts to minimise bus usage.
It is designed to allow the usage of **copy back** instead of **write back**, meaning that the next level of memory is not updated until a dirty cache line is displaced.
This is achieved with a simple extension of the usual cache tags; In a regular copy back cache, there are 2 bits for invalid and dirty. Invalid is set if the current cache line is unusable, and dirty is set if the line must be committed to higher memory when it is evicted.
MESI swaps these out for **4 distinct states** (still using just 2 bits):
- Modified - This cache line has been modified, and is yet to be written to main memory. This also means that this is the only copy. (essentially the same as "dirty").
- Exclusive - This cache line has **NOT** been modified, but it is the only cached copy.
- Shared - This cache line contains **unmodified** data that is also present in other caches.
- Invalid - This cache line contains invalid data and should not be used (same as with the regular cache.)

Cache lines transition from one state to another as a result of **memory access events**. This may be from the cache's own core performing some memory operation, or from the cache snooping on bus activity and reacting to another cache's actions. The state of a cache line is only affected if the address of some event matches its own address.
Here are some of the events described informally (state transition diagram comes later):
#### Local Read Hit
The core tried to read from the cache and the requested data was present.
For this to occur, the cache line must have been in M, E or S (i.e. not invalid.)
The cache can simply return the cached value to the core. There is no need to send a message or to change state.
#### Local Read Miss
The core tried to read from the cache but the requested data was not present.
This means that either the line was not present in the cache at all or it was with state I.
The cache must now send out a MEM_READ message along the bus. There are multiple possible outcomes of this:
- No other cache has a copy
	- The core waits for a response from main memory.
	- When the value arrives, it is cached and marked as E (this core is the only owner.)
- Another cache has an Exclusive (E) copy
	- The cache with the copy snoops on the bus and sees the message, then sends the value down the bus.
	- The memory access is cancelled, as it is not needed.
	- The requesting core caches the sent value, and sets the state to S.
	- The sending cache sets the state of the line to S (as the value is now shared).
- Multiple caches have a Shared (S) copy.
	- One of the caches with a copy snoops on the bus and sees the message, then sends the value down the bus.
	- The memory access is cancelled, as it is not needed.
	- The requesting core caches the sent value.
	- The receiver sets the tag to S, and the other caches remain with their existing state of S.
- Another cache has a Modified (M) copy.
	- The cache with the copy snoops on the bus and sees the message, then sends the value down the bus.
	- The memory access is cancelled, as it is not needed.
	- The requesting core caches the sent value and gives it a state of S.
	- **The sending core copies back the modified value to memory.**
	- The sending core then sets the state of the cache line to S.

#### Local Write Hit
The core tried to write to the cache, and the value it wanted to change was already cached.
This means the written cache line was either M, E or S.
There are multiple possible outcomes:
- The cache line is M - It is exclusive and has already been changed.
	- Update the value in cache.
	- No state change or message required.
- The cache line is E - It is exclusive, but not changed.
	- Update the value in cache.
	- Change the state from E to M. No message is required.
- The cache line is S - It is shared among other caches.
	- The core broadcasts an **invalidate** across the bus.
	- Snooping cores will invalidate their copies. (i.e. set the state to I)
	- Update the value in the local cache.
	- Change the state from S to M.

#### Local Write Miss
The core tried to write to the cache, but the value it wanted to change was not there.
The core sends a **Read with intent to modify** (RWITM). There are multiple possible outcomes:
- No other cache has a copy.
	- Memory sends the data.
	- The local cache saves the written value and sets the cache line's state to M.
- Other caches have **non-dirty** (E or S) copies. 
	- The snooping cores see the RWITM and invalidate their copies.
	- The local cache saves the written value and sets the cache line's state to M.
- Another cache has a **dirty** (M) copy.
	- The snooping core with the dirty copy sees the RWITM and blocks the request.
	- The snooping core **copies back** to memory, and sets the state of it's copy to I.
	- The original core, having gotten no response, re-issues its RWITM. This is now identical to the first case (no other cache has a copy).

#### In the form of state transition diagrams
The sender has this behaviour:
![](Pasted%20image%2020230511124432.png)
And the snooping caches have this behaviour:
![](Pasted%20image%2020230511124446.png)
## Directory Protocols
Snooping protocols scale badly to large numbers of cores, due to the need to share a bus. Directory protocols 