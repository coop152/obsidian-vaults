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

The images are examples of n-channel MOSFETs. The drain and source are n-type, like given in the name, meaning that the MOSFET will transfer 