## Recaps
- The 3-box architecture: A processor that executes a sequence of instructions, some memory that presents a set of locations which can hold information, and some bus(es) that allow communication between the processor and memory.
- Types of instructions:
	- Memory operations: Moving data between memory and registers (e.g. `LDR r1, a` or `STR r5, b`)
	- Processing operations: Perform calculations on values in registers (e.g. `ADD r1, r2, r3` or `AND r5, r3, r1`)
	- Control flow instructions: Make decisions, repeat operations, etc. (e.g. `B loopstart`, `BEQ equal_case`)
## The fetch-execute cycle
The fetch-execute cycle: Instructions are fetched from memory, then decoded and executed by the CPU in an unending loop. You can break this down further:
- Fetch involves getting the instruction from memory (IF), decoding the instruction and selecting registers (ID)
- Execute involves performing an operation or calculating an address (EX), accessing an operand in memory (MEM), and writing the result to a register (WB)

This is the 'worst case' data path, which works for all instructions. Some instructions won't require some of these steps (e.g. `ADD r1, r2, r3` doesn't need the MEM step).
![](Pasted%20image%2020230307141030.png)
## Cycles of operation
Most logic circuits are driven by a clock. In it's simplest form, one instruction would take one clock cycle (a so-called *single-cycle processor*). This assumes that getting the instruction and accessing data memory can each be done in a fifth of a cycle (i.e. there must be a cache hit).
![](Pasted%20image%2020230307141334.png)
With a single-cycle processor, each stage will do its work and pass the result on to the next, then sit idle for a fifth of a cycle. The sequence of events will look something like this:
![](Pasted%20image%2020230307141447.png)
While this is functional, it isn't efficient - at any one point, only a small section of the CPU is actually doing anything. Can we increase the utilisation of our components somehow?
## Buffers
Instead of a direct connection between stages like before, add buffers in-between to hold state. Clock these buffers once per cycle; now instead of the entire sequence taking one clock cycle, just a single step can take 1 clock cycle, with all components doing work at the same time!
![](Pasted%20image%2020230307141722.png)
This is like a production line; when you're building a car, you don't wait until the first car is fully completed before you start another. While the first car is being painted, the second car is being sanded down (or something like that).
We're still executing one instruction per cycle, but now that the units of work being done per clock are smaller we can increase the clock frequency by 5x!
![](Pasted%20image%2020230307142344.png)
#### Why 5 stages?
Early pipelined processors decided that dividing into these 5 stages was appropriate, due to their roughly equal complexity. Recent processors have used more than 30 pipeline stages, but this course stays with 5 for simplicity.

#### Example real-world pipelines
![](Pasted%20image%2020230307142510.png)
## Pipeline Scaling Limits
- Higher clock frequencies => higher power usage
- More stages means:
	- More extra hardware
	- A more complex design
	- It's more difficult to split into uniformly sized chunks
	- Hitting up against the limit on the minimum cycle period from the loading time of the registers
- There are some hazards with control and data. A longer datapath means a higher probability of hazards occurring and worse penalties when they do happen.

## Control Hazards (The Control Transfer Problem)
Normally, instructions are fetched sequentially (i.e. just incrementing the PC). But what if we fetch a branch? We only know it's a branch when we decode it in the second stage of the pipeline, but by that time we are already fetching the next instruction in serial order. We have a 'Bubble' in the pipeline.
![](Pasted%20image%2020230307142946.png)
In this example, we have had to do two extra pieces of work; we've marked the next thing in the pipeline as unwanted, which requires extra complexity to ignore it, and we've wasted a cycle in the process. But it gets worse!
Consider a conditional branch. We aren't able to determine the outcome of the branch until the execute stage, which is the 3rd stage. So now we have 2 bubbles in our pipeline!
![](Pasted%20image%2020230307143325.png)
These 'bubbles' are called **Control Hazards**. They occur because it takes one or more pipeline stages to detect a branch; the more stages it takes to detect a branch, the longer it's likely to take, meaning longer pipelines suffer more from control hazards. Is there a way around this?

