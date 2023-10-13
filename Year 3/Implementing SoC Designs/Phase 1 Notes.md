# Unit 1
- Lines is a pixel too long at the end. That is, if the line is vertical then it goes one pixel too far in Y, if the line is horizontal then it goes one pixel too far in X, and if it's 45 degrees then it goes one pixel too far in both

# Unit 2
- `ack` is staying high for the same amount of time that `busy` does - it should be a single clock pulse.

# Unit 3
- Disregards the start point and always draws from (0,0)

# Unit 4
- `de_req` is really freaking out on the vertical line. **investigate further** - May not actually be an issue, just strange?
- Something strange is happening to things drawn in the bottom right corner. **investigate further, and test the others with these bottom-right corner tests**

# Unit 5
- Is treating `de_nbyte` as active high instead of active low.