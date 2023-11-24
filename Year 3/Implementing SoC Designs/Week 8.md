# MOSFETs
![](Pasted%20image%2020231123124704.png)![](Pasted%20image%2020231123124710.png)
MOSFETs (Metal Oxide Silicon Field Effect Transistors) are a three terminal device.
- Source - source of current (charge carriers)
- Drain - sink for current (charge carriers)
- Gate - voltage across the gate controls the current flow between source and drain

There is also a fourth terminal, the substrate, which is usually omitted from figures. 
MOSFETs come in two variants, p-channel (PMOS) and n-channel (NMOS).
The MOSFETs we use are **enhancement** mode devices, meaning they are normally turned off until voltage on the gate **enhances** conduction.
MOSFETs are symmetric. There is no physical difference between the source and the drain - they are just labels. 
## Details
A MOSFET consists of a source contact and a drain contact separated by a channel. This channel is *doped* such that it will not conduct when a voltage is applied between the drain and source. The doping stops the channel from conducting because it has no charge carriers of the type produced by the source electrode. If a suitable voltage is applied between the gate and the source then an *electric field* is set up across the gate's oxide layer and a charge is induced in the channel. This charge consists of the same type of carriers as in the source, so the device can conduct from source to drain.
N-type doped materials are materials that donate electrons, and thus conduct negative charges. Conversely, p-type doped materials are full of 'hole' impurities that carry positive charges.
In an n-channel device, we have n-type source and drain contacts. The channel must not conduct without a voltage from the gate, so it is a p-type material.
In a p-channel device, we have p-type doped source and drain contacts. The channel must not conduct without a voltage from the gate, so it is an n-type material.
As these devices are off by default unless a voltage is supplied on the gate, these types of MOSFETs are known as **enhancement** mode devices. Depletion mode devices exist (where they are *on* by default until a voltage is supplied) and have their uses.
The substrate is usually omitted from circuit diagrams, but on an n-channel device it is connected to the most negative voltage in the system and on a p-channel device it is connected to the most positive.
## Diode Junctions
![](Pasted%20image%2020231123124726.png)
A semiconductor diode is formed by a junction of p-doped and n-doped material. An 'ideal' diode would allow current to flow from P to N but not the other way; in reality it requires some forward bias voltage to make the current flow.
![](Pasted%20image%2020231124110322.png)
## Structure of a MOSFET
![](Pasted%20image%2020231124110424.png)
- **M**etal - the gate is made from metal.
- **O**xide - the insulator between the gate and substrate is historically made from Silicon Dioxide (SiO2), though nowadays it's probably made from "high-k dielectric" instead.
- **S**ilicon - the substrate is made of silicon doped with Boron, Arsenic, Phosphorus, etc.
- **F**ield **E**ffect - The current in the gate controls an electric field which causes the substrate to change conductivity...
- **T**ransistor - Which creates a transistor.

The images are examples of n-channel MOSFETs. The drain and source are n-type, meaning they conduct negative charge. The substrate separates them and is p-type, meaning that in it's initial state the MOSFET will not conduct from source to drain; the substrate conducts positive charge, so the negative charge of the source cannot get to the drain. When positive voltage is applied to the gate, a negative charge is induced in the substrate which allows it to conduct negative charge. This connects the source and drain, allowing charge to flow. The opposite it true for p-channel devices.
![](Pasted%20image%2020231124111948.png)
The important parameters of a MOSFET are the length of the channel (L), the width of the channel (W) and the thickness of the gate's insulator ($t_\text{ox}$). The channel length is usually what is being referred to when a size is given for a transistor (e.g. "28nm"). L and W can be controlled easily when making a design. We might have some control over $t_\text{ox}$, but this is usually fixed by the CMOS process being used (i.e. the manufacturer).
The doping levels may also vary which will change the threshold voltage ($V_\text{th}$). Low-threshold transistors will start to conduct early after being switched on, making them good for 'fast' gates, but they turn off more slowly and therefore leak charge, increasing power dissipation. A high-threshold transistor will inversely be slower but lower power.
## Layout
![](Pasted%20image%2020231124113002.png)
Shown in the figure is a transistor circuit, and an illustration of how that circuit might be implemented. These are the areas:
- Green represents the diffusion areas (sources and drains)
- Red represents the gates (which sit above the diffusion areas)
- Blue represents wires into the transistor, which come in from above and are connected vertically (represented by the black boxes)
- Grey represents the substrate (which also goes under the diffusion areas)

