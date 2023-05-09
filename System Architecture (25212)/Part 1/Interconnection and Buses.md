## Interconnection
In this example, "bus" refers to the logical bundle of connections between the processor(s) and memory. e.g. address line, read and write lines or data line, enable line.

![](Pasted%20image%2020230509112322.png)

In general, a bus is a bundle of connections that connect many components together, for example the buses connecting the processors to memory, IO, the DMA controller, the graphics system (i.e. GPU).

A single shared bus is simple, and it scales to more chips easily, but it becomes a bottleneck due to the number of chips sharing it:
![](Pasted%20image%2020230509112353.png)
A crossbar offers parallelism which improves performance vs a single bus, but it scales very badly to larger amounts of chips ($O(n^2)$ with regards to the number of chips):
![](Pasted%20image%2020230509112412.png)
Network on a Chip is a rectangular grid pattern well suited to a silicon layout. Offers improved performance over a shared bus, but doesn't scale as badly as crossbar. Is more architecturally complex, however:
![](Pasted%20image%2020230509112526.png)

## Bytes and Words
- A byte is usually the smallest addressable unit (for example, in ARM)
	- Stable definition as 8 bits. Sometimes called an octet.
	- Originally a character.
- The size of a "word" depends on the machine.
	- Usually the "natural" data size of the architecture
	- But increasingly refers to 32 bits (4 bytes).
	- Leads to other definitions such as 
		- Halfword - 16 bits
		- Doubleword - 64 bits
		- Quadword - 128 bits
		- etc. etc...
- Widths of buses aren't always what you might expect

## Alignment
- Buses have particular widths, such as 32 bits or 64 bits
	- The width of a bus is the size of a data transfer
- Misaligned operations will be slower, or maybe even malfunction
	- Therefore, keep data at appropriate addresses that lie on the right boundaries
	- Depending on the ISA, this also applies to the code itself (e.g. x86 with its variable length instructions)
Imagine trying to read something from memory that is contained in two separate words. You can only address one of the words at a time; you need to do two reads and then stitch the results together to get what you wanted!
![](Pasted%20image%2020230509112852.png)
Some ISAs may not even try to give you the result you wanted; if you do an unaligned read on arm, it will simply shift the first word it received from memory such that you can access the least significant byte. Not good if you expected all 4 of those bytes.

#### Code Alignment
- Some architectures may enforce code to be aligned to word boundaries. e.g. RISC architectures with fixed length instructions
- However, some others with variable length operations can't feasibly do it. e.g. x86 with its variable length instructions. Compilers may still try though, as there is a performance gain to be had.

## Endianness
The order that bytes are arranged in a word.
**Little Endian**: Least significant byte at the lowest address.
**Big Endian**: Most significant byte at the lowest address.
Becomes confusing when mixing transfer sizes

## Von Neumann & Others
Most mainstream processors are von Neumann. At least, from a software perspective; Not always at an architectural level.
Harvard architectures have separate address spaces, e.g. Code, Data, Constants.
A common approach is to present a Harvard interface to the processor, but keep a von Neumann model for the programmer. The memory has a "soft" partition that can be moved. This allows the flexibility of von Neumann for the programmer with some of the improved performance of Harvard,
(IMAGE OF THE NEUMANN/HARVARD CACHE THING FROM THE SLIDES)

## System Buses
(IMAGE OF NORTHBRIDGE DIAGRAM FROM THE SLIDES)
- The buses form a hierarchy with the memory
	- Wide buses to increase bandwidth
	- Slower I/O is lower down the hierarchy, past more buses and bridges

## Recap: Info carried by Buses
- Operation
	- NOP/write data/read data/fetch instruction, etc.
- Address
	- Desired location in memory, may include other lines such as byte selection as well as word selection
- Data
	- Holds the data. May be bi-directional.
- Privilege
- Ready
- Abort