# Unit 1
- Lines are a pixel too long in the Y direction at the end. For example, drawing a single pixel will result in a 2 pixel tall line, and drawing a horizontal line will result in an extra stray pixel at the end with y coordinate +1 from the rest of the line.

# Unit 2
- `ack` is staying high for the same amount of time that `busy` does - it should be a single clock pulse.

# Unit 3
- Is calculating dx as x1 - y0 and dy as y1 - x0
- Calculates dx as x1 - y0 instead of x1 - x0, and calculates dy as y1 - x0 instead of y1 - y0 in Bresenham's. This causes the unit to draw lines that start at the correct point but are the wrong length and angle, and sometimes go out of bounds.

# Unit 4
- Fully functional, though it's slower than the others.

# Unit 5
- Is treating `de_nbyte` as active high instead of active low.