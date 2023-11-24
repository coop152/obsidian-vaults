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