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
- **Switching**: How traffic moves from one component to the next

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

