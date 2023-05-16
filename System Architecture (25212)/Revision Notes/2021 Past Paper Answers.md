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
3. The write is complete. When this cache line is evicted/invalidated, the value will be copied back to L2, and then to main memory if the line in L2 is evicted/invalidated.

2a. A write buffer allows the memory system to perform write operations without needing to stall the processor and wait for slow memory to finish storing the data.
2b. Currently queued operations in the write buffer have an address and a value, meaning that if the processor reads from an address that is waiting to be written to, the updated value can be taken right from the write buffer at a high speed.
2c. If the write buffer becomes full, a new structural hazard is introduced; the processor must stall on the current write operation and wait for the write buffer to complete whatever it is currently writing, so that the write buffer can receive the new write command. Stalling, while bad for performance, will prevent any functional problems from arising as all of the intended writes will still be executed.
2d. A victim cache should be fully associative. This is because direct mapped and set-associative caches don't have any inherent ordering, which adds complexity to the implementation of the copy-back queue. 
2e. **(come back to this later)**

3a. The SUB depends on the result of the first two LDR instructions, causing a 2 cycle stall. The first MUL depends on the result of the third LDR, causing another 2 cycle stall. The second MUL also depends on the result of the first MUL giving another 2 cycle stall. Finally, the STR depends on the result of the second MUL adding another 2 cycle stall. Therefore, the program executes in 7 + 8 + 4 = 19 cycles despite only being 7 lines of code long, giving a poor CPI of 19/7 = ~2.7.
3b. The performance of the pipeline could be improved by implementing two forwarding path. The first one takes the result of the EX stage (from the input of the MEM stage) into the input of the EX stage on the next clock, allowing an arithmetic instruction to share its result with the next instruction immediately. This forwarding path will entirely eliminate the stall between the two MUL instructions and before the final STR instruction. The second path takes the result of the MEM stage (the input of the WB stage) and forwards it to the input of the EX stage, allowing a memory load instruction to share its result with the next instruction 1 cycle sooner. This forwarding path will reduce the stalls between the LDR and SUB instructions and between the LDR and first MUL instruction by 1 cycle. With these forwarding paths, the program will execute in 7 + 2 + 4 = 13 cycles, giving an improved CPI of 13/7 = ~1.86.
3b. Dependencies: (1 -> 3), (1 -> 4), (1 -> 7), (2 -> 6), (3 -> 5), (4 -> 6), (7 -> 8)
Anti-dependencies: ()