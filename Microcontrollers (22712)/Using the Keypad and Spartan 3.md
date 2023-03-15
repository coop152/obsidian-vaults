To use the keypad we must interface with the Spartan 3 FPGA, as the connectors that the keypad connects to are accessed through it. 
The FPGA is mapped to `0x2000_0000`, and each location is only a byte wide. You **MUST** use `LDRB` and `STRB` when interacting with it.
![](Pasted%20image%2020230315154347.png)
We have the keypad connected to the lower left port (named S0), so we are only concerned with these locations:
![](Pasted%20image%2020230315154822.png)
In fact we only use upperS0, so all we care about is this:
![](Pasted%20image%2020230315155011.png)
So we have a data register mapped to `0x2000_0002` and a control register mapped to `0x2000_0003`.
The data register can be imagined as a direct connection to the keypad. (It's actually going to the FPGA, which is relaying the signals to the keypad).
The control register defines, for each bit of the data register, if that bit is an input or an output. If a bit is 1, then the corresponding bit of the data register will be an **input** to the FPGA, meaning you can use it to read the output of the connected peripheral. If a bit is 0, then the corresponding bit will be an **output** from the FPGA, meaning you can use it to send data to the input of the connected peripheral.

## Configuring the FPGA to use the keypad
To discover how we should configure the control bits, let's consider the matrix of the keypad:
![](Pasted%20image%2020230315154332.png)
Bits 0..3 are outputs of the keypad (and thus inputs to the FPGA), while bits 5..7 are inputs of the keypad (and thus outputs of the FPGA). Therefore, we want bits 0..3 to be inputs and bits 5..7 to be outputs. We accomplish this by setting the upperS0 control register to `0b0000_1111` (the value of bit 4 is irrelevant).

```arm
			MOV r0, #0x2000_0002        ; keypad port
			MOV r1, #0b0000_1111        ; bit pattern to configure for keypad
			STRB r1, [r0, #1]           ; send to control reg (port + 1)
```

## Reading the keypad (scanning)
To check a single button on the keypad, we set its row high and then check if its column is high. For example, if we want to check if the '5' key is pressed, we should bring bit 6 of the data register high and then check bit 1.

```arm
			MOV r0, #0x2000_0002        ; keypad port
			MOV r1, #0b0100_0000        ; bring correct row high
			STRB r1, [r0]               ; send to data reg
			LDRB r1, [r0]               ; get result back from data reg
			TST r1, #0b0010             ; test the right bit
			BZS was_not_pressed         ; ANDS gave zero? button wasnt pressed
			BNZ was_pressed             ; ANDS non-zero? button was pressed
```
