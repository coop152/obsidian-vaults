# Synthesis report
Not including the time spend waiting for the framebuffer, the unit can draw 
Copy-pasted from the Phase 4 notes:
The circle drawing block uses:
- 1 FSM
- 13 adders and subtractors
- 135 flip-flops making up 16 registers
- 6 comparators
- 1 decoder

The estimated max clock frequency of the SoC is 87.897MHz.
# Register-level documentation
The example column shows the values for drawing a red circle centre (320,240) with radius 100.

| Argument | Bits | Function | Example |
| -------- | ---- | -------- | ------- |
| r0       | 10   | X coordinate of centre | 320 (decimal)     |
| r1       | 10   | Y coordinate of centre | 240 (decimal)    |
| r4       | 10   | Radius                 | 100 (decimal)    |
| r6       | 8    | Colour                 | 111_000_00 (binary)      |

