In recent years, the power scaling of new processors has mostly been thanks to increasing core counts as opposed to increased speeds. As transistors and chips get smaller, it becomes impossible to keep running them faster while keeping them functional and cooled. On top of that, transistors shrink slower and slower as time goes on, meaning we can't rely on transistors getting smaller and more efficient for our performance gains any more.
Instead, we have moved to using **Chip Multiprocessors**, or processors which contain multiple independently functional but connected processing units (cores). The number of cores in processors has grown steadily since the 2000s, leading to growth like this:
![](Pasted%20image%2020240202144946.png)
Ignoring the slow spot in the early 2010s, server CPUs have been doubling in core count every 2 years, and GPUs have been exploding in core count into the realm of tens of thousands. A high-end server CPU released in 2020 has 32 physical cores; if you extrapolate this trend to 2030, then you could expect server CPUs in 2030 to have around 1000 cores! Of course, there are limiting factors that are likely to stop that from happening.

# Non-chip multiprocessors
While single-chip multiprocessors began being used in the 2000s, multi-chip multiprocessor machines have a much longer history that stretches back into the 1960s. For example, the Cray T3E (released in 1996) was a highly parallel supercomputer that had more than 2000 cores in a single system. It was comprised of an immense number of small boards, each of which housed 4 whole CPUs.
![](Pasted%20image%2020240202145825.png)
Traditional multiprocessor configurations are still in heavy use for high performance applications, such as servers and supercomputers. The chips they use will be chip multiprocessors with a number of their own cores, but there will be many of these entire CPUs combined on single boards, or even across multiple connected chassis.
There is a degree of overlap between these traditional multiprocessors and modern chip multiprocessors, but they are in many ways distinct:
![](Pasted%20image%2020240202150018.png)
# Flynn's Taxonomy
Flynn's Taxonomy categorises processors into 4 (5?) categories based on the quantity of data and instructions they take on at a time.
![](Pasted%20image%2020240202150535.png)
**SISD (Single Instruction Single Data)** - Traditional processors. They sequentially take instructions and execute them one-at-a-time, operating on single pieces of data.

**SIMD (Single Instruction Multiple Data)** - Commonly seen as an extension to traditional processors (e.g. MMX/SSE/AVX, arm NEON) and also the basic architecture of modern GPUs. They sequentially take instructions and execute them one-at-a-time, similar to a traditional processor, but each instruction operates on multiple pieces of data, providing parallelism while keeping generally the same architecture as a traditional processor.

**MIMD (Multiple Instruction Multiple Data)** - True parallel processors with multiple independent processing units - multiprocessors. Each unit takes its own instructions which may be distinct from the other units, and they all execute them at the same time, acting on their own data.

**SPMD (Single Program Multiple Data)** - A special case of MIMD where each processing unit is still independent, but they all execute the same code on different data. This is conceptually approaching SIMD, but the parallel instructions need not be in lockstep with each other and can diverge. Falls somewhere in-between SIMD and MIMD in the taxonomy.

**MISD (Multiple Instruction Single Data)** - Apparently not interesting enough to get a definition in the lecture.

Most processors are a mixture of these: A modern processor will have many cores, making it MIMD, but will also feature vector extensions such as AVX, also making it SIMD. GPU cores make heavy use of SIMD, but they feature multiple cores which often run the same code, making it SPMD.
# Interconnection
Regardless of the type of system, parallelism requires some kind of communication between units. Without coordination, they cease to be a single entity and just become a bunch of separate processors.
Communication is required between cores for coordinating work, transferring results, getting new inputs, etc., but communication with the shared memory is also required to determine who can read and write from where, what parts of memory are currently valid, etc.
The communication that takes place on a chip multiprocessor is necessarily different from the kind that takes place off-chip, between distinct components. It is heavily limited in footprint and power usage due to being on the die with the rest of the processor. On one hand this is a negative, as it imposes heavy limitations on the design and functionality of the network, but on the other hand it allows the communication to be much, much faster than any off-chip network could be.
There are a variety of interconnect designs that have been created to work around these limitations.
## Grid
![](Pasted%20image%2020240202152905.png)
Grid is the simplest interconnect in layout. Each core is connected to its neighbours and its own private memory. Communications with neighbours is fast due to the direct connection, but communications with non-neighbours requires a slow and complicated message-passing scheme to be devised. Assuming nothing goes wrong in transmission (and there are many things that can go wrong), on an n-by-n grid of chips it will take at most 2n-1 jumps for a message to reach it's destination.
# Torus
![](Pasted%20image%2020240202153303.png)
Similar to grid, but with connections between opposing edges as well as between neighbours. The extra links improve the symmetry of the network, reducing the maximum number of message hops from 2n-1 to n hops. This benefit is limited in small systems (like the one in the example image) but can be pronounced in large systems.
In exchange, Torus requires more wires and wires with complex routing at that. On a large system there may be a significant distance between opposing chips, which can pose difficulties both with the physical routing and electrically.

