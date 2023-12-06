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