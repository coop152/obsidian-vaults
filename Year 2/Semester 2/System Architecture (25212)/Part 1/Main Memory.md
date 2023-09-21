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
- Main memory in 'small' systems (e.g. microcontrollers)
- Cache memory in 'big' systems (e.g. regular computers)
- Buffer memory in interfaces (e.g. USB, Ethernet)
- Temporary storage in hardware (e.g. FSMs)
- And others.
As you can see, SRAM is a standard architectural component with a wide variety of usecases.
## Multi-port SRAM
It is feasible to make "macrocells" of SRAM with many buses, which are capable of simultaneous access.
However, the cost is high:
- Area per bit may be doubled (due to the duplicated wiring)
- Energy per access is also increased
- Speed reduced (by around 30%?)
Not usually worthwhile compared to time-division multiplexing.
FPGAs often provide this feature already.
## Layout implications for RAM
Bigger memories require longer wires, so they're slower and more power-hungry. Therefore, **bigger is slower, smaller is faster**.
The usual logical representation of RAM is quite "long and thin"; you generally imagine it as one long contiguous string of data. In reality, the physical construction of ram is closer to a square: (logical model on the right, "real" shape on the left)
![](Pasted%20image%2020230216134343.png)
## Content Addressable Memory (CAM)
Also known as "associative memory".
In RAM, an address is the input and some data is the output. In CAM, the data is the input and the address is the output.
As it has a different function to RAM, it is only used for special use cases such as:
- Highly associative caches
- Packet routing in networks
- AI/ML acceleration
## Dynamic RAM (DRAM)
- Not completely randomly accessible.
- Very compact, best bits per unit area of any kind of 'RAM'.
- Moderate power usage.
- Volatile, **and** must be continuously refreshed to hold data
- Requires a specialised manufacturing process, can not be made with regular logic gates
- Extensively used.

Note that the 'SDRAM' featured later is a kind of DRAM. The S does not stand for static!
![](Pasted%20image%2020230222112020.png)
![](Pasted%20image%2020230222112140.png)
The row and column addresses are parts of the input address. When something is read from DRAM, the entire row of data is actually destroyed, but not before it's sent to the data interface. The data is sent through some latches, which save the value temporarily and allow it to be written back. 
#### DRAM Timing
![](Pasted%20image%2020230222112706.png)
- Latency is significantly reduced in DRAM if using an already 'open' column.
- The easiest way to exploit this 'feature' is using burst access (i.e. reading from consecutive addresses). This depends on the address bits being allocated appropriately.
- Burst access is appropriate for much code behaviour. Most of the time, instructions will be fetched in a continuous stream.
- Burst access is also excellent for filling cache lines.

## Synchronous DRAM (SDRAM)
The modern way of interfacing with DRAM. Provides a clocked wrapper ('synchronous') around the DRAM to make timing/interfacing easier.
This provides high bandwidth burst access, but the long latency and scheduling complications remain.

#### Banks
![](Pasted%20image%2020230222114148.png)
An SDRAM chip may contain multiple DRAM arrays, called 'Banks'. These banks operate largely independently, allowing multiple operations to interleave. The interface is still shared so operations cannot truly be done in parallel, but there is still a speed improvement.

#### Timing
![](Pasted%20image%2020230222114849.png)

#### Controller
![](Pasted%20image%2020230227141341.png)
The controller is a unit which translates instructions from a processor or other component into the appropriate sequence of commands for SDRAM chips. They will be programmed with the RAM's timing characteristics and clock speed in mind, allowing them to sequence commands at the correct times. An SDRAM controller will be able to schedule (and interleave) multiple RAM requests with high efficiency.

One SDRAM "module" may be servicing requests from multiple sources, such as multiple processors in a computer or multiple computers on a network.

#### Addressing
In true **Random Access** Memory, the location of each address bit is irrelevant. However, it matters with (S)DRAM.
![](Pasted%20image%2020230227142644.png)
Significant is that the column bits are low in the address; this allows consecutive address accesses to be translated into bursts. 

Bank bits may be better placed lower in the address, assuming the given addresses are expected to "walk" linearly through the address space. This allows the banks to precharge while the others are being accessed. 

However, it may be preferable to have the bank bits high in the address if servicing a multi-core system, as it may be possible to access multiple banks "simultaneously".

#### Line closure
You've just read an SDRAM burst. Do you:
- Close the row, ready for the next operation?
	- This would reduce latency for the next access (cause you'll be ready to change row)
	- But if the next access is on the same row the latency will be high.
- Leave the row open and hope that the next access is on the same row?
	- If your access is indeed consecutive, latency will be reduced.
	- If it isn't, latency is increased.

#### Address Alignment
It is likely that SDRAM data bursts will be **address-aligned**. This property may be visible to the user. If you assume an SDRAM burst size of, for example, eight 32-bit words:
- Starting at address `0000_1000` fits everything in one burst.
- Starting at address `0000_1004` needs two bursts, with 8 words discarded.
Cache fetches will always be aligned.

#### Circuit Layout
![](Pasted%20image%2020230227141803.png)
All of these components come together to provide a public interface that allows processors or other chips to interface with the underlying DRAM array as if it were truly random access memory, like SRAM.

This PCB-level interface can easily impose latency of a clock-cycle or more, both ways. To allow both sides of the exchange to sync up, the controller sends its clock with the outgoing signals and the SDRAM returns its own clock with the incoming signals. The frequency of the communications is guaranteed, but the latency is uncertain, so the controller has to resynchronise the incoming data. 

It is vital that the **skew** (time difference) amongst signals in the interface stays small. To remedy skew, PCB designs may include twists and turns in the traces in order to delay certain signals, like this:
![](Pasted%20image%2020230227142457.png)

#### Miscellanea
There are a few more things the controller must do:
- Refreshing
	- DRAM forgets data if you don't periodically 'refresh' it. This is done by activating and then precharging every row in every bank every few milliseconds.
	- This doesn't require a read.
- Interface timing calibration
	- The controller has to synchronise the returned data with its internal delays. This can cause an operational hiatus, which is likely to impact performance in a somewhat random manner.


## Memory Reliability
It is not uncommon for (cosmic) radiation to hit memory and change the value of a bit. Well, it is uncommon, but there's so much memory in a computer that the chance becomes quite significant; the SpiNNaker loses multiple bits per hour in SRAM to radiation. Obviously, this isn't conducive to correct function of a device.

You can employ Error Correction Codes (ECC) to detect and (usually) correct bit errors automagically. You might also implement a parity scheme; that is, use an extra bit per byte or word which encodes if the labelled data is even or odd, allowing the detection of an odd number of bit flips. Parity is bad, however, so you would never actually do this.

#### Error Correction Codes (ECC)
There are many types of error correction codes, but the ones covered here are **block codes**. Examples include:

**Triple Modular Redundancy** (TMR): Repeat every value three times and take the most common value.

**Hamming Codes** (also called ECC): A more complicated method that has less overhead and is also used for memory protection sometimes.

#### Hamming Codes
Group up data bits in different patterns. For each group, add "check" bits which force a known parity for that group. Assuming the bit flip was only a single bit, you can find the location of the error and fix it.
![](Pasted%20image%2020230227145715.png)
For longer bit sequences:
![](Pasted%20image%2020230227150428.png)
Note the pattern. I can't explain it in words so just look really hard at it idk

Hamming codes are fairly cost effective for memory protection: The overhead shrinks as the data size increases.
![](Pasted%20image%2020230227150941.png)
