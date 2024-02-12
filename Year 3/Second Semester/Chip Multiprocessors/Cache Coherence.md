In a shared memory multiprocessor where each core has it's own cache, we need to do extra work to maintain **cache coherence**: One memory location can be cached in each cache, and we need to make sure that every copy matches so that we have a consistent view of memory.
![](Pasted%20image%2020240212100612.png)
We also need **cache-to-cache communication** if we want to keep the performance advantages of the caches.
# Bus snooping
![](Pasted%20image%2020240212100716.png)
Each cache is connected to RAM via a central bus. We have some hardware in the cache that observes all transactions on the bus (specifically, transactions between memory and *other cores*). If a transaction includes something relevant to that cache (e.g. some data is being written to memory, and the cache has a local copy that will be made outdated) then it can grab the data off the bus or otherwise act on the info to resolve the issue.
## MSI protocol
![](Pasted%20image%2020240212100935.png)
The caches will store 2 status bits with each cache line which encode the current status of that line across all cores; this information can be used in combination with bus snooping to detect cache incoherency and resolve it.
These are the states:
- **Modified** - The cache line has been updates in this core only, and is outdated in memory. This can only be true for a certain address in at most one core. 
- **Invalid** - The cache line is known to be invalid (i.e. outdated) and shouldn't be used to serve data to the core.
- **Shared** - The cache line is valid and hasn't been modified. This can be true for a certain address in *any number of cores*.

These states behave in this way:
![](Pasted%20image%2020240212101515.png)
(a): x has been modified in one core, so it must be invalidated in the other cores to prevent an outdated value being used.
(b): x has been modified (perhaps by an external peripheral with direct memory access?) and no core has an up-to-date copy, so all copies are invalidated.
(c): x has been modified and core 2 has written it back to memory. Core 1 still needs a new copy, so it is invalid, but when it does get a copy it can be shared.
(d): Any changes to x have been propagated to all cores.
These states should be impossible to achieve:
![](Pasted%20image%2020240212101900.png)
Because they mean that there are conflicting values for a certain address in each core.
### Transitions
To maintain these valid system states, the cores must behave according to these rules:
#### From (a)
![](Pasted%20image%2020240212102048.png)
If core 1 reads or writes, it can use the correct cached copy. No extra action is required.
If core 2 tries to read:
- 2 places a read request on the bus, which core 1 snoops
- 1 writes the updated value to memory and goes to Shared state
- 2 snoops the updated value from the bus, stores that value and also goes to Shared state
If core 2 tries to write:
- It first tries to read, leading to the previous actions taking place
- It then places an invalidate request, which 1 snoops. 1 switches to Invalid
- core 2 writes it's updated value to it's cache and goes to Modified state

#### From (b)
![](Pasted%20image%2020240212102606.png)
If core 1 tries to read, it is a cache miss and it will fetch the value from memory, and enter Shared state.
If core 2 tries to read, the result is similar.
![](Pasted%20image%2020240212102809.png)
If core 1 tries to write:
- 1 places a read request on the bus to see if any other cores have a copy (they do not)
- 1 updates the value in it's own cache and goes to Modified state

Same for core 2 writing.

#### From (c)
![](Pasted%20image%2020240212103028.png)
If core 1 tries to read:
- 1 places a read request, 2 snoops it
- 2 sends the value to 1, which stores it and goes to Shared state

If core 2 tries to read, it will have a cache hit and nothing will change.
![](Pasted%20image%2020240212103151.png)
If core 1 tries to write:
- 1 places a read request, which 2 snoops
- 2 sends the line to 1
- 1 sees this response and places an invalidate request, 2 goes to Invalid
- 1 performs the write in cache and goes to Modified

![](Pasted%20image%2020240212103348.png)
If core 2 tries to write:
- 2 places an invalidate request, which 1 snoops and goes to Invalid
- 2 writes to it's cache and goes to Modified

#### From (d)
![](Pasted%20image%2020240212103409.png)
If core 1 tries to read, it's a cache hit: read from the cache. Same with core 2.
If core 1 tries to write:
- 1 places invalidate request, which is snooped by 2
- 2 switches to Invalid, no writeback needed because it wasn't in Modified
- 1 performs write in cache, goes to Modified

Similar for core 2 writing.

### Write-invalidate vs Write-update
The MSI protocol shown previously was a **write-invalidate** protocol, which is the most widespread kind. In these protocols, when a core updates a cache line's value, other copies of that line in other caches are **invalidated**. This requires future accesses from those caches to fetch the line.
Another kind of bus snooping protocol is **Write-update**, where instead of updated lines being invalidated in other caches, their value is updated. This might seem more efficient at first (involving memory less, etc.) but the increased bus traffic makes it generally less desirable.
Examples of write-invalidate protocols are MSI, MESI and MOESI.
Examples of write-update protocols are Dragon and Firefly.

### Limitations
Cache coherence is **required** in shared memory multiprocessors, but the cache coherence protocol can be a significant limiting factor to the number of cores. Specifically, bus-based snooping protocols require a centralised bus, which imposes all of the limitations that come with that. In addition to that, each core must see the invalidate in **the same clock cycle**, which makes the limitations even more strict.

## MESI Protocol
In the MSI protocol, each cache doesn't know the state of the other caches. This can lead to unnecessary usage of the bus, for example:
![](Pasted%20image%2020240212110512.png)
In the first case, core 2 writes to a Shared line, making it Modified. Because core 2 doesn't know that the other cores don't hold that line, it has to send out an invalidate request, even though that request will do nothing.
The second case shows that (in the MSI protocol) we still need to send this invalidate request; in this case the request actually makes other cores switch to Invalid, and the caches would not be coherent if this request were skipped.
The problem is that the core sending these invalidate requests doesn't know if there are any cores that actually need to be invalidated; therefore, it has to take the safe option and send it every time. 

