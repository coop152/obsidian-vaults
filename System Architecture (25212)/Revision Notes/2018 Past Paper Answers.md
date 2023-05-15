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

3c. Scoreboard is centralised, with the scoreboard