Here is a 3D representation of a similar (but simpler) circuit:
![](Pasted%20image%2020231124113534.png)
This also includes some overhead wires that don't interact with the circuit.
In these pictures, the transistor's length is the short horizontal length of the gate. The width is the other dimension of the gate (vertical in first diagram, into the image on the second).
There are minimum and maximum widths and clearances for these features (e.g. minimum gate length, minimum overlap of gate and diffusion...) which are known as the **design rules**. These must be followed if the fabricated layout is to work. CAD tools provide an appropriate Design Rule Checker (DRC).
# CMOS Gates
CMOS stands for *Complementary* MOS. By complementary it means that a CMOS circuit consists of PMOS and NMOS transistors complementing each other, where PMOS transistors are good at pulling outputs high and NMOS transistors are good at pulling them low.
Here is a simple example of a CMOS gate, an inverter:
![](Pasted%20image%2020231124114249.png)
This circuit simply switches the input from 1 to 0 or vice versa. For example:
![](Pasted%20image%2020231124114456.png)
It works like so:
- With a high input the PMOS transistor is 'off' and the NMOS transistor is 'on', connecting the output to ground and outputting zero.
- With a low input the PMOS transistor is 'on' and the NMOS transistor is 'off', connecting the output to power and outputting one.
- When the gate is switching from one situation to the other, both transistors swing from on to off or vice versa. Because both are doing it at the same time, this tends to "sharpen" the edge of the change.

Note that the two types of transistor are not quite complementary. This is covered later.

The gate threshold of a transistor is the input voltage for which the gate's output switches.
![](Pasted%20image%2020231124115800.png)
This may not necessarily be a halfway point.
The transfer from one output to the other is curved because of the time it takes for a transistor to change:
![](Pasted%20image%2020231124120248.png)
Observe how the switching behaviour of an entire CMOS gate and a single transistor (or capacitor in this case) are different.
## Transistor sizes
The bigger the load, the slower the output edge. Load is imposed by the inputs of other gates (because each transistor is a capacitor) and also by the interconnection wiring. Both of these in turn depend on the network **fanout**; in practice counting the number of gates wired to will give a decent approximation of the scale of the wiring load.
When fanout becomes big, edges can become slow. To alleviate this you can simply use a bigger transistor! It is common for gate libraries to contain a range of implementations of each logic function where the drive strength of the gate can be selected. Bigger gates have downsides, namely that they occupy more space and that the thing driving them will also have a higher load.
To get a bigger transistor, it is legitimate to place two or more devices in parallel:
![](Pasted%20image%2020231124120913.png)
## Standard cells
The majority of VLSI is built from standard cells. These are implementations of widely used components, largely equivalent to a single gate. There will likely be some complex gates to choose from in addition to the simple ones.
The design needs to be converted into cells which can be laid out on a chip. This phase is called **technology mapping**. CAD tools convert a circuit specification into cells from a **component cell library**.
Each component of this library will be visible in the layout. The full design may be visible, although sometimes manufacturers will protect these by only providing a 'phantom' which shows the size and connection points. This phantom will be a *characterised* model of the true cell, which can be used by appropriate cad tools to simulate its behaviour with it's true capacitive loads, output drive, etc.
![](Pasted%20image%2020231124122931.png)
Standard cells will have a fixed height with power rails of a known width in defined places. They will differ in width, though they may conform to multiples of a standard unit (to fit more easily on a 'grid').
Cells will already be correct according to the design rules internally, and usually in some extra cases (e.g. reflected left-right or even top-bottom).
Layout is in rectangular blocks with power fed from rails at the edges. (Other power rails may be placed above and linked down.)
Dummy 'spacer' cells will be added to fill in any gaps, and for other functionality such as providing contacts for the wells. 

