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
$V_{ss}$ is the voltage of the source supply (usually interchangeable for "ground", but sometimes it is a "low" supply rail)