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

## Changing Mode
```arm
MRS
```