**Q: What resources on the FPGA are used by the block?**
A: The circle drawing block uses:
- 1 FSM
- 13 adders and subtractors
- 135 flip-flops making up 16 registers
- 6 comparators
- 1 decoder

**Q: What resources on the FPGA are used for the whole SoC?**
A: The SoC uses:
- 3 FSMs
- 19 adders and subtractors
- 4 counters
- 1 accumulator
- 424 flip-flops making up 71 registers
- 32 latches
- 7 comparators
- 6 multiplexers
- 1 logic shifter
- 3 decoders

**Q: How 'full' is the device?**
A: The design uses this much of the FPGA's resources:
- 32% of the slices
- 15% of the slice flip-flops
- 27% of the 4 input LUTs
- 67% of the bonded IOBs
- 62% of the GCLKs

**Q: What is the estimated maximum speed of the SoC?**
A: The estimated max clock frequency of the SoC is 87.897MHz.
# Critique
**What, in hindsight, was well designed?**
I think that the DRAW and ITER states (the ones where a pixel is sent to the framebuffer and where the angle is incremented, respectively) are well designed, specifically the portion of DRAW that modifies the current coordinates to get all 8 transformations. Because the coordinate offsets are transformed in-place, the portion of code that interfaces with the framebuffer can just blindly push pixels, keeping the framebuffer interaction and the transformation separate.

**What would you do differently if you were starting again?**
My initial plan for the FSM states didn't entirely work, which required introducing some extra state registers (e.g. `iterate` and `skipped_pixel`) alongside the state. If I were starting again, I would:
- Have the DRAW state go to itself when skipping a pixel, instead of going to WAIT and setting `skipped_pixel`, which would remove a register, simplify the code and save a cycle every time a pixel is skipped.
- Have the WAIT state decide if DRAW or ITER should be the next state based on the number of pixels drawn in this iteration (`pix_num`), instead of having DRAW set `iterate` and then having WAIT decide based on that. 

**What were the major surprises and lessons learnt?**
- More than once I had trouble adapting my initial FSM design to the fact that `de_ack` only goes high for one cycle: For example, I had intended DRAW to go straight to ITER when an iteration was needed, but this meant that WAIT would run 2 cycles after a pixel was drawn instead of 1 and `de_ack` would have already gone high and then low, causing WAIT to loop infinitely.
- More than once I forgot that nonblocking assignments didn't block because I was blindly copying pseudocode, specifically in the ITER state where both $p$ and $x$ are updated and then the updated values subsequently used. This led to annoying debugging where the code 'worked' but the circles were very slightly misshapen.
