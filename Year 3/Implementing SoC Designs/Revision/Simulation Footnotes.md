Simulation is intended to verify:
- All RTL descriptions function as expected
- The completed chip is correct
- The proposed product (chip, board, etc.) has acceptable timing and electrical properties (and thermal)

**Observability** - Simulation allows you to examine parts of a design that are normally hidden, which can make it very easy to find issues that are deeply integrated in a design (compared to simply testing a chip from the outside)

Simulation is performed in different "levels":
- Functional, to check if the device behaves as expected
- Timing, to check what speed the device can run at before critical paths start interfering
- Electrical, to check that the device can actually get power and function with that power as designed
- Physical, to

Each of these levels requires a more detailed (and therefore slower) simulation model.
You might perform **functional tests** on a high-level architectural model using a TLM (Transaction Level Model). This is very simple and therefore fast to simulate, but doesn't tell you much beyond the feasibility of the architecture.
Knowing that the architecture of your device is feasible, you would create a behavioural model (in the form of your HDL code) and test this using a lower level simulation. This tests that the logic as-described does what it is intended to do. They do not, however, guarantee that the design fits all requirements and is ready to be manufactured: the design may be too slow to be clocked as fast as required, or use more resources than is available on the physical medium (e.g. cells on the FPGA, space on the silicon).

After creating a functionally correct design, you would perform **timing verification**. This simulation estimates and sums up the various delays in your implementation and finds the critical path. This requires a synthesised representation of the device, as it uses the properties of the target technology to calculate this critical path (e.g. the number of gates and flip-flops, and their various delays).
There are two phases of timing simulation: pre and post-layout. Pre-layout takes place on the initial netlist of cells, and doesn't know how anything is placed, just what is connected to what. This limits the accuracy of it's estimations, but modern tools can still get reasonable first answers. Post-layout takes place after Place-And-Route (PAR) and can more accurately estimate timing, taking into account the wiring delays and other things (e.g. if electrical amplification must be added).

If you know the timing of your device is acceptable, you then check the **electrical characteristics** of your device. Your device needs power, and the wires must be big enough to handle the required current. If a wire is too small it'll blow like a fuse, or more likely it'll "age" due to electromigration and shorten the lifetime of the device.

Following these you would perform other physical tests, like checking the thermals.

