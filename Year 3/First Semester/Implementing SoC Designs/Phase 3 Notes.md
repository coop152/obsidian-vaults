# Synthesis report
Assuming that the framebuffer doesn't take longer than a cycle to draw a pixel, my circle drawing unit runs at **2.125 cycles per pixel**. This is because it can draw eight pixels (which are all simple transformations of the last) without any extra time spent beside waiting for the framebuffer, but it requires an extra cycle after those eight while it iterates to the next angle. 

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

