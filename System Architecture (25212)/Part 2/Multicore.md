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

