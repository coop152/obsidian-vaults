# VDU Controller
The task of the VDU controller is to:
- Generate Sync signals and blanking signals
- Framestore addresses and read the framestore data

It is a fairly simple state machine, which can often be programmed to accommodate different displays (size, colour, aspect ratio).

# Framestore/Framebuffer
In theory, a 2D array of memory whree each cell is a 'numeric' representation of a single pixel's colour.
Depending on the colour depth, the length of one location will vary; this course assumes 8-bit colour (as in 8 bits per pixel, not per colour. 256 colours.)

# Drawing Lines
