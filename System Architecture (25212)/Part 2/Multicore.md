## Moore's Law
Moore conjectured (in 1965) that every two years, the number of transistors in a processor would double. This translates to a doubling of processor performance roughly every 18 months. This conjecture was driven mainly by the shrinkage of circuits; as technology developed a transistors got shorter, meaning more can fit in the same space, connections within the chip got shorter, and capacitance got lower.
This "law" has been true for about 50 years, but it is coming to an end - the number of transistors is still doubling, but it gets harder to accomplish every year. Performance cannot keep pace by just shrinking the circuits.
![](Pasted%20image%2020230418112129.png)
## Why not just make a single core faster
- Caches can minimise the impact of memory accesses
- Pipelines and superscalar architectures can increase the amount of parallelism supported by the processor, maximising use of the hardware
- Out-of-order execution and Multithreading can increase the amount of independent instructions per cycle
- And other mechanisms (e.g. branch prediction, forwarding, register renaming) can minimise the impact of the hazards which are introduced by these methods

But we have seen that these methods all have limited scalability:
- Scaling a chip physically beyond a certain point is not practical
- Hardware, power and cost increase more than linearly...
- ... but performance increases less than linearly, and is limited by the applications that are run on the processor
- **Power Wall** - As power density increases, cooling becomes a serious problem
- **ILP Wall** - Some applications have limited parallelism
- **Memory wall** - Memory does not get faster at the same rate as processors
- **Unreliability** - Smaller transistors have less predictable characteristics
- **Design complexity** - At some point, architectural innovation hits upon the complexity of the designs, causing problems

#### The power wall
The power dissipation (that is, as heat) depends on clock rate, capacitive load and voltage:
$$\text{Power} = \text{CapacitiveLoad} \times \text{Voltage}^2 \times \text{Frequency}$$
Higher clock frequency means more power dissipation. Lower voltage reduces the dynamic power consumption, but it increases the static power leakage from the transistors, which is becoming the main contributor to power consumption due to the rapid shrinking of transistors.
Without extreme cooling, microprocessors cannot move past this wall. Extreme cooling isn't practical for many devices such as laptops or phones, and even for larger devices such as desktops or servers it is unreasonable to expect every device to be equipped with expensive cooling.
#### The ILP Wall
When executing one thread, the implicit parallelism is limited in many applications. Many of the previous mechanisms we have studied are reliant on this, which poses a problem for performance. One way to express this is **Amdahl's Law**, which estimates a parallel system's **maximum performance** based on the available parallelism of an application:
$$\text{Speedup} = \frac{S + P}{S + (P/N)}$$
Where
$S$ is the fraction of code which is serial
$P$ is the fraction of code which can be parallel (that is, $S + P = 1$)
$N$ is the number of processors
This formula was originally given as a way to discourage the development of parallel architectures, as it demonstrates the poor scaling of them when even a small percentage of instructions are serial:
![](Pasted%20image%2020230418114124.png)
#### The memory wall
From the mid 80's to the mid 2000's, CPU speed was increasing over 50% per year. However, memory was lagging behind at only 10% per year.
![](Pasted%20image%2020230418114224.png)
This posed a problem in that processor utilisation was being increasingly capped by the need to fetch data from memory. In memory intensive applications, CPU utilisation may be as low as 10%.
![](Pasted%20image%2020230418114337.png)
## A solution
Put more "cores" (CPUs) on a single chip, and use these cores in parallel to achieve higher performance. This is simpler to design than adding more complexity to a single processor, and it introduces a tempting idea: if you need more computing power, just add more cores. But is it that easy?
## Putting the cores together
How do we assemble these CPUs into one cohesive unit? We could:
- Have independent processor/memory pairs (**Distributed memory**)
	- Each core has its own independent memory
	- Communication/synchronisation must be coded explicitly
	- An example is Intel's 48-core single-chip cloud computer (2009)
- Have **shared memory**
	- This is generally accepted as the right answer
	- All processors have the same view of memory
	- Communication/synchronisation is implicit - makes programming easier
	- An example is any modern traditional x86 chip (Intel Xeon, Core, Pentium, AMD Ryzen, Epyc)
	- When considering more than a few cores, shared memory becomes difficult
