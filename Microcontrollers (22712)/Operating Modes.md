ARM has seven operating modes. Six of them are **privileged** and are provided for use by the operating system, while the other, **user mode**, is provided for application programmes.
By default, ARM runs in **supervisor mode**. This mode is meant for software set-up and for servicing supervisor calls (`SVC`) - ideally, applications should be run in user mode.
The current mode is given by the lower five bits of the `CPSR`. To change mode, you change these bits. For security reasons these cannot be changed in user mode, so once you enter user mode *there is no way back*. This security feature prevents user code from performing *privileged* operations.

| Abbreviation | Mode           | CPSR Code <br> (Binary) | CPSR Code <br> (Hex) |
| ------------ | -------------- | ----------------------- | -------------------- |
| USR          | User           | `1 0000`                  | `0x10`                 |
| FIQ          | Fast Interrupt | `1 0001`                  | `0x11`                 |
| IRQ          | Interrupt      | `1 0010`                  | `0x12`                 |
| SVC          | Supervisor     | `1 0011`                  | `0x13`                 |
| ABT          | Abort          | `1 0111`                  | `0x17`                 |
| UND          | Undefined      | `1 1011`                  | `0x1B`                 |
| SYS          | System         | `1 1111`                  | `0x1F`                     |

Note: Don't frivolously change the other bits of the `CPSR`! They are also important.
Example of changing from supervisor to user mode:
```arm
MRS r0, CPSR        ; get the current CPSR
BIC r0, r0, #&0F    ; clear the lower 4 bits
MSR CPSR_c, r0      ; rewrite the CPSR
NOP                 ; may be necessary on some ARM chips
```
Note that the extra `NOP` may be required on some older ARM chips due to a bug where the change was delayed.

## Multiple stack pointers
Each mode$^1$ has its own copies of `SP` and `LR` (along with the rest of the registers), so they can all have their own private stacks. It is advisable to set up the stack space and pointer for each space before using it. That means setting up the stack space for Supervisor and User initially, but the number will grow later. You only need to allocate "enough" space for the anticipated usage; for Supervisor mode, this won't be much. For user mode, it depends on the program.

$1$: But how do you set User's stack at the start if Supervisor can't access `R13_USR`? Well, there exists another mode called **System** that is privileged like Supervisor, but shares registers with User. Therefore you can switch to System mode, set `R13_USR`, and then **switch back to Supervisor**.

## Aborts
For security reasons it is desirable to offer protection for certain parts of the address space. For example in a multi user environment, one user should not be able to sabotage the code of another. In particular, no user should be able to access or modify I/O space directly as this could cause all kinds of undesirable effects.
In a desktop computer, the I/O devices will always be protected by the OS, which is trusted code that the user cannot modify.
At the machine level this means that certain memory accesses (specifically, when performed in user mode) may be disallowed. In the lab, the most likely scenarios for encountering an abort are:
- Code crashing and running off the end of the RAM => prefetch abort
- Attempting to read or write to an I/O port in user mode => data abort

## What happens when I try accessing a protected area?
First, the access cannot be allowed so the memory (or I/O) state won't be changed. And because something 'wrong' has occurred the processor traps (i.e. enters an exception) and enters trusted operating system code.
Arm has two aborts: **prefetch abort** and **data abort**.
- A prefetch abort occurs when the processor tries to fetch an instruction from a disallowed memory area.
- A data abort occurs if the processor tries to transfer data to or from a memory area which is disallowed.

## How to handle aborts on the lab machines?
When (if) you perform some kind of invalid action, the `PC` will jump to either `0x0000000C` for a prefetch abort or `0x00000010` for a data abort.