1a. 
1. The processor sends the command to load the word, using the virtual address.
2. The TLB and L1 data caches simultaneously receive the command. As this word has not previously been accessed, the L1 data cache will miss.
3. The TLB will also have missed, invoking a page walk to get the corresponding physical address.
4. When the page walk is finished and the TLB has the physical address, it stores this mapping for later. It then sends the address forward to the L2 cache, which uses physical addresses