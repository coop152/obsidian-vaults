## Module
Represents a design in it's entirety. Takes some number of input signals and generates some output signals. Encapsulates all of the components and/or functionality within, providing a black box that other modules can use. Vital for a hierarchical design pattern.
This module has three inputs, two of which are buses, and two outputs, one of which is a bus.
```verilog
module example(
			input wire [3:0] a,
			input wire [7:0] b,
			input wire       c,
			output wire [7:0] d,
			output wire       e
);
// module implementation goes here
endmodule
```
The type of an input is always `wire`, but the type of an output depends on the implementation:
`output wire`: When the output is being set using structural Verilog or the `assign` keyword. for example:
```verilog
module example (output wire a);

assign a = 1; // continuous assignment

otherMod omod(x, y, a); // structural Verilog

endmodule
```
`output reg`: When the output is being set using behavioural Verilog, e.g. blocking `=` assignments or non-blocking `<=` assignments.

## Assignments
**Blocking assignment** `=`: Use *only* for combinatorial blocks. e.g.
```verilog
always @(a, b, c) begin
	internal_x = a;
	if (b)
		internal_x += c;
	q = internal_x;
end
```
**Non-blocking assignment** `<=`: Use in any other case, such as in synchronous logic. e.g.
```verilog
always @(negedge clk) begin
	if (clk_enable) begin
		q <= d;
		qbar <= ~d;    // DO NOT: qbar <= ~q;
	end
end
```
**Don't mix variables in always blocks**: If a variable is used in one always block, don't use it in another.
> [!fail] DO NOT
```verilog
always @(posedge clk) begin
	if (CE) Q <= D;
end

always @(posedge reset) begin
	Q <= 0;
end
```
> [!check] DO
```verilog
always @(posedge clk, posedge reset) begin
	if (reset) Q <= 0;
	else if (CE) Q <= D;
end
```

## Examples
A register module with the following specification:
Inputs:
-   clock – global clock signal
-   reset – active low signal that acts asynchronously and sets the output Q to 0
-   preset – active high signal that acts asynchronously and sets the output Q to 1
-   CE – clock enable - only allows the output Q to be updated with input D when CE = 1 on the rising edge of the clock. When CE = 0 the output is not updated.
-   D – 1-bit data input to the register
Outputs
-   Q - 1-bit stored value
-   Qbar – the inverse of the stored value
```verilog
module register(input wire clock,  
                input wire reset,  
                input wire preset,  
                input wire CE,  
                input wire D,  
                output reg Q,  
                output wire Qbar);  

assign Qbar = ~Q;  

always @(posedge clock, negedge reset, posedge preset) begin  
	if (reset) Q <= 0;  
    else if (preset) Q <= 1;  
    else if (CE) Q <= D;  
end  

endmodule
```