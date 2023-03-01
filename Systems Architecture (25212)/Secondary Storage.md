Data storage that is outside the main address space.

## Disks
Disks rotate. Magnetic disks are usually in stacks, where both sides of each platter are used.
An arm with magnetic read/write heads move radially around the platter.
The bits stored on the disk are encoded by the polarisation of 'domains' on the surfaces.
The data is organised and indexed in blocks.
One drive might provide ~20TB of storage.
- Latency
	- Depends on the time taken to move the heads to the right track. Therefore, it depends on the starting point of the head and the desired track.
	- Smart organisation of data on disc can put important stuff "close".
	- In addition to that, (on average) half of a rotation to reach the start of the data.
	- Frequently required things may be placed close to the parking location of the heads to reduce latency.
	- Can be in the tens of milliseconds! (millions of instructions)
- Bandwidth
	- Depends on the disk rotation speed and area density.
	- Reduced significantly by **fragmented** placement.

#### Fault tolerance
Disks are not 100% reliable (at the bit level). Historically **CRC** has been used as a block code for error detection. More recently, **ECC** (e.g. Reed-Solomon or LDPC) have used to accept and correct faulty bits.
When these detection methods are not sufficient, bad sectors will be remapped so that faulty parts of the surface aren't used.