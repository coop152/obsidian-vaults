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
`output reg`: When the output is being set using behavioral verilog, e.g. blocking `=` assignments or non-blocking `<=` assignments.

## Assignments
**Blocking assignment** `=`: Use *only* for combinatorial blocks. e.g.
```verilog
always
```