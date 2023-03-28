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