Cell libraries will vary but you can expect them to at least include the common gates (e.g. inverter, AND, NAND, OR, NOR, XOR, XNOR) in a variety of input types and drive strengths.
You can also expect a number of simpler complex gates (e.g. and-or-invert, or-and-invert) because they can reduce the critical path of a logic block.
There will be a number of latching elements: D-type flip-flops of many kinds (with or without resets, enables, etc.) and various kinds of latches.
Other cells are less likely, but multiplexers are not uncommon.

## Examples of CMOS gates
Gates are formed using serial and parallel combinations of transistors.
![](Pasted%20image%2020231124124218.png)
These basic gates are always inverting (e.g. NOT, NAND, NOR).
They provide a load on their inputs and actively drive their outputs.

Because a single CMOS logic stage inverts the output, a standard AND gate will actually be a NAND-INV combo. This will be slower, although the inverter may provide some good drive strength.
![](Pasted%20image%2020231124123913.png)
Larger fan-in gates may be constructible using other non-inverting cells, which is often preferable. For example, this 4-input AND:
![](Pasted%20image%2020231124124108.png)
Which is clearly simpler than 3 of the previously shown AND gates.

## Transistor sizes (again)
The resistance of a transistor when it is 'switched on' is inversely proportional to its gate width. If there is (for example) double the width then there is twice as much material to carry the appropriate current density, so there is twice the current.
The resistance is also proportional to the gate length. Normally (but not always) transistors are made with the minimum feasible gate length to reduce resistance.
Two 'on' transistors in series are roughly equivalent to one transistor of twice the length. Therefore, a transistor 'stack' of two transistors will have twice the resistance of a single transistor, 'weakening' the gate's drive strength. The transistors' widths may be doubled to counteract this. Alternatively, you could use two parallel transistors.
## PMOS vs NMOS
PMOS channels are less conductive than NMOS ones; as a rule of thumb, a PMOS transistor needs to be **twice as wide** as its NMOS equivalent to provide similar channel resistance. Thus for an inverter, to provide equal rising and falling output edges:
![](Pasted%20image%2020231124125811.png)
To provide equal switching edges from gates, transistor widths are increased proportionally to the depth of the **series stack** they are in, from power supply to output. You *do not* decrease the width of parallel transistors, because we assume that only one input change (and thus one transistor) will be responsible for the output change.
Here is an example for NAND gates:
![](Pasted%20image%2020231124130205.png)
The input load to the gate increases proportionally.
NOR gates are 'worse' in this regard because the PMOS transistors scale from a larger base. Therefore, NAND logic is slightly preferable.
# Complex Gates
![](Pasted%20image%2020231124130538.png)

![](Pasted%20image%2020231124131557.png)
![](Pasted%20image%2020231124131706.png)
# Pass Transistors
Instead of using transistors to connect an output to the appropriate power supply, use a transistor to connect an output directly to some input:
![](Pasted%20image%2020231124132518.png)
When the transistor is 'off' the output will be high-impedance, as nothing is driving it. When the transistor is 'on', it will do a good job at transmitting it's preferred type of current (i.e. zeros for NMOS, ones for PMOS) but will still get some of the way there for the opposite kind. So, an NMOS will pass along a zero perfectly and pass along a one as a sort of 'three-quarters'.
An example of pass transistors in action is an SRAM cell:
![](Pasted%20image%2020231124132906.png)
When word is high, the transistors will allow the data into the inverter loop. One of the inputs will be of poor strength (near-zero or near-one). This is good enough to store the desired state, and (over time) the inverters will strengthen the stored value.
(NMOS transistors are used because their resistance is preferable at a given size to PMOS.)
## Transmission Gates
NMOS can pass zero well but not one. PMOS can pass one well but not zero. If we combine them, we can made a device with behaviour similar to a pass transistor but that delivers a strong output no matter the input. This is called a transmission gate.
![](Pasted%20image%2020231124133249.png)
Note that we need complementary enables.
This gate is a switch, so it will pass signals in either direction. This is useful but it also causes problems: without buffering the output may have unwanted effects on the input.
Here is an example usage of a transmission gate in a multiplexer:
![](Pasted%20image%2020231124133425.png)
When Sel is high, the top transmission gate is switched off, so it's output is high impedance. The bottom transmission gate is switched on, passing on the input to the final inverter. This type of structure also scales quite well, e.g. to a 4:1 mux.
