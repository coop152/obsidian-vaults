# Unit 1
- Lines are a pixel too long in the Y direction at the end. For example, drawing a single pixel will result in a 2 pixel tall line, and drawing a horizontal line will result in an extra stray pixel at the end with y coordinate +1 from the rest of the line.

# Unit 2
- `ack` is staying high for the same amount of time that `busy` does - it should be a single clock pulse.

# Unit 3
- Something very wrong with the line drawing implementation. Draws lines with completely wrong lengths and angles, as well as going out of the bounds of the screen.

# Unit 4
- Fully functional, though it's slower than the others.

# Unit 5
- Is treating `de_nbyte` as active high instead of active low.