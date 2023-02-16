## Virtual memory
Every process has its own private address space. The process' virtual addresses are translated into the machine's physical addresses; the translation is defined in the page table(s).
Translation can be simplified significantly by assuming locality (i.e. dividing chunks of space into pages, not single words). But even so, very large tables could be required.
#### Multi-level page tables
Essentially trading lookup speed for space savings. Instead of having one massive table, have multiple chained tables. They have the same number of entries, but child tables that aren't being used can be left out, saving memory. A single level page table is essentially impossible on 64 bit architectures, making this essentially mandatory. So how to improve the speed?
#### Translation Look-aside Buffers (TLB)
![](Pasted%20image%2020230216131440.png)
Cache translations from virtual to physical addresses, making subsequent translations very fast. 
