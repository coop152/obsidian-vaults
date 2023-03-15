We are now done with the front/middle-end and have reached the back-end, and we have some (partially) optimised IR that needs to be converted to the final target code. The broad strokes of this issue are:
- We need to select the right instructions in the target language - this is a pattern matching problem
- We need to perform further optimisations, this time according to the specific capabilities of the target platform

The concrete steps involved in the back-end are:
- Instruction selection: map the intermediate representation into target code
- Register allocation: Decide which values will reside in which registers (only applies to an assembly/machine code target)
- Instruction scheduling: Reorder operations to hide latency (e.g. in pipelined and superscalar systems)

We could solve all of these independently, but they all end up being inter-related.

## Instruction Selection
This step is essentially pattern matching; we need to match certain IR patterns to snippets of target code. It is easy to make locally optimal choices (i.e. just choose the most optimal instructions for the current pattern) but making a globally optimal program is an NP-complete problem. Note that in this step we assume we have enough registers to represent all values.
For ongoing examples, assume a RISC-like target language defined like this:
![](Pasted%20image%2020230315121204.png)
#### Code generation for arithmetic expressions
We can adopt a simple tree walk scheme, and emit code in post-order:
![](Pasted%20image%2020230315121346.png)
