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
Disks are not 100% reliable (at the bit level). Historically **CRC** has been used as a block code for error detection. More recently, **ECC** (e.g. Reed-Solomon or LDPC) have been used to accept and correct faulty bits.
When these detection methods are not sufficient, bad sectors will be remapped so that faulty parts of the surface aren't used.

## RAID
Redundant Array of Inexpensive Disks (originally).
Improved reliability can be achieved by either spending more on higher quality parts, or by employing redundancy to improve the fault tolerance of less reliable (cheaper) parts. This is the original aim of RAID (now called RAID 1).

#### RAID 1
Mirror the same data on more than one disk. When reading data, the data can come from any of the (working) disks. This increases fault tolerance, as only one of the many drives needs to have correctly stored the data in order to access it.
Using a RAID 1 array will not affect the performance of single requests, but it can increase bandwidth for simultaneous (parallel) reads.
Same performance, significantly more reliable, but sacrifices storage space.

#### RAID 0
Interleave the data on multiple disks. Improves performance, as it allows parallel access. However, it isn't redundant: If any one of the disks fail, the entire array is useless. 
More performant, doesn't sacrifice storage space, but significantly less reliable than a single disk.

#### Other RAID varieties
- RAID 2 - Like RAID 1 but with Hamming codes
- RAID 3 - Like RAID 1 but stripe on bytes instead of bits, and calculate parity for the bytes
- RAID 4, 5, 6 - IDK (not examinable)
You can also nest RAID arrays to get hybrid systems, for example:
- RAID 01 - a RAID 1 array of RAID 0 arrays
- RAID 10 - a RAID 0 array of RAID 1 arrays

## Connecting Disks
Generally connected to a computer using **SATA** (Serial AT Attachment, AT as in IBM PC-AT). SATA is a serial bus interface that can carry up to 6.0 Gb/s (600 MB/s). A spinning platter disk can usually manage about 100 MB/s.
You can also connect a drive using PCIe (in the form of an M.2 connection, commonly) for even higher speeds.

## Disk Caches
The hard drive will contain an SDRAM "disk buffer" which allows the disk to save pages, act as a write buffer, etc...

## SSDs (Solid State Drives)
Non-volatile semi-conductor stores, typically using 'Flash' memory.
Pros:
- Robust and quiet (no moving parts)
- Compact (e.g. flash drives, SD cards)
- Lower latency, random access
Cons:
- Long term data retention is (probably) worse.
- Finite number of write operations before the flash wears out.
