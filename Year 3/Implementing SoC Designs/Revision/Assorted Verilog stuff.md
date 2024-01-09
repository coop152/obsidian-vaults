### Guidelines
- Use blocking assignments for combinatorial logic
    - this should evaluate every time its inputs change
- Use non-blocking assignments for D-type (edge triggered) registers
    - typically ‘posedge clk’
- Don't mix blocking (=) and non-blocking (<=) assignments ...
    - ... to a particular variable
    - ... within the same always block
- Keep all assignments to a particular variable in the same block
- Try to avoid transparent latches  
    always @ (D, En) if (En) Q = D;
    - only do this if you must
    - easily created accidentally: remember ‘else’s and ‘default’s
    - may be functionally harmless but introduce redundant logic
    - some tools may produce warnings if you do