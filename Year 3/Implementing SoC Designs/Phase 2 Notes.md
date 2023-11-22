# Inputs
We say that
- r0 is x0
- r1 is y0
- r4 is radius (as to not step on x1 and y1's toes from line drawing)
- r6 is colour (to match with line drawing)
# Circle algorithm in python
```python
def circ(x0, y0, r, fb):
    x = 0									# IDLE 
    y = r									# IDLE 
    p = r									# IDLE 
    while x <= y:							# ITER 
        fb[x0 + x, y0 + y] = (255,0,0)	    # DRAW
        fb[x0 + y, y0 + x] = (255,0,0)	    # DRAW
        fb[x0 - x, y0 + y] = (255,0,0)    	# DRAW
        fb[x0 - y, y0 + x] = (255,0,0)    	# DRAW
        fb[x0 + x, y0 - y] = (255,0,0)    	# DRAW
        fb[x0 + y, y0 - x] = (255,0,0)    	# DRAW
        fb[x0 - x, y0 - y] = (255,0,0)    	# DRAW
        fb[x0 - y, y0 - x] = (255,0,0)    	# DRAW
            
        p = p - 2*x					    	# ITER
        x = x + 1					    	# ITER
        if p < 0:					    	# ITER
            p = p + 2*y			        	# ITER
            y = y - 1			        	# ITER
```
# State switching
```c
switch (state) {
	case IDLE {  // waiting for command
		if (req) {  // command issued!
			// acknowledge command (remember to take low later)
			ack = 1;
			busy = 1;
			// latch inputs
			x0 = whatever1;
			y0 = whatever2;
			r = whatever3;
			colour = whatever4;
			// set up initial values for iteration
			x = 0;
			y = r;
			p = r;
			// start drawing (goto wait in case the framebuffer is still working)
			state = WAIT;
		} else state = IDLE;
	}
	case WAIT {
		// ack has been up for a cycle, take it down
		ack = 0;
		if (de_ack) {  // framebuffer is finished
			state = DRAW;
		} else state = WAIT;
	}
	case DRAW {
		/* see in its own section, itll be big */
	}
	case ITER {
		if (x <= y) { // off by one error? CHECK LATER
			// go again
			pixnum = 0;
			p = p - 2*x;
			x = x + 1;
			if (p < 0) {
				p = p + 2*y;
				y = y - 1;
			}
			state = WAIT;  // (and make sure the framebuffer is ready)
		} else {
			// we're done
			busy = 0;
			state = IDLE;
		}
	}
}
```

## DRAW state
```c
// do this regardless of pixnum (this is the part that interacts with the framebuffer)
draw_pixel(x, y, colour);
pixnum += 1;  // increment pixnum
switch (pixnum) {
	case 1, 3, 5, 7 { // after the first, third, 5th and 7th pixels, flip y coord
		x = x;
		y = -y; 
		state = WAIT;
	}
	case 2, 6 {  // after the second and sixth pixels, flip x coord
		x = -x;
		y = y;
		state = WAIT;
	}
	case 4 { // after the fourth pixel, swap coordinates
		x = y;
		y = x;  // (i believe verilog will actually swap using <=)
		state = WAIT;
	}
	case 8 {  // after the final pixel
		x = -y;
		y = -x; // swap and negate coords, to get back to the un-reflected/flipped coord
		// and go onto the next angle
		state = ITER;
	}
}
```
## Checking flow
testbench outputs the pixels that were sent to the framebuffer in a format like this:
```
[Shape 1]
ADDR X Y COLOR
ADDR X Y COLOR
ADDR X Y COLOR
ADDR X Y COLOR
ADDR X Y COLOR
[Shape 2]
ADDR X Y COLOR
ADDR X Y COLOR
ADDR X Y COLOR
ADDR X Y COLOR
ADDR X Y COLOR
```
this is written to `results.txt`.
The python script is invoked after the file is closed, and reads these results in. In-mem structure something like this:
```python
results = {
		   "Shape 1": [(X, Y, COLOR),
				   (X, Y, COLOR),
				   (X, Y, COLOR),
				   (X, Y, COLOR),
				   (X, Y, COLOR)],
		   "Shape 2": [(X, Y, COLOR),
				   (X, Y, COLOR),
				   (X, Y, COLOR),
				   (X, Y, COLOR),
				   (X, Y, COLOR)]
}
```
python script generates its own list of results