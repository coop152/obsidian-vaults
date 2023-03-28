## What is concurrency?
Concurrency is when multiple parts of a computation happen at the same time. Examples of "parts" might be:
- Instructions
- Threads
- Local Processes
- Remote Processes

And some examples of "computations" are:
- Functions
- Programs
- Distributed Systems (i.e. databases)

## Why use concurrency?
- To maximise resource utilisation
	- Pipelined processors
	- Out-of-order execution
	- Preemption
	- Relaxed consistency (coming up later)
- To increase performance
	- Multicore (only way to achieve high performance with current processors)
	- Massively parallel systems (only way to solve big problems, i.e. supercomputers)
	- Distributed systems (only way to run a global digital service)

## The problem with concurrency?
We don't think about programs concurrently. We expect from our programs that they will be:
- Atomic - That computations will happen in a single moment in time
- Ordered - That computations happen in a specific order

But in reality, even for a single core, local application these are not true:
- Granularity of atomicity - Computations beyond a certain level take time, and a processor will not wait nicely for long running computations when there are other things that may be done
- Interleaving - You cannot simply consider the one order you gave, you must also consider all other **consistent orderings**.

Of course, you **can** assume those things to be true for a single core application because the hardware essentially lies to you and pretends to not be concurrent. That's nice from the perspective of the program, but it is a lie nonetheless.
