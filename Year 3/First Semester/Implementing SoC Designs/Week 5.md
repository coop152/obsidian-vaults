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
Timing closure is the act of fitting all of the required logic into the desired clock period. It often dominates the later phases of chip design.
This is often assisted by **Static Timing Analysis** (STA) tools which deduce the logic paths by finding the registers, estimate the signal delays through all of the paths, and note the slowest.
![](Pasted%20image%2020231025091650.png)
Free time in a clock is called "slack" - negative slack means that your logic is too slow for the chosen clock period.

# Clock domains
![](Pasted%20image%2020231025092853.png)
Keeping a unit fully synchronous is a useful principle... but a modern SoC is usually a conglomeration of modules which may use different clocks. This has many benefits:
- The hierarchical design eases timing closure problems
- It facilitates designs to be created in parallel, without as much concern over the shape of the units
- The clock frequency of a unit may be varied dynamically for a power/performance trade-off
- Some units may *require* certain clock frequencies (e.g. Graphics devices vs USB transfers vs Radio transceivers, etc.)
- Some blocks may be completely shut off

## Crossing clock domains
If we have two synchronous domains with no certain frequency or phase relationship, then it is **impossible** to transfer signals with 100% reliability. At some point, the input setup/hold times of some flip-flop will be violated and the flip-flop will behave strangely.
The chance of these failures can be made very small with a **synchroniser**, which:
- Delays the input to give it a chance to resolve
- Also delays the output, for the same reason, hence increased latency

Here is an example of a synchroniser with two clock domains:
![](Pasted%20image%2020231025093822.png)
With this timing:
![](Pasted%20image%2020231025093846.png)

Not every signal needs synchronising, though. In a data bus the data can simply be latched by the receiver, as long as the control signals are synchronised.
![](Pasted%20image%2020231025094059.png)
Depending on what you are sending over the clock domain, there are many ways to reduce the impact of synchronisation:
![](Pasted%20image%2020231025094414.png)

# Time stealing
This is a mechanism that can alleviate the impact of a single slow pipeline stage. You selectively delay part of the clock distribution, which allows the slow step in the pipeline to finish but still allows the clock speed to be increased.
![](Pasted%20image%2020231025094723.png)
This is useful around slow macrocells such as RAM blocks.

# Clock Generation
Dividing a clock is easy; just make an FSM:
![](Pasted%20image%2020231025094923.png)
Clock multiplication (which is usually required) is much harder and requires a **Phase-Locked Loop** (PLL) or similar.
![](Pasted%20image%2020231025095038.png)
Given a reference frequency, the phase comparator compares that frequency to the output of the circuit. If they are different, it sends a signal to the low-pass filter, which passes it along to the voltage controller which will slightly slow down or speed up the clock. This is a partially analogue signal, which makes this a very specialised job.
Why do this?
- It is difficult to carry UHF signals across a PCB; great care with tracking is required.
- Switching signals dissipates power. The more you switch, the more it costs.
- Switching signals transmits Radio Frequency Interference (RFI). (Where do you think the power goes?) This is a Bad Thing, and may be illegal.
- Generating stable clocks at UHF directly is impractical.