## Branch Prediction
In most programs, lots of branch instructions are executed many times (e.g. loops and functions). What if, when we execute a branch:
- We take note of its address
- We take note of the target address
- We use this info next time the branch is fetched?
#### Branch Target Buffer
We could do this with some sort of (small) cache:
![](Pasted%20image%2020230307143713.png)
As we fetch the branch, we check the BTB; if the branch address is in the BTB, we use its target to fetch the next instruction instead of the PC.
This approach is very effective for unconditional branches, as we get it right every time (apart from the first time, when it hasn't been cached yet). For conditional branches, it depends on the probability of the branch being taken again. For example, a for loop will only encounter a bubble on the first and last iteration, when the branch is first taken and when the branch is finally not taken at the end. This is just a prediction, and a failure to predict correctly incurs a penalty.

[Interactive Branch Prediction Demo](https://online.manchester.ac.uk/webapps/blackboard/execute/displayLearningUnit?course_id=_72775_1&content_id=_13877653_1)
#### Other methods
While BTB is simple to understand, it's expensive to implement (due to the memory required) and only predicts based on the last branch. In practise, the accuracy of predictions depends on:
- More history (from several previous branches)
- Context (How was this branch reached compared to the others?)
Real-world branch predictors are much more complex, and are vital to the performance of deep pipelines. 
![](Pasted%20image%2020230307144739.png)

## Data Hazards
Consider this code:
```arm
ADD r1, r2, r3
MUL r0, r1, r1
```
The `ADD` produces a value in `r1`, but the `MUL` wants to use the result in `r1` as an input. There is a **data dependency** between them. This is another problem that can be caused by pipelines. Imagine these instructions in the pipeline:
![](Pasted%20image%2020230307145105.png)
At the end of the ID cycle, the `MUL` instruction *should* have selected the value in `r1` to put into the buffer and pass along to the EX stage as an input. However, the desired value for `r1` from the `ADD` instruction is still being put into the buffer at the output of EX at this time. It won't get to the input of WB until one cycle later, and it'll probably take another cycle to write into the register bank on top of that.
## Dealing with Data Dependencies
1. Detect dependencies in hardware and **hold the instructions** in the ID stage until the data is ready; this is known as 'pipeline stall'. This is undesirable, as it introduces bubbles and wasted cycles.
![](Pasted%20image%2020230307145532.png)
2. Detect dependencies in the compiler and attempt to **reorder instructions** to avoid conflicts. This only provides any benefit if there is something useful we can do; if not, then we have to insert `NOP`s and it's just as bad as holding the instructions back.
![](Pasted%20image%2020230307150359.png)
3. Implement **forwarding**.

#### Forwarding
Add **extra data paths** that account for these specific data dependency cases. With this solution, the output of EX will feed back into the input of EX, sending the data to the next instruction. This will stop any cycles from being wasted, but it will increase the complexity of the control.
![](Pasted%20image%2020230307150525.png)
But this still isn't a solution; this only accounts for cases where the result is ready after the EX stage. What about this?
```arm
LDR r1, [r2, r3]
MUL r0, r1, r1
```
Now the result of the first instruction isn't ready until the MEM stage, and we can't apply the same solution because the second instruction's EX stage is already over. Therefore, the MUL will just have to wait until the preceding LDR has finished its MEM stage. We can add extra paths from MEM to EX similarly to the other solution, which introduces even more complexity into the control path. This does reduce the penalty, but there is still an unavoidable one cycle bubble.
![](Pasted%20image%2020230307151443.png)
With both methods of forwarding implemented, we get results like this:
![](Pasted%20image%2020230307151610.png)
Forwarding is an example of something that gets less effective as the pipeline gets deeper; as the distance gets bigger between dependent steps, the forwarding gets more complicated and more stalls are introduced.
A bigger example of no forwarding vs forwarding:
![](Pasted%20image%2020230309135127.png)
## Summary
There are two kinds of Hazard:
- Control Hazards - If a branch instruction changes the flow of the program, some pipelined instructions will become invalid
- Data Hazards - If one instruction depends on the result of another, it needs to wait for that data to be ready

These hazards introduce "bubbles" in the pipeline. That is, they force the pipeline to stall the  execution of instructions, wasting cycles.

To fix control hazards, you employ branch prediction such as a BTB. There is no penalty when a branch is guessed correctly (the pipeline can just continue, as the correct instructions were already being executed), so increasing the likelihood of the initial guess being correct will eliminate control hazards.

To fix data hazards, you implement **forwarding** at various points of the pipeline, allowing the output of one stage to reach other stages that need it sooner. For example, the result of the EX stage (or the input to the MEM stage, depending on how you look at it) can be forwarded to the input of the next instruction's EX stage, eliminating any stalling for situations like this:
```arm
ADD r1, r2, r2
SUB r2, r3, r1    ; data dependency on result of the last ADD
```
You cannot entirely remove stalling using forwarding; if the required result is simply too many stages ahead then there is nothing that can be done. Forwarding simply allows you to take an already complete result that hasn't propagated yet and give it a "shortcut"; if the result is not ready yet (i.e. it needs to be loaded from memory, but the dependent instruction is ready to perform a calculation with it) then there is nothing that can be done.