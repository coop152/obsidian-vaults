# Interconnects
You can broadly divide the topic of interconnecting blocks into two categories:
- Logical interfacing
	- Standard interfaces (AMBA, OCP, etc.)
	- The operation of the interface
- Electrical interfacing
	- Skew (Clock skew, bit skew for buses)
	- Round trip delays
	- Drive capacity

The latter set of topics is mostly saved for later weeks.
# Block Interconnection
In any sizeable SoC you are very likely to use IP (Intellectual Property) blocks to save time and effort.
When using IP blocks, standard interfaces are important. These cores are **black boxes**, so you have no control over how the core presents its interface - you need a way to fit it into your design in a consistent way, and standard interfaces are the means to this end.

## AMBA (Advanced Microcontroller Bus Architecture)
AMBA is an open standard (or a set of standards) which are by far the most popular standard on-chip interconnect. The standards specify what signals are used in the interconnection and their timing relationship on a cycle-by-cycle basis.
AMBA comes in several variations:
- APB (Advanced Peripheral Bus), which is simple but slow, and is intended for communications between rarely interacting peripheral devices.
- AHB (Advanced High-performance Bus), which is similar to APB but higher performance.
- AXI (Advanced eXtensible Interface), which is faster still.

APB and AHB are **shared buses**, so they connect multiple devices together. This makes them suitable for uses such as a bus connecting many peripherals (e.g. RTC, timer, GPIO pins, etc.) to a microcontroller but unsuitable for low latency applications such as a data bus to memory.
AXI is a 