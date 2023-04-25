## Reminders
- The difference between Snoopy and directory-based cache coherence protocols
	- **Global view** vs **local view** with a directory
	- **Minimal info** vs **extra info** for directory and remote shared lines
	- **Centralised communications** vs **parallel communications**
	- **Poor scalability** vs **better scalability**
- The concept of **false sharing**:
	- A behaviour that arises when two unrelated variables are stored in the same cache line
	- If both variables are written by two different cores often, they will generate a lot of invalidate/update traffic

We've seen a concrete implementation of a Snoopy cache coherence protocol, but how would a directory-based one work? What would be connecting all of the chips?
## On-chip Interconnects/Networks
Any multi-core system must clearly contain the means for cores to communicate - both with memory and with each other, for synchronisation and coherence. There are many ways to connect these components, each having different characteristics and tradeoffs (performance, energy, area, fault-tolerance, scalability...). Each one may not have the same 
functionality, which can restrict the coherence mechanism that you must use with it.
In addition to the base architectural requirements that the interconnect must fulfil (e.g. cache coherency, core synchronisation) it also needs to be effective at allowing programs to communicate between cores; most multi-core applications will require the cores to communicate in some manner, and what constitutes "effective" depends on the context.
#### More recap
![](Pasted%20image%2020230425104435.png)
#### Distributed memory applications
- Instead of having many cores sharing one memory, each core has its own memory independent from the rest
	- This gives **no coherency** at the processor level
	- Chip area is saved
- Communication and synchronisation is explicit in the code, through **message passing**.
	- This needs to be handled efficiently to avoid becoming a bottleneck
- The interconnection network becomes an important part of the design, which much of the performance hinges on.
- An example of this is the Intel Single-chip Cloud Computer, or the Xeon Phi (which was cache-coherent)
## Evaluating a network
- **Bandwidth**: How much data can it move per unit of time?
- **Latency**: How long does it take a single piece of a message to traverse the network?
- **Congestion**: How are bandwidth and latency impacted when the network is close to peak usage?
- **Fault tolerance**, **Area**, **Power dissipation**, etc...

For example:
A truck carrying one million 256GB USB drives from Manchester to London
- Latency = 4 hours
- Bandwidth = 128Tbit/s (128 * 10$^{12}$ bits/s)

An average broadband internet connection
- Latency = 100 microseconds (10$^{-4}$ sec)
- Bandwidth = 100Mbit/s (10$^{8}$ bits/s)

#### Important Attributes of a Network on Chip
- **Topology**: How the cores and networking elements are connected together (like a regular network topology)
- **Routing**: How traffic moves through the topology (like regular network routing)
- **Switching**: How traffic moves from one component to the next (like regular network packet switching)

## Topologies
#### Bus
![](Pasted%20image%2020230425110226.png)
- Has one common wire interconnect - the **broadcast medium**
	- Only one thing can use the wire at any point in time
	- Controlled by a clock, so it must be divided into time slots
	- The sender must 'grab' a time slot (via arbitration) to transmit a message
- Often utilises **split transactions**
	- for example, one component sends a memory address in one time slot
	- the data is returned by another component in a later time slot
	- The time slots in-between those two are free for use by other components
- Main **scalability issue** is the limited throughput
	- The bandwidth of the bus is divided by the number of cores

#### Crossbar
![](Pasted%20image%2020230425110642.png)
- This structure will connect N inputs to N outputs
- Can transmit from any input to any output in **parallel**
- Main **scalability issue** is the area and power scale growing quadratically to the number of nodes

#### Tree
![](Pasted%20image%2020230425110939.png)
and **Fat Trees**
![](Pasted%20image%2020230425111003.png)
#### Ring
![](Pasted%20image%2020230425111021.png)
- Simple, but low bandwidth and variable latency
- Used in the Cell processor in the PS3

#### Mesh/Grid
![](Pasted%20image%2020230425111239.png)
- Reasonable bandwidth
- Variable latency
- Convenient for very large systems (in terms of physical layout)
- Examples include:
![](Pasted%20image%2020230425111351.png)
## Routing
#### Minimal vs non-minimal Routing
**Minimal**:
![](Pasted%20image%2020230425111448.png)
- Always select the shortest path to a destination
	- That is, packets will always move closer to their destination
- Packets are more likely to be blocked in this scheme, as there is no variance in route

**Non-minimal**:
![](Pasted%20image%2020230425111658.png)
- Packets can be diverted
	- to avoid blocking and to keep traffic moving
	- or, to entirely avoid congested areas
- There is a risk of a livelock, if the packets find themselves in a circular loop of avoiding each other and never moving to the destination

#### Oblivious vs Adaptive Routing
**Oblivious**:
![](Pasted%20image%2020230425111954.png)
- Packets are unaware of the network state
	- Can be deterministic, e.g. fixed path
	- Can be non-deterministic, e.g. variable path, which requires more complex strategies
- Results in a simpler router, which is deadlock-free
- However, is prone to contention

**Adaptive**:
![](Pasted%20image%2020230425112421.png)
- Packets are aware of the network state, and will adapt to avoid contention
- Provides higher performance due to the lack of contention
- However, is much more complex due to the required instrumentation, and is deadlock prone which requires even more hardware to remedy
- This is barely used in Network on Chips

## Switching
![](Pasted%20image%2020230425112651.png)
- Data is split into small packets, and each packet into **phits**
- Some extra info is added to the packets to identify the data and to perform routing
- This allows time-multiplexing of network resources, which generally leads to better performance (especially for short messages)
- There are several packet switching strategies, e.g. Store and forward, cut-through, wormhole...

#### Store and Forward
![](Pasted%20image%2020230425113115.png)
- A node will not forward a packet until all of its phits have arrived
- Provides **on-the-fly failure detection**
- However, is low performance (Latency = distance * phit count) and requires lots of buffering
- Good for long, bursty transmissions (e.g. the internet) but not so much for a Network on Chip

#### Cut-through/wormhole
![](Pasted%20image%2020230425113345.png)
- A node will forward a packet as soon as its header has arrived
- Provides better performance (Latency = distance + phit count)
- However, faults can only be detected at the final destination (this does mean less hardware at the intermediate nodes, though.)
- 

