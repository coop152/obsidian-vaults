# Power Distribution
As devices shrink, the energy demand per operation reduces. Roughly speaking:
- Transistor gate length decrease == reduced capacitance == reduced energy demand
- Transistor gate width decreases == reduced capacitance == reduced energy demand
- Transistor gate spacing decreases == increased capacitance == increased energy demand
- Local wiring length decreases == reduced capacitance == reduced energy demand
- More units integrated on chip == more energy demand on the chip == increased power density
- Clock speed increase == increased *power* demand (not energy)
- Operating voltage decrease == decreased energy demand

## Definitions
$V_d$ is the voltage of the transistor's drain.
$V_s$ is the voltage of the transistor's source.
You double the subscript to refer to the supply that these are connected to:
$V_{dd}$ is the voltage of the drain supply (also known as "power").
$V_{ss}$ is the voltage of the source supply (usually interchangeable for "ground", but sometimes a "low" supply rail)
Sometimes $V_{cc}$ is used (incorrectly) in the place of $V_{dd}$. This is technically correct when talking about bipolar transistors, but does not apply to CMOS transistors.
## Voltage Drop
An active circuit always has some demand for electric current: The amount depends on the circuit's capacitive load and switching rate. Load can be **extracted** from the layout; the **activity factor** ($\alpha$) for the logic can be estimated, or derived from the activity in a functional simulation.
The power distribution network is made of metal wires which all have some inherent **resistance**. Because of this, there is an unavoidable **voltage drop** from the supply to the actual circuit (typically power is supplied from the chip's periphery, in a layout called *pad ring*.)
Therefore, the supply voltage at the circuit will be less than the original specification. The circuit will be operating under (slightly) different conditions from those intended.
These differences must be kept in controlled bounds for the system to work correctly. CAD tools can *estimate* voltage drop, providing a map where problems may occur. The response is to supplement wiring in those areas, but in some cases a completely different floorplan may be required.
## Decoupling
CMOS' power demand varies with the clock cycle: Following a clock edge there is a lot of switching, which tends to diminish until the next clock edge. This means the current in the power rails is not constant.
**Self-inductance** of power supply wires creates resistance to changes in current flow, meaning a local demand for current will cause a drop in supply voltage (plus a complementary spike as it switches 'off' again). This can be suppressed using 'decoupling' (or 'bypass') **capacitors** along the power rails - as close to the demand as possible - to act as charge 'tanks' that absorb the variations. It is difficult to build sizeable capacitors on chip, but typically there will be many capacitors scattered on the circuit board close to the device.

# Dynamic Power Dissipation
Dynamic dissipation is caused by switching gates. Therefore it is proportional to the clock frequency; if you double the frequency then the gates switch twice as often and power is doubled. It is also related to the activity of the circuit, as not every gate will switch on every cycle and some logic blocks may idle for long periods.
Dynamic dissipation in CMOS circuits is made of two parts: The first is the load capacitance of the network. To switch a node 'high' it is connected (via a pull-up tree) to the power supply and charge flows from there into the net until the voltage equalises. This charge is defined by:
$$Q =C.V$$
When the node discharges (via the pull-down tree) the charge flows to ground. The effect of the cycle is to move charge (Q) through the potential between the rails (V), so the energy dissipated per cycle is:
$$E=Q.V=C.V^2$$
This doesn't depend on edge speeds, etc. Note that this is proportional to the **square** of the voltage, so halving the supply voltage will quarter energy dissipation.
The other part of dynamic dissipation comes from "short circuit" or "crowbar" current which results from the brief moments where both the pull-up and pull-down trees are conducting (to some extent) as one turns off and the other turns on. This is difficult to quantify as it depends on a number of factors including transistor widths and the direction that transistors are switching. There are two 'simple' ways to reduce this:
- Keep input edges fast - reduce the time both trees are on simultaneously. This can be done by increasing the 'strength' (transistor widths) of the input gates, however that in turn increases their capacitive loads and slows their own inputs down.
- Use high threshold transistors - these turn on later and off sooner, minimising time spent when charge is wasted. This will also slow down switching and thus increase the propagation delay of the gate. This is best employed on gates off the critical path.

## Static Power Dissipation
![](Pasted%20image%2020231128142258.png)
# Transistor threshold
A CMOS transistor can be regarded as a switch (for digital circuits).
The threshold of a transistor is the gate voltage where it switches from 'off' to 'on'. This voltage is measured from the gate to the channel - for PMOS this is between the gate and drain ($V_{gd}$) and for NMOS this is between the gate and source ($V_{gs}$).
![](Pasted%20image%2020231128142649.png)![](Pasted%20image%2020231128142656.png)
![](Pasted%20image%2020231128142705.png)
This threshold can be determined when the transistor is manufactured. Different threshold devices can be constructed by applying different dopant concentrations; you can mix different thresholds of transistor on a single chip.
![](Pasted%20image%2020231128142853.png)
Low threshold transistors pull the NMOS' 'on' earlier and the PMOS' 'off' later so that switching occurs soon after the gate voltage changes. This leaves a significant time where they're both 'on', allowing current to flow between the two power rails. This wastes energy, but is fast.
High threshold transistors pull the NMOS' 'on' later and the PMOS' 'off' sooner so that there is little to no time where both are 'on', minimising the current that flows between the power rails. As a consequence it takes longer for the transistors to switch. These are slow, but they are low power.

Note that inputs to a transistor should always be held at some logic level. If the input is left floating then it has a very high impedance and might adopt any voltage. If that voltage happens to fall between the logic levels then both transistor stacks may turn 'on' somewhat, allowing current to flow between the rails and wasting significant amounts of power.

Don't confuse transistor threshold with gate threshold; a gate's threshold is typically around half of the supply voltage, and varies according to many factors including internal structure.

# Mixing Thresholds
You can mix thresholds of transistors to reduce power usage.
For example, you might use high-speed (but high power) gates on the critical path and use low-power (and low speed) gates away from there.
High speed transistors, in addition to leaking lots of energy when switching, also have a higher static dissipation. You can use low-power transistors to power-gate high-speed logic for efficiency gains:
![](Pasted%20image%2020231128144224.png)
- Power the low-power blue transistor 'off' when not in use, preventing the high-power transistors from leaking energy
- Power the low-power blue transistor 'on' when the high-power transistors are required
	- This will be slow

This blue transistor is a **pass transistor**.

## Exotic FET structures
![](Pasted%20image%2020231128144645.png)

# Power Domains
On some SoCs it may be appropriate to run different parts at different voltages. This will reduce power consumption, as long as those units on the SoC can be run at a lower voltage without causing problems.
The main problems with this are that a more complex supply distribution network is needed, and signals that need to cross these domains must be reinterpreted.
## Clock Gating
A clock distribution network can be a major contributor to power dissipation due to the constant activity and large fan-out. It is quite common to employ **clock-gating blocks** which are idle when possible. CAD tools widely support this. Power can remain on for components that need it (e.g. registers, memory).
For example, it is common for embedded processor cores to go into an inactive "sleep" mode whilst waiting for an interrupt; this is a situation where clock gating could be used to save even more power.

## Dark Silicon
To most power saving possible for a section of a chip is to just turn it off. Dark Silicon is a term that refers to parts of a system that are present but completely unpowered.
In some devices, it may even be **necessary** to turn components off when not in use to stop the power dissipation exceeding the amount which keeps the temperature in operating limits.
There are disadvantages to this strategy: It must be ensured that no output from the disabled block is accepted while it is off, as the given values will be random. More importantly, memory cannot hold data when entirely unpowered so care will have to be taken to restore everything to a usable state when powering back up.
For example, ARM have their big-little design CPUs which contain both low and high performance cores. When the low-performance cores can handle a workload, the high-performance cores can be entirely turned off. When they are needed, the workload can migrate to the high-performance cores.
# Level Shifting
Running a circuit on multiple different supplies can cause problems:
![](Pasted%20image%2020231128145728.png)
Not only does this give a potentially incorrect result (depending on how the next thing in the chain interprets the odd voltage), it will **always** increase the static power dissipation by allowing current to move between the drain and source.
Using a level shifter, we can transform a "high" from one supply to the "high" of another:
![](Pasted%20image%2020231128145930.png)
Both of these designs use 0V as 'low', so no translation is needed.
This works essentially by using the input values to connect the output directly to the corresponding value for the new supply, instead of directly sending it through. Observe how an input of 0.8V activates the transistor connecting the output to 1.2V supply, and an input of 0V connects it to 0V.
# DVFS
Dynamic Voltage/Frequency Scaling is another technique for increasing power efficiency. It works in principle like this:
![](Pasted%20image%2020231128150336.png)
So, if it is acceptable to run more slowly:
![](Pasted%20image%2020231128150447.png)
As long as the workload can be predicted this can be applied , slowing down when there's no rush and speeding up when demand is high.
Note: Specifically, you have to reduce the frequency before you reduce the supply voltage and increase supply voltage before increasing frequency.