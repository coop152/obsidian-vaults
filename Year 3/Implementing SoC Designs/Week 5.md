# The synchronous model
Using a clock to synchronise all operations in a functional block leads to significant simplifications in design. It allows you to use an instantaneous "snapshot" of the unit's state to derive the following state (from both the unit's current internal state and the synchronous inputs.)
Finite State Machines, for example, are very simple to design with a clock:
![](Pasted%20image%2020231025090259.png)
There is work to do if you want a synchronous design:
- Everything must be kept synchronous
- Everything must fit within the clock period

## D-type flip flop recap
D-type flip-flops are units of memory, that store a piece of data according to the clock. They are often grouped together to make registers.
![](Pasted%20image%2020231025090550.png)
- t_su - Setup time, how long data takes to become stable before the clock
- t_hold - Hold time, how long data must stay stable after the clock
- t_pd - propagation delay, how long data takes to start being output after the clock

## Clock Distribution
The model assumes that clock is distributed in such a way that synchronisation is maintained. The clock has a very high fan-out due to it's connection to essentially every component in a synchronous unit, and requires amplification/buffering to keep signal strength up and keep clock aligned across the unit.
When clock becomes misaligned across the unit due to distance, we get **clock skew**.
![](Pasted%20image%2020231025091127.png)
The H-tree layout is a common way to distribute clock that keeps the distance between each endpoint equal, thus minimising clock skew.

# Timing closure
Timing closure is the act of fitting all of the required logic into the desired clock period.
This is often assisted by **Static Timing Analysis** (STA) tools which deduce the logic paths by finding the registers, estimate the signal delays through all of the paths, and note the slowest.
![](Pasted%20image%2020231025091650.png)
Free time in a clock is called "slack" - negative slack means that your logic is too slow for the chosen clock period.
