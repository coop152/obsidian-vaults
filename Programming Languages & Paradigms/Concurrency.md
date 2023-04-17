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

## Data Races
When two concurrent "parts" of the computation use a common piece of data (a register, a table in a database), the actions can be interleaved in many ways, most of which do not behave how you expect. Consider this function being run concurrently, twice at a time:
```C
int old_a = a;
a = a + 5;
```
This is actually many more instructions from the processor's perspective:
```C
register temp1 = a;
int old_a = temp1;
register temp2 = a;
temp2 = temp2 + 5;
a = temp2;
```
So how can this safely be interleaved?
```C
register temp1 = a;
int old_a = temp1;
register temp2 = a;
temp2 = temp2 + 5;
a = temp2;

register temp1 = a;
int old_a = temp1;
register temp2 = a;
temp2 = temp2 + 5;
a = temp2;
```
One after the other works fine.
```C
register temp1 = a;
int old_a = temp1;
register temp2 = a;
temp2 = temp2 + 5;

register temp2 = a;

a = temp2;

temp2 = temp2 + 5;

register temp1 = a;
int old_a = temp1;
register temp2 = a;
```
But this breaks it. Or is any one of these really the "correct" outcome in the first place?
Somehow the program needs to describe the constraints on interleaving and thus make the "correct" outcome explicit. In high level languages, this may be handled by safety/liveness properties (this comes later), and in low level languages there needs to be added code to coordinate the "parts" of the computation and ensure a partial order.

## Software Concurrency
- Threads
	- Independent streams of computation with shared state (same memory)
	- Interactions between threads is implicit: **they are reading/writing to the same data**
	- Coordination must be explicit
- Processes
	- Each process has its own internal state
	- Interactions between processes are explicit; messages must be passed
	- Less coordination is needed
- Nothing? (Let the compiler figure it out)
	- Very difficult to implement, though easier for functional languages.
	- Would be ideal if only it weren't so hard.

## Mutual Exclusion (Mutex)
A basic coordination principle for threads. Threads can only access shared state in **Critical Sections**. Only one thread is allowed in each critical section; this makes them effectively atomic.
The "granularity of atomicity" (i.e. the size of an atomic operation) is increased from a single memory operation with regular threads to a whole critical section with a mutex. In other words, none of a thread's changes are presented to the other threads until a critical section ends, while with regular threads the changes would be seen right as they were written to memory.
#### Creating a critical section
A critical section requires an entry and an exit, called a **preprotocol** and a **postprotocol** respectively:
![](Pasted%20image%2020230417092701.png)
The preprotocol checks if a critical section is safe to enter (that is, if it is the only thread that will be running it) and then continues if it is, or waits if it isn't. If the critical section is entered, then it will be announced to the other threads that the critical section is currently occupied and that they must wait.
The postprotocol announces to the other threads that the critical section is over and that they may enter it.
#### Stipulations
The time that a critical section takes must be limited. It must be **at least** definitely finite - if a critical section does not complete then the other threads will be **starved**, meaning they will wait forever. They should also preferably be short, containing only the absolute minimum of operations that definitely need to be atomic. Critical sections reduce the speed of the program by essentially reducing them to a single thread temporarily; of course, you want to avoid this if it all possible.