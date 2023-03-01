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
	- In addition to that, (on average) half of a rotation's time.
- Bandwidth
	- Depends on the disk rotation speed and area density.
	- Reduced significantly by **fragmented** placement.
