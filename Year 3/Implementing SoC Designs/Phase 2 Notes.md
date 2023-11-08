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
		if (de_ack) {
			
		}
	}
}
```