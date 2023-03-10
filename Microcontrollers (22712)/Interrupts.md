When an IRQ signal is raised, the interrupt service routine will be called as soon as the current instruction has been completed. The following actions are then performed:
- The IRQ link register is set to the address of the next instruction + 4
- The CPSR is saved in the SPSR
- The mode is set to IRQ in the CPSR
- IRQ is disabled in the CPSR (the IRQ disable bit is set high)
- The Thumb bit is cleared
- PC is set to 0x00000018

FIQ behaves similarly, except that both IRQ and FIQ are disabled and the PC is set to 0x0000001C.
The only user-mode registers that are changed are the PC and the CPSR, which are both stored in the IRQ-mode LR and the SPSR respectively. As interrupts change the operating mode, the mode must be changed back to user to return correctly.

## Interrupt Enable/Disable
Bit 7 in the CPSR will disable IRQ **when set high**, and bit 6 will disable FIQ. These bits can only be modified in privileged mode, as they are in the control byte of the CPSR.
On hardware startup these bits are set, meaning no interrupts will be serviced until they are cleared.
These interrupt disable bits are in the processor, and apply to all interrupts; Individual devices often have their own interrupt enable/disable bits that allow you to prevent interrupts from that device from reaching the processor at all. The interrupt enable bits are exposed on the boards at address `0x1000001C`:
| Bit | Function                                  |
| --- | ----------------------------------------- |
| 0   | Timer compare                             |
| 1   | Spartan FPGA                              |
| 2   | Virtex FPGA (Not fitted on boards)        |
| 3   | Ethernet interface (Not fitted on boards) |
| 4   | Serial RxD ready                          |
| 5   | Serial TxD available                      |
| 6   | Upper button                              |
| 7   | Lower button                              |
You also need to know which device raised an interrupt; interrupt bits are exposed at `0x10000018` in this same pattern which will be 1 when a device has raised an interrupt. Check this address to see which device raised the interrupt.

## State Preservation
The entry sequence only preserves the minimum required amount of state. The ISR must preserve any state that will be visible in user-mode (e.g. the contents of r0), which can be done by allocating a stack for IRQ mode.

## Interrupt Service Routine
During the ISR it is necessary to acknowledge the signal from the interrupt source; otherwise, it would not know that it's request had been satisfied and would continue to send interrupts.
On the boards, you acknowledge an interrupt by clearing the interrupting device's bit at `0x10000018`. If more than one bit is set, you should leave those until they have been serviced.

## ISR Exit
When the ISR is complete the state of the machine must be restored. Any saved registers must be reloaded, and the interrupt entry sequence must be reversed.
Note that the address which is saved in LR_irq is not the address of the next instruction; you must subtract 4 to get the correct address. You must also restore the previous mode and re-enable interrupts. This instruction will do all of this:
```arm
			SUBS PC, LR, #4
```
(Recall that moving a value to the PC with set flags enabled will also copy the contents of the SPSR into the CPSR.)
You can also add a hat (`^`) to an `LDMFD` (`POP`) instruction to make it restore the SPSR:
```arm
			SUB LR, LR, #4                    ; correct return addr
			STMFD SP!, {r0, r1, r2, LR}       ; save working registers
			...                               ; service the interrupt
			LDMFD SP!, {r0, r1, r2, PC}^      ; restore and return
```
