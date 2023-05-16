1a. 
1. The processor sends the command to load the word, using the virtual address.
2. The TLB and L1 data caches simultaneously receive the command. As this word has not previously been accessed, the L1 data cache will miss.
3. The TLB will also have missed, invoking a page walk to get the corresponding physical address.
4. When the page walk is finished and the TLB has the physical address, it stores this mapping for later. It then sends the address forward to the L2 cache, which requires physical addresses.
5. The L2 cache will also miss. It will request the data from main memory.
6. Main memory will load the word (and many nearby words, if the cache has wide lines) and send it back to the L2 cache.
7. The L2 cache will store the value for later, under the physical address. It will then send this up the chain to the L1 data cache.
8. The L1 data cache will store the value as well, under the virtual address that was initially requested. It will then finally send the word to the processor.

1b. 
1. The processor send the command to write to the word, using the virtual address.
2. The L1 cache updates the value that it holds, and marks the cache line as dirty/modified.