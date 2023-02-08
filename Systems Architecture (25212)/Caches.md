## Principles
Most access patterns are not random; the keyword is **locality**.
- Spacial locality: If something was used, then things nearby will probably be used too.
- Temporal locality: If something was used, it'll probably be used again soon.
Caches exploit this by putting thinks that are likely to be 'in use' into faster memory, which changes over time. This approach is statistical; it assumes that the software (both the code and the data it's working on) are behaving 'normally'.

## Operation
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
The simple view of a cache with N entries is that it can cache N completely different and unrelated items. This is called **fully associative**. However, this is expensive; each lookup requires a search of the entire tag table which is $O(n)$.

#### Direct-mapped cache
An alternative view of caching, that simplifies the implementation. Any particular item has a predetermined place that it might be cached in, which is determined by the lower bits of its address. Therefore, incoming addresses only need **one** tag comparison. Therefore, it is **lower power** and **considerably faster**.