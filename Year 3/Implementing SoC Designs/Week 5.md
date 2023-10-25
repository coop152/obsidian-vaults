# Timing & Clocking
## The synchronous model
Using a clock to synchronise all operations in a functional block leads to significant simplifications in design. It allows you to use an instantaneous "snapshot" of the unit's state to derive the following state (from both the unit's current internal state and the synchronous inputs.)
Finite State Machines, for example, are very simple to design with a clock:
![](Pasted%20image%2020231025090259.png)
There is work to do if you want a synchronous design:
- Everything must be kept synchronous
- Everything must fit within the clock period
- 