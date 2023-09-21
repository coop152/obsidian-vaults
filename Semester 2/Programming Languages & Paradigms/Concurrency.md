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
#### Atomic instructions
The preprotocol itself must be atomic. This posed a problem in the past, and spawned many complicated preprotocol algorithms to deal with this requirement for atomicity. However this is now a non-problem, as modern processors offer **atomic instructions** for this very purpose:
- Exchange -> Swap two memory locations
- Fetch and Add -> read, add something, then write back
- **Compare and Swap (CAS)** -> Modify a variable only if it has a certain value, then return the old value

The important one here is Compare and Swap, which can be used to both check if a critical section is active and to enter it if it isn't, all without leaving an opportunity for another thread to read the flag at the same time and also enter the critical section. Essentially, CAS works like this:
![](Pasted%20image%2020230417093902.png)
(this is a single instruction in the instruction set.)
This allows a mutex to be created like so:
![](Pasted%20image%2020230417093955.png)
(again, remember that CAS is a single instruction and not actually a function.)
#### Higher level mutexes
You don't usually use these hardware atomics directly, unless in exceptional cases such as embedded programming or low-level OS programming. Usually, these will be present in libraries or OS utilities and will therefore be handled for you as part of more programmer-friendly "atomic" functions or containers. This is more portable to different hardware and less susceptible to programming mistakes.
A common high-level way of representing a mutex is a **lock**. You call a function such as `acquire()` or `lock()` to enter a critical section (the preprotocol), then you call a function such as `release()` or `unlock()` to exit it (the postprotocol). This is essentially the same as the previously given code, but notably these methods will usually make use of operating system/device sleep capabilities to wait, as opposed to the previously given code which just repeatedly checked again and again. Here is a simplified version of the C stdlib implementation of a `lock()` function:
![](Pasted%20image%2020230417094642.png)
## Semaphores
A mutex represents a resource that can only have one "user" at a time, but some resources can have multiple users at once. Semaphores are a way of controlling concurrent access to a resource which can only have a limited number of users. A semaphore is similar to a mutex in that it has an entry and exit function:
`wait()`:
1. Acquire the resource
2. Decrement some counting variable if it is positive
3. If it isn't positive, sleep

`signal()`:
1. Release the resource
2. Increment the counting variable if positive
3. If it isn't positive, wake up a thread

## Monitors
A monitor is like a mutex for an object (also known as a monitor lock). It also includes support for signalling, with functions like such:
`wait()`:
1. Called when some computation cannot progress any further
2. Joins a waiting queue and releases the lock on the object
3. Sleeps

`signal()`/`notify()`:
1. Called by some thread when the computation can progress further
2. Wakes up the front of the waiting queue

These are used extensively in Java concurrency.

## Process-based concurrency
While thread-based concurrency is good for its low overhead, it isn't always possible to use it. For example:
- In distributed, multi-node systems (e.g. databases)
- In Python

It is also sometimes undesirable to have implicit communication, as it makes it harder to reason about the correctness of a program.
But how can processes communicate in order to work concurrently?
#### Inter-process messaging
Processes communicate through messages; everything between two messages is effectively atomic, as processes do not share memory and therefore do not work on the same data by default.
These messages are sent over different channels, usually abstracted away with handles. Some channels are
- Shared memory objects
- Network sockets
- Unix sockets
- etc...

There are many ways that processes can communicate across these channels:
- Remote procedure calls -> The sender asks the receiver for something and **waits** for the receiver to **reply** (e.g. Solidity, NFS, gRPC).
- Synchronous messages -> The sender **waits** for the receiver to **get the message**, but doesn't wait on the reply (e.g. Go, Python pipes)
- Asynchronous messages -> The sender sends the messages and **continues executing immediately**, waiting for nothing (e.g. Erlang, Scala, Python queues).

The choice among these is critical for the semantics of the program.
RPC is the simplest to reason about, but it has a large overhead; all of the coordination leaves the calling process idle for a long time.
![](Pasted%20image%2020230417100751.png)
Synchronous messages spend less time idling in the calling process, but there is still a significant overhead. It is still easy to reason about, as the send and the reply are atomic, but the state may have changed between them which adds some complexity.
![](Pasted%20image%2020230417101016.png)
Asynchronous messages are significantly faster than the other two methods, with very little overhead, but they are much harder to reason about as a result. There is no guarantee that the caller's message will be received, requiring logic to deal with this case. It also requires buffers for managing messages, as there is no way for process 1 to know if the second process has handled one request before sending another.
![](Pasted%20image%2020230417101456.png)

## High-level Concurrency
Mutual exclusion is tedious and difficult. Message passing is tedious and slow. These methods, while somewhat effective, have not solved all high-level concurrency problems:
- Deadlocks -> An unending cycle where every process is waiting for the next process to leave a critical section
- Livelocks -> An unending cycle where every process is trying to enter a critical section but they're all failing
- Starvation -> Where processes aren't allowed to progress, waiting forever

These bugs are hard to identify automatically (i.e. by a compiler or a checking tool, but also to humans), and are hard to debug. 
For example, there is the **dining philosophers problem**. (just google it.)

So, we need to reason about the program's correctness:
- Safety properties should **always** be true. (e.g. "Two philosophers should never have the same chopstick")
- Liveness properties should **eventually** become true (e.g. "Every philosopher will eat noodles")

Formal descriptions will allow bugs to be discovered automatically. This might be through formal specifications with verification, or through a static checking system with behaviourally specialised/limited types.
#### Transactional Memory
Like database transactions, but for memory.
- Mark atomic regions of code with `atomic {}` blocks. Data read by these regions should not be written elsewhere and data written by these regions should not be accessed elsewhere.
- The compiler or the hardware monitors data usage and rolls back the process state if atomicity is violated.
- This is ideal when collisions (and therefore rollbacks) are unlikely.

#### Automatic concurrency
Have the compiler automatically introduce parallelism to the program (this is called speculative parallelisation). This is ideal, but is extremely hard to do for most kinds of programming language. The semantics of most programming languages inherently require the programmer to describe a sequential order, and automatically reasoning about the modification of that order is near impossible.

Speculative parallelisation can handle some cases if the code "looks" parallelisable, but the compiler usually can't be 100% sure about this. Therefore, it might assume there is no implicit communication and run it concurrently, but track memory accesses using a method such as transactional memory in order to verify correctness.

Functional programming is much better suited for this, as the functions have no explicit ordering, no state, and the flow of data is explicit.