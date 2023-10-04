# Simulation
Simulation is a part of modelling which takes a high-level model. The purpose of simulation is:
- To verify the functionality of an RTL description
- To confirm the correctness of a completed (manufactured) chip
- To verify the timing and electrical properties of the proposed product

To finalise a design, you must perform a number of different "levels" of simulation:
- Functional (check it works as expected)
- Timing
- Electrical
- Physical

Each level is concerned with different aspects of the design. The prior list is in order of the required detail of the simulation; a physical simulation requires a significantly higher amount of detail than a functional one.

This section concentrates on functional verification.

# Functional Simulation Flow
![](Pasted%20image%2020231004091832.png)
- Take the input Verilog and compile to a Netlist (no schematics in this course)
- Provide the simulation with some stimulus and simulate the design
- Compare the results with the expected results
- Check for errors (in the expected results too, perhaps)
- Inspect the trace of the simulation in a waveform viewer
- Consider errors and oddities in the traces, and modify the Verilog
- Repeat until satisfactory

The objective is to verify the logic behaviour of the design; we want to exercise every function. Like in software development, this can be assisted by **test-coverage tools**:
- Which HDL statements have (not) been executed
- Which 'branches' have (not) been taken
- Which nodes have (not) adopted both binary states during the simulation (which implies they aren't being exercised)
	- Can be tricky for behavioural HDL where there are no 'nodes' to check

Achieving complete coverage can range from challenging to literally impossible.