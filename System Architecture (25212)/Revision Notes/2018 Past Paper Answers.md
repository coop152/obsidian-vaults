1a. Compulsory miss: Occurs when the cache has just been started, for example when the program first starts. The cache doesn't contain anything yet, so any reads or writes will necessarily cause a cache miss. This kind of miss happens on any kind of cache.
Capacity miss: Occurs when the cache is too full to hold a new cache line, and must evict an old cache line to make space. This kind of miss is most common on a fully associative cache. 
Conflict miss: Occurs when the cache already holds an old cache line with the same location (or index) as the cache line that is being inserted, meaning that the old cache line must be evicted. This kind of miss is most likely to happen in a direct mapped cache, is less likely to happen in a set-associative cache, and cannot happen in a fully associative cache.

1b. Temporal locality: When something in memory is used, it will likely be used again soon. To exploit this, you may implement "blocking"; that is, breaking a problem down into smaller chunks so that the working set of data can fit entirely in the cache. This improves performance in algorithms where data is repeatedly used, by preventing data that is still in use from being evicted from the cache.
Spacial locality: When something in memory is used, it is likely that something nearby will also be used. To exploit this you may use "flat" data structures over ones with more indirection; using an array allows a cache with wide cache lines to retrieve multiple entries in the array at once, while a linked list or similar structure will only retrieve one element at a time, drastically increasing the number of cache misses.

3a. Dependencies: (1 -> 2), (3 -> 4), (5 -> 6), (2 -> 7), (4 -> 7), (6 -> 8)
These dependencies are negatively affecting the performance of the code, as the instructions depending on the memory loads are put immediately after the loads, causing a 1 cycle bubble each time. If the loads were all back to back, and the following MUL instructions were in a different order, there could be no bubbles at all.
It will take 15 cycles. This is because it will take 1 cycle per instruction (8 instructions), plus 3 wasted cycles from the load-then-use dependencies, then 4 cycles at the start while the pipeline fills up and no instructions are being completed. 8 + 3 + 4 = 15.
CPI = 15 / 8 = 1.875

3b. To solve the dependencies, put the three LDR instructions together at the start, and calculate the MUL of one of the first two registers loaded first. For example, this line order:
```
LDR R1, X
LDR R2, Y
LDR R3, Z
MUL R1, R1, R1 
MUL R2, R2, R2 
MUL R3, R3, R3 
ADD R1, R1, R2 
ADD R1, R1, R3
```
This ordering has no data hazards, and therefore no wasted cycles.
With no wasted cycles, the execution time will be 8 + 4 = 12 cycles. This gives a CPI of 12/8 = 1.5.

3c. Scoreboard is centralised, with the scoreboard handling data dependencies and distributing work to the functional units. Tomasulo is decentralised, with each functional unit's Reservation Station handling the resolution of data dependencies and work distribution by itself. Tomasulo provides better performance in multiple ways:
1. Removing the scoreboard as a centralised bottleneck allows a larger instruction window, and improves scaling to larger numbers of FUs
2. The use of register renaming allows Tomasulo to avoid WAR and WAW dependencies without stalling, unlike Scoreboard which needs to stall

Another way that they differ is in their handling of writeback - Scoreboard is connected directly to the register bank, so it can perform parallel writes of results. On the other hand, Tomasulo is limited to serial writing due to its use of a Common Data Bus. This could give better performance for Scoreboard, although Tomasulo's CDB also allows reservation units to snoop on the results of other reservation units and take results directly from them instead of from the register bank, cutting out the wasted cycle of writing then reading from the register bank.

4a. Cache is "coherent" in a multicore system if every core's cache can present the same up-to-date view of memory to their core. For example, consider one core writing a value to memory and its cache holding it locally, waiting to copy it back later. If another core tries to read that same memory location, that core's cache needs to be aware that the other cache holds an updated value and that any existing copy it holds is no longer valid, even if the updated value hasn't been written to main memory yet. This is important to ensure that all cores are seeing the same up-to-date values. 

4b. In a Snoopy cache coherence protocol, every cache is connected to every other cache and to main memory through a common bus. Caches keep the whole system up-to-date by sending globally visible messages down the bus. For example, if a core modifies a value locally then that cache will send out a message along the bus telling the other cores to invalidate their own copies (if they have them) as they are out of date. Snoopy keeps track of the global state of cache lines using a scheme like MESI (Modified, Exclusive, Shared, Invalidated); Each cache is aware if other caches do or do not hold the same cache line, allowing them to act accordingly when messages are received.
In a directory cache coherence protocol, a centralised Directory keeps track of the state of each cache line on behalf of the caches, meaning that the caches themselves are not aware of the state of the others like in Snoopy. The caches themselves have a more typical set of states such as Modified, Shared (which does not actually indicate if other cores share the line or not) and Invalid, as in a regular single-core cache system. The directory stores extra state for each line, marking it as either Not Cached, Shared or Modified, as well as a listing of which cores currently have that line cached. The directory can then use this information to keep the caches in sync; for example, if one cache modifies a cache line it will send a message to the Directory, which will see that the line is shared and other cores hold it. The directory will then signal these cores to invalidate their copy.
Directory schemes have many advantages; while the Directory itself is a centralised structure, it is more scalable than a centralised interconnect like the common bus of Snoopy. Because each cache now has its own connection to the directory instead of having to share a single bus, messages can be sent in parallel, which increases bandwidth. Snoopy does have an advantage in that it requires less messages than Directory.

4c. 
1) Single thread peak performance (IPS)
Prov#1 = 2,000,000,000 * 4 = 8,000,000,000 IPS
Prov#2 = 1,000,000,000 * 3 = 3,000,000,000 IPS
2) Peak IPC of whole system
Prov#1 = 4 * 8 = 32 IPC
Prov#2 = 3 * 36 = 108 IPC
3) Peak instructions per second
Prov#1 = 2,000,000,000 * 4 * 8 = 64,000,000,000 IPC
Prov#2 = 1,000,000,000 * 3 * 36 = 108,000,000,000 IPC
4) Total hardware threads
Prov#1 = 8 * 2 = 16 threads
Prov#2 = 36 threads

4d. 
Prov1#: i) 2.7 * 2,000,000,000 = 5,400,000,000 IPS
ii) 1.5 * 16 * 2,000,000,000 = 48,000,000,000 IPS all core
Prov2#: i) 1.4 * 1,000,000,000 = 1,400,000,000 IPS
ii) 1.4 * 36 * 1,000,000,000 = 50,400,000,000 IPS all core
Provider 1 runs at about 3.9x the speed of Provider 2 in the first phase, but Provider 2 runs at about 1.05x the speed of Provider 1 in the second phase. As both phases are said to be roughly even in size, provider 1 has the superior performance due to its significantly better performance in phase 1 and its very minor performance loss in phase 2.