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
When the node discharges (via the pull-down tree) the charge flows to ground. T