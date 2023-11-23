# MOSFETs
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
