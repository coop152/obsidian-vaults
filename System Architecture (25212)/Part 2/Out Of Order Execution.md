## Relevant recap
- Pipelining and its benefits
- Control hazards and how to mitigate them
- Data hazards and how to mitigate them
	- Forwarding (in hardware)
	- Reordering/adding instructions (in software)
- Superscalar processors (processors that can execute more than 1 instruction in parallel)
	- What do you need to add in order to make a processor superscalar? (e.g. extra execution units like ALUs, more ports to memory and register bank)
	- Limitations to scalability with superscalar architectures

## Reordering instructions
Reordering can be done by the compiler, which may change the order of instructions in the code it produces in order to separate data dependencies and reduce or eliminate data hazards. The compiler cannot always do this, so you still need hardware to handle the hazards.
But what if the compiler could be relied on to reorder instructions, such that we didn't need this hardware to check for conflicts? This is the principle of **VLIW (Very Long Instruction Word)**. 

## Compiler Limitations
- Legacy binaries - optimised binaries are tied to a particular hardware configuration
- There is likely to be some 'Code bloat' with **VLIW** - lots of useless NOPs where not many instructions could be ran in parallel.
Because of these, we rely on hardware to re-order instructions instead of the compiler. These **out-of-order processors** are complex, but effective.

## Out of Order Processors
You need:
- An **instruction buffer** to store all of the issued instructions.
- A **dynamic scheduler** to pick non-conflicting instructions and send them around the CPU to be executed.
- A way of delaying memory and register accesses until all older instructions are finished, in order to comply with the semantics of the application.

The datapath of a regular processor is structured like this:
![](Pasted%20image%2020230313103358.png)
An out of order processor, with its added complexity, will be structured like this:
![](Pasted%20image%2020230313103514.png)

## Structural Hazards
A classic 5-stage pipeline (like we've been covering till this point) might look something like this:
![](Pasted%20image%2020230313103619.png)
But a more realistic modern pipeline will contain many execution flows of variable structure, like this:
![](Pasted%20image%2020230313103720.png)
Notice that some of these **functional units** can be pipelined, but some can't; for example, the Div line in this pipeline has no buffers, and therefore cannot be pipelined. Because of this, the Div unit can only be used by one instruction at a time. This is called a **structural hazard**.
#### Example
![](Pasted%20image%2020230313104138.png)

## Modern Pipeline Architectures
![](Pasted%20image%2020230313104206.png)
Notice the significant increase in complexity in the out of order processor.

## Summary
A processor using out of order execution will **not** preserve the original order of instructions in the program. It will execute instructions as input data becomes available, just like a regular in-order processor. However, if the pipeline stalls due to **conflicting instructions**, it will minimise the performance impact by processing instructions *which are still able to be run immediately*. This takes full advantage of the ILP (Instruction Level Parallelism) of a superscalar processor and causes an increase in IPC (Instructions Per Cycle).
Conflicted instructions can be caused by:
- Cache misses - An instruction has to wait for a long time for something to be retrieved from main memory
- Structural hazards - The required resource (i.e. a functional unit) is not available
- Data hazards - An instruction depends on the result of another instruction

## More on data dependencies
Out of order execution imposes some new types of data dependencies to preserve program semantics:
![](Pasted%20image%2020230313111226.png)
## Dynamic Scheduling
The key idea behind dynamic scheduling is to allow instructions behind a stall to proceed; there are multiple execution units, so use them.
![](Pasted%20image%2020230313111420.png)
This overcomes the limitations of in-order pipelined execution.

## Concrete Implementation: Scoreboard
The scoreboard is a **centralised** hardware mechanism; instructions are executed as soon as their operands are available and there are no hazard conditions to stop them.
The hardware dynamically constructs a dependency graph for a window of the instructions as they are issued in the regular program order.
The scoreboard is a data structure that provides the information necessary for all pieces of the processor to work together.

Out of order execution divides the Instruction Decode stage:
1. Issue - Decode instructions, check for structural hazards
2. Read operands - wait until there are no data hazards, then read the operands

Scoreboard allows the instruction to execute whenever both of these stages can be completed, without waiting for prior instructions.
Our implementation will use in-order issue, out-of-order execution, out-of-order commit (aka completion).
A typical scoreboard will look like this:
![](Pasted%20image%2020230313111929.png)
With a pipeline like this:
![](Pasted%20image%2020230313111954.png)

#### Stages
1. Issue (ID) - Decode the instructions and check for structural (or write after write) hazards. If a suitable functional unit is free (there are no structural hazards) and no other instruction has the same destination register (no write after write), then the scoreboard issues the instruction to the FU and updates its info. If a hazard **does** exist, then the issue stalls and no further instructions will issue until the hazards are cleared. **This stage is always done in program order.**
2. Read operands (RO) - wait until there are no data hazards, then read the operands. An operand is available if no active instruction is going to write it (no Read after Write). **This stage can be done out of program order.**
3. Execution (EX) - operate on the operands. The functional unit begins execution on the recieved operands. When the result is ready, it notifies the scoreboard. **This stage can be done out of program order.**
4. Writeback (WB) - Finish execution and write the results back. Once the functional unit completes execution, the scoreboard checks for Write after Read hazards. If there are none it writes the results, otherwise the stage is stalled and the functional unit stays busy. **This stage can be done out of program order.** For example:
![](Pasted%20image%2020230313112612.png)

#### Storing status in the Scoreboard
![](Pasted%20image%2020230313112943.png)
![](Pasted%20image%2020230313113000.png)
**Exercise:** Draw the dependency graph for this code: (Solutions are on the right, cover them up).
![](Pasted%20image%2020230313113621.png)
Solution:
![](Pasted%20image%2020230313113758.png)
Now, execute these instructions and fill in this scoreboard:
![](Pasted%20image%2020230313113843.png)
Watch a walkthrough of this in week 7 video 2, at about 16 minutes in.