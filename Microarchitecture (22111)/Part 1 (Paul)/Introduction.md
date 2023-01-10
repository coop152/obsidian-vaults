## Definitions
**Abstraction**: Hiding the complexity of the design.

**Hierarchical Approach**: Breaking the design up into smaller, more manageable elements.

**Specification**: Defines all of the requirements and features of the design. Doesn't necessarily include technical details (i.e. may be an abstraction).

**Top-down Design**: Designing a specification first, and elaborating on the specific implementation later; begins completely abstract and becomes more concrete as the design progresses.

**Bottom-up Design**: Begin the design with the lowest-level components (e.g. transistors) and bring these smaller components together.

**Latency**: The time taken from when inputs are given to when output is recieved.
**Throughput**: The rate at which data can be processed.
Latency is not concerned with the size or count of inputs, while throughput is; for example, if circuit A can process 4 inputs simultaneously with a latency of 2 time units and circuit B can only process 1 with a latency of 1 time unit, then circuit A has the greater throughput (2 pieces of data per unit time vs 1 piece of data per unit time) but circuit B has the superior latency (1 unit vs 2 unit).

### Miscellaneous Things
- [[Stump Instruction Set.pdf|Stump Instruction Set]]
- [[Stump ISA.pdf|Stump ISA]]
- The shift on a type 1 instruction applies to the first source operand. 
	`ADD r1, r2, r3, ROR` means `r1 = ROR(r2) + r3`
- Check for signed overflow by comparing the signs of the operands and the result. `ADD` results have overflowed if the operands were both positive and the result was negative, or vice versa. `SUB` results have overflowed if the operands had opposite signs and the sign of the result is different from the first.
- Stump implements right shifting instead of left shifting because left shifting is trivial without specialised hardware (just add the number to itself).
- 