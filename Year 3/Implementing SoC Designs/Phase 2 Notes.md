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
