## Random Access
Strictly speaking, in RAM any address should get the same treatment as any other. Access time should be *constant*, regardless of previous accesses or absolute location (but in practise this isn't always true).
This is in contrast to **serial access memory** (e.g. a tape reel) which has drastically varying access speeds depending on the address of the last access vs the current one.
## Static RAM (SRAM)
- Truly random access.
- Fairly compact.
- Low power, especially when not in use.
- Volatile: only holds data while powered.
- Can be manufactured with standard logic gates, making it amenable for use on logic chips.
- Extensively used.

Applications include:
- Working RAM in 'small' systems (e.g. microcontrollers)
- Cache memory in 'big' systems (e.g. regular computers)
- Buffer memory in interfaces (e.g. USB, ethernet)
- Temporary storage in hardware (e.g. FSMs)
- And others.
SRAM is a standard architectural component.
## Multi-port SRAM
SRAM macrocells with many buses, capable of simultaneous access are feasible.
However, the cost is high:
- Area per bit may be doubled (due to the duplicated wiring)
- Energy per access is also increased
- Speed reduced (by around 30%?)
Not usually worthwhile compared to time-division multiplexing.
FPGAs often provide this feature already/.
## Layout implications for RAM
Bigger memories require longer wires, so they're slower and more power-hungry. Therefore, **bigger is slower, smaller is faster**.
The usual logical representation of RAM is quite "long and thin"; you generally imagine it as one long contiguous string of data. In reality, the physical construction of ram is closer to a square: (logical model on the right, "real" shape on the left)
![](Pasted%20image%2020230216134343.png)
