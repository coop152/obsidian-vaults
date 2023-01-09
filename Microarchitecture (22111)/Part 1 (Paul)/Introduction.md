## Definitions
**Abstraction**: Hiding the complexity of the design.

**Hierarchical Approach**: Breaking the design up into smaller, more manageable elements.

**Specification**: Defines all of the requirements and features of the design. Doesn't necessarily include technical details (i.e. may be an abstraction).

**Top-down Design**: Designing a specification first, and elaborating on the specific implementation later; begins completely abstract and becomes more concrete as the design progresses.

**Bottom-up Design**: Begin the design with the lowest-level components (e.g. transistors) and bring these smaller components together.

## Stump Recap
[[Stump Instruction Set.pdf|Stump Instruction Set]]
[[Stump ISA.pdf|Stump ISA]]
Type 1 Instructions are arithmetic, logic and memory instructions that operate on three registers. There is an optional shift applied to the second operand. e.g:
```
ADD r1, r2, r3
LD r1, [r2, r3]
SUB r1, r2, r3, ROR    ; right rotate applied to r2
```
Type 2 inst
