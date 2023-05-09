## Virtual memory
Every process has its own private address space. The process' virtual addresses are translated into the machine's physical addresses; the translation is defined in the page table(s).
Translation can be simplified significantly by assuming locality (i.e. dividing chunks of space into pages, not single words). But even so, very large tables could be required.
#### Multi-level page tables
Essentially trading lookup speed for space savings. Instead of having one massive table, have multiple chained tables. They have the same number of entries in theory, but child tables that aren't being used can be left out, saving memory. A single level page table is essentially impossible on 64 bit architectures, making this mandatory. So how to improve the speed?
## Translation Look-aside Buffers (TLB)
![](Pasted%20image%2020230216131440.png)
Cache translations from virtual to physical addresses, making subsequent translations very fast. 
#### Table walking
A TLB hit is able to deliver:
- The page translation (which may or may not be needed, depending on if L1 cache has a hit)
- The page permissions/metadata (which is absolutely needed)

The TLB delivers these in parallel with the L1 cache's access.
If the TLB misses:
- The processor has to stall
- Performs the page table (physical memory) reads
- Caches the new translation (in the TLB)
- Instigates the L1 cache data fetch
## Memory Management Unit (MMU)
An MMU protects areas of memory from unauthorised operations.
- It checks operation direction, privilege and address.
- It may reject the cycle, causing an exception. This could be a segmentation fault, or due to paging.
The MMU also includes the hardware that facilitates virtual address lookup (e.g. a TLB).
## Multiple levels of cache
![](Pasted%20image%2020230216132621.png)
