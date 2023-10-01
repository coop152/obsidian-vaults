# Intro Hour
## VDU Controller
![](Pasted%20image%2020230928110537.png)
The task of the VDU controller is to:
- Generate Sync signals and blanking signals
- Framestore addresses and read the framestore data

It is a fairly simple state machine, which can often be programmed to accommodate different displays (size, colour, aspect ratio).

## Framestore/Framebuffer
In theory, a 2D array of memory where each cell is a 'numeric' representation of a single pixel's colour.
Depending on the colour depth, the length of one location will vary; this course assumes 8-bit colour (as in 8 bits per pixel, not per colour. 256 colours.)

# Verilog

## An example testbench
```verilog
// clock with a period of 10
initial clk = 1;
always #5 clk <= !clk;

// limit on simulation runtime
initial #1000 $stop;

initial
begin
	// initialise the DUT
	reset <= 1;
	en <= 1;
	@ posedge clk; // wait for a clock cycle
	reset <= 0;
	while (c == 0) @ posedge clk; // wait and keep checking on each cycle until c == 1
	en <= 1`bx; // Make en undefined
end
```
## An example circuit
```verilog
module counter (input wire clk
			    input wire en
			    output reg [3:0] count
			    output wire c);
// Internal variables
reg [3:0] next;

// run when count changes
always @ (count)
begin
	next = count + 1; // blocking assignment
	if (count == 9) next = 0;
end

// run on every clock or when reset goes high
always @ (posedge clk, posedge reset)
	if (reset) count <= 0; // asynchronous reset
	else if (en) count <= next;
	else count <= count; // can be safely omitted, but is more clear

assign c = (count == 9) // continuous assignment 
endmodule
```
## Structural Verilog
```verilog
and2 gate_1 (.a(x), .b(y), .q(z));
```
Where:
- `and2` is the module/part name
- `gate_1` is the name of the instance being created
- `a`, `b` and `q` are the names of the module's inputs/outputs
- `x`, `y`, `z` are wires at the higher hierarchical level
## Synthesis
![](Pasted%20image%2020230928110349.png)

## Truth tables
![](Pasted%20image%2020230928110406.png)
A wire can be in 4 states: 1, 0, z, x.
Z (high impedance) is the state of a wire when there is nothing driving the wire. For example, if you forgot to connect something. 
X is a simulation-only state that represents a wire which is either 1 or 0, but the simulator doesn't know which. Can occur intentionally, or by mistake if two value outputs are connected together.

## Generate
Kinda like a macro. Compile/synthesis-time code.
```verilog
generate
genvar i; // variable only exists at compile/synthesis-time
for (i = 0; i < 4; i = i + 1)
	begin: block
		// instantiate module inside loop, using loop variable
		memory mem((.CE(en[i]), .WE(WE), ... etc ...))
	end
endgenerate
```
(This code would go directly inside a module.)

## Parameters
```verilog
// module with parameter `n`
module adder (a, b, s);
parameter n = 16;
input  wire [n-1:0] a, b;
output wire [n-1:0] s;

assign s = a + b;

endmodule

module example();
/* define wires here */

adder add16(pp, qq, rr); // use default size
adder add32 #(32) (ss, tt, uu); // verilog 95 style (give params in order)
adder add8 #(.n(8)) (.a(vv), .b(ww), .s(xx)); // verilog 2001 style (named like connections)
endmodule
```