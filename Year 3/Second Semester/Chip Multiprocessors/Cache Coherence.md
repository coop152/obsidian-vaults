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
At first this may seem like a minor optimisation, but the "unshared Shared" case is very common in most programs. Consider that every single thread-private variable is never shared between cores, and in most cases these are the majority of variables.