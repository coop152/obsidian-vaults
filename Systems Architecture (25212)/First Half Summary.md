## Parallelism
When you need higher bandwidth but you can't decrease the latency any further, you add more parallel lines to handle more data at once. Examples include:
- In buses, widths increase and the size of words in memory increases
- In PCIe, use more lanes
- In disks, use RAID 0
- In processors, use a superscalar architecture (multiple components such as ALUs or FPUs acting in parallel)
This can cost a lot (more wires, pins or space required).
## Interleaving
Dividing responsibilities of something between multiple of it. For example:
- RAID 0 interleaves blocks along multiple drives
- Interleave multiple signals to a RAM chip to approximate multiport RAM
## Caching
Keep some subset of data in a 'close', fast store. Performance depends significantly on the behaviour of the user being 'typical'. For example:
- Computer main memory cache
- Page translation in the TLB
- Virtual memory pages
- Blocks on an HDD
- Branch prediction
- Web browsers (caching in software)
- Recently used files in software ('caching' for the user)
## Speculation
Try and predict the future, in order to prepare for the most likely case and improve performance.
- Instruction prefetch
	- Relies on the fact that most instruction fetches will be sequential
	- Improved by **branch prediction**, which deduces the fetch path from the past behaviour of the program
- Pipelining
If the speculation is wrong it will cost performance, but if it's right performance will improve. 
## Lazy vs Eager
- Lazy - don't start on something until it's needed.
	- Efficient in effort, but can have poor latency
- Eager - Try and do things before they're needed.
	- Typically reduces latency, but may waste effort is something isn't required after all.
	- May also do the 'wrong' thing, depending on what's being done eagerly.
## Synchronisation
Important to present information in the original order, regardless of if it was calculated in that order or not. Examples include:
- Write buffers
- Processor pipelines
	- e.g. instructions may be issued out of order
- Important for cache coherency
	- e.g. modifying your own instructions with separate caches
	- Whenever multiprocessors are involved
- Synchronising parallel threads
	- e.g. atomic operations in memory