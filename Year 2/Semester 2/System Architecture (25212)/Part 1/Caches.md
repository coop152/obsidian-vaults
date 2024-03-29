## Principles
Most access patterns are not random; the keyword is **locality**.
- Spacial locality: If something was used, then things nearby will probably be used too.
- Temporal locality: If something was used, it'll probably be used again soon.
Caches exploit this by putting thinks that are likely to be 'in use' into faster memory, which changes over time. This approach is statistical; it assumes that the software (both the code and the data it's working on) are behaving 'normally'.

## Operation
![](Pasted%20image%2020230509114703.png)
(generally speaking)
A cache comprises of two 'parts':
- A **tag** field which notes the locations which are currently stored in the cache.
- A corresponding **data** field which holds the data from the tagged location(s).
When memory is accessed via the cache:
1. The tags are checked for the given address.
2. If the address in in the tags, the data is cached; cache hit, send the data back immediately
3. If the address isn't in the tags, pass the address along to memory.
4. Read memory and receive the result, if necessary

## Hit and miss
$T_{ave} = T_{hit} + P_{miss} \cdot T_{miss}$
Average access time = access time for cache hit + (prob of cache miss * access time for cache miss)
For example:
- $T_{hit} = 1ns$
- $T_{miss} = 10ns$
- $P_{miss} = 10\%$
- $T_{ave} = 1ns + (10\% \cdot 10ns) = 2ns$

If 10% of accesses miss cache, speed is halved.

## Size Trade-offs
Big memory is slow, and small memory is fast.
SRAM is good for random access, and fairly fuss-free.
SDRAM is good for big memories with burst access but it's harder to use.

## Associativity
#### Fully associative cache
The simple view of a cache with N entries is that it can cache N completely different and unrelated items. This is called **fully associative**. However, this is expensive; each lookup requires a search of the entire tag table which is $O(n)$.
![](Pasted%20image%2020230509114720.png)

#### Direct-mapped cache
An alternative view of caching, that simplifies the implementation. Any particular item has a predetermined place that it might be cached in, which is determined by the lower bits of its address. Therefore, incoming addresses only need **one** tag comparison. Therefore, it is **lower power** and **considerably faster**. As a consequence, it is less flexible and more complex.
![](Pasted%20image%2020230509114829.png)

## Cache misses
Common classifications:
- Compulsory misses: The data you're looking for wasn't used previously. **Will always occur** no matter the cache implementation, since the cache starts out empty when a program is first executed.
- Capacity misses: The cache is full, and more space was needed. Therefore, the data you were looking for was evicted.
- Conflict misses: The architecture of the cache limits the tags that may be stored. Therefore, the data you wanted was evicted to store something else, even though the cache may not be full.
	- These don't occur in fully associative caches
	- They occur frequently in direct mapped caches

Every miss imposes a performance penalty.
- A fully associative cache is slower but misses less often
- A direct mapped cache is faster but it misses more often

## Set-associative Cache
A compromise between fully associative and direct mapped. Like multiple direct mapped caches combined; each tag can hold more than one value.
For example, have two half-size direct mapped caches in parallel:
- Each item can be cached in one of two places.
- Therefore, conflicts are reduced - a bit.
- But cost is also increased - a bit.
- And the speed of a cache hit is decreased - a little bit.
- Otherwise, acts like direct mapping

![](Pasted%20image%2020230509114914.png)
(pictured here is 2-way set associative)
This can be extended to more sets (4-way, 8-way and onwards).
## Cache Lines
This is another way to exploit **spatial locality**; instead of storing one word per tag, store many words per tag. In practise, this causes very little loss in effectiveness while significantly reducing the tag overhead.
![](Pasted%20image%2020230509115012.png)
Typical line sizes are 4 or 8 words. For example, 64 bytes (8 bytes * 8) on Intel x86_64 chips.
Why does this cause very little loss in effectiveness? Wouldn't this lead to lots of cache lines containing only a single useful word? No, because of spatial locality; a large proportion of memory operations are likely to be done in sequence (for example, loading instructions, processing an array, accessing local variables that are next to each other on the stack...). This means that for most applications, the likelihood of this method wasting space is very low.
## How to Choose?
Any cache architecture will be a compromise. You have to choose between 
- Size (as in physical size, not capacity) - depends on the space available
- Expected use - more locality would benefit from a bigger cache line size
- Associativity - higher for smaller caches
- Power - Higher associativity leads to more activity, thus more power usage
- A PC might also use multiple levels of cache - another thing to decide on

Here is how modern processors are generally structured, which illustrates the compromises that are made:
![](Pasted%20image%2020230509115247.png)

## Cache Replacement Policy
There's no need to pick one in direct mapped as nothing ever gets replaced. Otherwise, pick one:
- **Round Robin** - Replace the oldest entry, with no regard for usage.
	- Easy to implement, but can produce nasty behaviour.
- **Least Recently Used (LRU)**: Replace the entry that hasn't been used for the longest time
	- Difficult to implement in hardware, but has good results.
- **Random**: Just pick an entry at random to replace.
	- Reasonably easy to implement, and reasonably effective.

## Cache Writes
That's lots of content on reading - what about writing? Cache is a copy of the data: but a write changes the data. So what to do?
1. **Write-through cache** - Modify the cache entry *and* the memory copy. Every write has to access slow memory.
2. **Copy-back cache** - Only modify the cache entry. Now memory will become outdated, so it needs to be written back all at once at some point.

![](Pasted%20image%2020230509115423.png)
#### Write-through
Write to both the cache and memory, keeping everything in-date. Very slow (even with [buffering](More%20Caches.md#Write%20buffer)) and very wasteful when an address is repeatedly written to. The advantage is in the simplicity - the cached value can be discarded at any time with no coordination with memory at all.

#### Copy-back
Only write to the cache, and allow memory to become outdated. Fast and low-power, as it avoids use of memory, but also very complicated; memory needs to be brought up to date at some point, especially in a multitasking system where cache may be frequently cleared out during process switching.
When something is written, that cache line will be labelled "dirty" so that only changed values in the cache need to be copied back.
Sometimes it is required to bring main memory back up to date with the cache; this process of writing back to memory is called a **cache flush**. A flush is required for caches with virtual addresses when the operating system switches context; those addresses are all invalid for the program that is being switched to. So in addition to all of the virtual addressed caches being invalidated, the dirty lines must be written back to memory (expensive!)