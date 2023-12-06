# Design to production
## Packaging
A significant constraint on a chip is the number of external contacts it can have. These may be referred to as "pads" (specifically, the contact position on the silicon) or as "pins" (specifically, the metal contact outside the package).
A traditional chip will have a "pad ring" around its periphery. Each pad occupies significant space because it has to be large enough for a bonding machine to attach a wire. It will also contain amplifiers for driving the (much larger) off-chip loads, and significant electrical protection. The pads will also be linked to the internals with large tracks, to aid in power supply distribution.
A very small device may be "pad limited" if, once the pads are placed and spaces, the central area afforded for the "core" is not filled.
![](Pasted%20image%2020231206111538.png)
Because silicon is expensive, smaller chips are much cheaper, so there may be a desire to reduce the pin count and overcome this pad limit. This is a trade-off the designer must consider.
**Example:** Microcontroller chips such as the Raspberry Pi feature many peripheral interfaces, but it is likely that not all of these are wanted by any specific user. Therefore, these IO connections are commonly multiplexed so that particular pins can serve different purposes depending on the user's choosing. For example, a user may choose to install a fan, in which case they can configure two of the GPIO pins to act as a PWM fan header.
This also has the benefit of making the device less application-specific, so fewer devices have to be designed, built and tested in general.

Due to the limitations of pad ring, there is a modern trend toward "flip-chip" mounting. This method involves connecting the external contacts to "microbumps" protruding from the top of the circuit (i.e. above the highest metal wiring layer). The chip is then mounted "upside down" in the package, this grid of bumps connecting with contacts on the package.
This has some advantages:
- More contacts can be made, because more than just the edge is used
- Wiring from the chip to the PCB can be shorter

Taken together these can have great effect. As the number of wires needed increases, the problems of increasing current demand and electrical noise due to inductance become significant. Shorter wires can resolve these issues.

**Note:** flip-chip is not the same as BGA mounting; While the idea is similar geometrically, BGA is between a package and a PCB (larger relative scale) while flip-chip is between silicon and package.

## Simulation
### Mixed Simulation
It is infeasible to simulate a whole SoC with max precision in a single run. Instead, we use a variety of simulators for different blocks according to the required precision. This allows us to optimise the precision to cost/time trade-off to a more reasonable level.
![](Pasted%20image%2020231206115117.png)
In some cases, the required precision may be so low as to allow a Transaction Level Model (TLM).
### SPICE
"SPICE" (Simulation Program with Integrated Circuit Emphasis) is the generic name for a variety of analogue circuit simulators, e.g. SPICE, HSPICE, Spectre. 

A SPICE takes as input a component netlist, an input stimulus file, and a detailed model of the characteristics of the components. It simulates circuit activity by solving differential equations, giving an accurate model of the circuit's behaviour when fabricated. This takes significant processing time, so SPICE simulations are typically limited to small circuits and limited time runs.

A typical use of SPICE is to **characterise** standard cells, producing simplified models to be used in larger analogue simulations. For example, a NAND gate may be "Spiced" to determine the propagation delay, output edge speeds with a certain input with a certain input edge timing, etc.
The feedback from this process will be used to tune the performance of a layout - for example to determine the transistor widths for different cells.

SPICE may not be needed by a high-level designer, but it will be used somewhere in the design flow and it becomes necessary when designing analogue or other custom circuits.
### Process corners
Due to variation in the manufacturing processes, transistor properties in a chip will vary. This causes each chip to function in not quite the same way.
The operating environment of the chip will also vary. The two greatest variables will be the supply voltage and the temperature; lower voltages or higher temperatures will slow down switching.
These variations are normally classified as "PVT" (Process, Voltage, Temperature) variations.

It is important that a chip works in a wide range of conditions. Thus, physical simulation will normally be carried out over a number of process "corners":
- Extreme variation in transistor characteristics (fast-fast, fast-slow, slow-fast, slow-slow, typical) due to process
- Voltage variations of a high amount (e.g. 10%)
- Temperature ranges from as low as $-40\degree C$ to as high as $125 \degree C$, depending on the intended environment of the chip

The device should function completely as intended over these variations.
This involves running a whole set of (ideally) *functionally identical* simulations using a timing-accurate simulation, which will not be computationally cheap.
## Design and Production Flow
![](Pasted%20image%2020231206120556.png)
This is a simplified diagram showing how a chip design goes from an initial behavioural model to a manufactured and tested ASIC, ready to be used. These steps in particular are of interest:
### Place and Route (P&R)
**Placement** (in this context) is the act of mapping cells onto the surface of the silicon so that they sit near each other as necessary, but do not overlap. Ideally, cells which are connected together ought to be placed adjacently though this is not always possible. There will likely be constraints that prevent cells from being placed in the ideal place.
Finding the optimal placement is prohibitively expensive, but finding something that approaches it is desirable because of how much easier it makes routing. It can also increase performance and reduce power consumption by shortening the interconnection wires.
The "ideal" placement would have all of the cells clumped tightly together, but this is not feasible. In some cases this could make routing entirely impossible, and some extra space has to be left for later modifications such as **buffer insertion**. Therefore, *utilisation* cannot be 100%.

After placement is complete, the **routeing** process takes place. Routeing consists of trying to interconnect the placed cells with wires. Obviously wires cannot touch or cross, but there will be a number of wiring layers available. The preference is to use lower layers only as much as possible - especially for local interconnection - as it minimises parasitic load capacitance.
If the placement and wiring is too dense then it may not be possible to complete routeing. In this case, the user must relax the utilisation/area constraints to make some space.
### Floorplanning
