## Constants (?)
The `EQU` directive creates a label with an arbitrary value; this can be used to replace immediate numbers. For example, you can convert this:
```
main            MOV     r1, #0x80000        ; load delay into r1
                MOV     r0, #0b0100_0100    ; load light pattern, red/red
                BL      light_and_delay
```
to this:
```
regular_delay   EQU     0x80000             ; approx. one second of delay
red_red_patt    EQU     0b0100_0100         ; red/red light pattern

main            MOV     r1, #regular_delay   ; load delay into r1
                MOV     r0, #red_red_patt    ; load light pattern, red/red
                BL      light_and_delay
```
The `DEF` directives (e.g. `DEFW`, `DEFB`, `DEFS`) allocate space in memory and create a label that refers to that space. If you want to change the value, or to hold anything that wouldn't fit in an immediate number, you have to use this.
Or, you can use the `LDR rd, =XXXX` pseudo-instruction to get full-size "immediate" numbers.

## Initialise stack pointer
```
		ADRL r0, _stack
...
		DEFS &1000
_stack
```

## Testing a bit
```arm
LDR r1, some_location   ; get something from memory you wanna test
TST r1, #0b0100_0000    ; test the 7th bit

BNE bit_was_set
BEQ bit_was_not_set
; or if you want
BZ bit_was_set
BNZ bit_was_not_set
```

## Bit Fiddling
	```arm
	LDR r1, some_location    ; wanna change bits in memory
	
	; set 6th bit to 1
	ORR r1, r1, #0b0010_0000
	
	; set 4th bit to 0
	BIC r1, r1, #0b0000_1000    ; equivalent to r1 = r1 && ¬(0b0000_1000)
	; or using AND
	AND r1, r1, #0b1111_0111    ; with the mask inverted
	
	; flip the 2nd bit
	EOR r1, r1, #0b0000_0010
	```