# State switching
```c
switch (state) {
	case IDLE {  // waiting for command
		if (req) {  // command issued!
			// acknowledge command (remember to take low later)
			ack = 1;
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
		}
	}
	case WAIT {
		// ack has been up for a cycle, take it down
		ack = 0;
		if (de_ack) {  // framebuffer is finished
			state = DRAW;
		}
	}
	case DRAW {
		/* see other section, itll be big */
	}
}
```

## DRAW state
```c
// do this regardless of pixnum
draw_pixel(x, y, colour);
pixnum += 1;  // increment pixnum
switch (pixnum) {
	case 1, 3, 5, 7 { // after the first, third, 5th and 7th pixels
		x = x;
		y = -y;  // flip y coord
	}
	case 2, 6 {  // after the second and sixth pixels
		x = -x;  // flip x coord
		y = y;
	}
	case 4 { // after the fourth pixel
		x = y;
		y = x;  // swap coordinates (i believe verilog will actually swap using <=)
	}
	case 8 {  // after the final pixel
		x = -y;
		y = -x; // swap and negate coords, to get back to (x, y) from the start
		// then go onto the next angle
		pixnum = 0;
		state = ITER;
	}
}
```
