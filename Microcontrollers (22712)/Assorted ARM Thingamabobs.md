## Labels vs Constants
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
The `DEF` directives (e.g. `DEFW`, `DEFB`, `DEFS`) allocate space in memory and create a label that refers to that space.
## Initialise stack pointer
```
		ADRL r0, _stack
...
		DEFS &1000
_stack
```

