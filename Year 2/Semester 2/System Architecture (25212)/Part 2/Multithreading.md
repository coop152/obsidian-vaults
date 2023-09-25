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
So, we start with a regular 5-stage pipelined processor:
![](Pasted%20image%2020230323142616.png)
We need to duplicate the components that hold thread-specific state, so we duplicate the PC, the register bank, and any other parts that involve address translation (e.g. the TLB):
![](Pasted%20image%2020230323142721.png)

These hardware threads are usually presented to the OS as **virtual processors**, equivalent to cores on a multi-core device. This requires explicit multiprocessor support from the OS.

This design needs some way of sharing or replicating some resources; registers usually need to be replicated between threads, and caches are usually shared between the threads, with each thread taking a fraction of the cache. This can cause some **cache thrashing** issues which can severely harm performance. Consider this scenario:
![](Pasted%20image%2020230323143216.png)
Given a direct mapped cache that uses this segment of the address as an index, each of these addresses would map to the same location, leading each access to evict the previous one and giving a potentially long string of cache misses.

So how do we exploit this new source of parallelism to improve performance?
## Fine-Grained Multithreading
We overlap the execution of several threads in time, fetching instructions from a different thread on each cycle. This typically uses a **Round Robin** (cyclic) approach among all of the ready hardware threads to choose which is ran next, though other policies are possible.
This method requires **instantaneous thread switching** due to the high frequency of the switching, which in turn requires complex hardware. This method looks like this:
![](Pasted%20image%2020230323143740.png)
The instructions of the two threads are simply interleaved.
Note that this kind of multithreading also helps to alleviate the impact of some data dependencies; consider this dependency on the blue thread:
![](Pasted%20image%2020230323143900.png)
No stall (or forward) is required.
It also allows a miss on the instruction cache to be overcome transparently, without wasting any processor time. Give all of the execution time to another thread while the other waits for an instruction from memory:
![](Pasted%20image%2020230323144045.png)
Likewise, if there is a data cache miss on a thread then you can also allow another thread to execute in the meantime:
![](Pasted%20image%2020230323144251.png)
If our multithreaded processor is also out-of-order, we might continue to issue instructions from both threads in the case of a data cache miss:
![](Pasted%20image%2020230323144427.png)
As you can see, this method increases the utilisation of pipeline resources leading to **better overall performance**. The perceived performance of one thread will be reduced, but the performance of all threads combined will be greater.
## Coarse-grained Multithreading
The hardware cost and complexity of Fine-grain multithreading is great, even if it does give good performance. How might hardware multithreading be implemented in a simpler and cheaper way?
Instead of interleaving individual instructions constantly, issue instructions from a single thread and operate like a simple pipeline. We will switch threads:
- If some expensive operation happens that holds up the current thread (e.g. instruction or data cache miss)
- If some amount of time has elapsed on the current thread (called a **Quantum of execution**)

Here is an example of how coarse-grained multithreading handles an instruction cache miss:
![](Pasted%20image%2020230323145455.png)
When Inst C has a miss during instruction fetch, it is discarded and the processor switches to the other thread.
![](Pasted%20image%2020230323145807.png)
The thread that encountered the miss will remain "paused" until the executing thread encounters another slow operation, then execution will return to the first thread which (hopefully) is now ready to be run with no cache miss.
Because an instruction cache miss happens at the beginning of the instruction's execution, it is easy to just discard the entire instruction and try again later. But what about a data cache miss?
![](Pasted%20image%2020230323145930.png)
The miss has occurred later in the pipeline, after some work has already been done. When this happens, we need to get rid of **every other instruction** that has started execution on the blue thread, and roll the blue thread's PC back to Inst a.
![](Pasted%20image%2020230323150124.png)
Clearly, this will incur a performance penalty as 4 instructions are discarded.
So, coarse grained multithreading is good to compensate for infrequent but expensive pipeline disruptions, but is not as effective as fine-grained multithreading. It requires **minimal pipeline changes**:
- Need to be able to abort the instructions that depend on the data cache miss, which requires some overhead
- Need to be able to resume the instruction stream and recover.

This kind of multithreading **does not** solve short stalls (e.g. data/control hazards) like fine grained does, because aborting the instructions and switching threads would be more costly than the hazards themselves.
It requires a **fast** thread switching mechanism, something that can switch threads faster than the time it takes to get a cache line. It doesn't have to be **instantaneous** like it does for fine-grained.

## Comparison
The performance improvement in a non-superscalar processor is something like this:
![](Pasted%20image%2020230323144844.png)
(ICM stands for Instruction Cache Miss.)
For a superscalar processor, it's something like this:
![](Pasted%20image%2020230323152953.png)