To fix this issue, we need to distinguish between these two shared cases:
![](Pasted%20image%2020240212111027.png)
This is the purpose of the **E** in **MESI**, which stands for **Exclusive**.
We split the Shared state into two versions:
- **Exclusive** - The state of a line after being read from memory (would have gone to Shared in MSI)
- **Shared** (in the true meaning, this time) - The state of a line after being read from *another cache*. i.e., the line is actually being *shared* among caches.

![](Pasted%20image%2020240212111259.png)
At first this may seem like a minor optimisation, but the "unshared Shared" case is very common in most programs. Consider that every single thread-private variable is never shared between cores, and in most cases these are the majority of variables. Because of this, MESI offers a significant reduction in bus activity versus MSI, and is generally used instead.
## MOESI
Another performance issue with MSI/MESI is the case of caches reading a line that is Modified in another cache. This requires the Modified line be written back to memory every time another core wants to read it, which is slow. Instead, what if we had the cache with the Modified line just send the updated value directly to the reading cache, leaving the memory out of it entirely? The MOESI protocol does this by splitting the Modified state into two, similarly to MESI:
- **Modified** - This line is held *exclusively* in this cache and memory has an outdated value
- **Owned** - This line is *shared* between multiple caches but memory is also outdated; this cache is the one that provided the updated value.

![](Pasted%20image%2020240212112600.png)
In this protocol, the core that owns the cache line is allowed to make any modifications it wants to the line and stay in the Owned state; it simply sends the updated value along the bus, and any cores sharing the line take the updated value. We only need to perform a writeback if the data in an Owned or Modified line is "evicted" - for example, if another cache modifies the line.

# Directory-based Protocols
Even with the optimisations that MESI and MOESI introduce, shared bus coherence protocols do not scale well to a large number of cores. We need a coherence scheme that works with a less directly connected network (for example, a grid).
One possible solution for this is to have a **centralised directory**, which handles all of the cache line information in one place.
![](Pasted%20image%2020240212114912.png)
In this scheme, the directory is just another component connected to the network; this allows any network to be used for interconnection, including ones that take variable hops between nodes, or those that use packet switching.
Each line has an entry in the directory which stores:
- Present bitmap - A series of bits (1 bit per core) that indicates which cores hold a copy of this line
- Directory dirty bit - A bit that is set when **one** of the cores has a copy of the line that is out-of-sync with memory

Lines in the individual caches also hold some extra information:
- Local valid bit - Is the line valid? (essentially the inverse of Invalid from M(OE)SI)
- Local dirty bit - Is this copy of the line out-of-sync with memory?

## Behaviour
### Read hit
![](Pasted%20image%2020240212115756.png)
If core 1 wants to read, and its local cache holds a valid value, then it can just use that value. No other components are involved.
### Read miss
![](Pasted%20image%2020240212120024.png)
Core 1 wants to read, but its local cache doesn't contain the desired line, or it is invalid. It sends a request to the directory, and a few cases may unfold:
- Line not present in another cache - The directory tells the core to get the line from memory, and updates the bitmap to indicate that core now contains a copy.
- Line present in another cache, directory dirty bit unset - The directory tells the cache with a copy to send it to the requesting cache, and updates the bitmap to indicate that cache now contains a copy.
- Line present in another cache, directory dirty bit set - The directory tells the cache with the dirty line to writeback to memory, clear its local dirty bit, and to send the updated value to the requesting cache. The directory then unsets the directory dirty bit and updates the bitmap to indicate that cache now contains a copy.

### Write hit
Core 1 wants to write, and its local cache contains a copy of the line. Again, there are a few cases:
![](Pasted%20image%2020240212120950.png)
- Local dirty bit set - Just update the local cache, and change no flags.

![](Pasted%20image%2020240212121344.png)
- Local dirty bit unset, directory dirty bit unset - Update the value in cache, set the local dirty bit, set the directory dirty bit, instruct every core that contains a copy (per the present bitmap) to invalidate it, and update the bitmap to indicate core 1 contains a copy.

- Local dirty bit unset, directory dirty bit set - This case **cannot happen**, as the line being both valid and having the local dirty bit unset means that the value is shared. There cannot be a unique owner, and therefore the directory dirty bit cannot be set.

### Write miss
Core 1 wants to write, but its local cache doesn't contain the line. Again, this has a few cases:
![](Pasted%20image%2020240212121623.png)
- Directory dirty bit unset - The directory checks (in the present bitmap) if any cores have a copy.
	- Other core has a copy - The directory orders one of those cores to send a copy of the line to core 1, then invalidates the line in them all. The bitmap is updated to show the line as only present in core 1, and the directory and local dirty bits are set.
	- No other core has a copy - The directory orders core 1 to retrieve the line from memory. The bitmap is updated to reflect core 1 as possessing the line, and the directory and local dirty bits are set.

![](Pasted%20image%2020240212122444.png)
- Directory dirty bit set - The directory orders the owning core to writeback to memory, clear its dirty bit, and invalidate its copy of the line. It also instructs the core to send the line to core 1, which then updates it and sets the local dirty bit. The directory dirty bit remains set, and the present bitmap is updated to reflect core 1 as the sole owner.

## Limitations
While using a directory protocol allows more scalable interconnects to be used, the directory itself is a serious bottleneck. There are further ways to optimise this, such as distributing the directory itself and using caching to speed *that* up, which becomes necessary for large-scale multi-socket systems. These **NUMA (Non-Uniform Memory Access) systems** may look something like this:
![](Pasted%20image%2020240212122817.png)
Compared to bus snooping protocols, directory based protocols generally have slower communications and long/variable delays. So, while they are a solution for scaling to large numbers of cores, they are not a panacea.
