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
A: The whole design (`drawing_top`) uses this much of the FPGA's resources:
- 32% of the slices
- 15% of the slice flip-flops
- 27% of the 4 input LUTs
- 67% of the bonded IOBs
- 62% of the GCLKs

**Q: What is the estimated maximum speed of the SoC?**
A: The estimated max clock frequency of the SoC is 87.897MHz.


# Critique
**What, in hindsight, was well designed?**