You can also have multi-dimensional torus layouts, with each dimension resulting in more neighbours for each chip. These are used in some supercomputers as on and off-chip interconnects (there have been fastest-in-the-world supercomputers that utilised 6D toruses) but are largely non-applicable to chip multiprocessors due to the difficulty of routing so many wires on a single chip.
## Bus
![](Pasted%20image%2020240202154156.png)
In a bus interconnect, every core is connected to every other by a shared bus. This is a very common layout, for many reasons:
- It is conceptually simple, with direct communications between the two interacting cores
- It is simple to build
- It has constant (worst case) latency between pairs, unlike grid
- The way memory is configured is flexible; each core can have an individual memory or there can be one shared memory

The biggest downside to the bus layout is the bus itself. It is one big, long wire that spans across the whole chip, requiring lots of area and power. Because the bus has to be shared among all cores, a method of splitting time among the cores is required, which introduces a large amount of complexity and limits the bandwidth. 
Consequently, bus has lower bandwidth than grid. The problems with bus get much worse as the chip gets bigger, making it infeasible for anything other than small chips.
## Others
None of the three previously mentioned topologies scale well enough for large systems, though bus is the worst. For high core count machines, different topologies are required. Among these are Trees and other Hierarchical layouts, as well as Crossbars, Hypercubes, Rings, Min, etc...
On large systems, circuit switched interconnect networks (i.e. networks where communicating units are directly connected) are largely insufficient. Most large systems make use of packet switching in a similar manner to Grid. This introduces a great deal of complexity, what with packets potentially taking variable paths, arriving out of order, etc... but there are methods of dealing with these downsides.

# Memory
It isn't only the cores that need to be connected - memory also needs to be incorporated into the system. Depending on the interconnect, you can have two different kinds of memory:
- *Shared memory* - Memory that is connected to all cores
![](Pasted%20image%2020240202161051.png)
- *Private/Distributed memory* - Memory that is connected to only one core
![](Pasted%20image%2020240202161238.png)

It is important to note that in a shared memory, just because every core is connected to the shared memory does not mean that they all have equal ease of access; it may be that some cores have significantly more "direct" connection to the memory than others.

This is from a hardware perspective, following on from all of the previous conversation. At this point, we also begin to concern ourselves with the software side of parallelism. In the context of software:
- *Shared memory* - Memory that is globally accessible from all threads. 
![](Pasted%20image%2020240202161511.png)
- *Private/Distributed memory* - Memory that is only locally accessible by the owning thread.
![](Pasted%20image%2020240202161904.png)

In both hardware and software, communication in a shared memory takes place by **reading and writing to memory**. If core/thread #1 wants to communicate something to core/thread #2, it writes a message to an agreed-upon place in memory, which #2 will periodically check. 
In contrast, communication in private memory takes place through **dedicated message-passing channels**. If core/thread #1 wants to communicate something to core/thread #2, it directly sends a message to it.

## Programming model
When speaking about software, these types of memory are named as such:
- **Data Sharing** - Globally accessible memory, equivalent of shared memory
- **Message Passing** - Thread-owned memory, equivalent of distributed memory
- **Serial** - Like data sharing, but often with software restrictions

It is important to remember that, even though the hardware and software models may line up conceptually, neither one implies anything about the other. That is, if your hardware utilises shared memory then there is no reason that your program cannot use message passing, or vice versa.
That said, there is good reason to use an appropriate model for the hardware you are given. Using message passing on a distributed memory system and using data sharing on a shared memory system are simply more efficient. Simulating a shared data environment on a distributed memory system, while possible, is very slow. Message passing on a shared memory system is relatively fast, but the extra traffic can still easily impact performance.

## Pros and cons
From a hardware perspective, distributed memory is much better. It's easier to implement, has a higher potential bandwidth (due to the lack of a shared bus), and as a result it scales better. In a supercomputer (Where hardware takes precedence over software), distributed memory will almost always be used.
From a software perspective, data sharing is much better. It's almost always easier to work with, it's more flexible (in that there is no required format or size for the data being shared), and it better reflects the mental model of a programmer used to serial programming. In a general purpose computer (Where software takes precedence over hardware), shared memory will almost always be used.

This is one of the central conflicts of parallel computing. Any performant piece of hardware will be complex. If we expose this complexity to the software, then:
- Developing software becomes harder, increasing dev costs
- Software must be more complicated
- Software will inevitably fail to use the hardware to it's full potential, wasting energy and time with inefficient code

If instead we try to hide the complexity in hardware:
- The hardware becomes even more complex, increasing the cost of developing and manufacturing it
- The design becomes even more complicated
- We have more hardware than before, so we use more power and it goes slower

This conflict appears in every aspect of parallel computing, software complexity vs hardware complexity:
- Thread Level Parallelism vs Instruction Level Parallelism
- Software managed on-chip buffers vs hardware managed caches
- In-order execution with complex instructions that compilers can emit vs out-of-order execution managed in real time by hardware
- etc...

There is no right answer, and whichever you choose depends entirely on the application.
In our case (that is, when talking about chip multiprocessors), software is usually prioritised and a shared memory approach is taken. It is simply easier to program for a shared memory system than for a distributed memory system.
Distributed memory is mostly reserved for supercomputers, where the hardware must be as simple and efficient as possible to achieve the maximum possible performance. In this environment, the increased software development costs and more complicated code are unimportant.