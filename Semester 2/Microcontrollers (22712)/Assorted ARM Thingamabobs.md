## Constants
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

## Initialise stack pointer
```
		ADRL r0, _stack
...
		DEFS 0x1000
_stack
```

## Testing a bit
```arm
LDR r1, some_location   ; get something from memory you wanna test
TST r1, #0b0100_0000    ; test the 7th bit

BNE bit_was_set
BEQ bit_was_not_set
; or if you want
BZS bit_was_not_set
BNZ bit_was_set
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

## Change operating mode

| Abbreviation | Mode           | CPSR Code <br> (Binary) | CPSR Code <br> (Hex) |
| ------------ | -------------- | ----------------------- | -------------------- |
| USR          | User           | `1 0000`                | `0x10`               |
| FIQ          | Fast Interrupt | `1 0001`                | `0x11`               |
| IRQ          | Interrupt      | `1 0010`                | `0x12`               |
| SVC          | Supervisor     | `1 0011`                | `0x13`               |
| ABT          | Abort          | `1 0111`                | `0x17`               |
| UND          | Undefined      | `1 1011`                | `0x1B`               |
| SYS          | System         | `1 1111`                | `0x1F`               |

#### Change from Supervisor to System
```arm
MRS r0, CPSR        ; get the current CPSR
ORR r0, r0, #0xF    ; set the lower 4 bits
MSR CPSR, r0        ; overwrite the CPSR
```

#### Change from Supervisor to User (and enter user code)
```
MOV r0, #0xD0       ; code for user mode with no interrupts
MSR SPSR, r0        ; store in SPSR, thus changing mode when it is restored
ADRL LR, main       ; get the user code entrypoint
MOVS PC, LR         ; branch to user code, and restore SPSR
```

## Jumptables
```arm
			CMP r0, #table_max
			BHS out_of_range
			ADR r1, jump_table
			MOV LR, PC                ; points two instructions ahead
            LDR PC, [r1, r0, LSL #2]

jump_table  DEFW routine_label_1
			DEFW routine_label_2
			DEFW routine_label_3
			DEFW ...
```

## Other
[LCD Command Reference](https://mil.ufl.edu/3744/docs/lcdmanual/commands.html#Cds)
[Lab Manual](lab_manual.pdf)
