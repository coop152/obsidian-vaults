## Parallelism
When you need higher bandwidth but you can't decrease the latency any further, you add more parallel lines to handle more data at once. Examples include:
- In buses, widths increase and the size of words in memory increases
- In PCIe, use more lanes
- In disks, use RAID 0
- In processors, use a superscalar architecture (multiple components such as ALUs or FPUs acting in parallel)
This can cost a lot (more wires, pins or space required).
## Interleaving
Dividing responsibilities of something between multiple of it. For example:
- RAID 0 interleaves blocks along multiple drives
- 