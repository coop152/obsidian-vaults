## Write buffer
When loading data, the latency is important because you have to wait for the data. But what about stores? As long as you know it'll reach the destination eventually, you don't need to wait for it. 
![](Pasted%20image%2020230216121516.png)
- Writes can be "fire and forget". The processor might carry on in parallel while data is still being written.
- However read operations may overtake the queued writes, if the processor tries to read a location that hasn't been updated by the buffer yet. A check with the buffer needs to be implemented.
	- The write buffer would contain a tag for each location alongside the data to be written, which could be compared to outgoing read addresses to determine if some data is in the write buffer.
- This is vital to keep operations in order (or to appear so, at least).
Write buffers are a FIFO queue of operations which forwards writes to memory as required. This is fine for most kinds of 'memory', such as regular RAM, but not acceptable for things such as memory mapped I/O.
Sometimes, ordering must be enforced. For example, the processor must know that a data block is in memory before it can tell a DMA controller to access it.
#### Ordering operations
1. Disallow buffering
Mark some area of memory (perhaps a page in the page table or an address recognised in hardware) as un-bufferable. Anything written there must be written to memory before the processor does anything else; the processor has to **stall while it waits**. For this purpose, buses will include a **write response** even for operations where no data needs to be returned.
2. Memory barrier (aka "fence")

![](Pasted%20image%2020230216122759.png)
A point in the stream of buffered operations which holds up all other operations; if there is a barrier in the write buffer, nothing can be read until the barrier operation has been committed to memory. This also needs the processor to **stall**.
## Victim cache
![](Pasted%20image%2020230216123044.png)
After the writes in a write buffer have been dispatched, the values still remain in the buffer until they are overwritten by a new operation again. So why not use them while they're there? 
With this method, any cached lines that remain left over in the write buffer will be forwarded, acting as an extra bit of cache.
## Cache Coherency
![](Pasted%20image%2020230216123127.png)
Sometimes, processors may (re)write their own code in the same manner as they write data (for example, JIT compilation). Multiprocessors might also interfere with data. How to keep all of the different levels and types of cache up-to-date? 
## Atomic Operations
When you have more than one processor sharing a bus, there can be a need for atomic operations (typically read-modify-write, or "RMW"). There also exist other kinds of transaction. This will be discussed in more depth later.
## Cache prefetch
Some memory access patterns are regular enough to be predictable. For example, think of accessing the column of a matrix (a common operation in certain applications); the data isn't sequential, but the manner in which it is being accessed is predictable. In this case, **cache prefetch** may be possible.
If a miss is simulated when the data isn't actually wanted, data that is going to be accessed soon can be cached early. This may be initiated by the software itself, by way of 'hint' instructions, or by the hardware through prediction.
## Cache location
Each cache entry is tagged by some address. But should it be a virtual or physical address?
Virtual addressed cache:
- Fast: no address translation needed
- Invalidated on context switch - expensive
- Used for L1 cache.
Physical address cache:
- "Slow": takes longer to access due to address translation (only slow by comparison, still extremely fast)
- Can persist over context switches - less expensive
- Usually used for anything below L1 cache.

![](Pasted%20image%2020230216125054.png)
If the cache is virtually addressed, then the translation look-aside buffer can be used in parallel to reduce the impact of cache misses; the time taken to check L1 cache can essentially be subtracted from the time it would usually take for the TLB to lookup the address and for L2 cache to respond.