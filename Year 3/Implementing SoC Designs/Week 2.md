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

# Useful Constructs
`if (bool_exp) statement_1 <else statement_2>`
Makes decisions, in hardware or testbench
`while (bool_exp) statement`
Useful for simulation control (e.g. waiting for handshake)
`for (addr = 0; addr < 1024; addr = addr + 1) statement`
Iterate over a number of items (e.g. testing memory)
Not useful in synthesised code.
`forever statement`
Repeat indefinitely. Must include some kind of delay (@ or # in the testbench) or it will stall at a single point in time forever.

# Parallelism
Hardware is highly parallel, and therefore HDL is too.
Each `initial` and `always` block is an independent parallel 'thread' within which you can have:
- A sequential block: `begin ... end`
- A parallel block: `fork ... join`

In a parallel block all statements are executed 'simultaneously' as if they were in separate blocks. Sometimes this makes no difference, but it is important when managing delays:
```verilog
begin  
#10 a = 1;  
#20 b = 0;  
end  
```
Elapsed time 30 units.
vs
```verilog
fork  
#10 a = 1;  
#20 b = 0;  
join  
```
Elapsed time 20 units.

# Simulation time
Time, of course, goes forth in a single continuous dimension. In Verilog time is discrete, but also multidimensional.
- There is the overall simulation time which represents real delays
	- Available as `$time`
	- The resolution of this time is controllable: can be a fraction of `#1`
- There is a list of things which happen simultaneously in one time-step:
	- Some are ordered, e.g. blocking assignments in the same block
	- Some are unpredictable, e.g. blocking assignments in **different** blocks
	- Things act in phases. First are blocking assignments, second is non-blocking assignments

To be clear; this has nothing to do with the real time it takes to run the simulation.

