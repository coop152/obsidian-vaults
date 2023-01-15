Absolutely vital to VLSI (Very Large Scale Integration).
**VLSI Verification**: Done before silicon development, to fix issues with the design. These may be bugs, or other quality issues. This includes multiple kinds of verification, such as IP verification, RTL verification, timing verification, etc. **SoC Verification** is a similar but more board term.
**VLSI Testing/Validation**: Done after the silicon is developed, to validate the quality of the resulting silicon. If a problem is found during testing, the only solution is to recycle the silicon and try again, which is very costly.
**Controllability**: The ease with which input data can be given to the device under test. A very controllable device is easier to test. An example may be a multiplexer before the inputs to the chip, thus allowing the normal inputs to be substituted for a set of test inputs.
**Observability**: The ease with which output data can be read from the device under test. A very observable device is easier to test.

## What is "testing"?
Three things:
- Verification - Does your design do what you want? Did you actually make what you intended?
- Validation - Does the design fulfil the requirements? e.g. size, power usage
- Testing - Does this singular specimen work?
Verification and Validation can (and should) be done in software, but Testing is purely physical.
In essentially all cases (except for perhaps FPGAs) this checking is done by simulation and not by making physical prototypes. Making a chip is expensive, and you don't want to print any mistakes!

## Verification
Need to prove the logic/implementation works as desired with *the minimum amount of effort possible*.
Techniques include:
- Digital simulation
	- Verifies the logic
	- Reveals initialisation problems
	- No (or approximate) timing
	- Quick
![[Pasted image 20230115135045.png]]
- Analogue Simulation
	- Verifies the logic, as well as the timing
	- Reveals the true speed of the edges
	- Slow
![[Pasted image 20230115135101.png]]
## SoC Simulation
![[Pasted image 20230115135346.png]]
A balancing act between speed and accuracy, dependent on many factors including the performance of the simulating PC.
### Hardware Emulators
Programmable devices such as FPGAs are now large enough to be used for hardware simulation. Large logic systems can be synthesised and loaded onto an FPGA, and with enough FPGAs once can load a whole chip description. This is sometimes referred to as "hardware emulation". However, this method lacks the debugging features of software simulation; you cannot set breakpoints, you cannot observe any signal, you don't have the concept of X nodes, etc. The tradeoff is that the hardware emulation will be significantly faster and will have some kind of timing model (though it won't match the manufactured ASIC so its usefulness is limited).

## Testing
- VLSI chips are made on silicon wafers.
- Defects will always occur on these wafers; thus some chips will not work.
- The proportion of working chips in a batch is known as the "yield". Yields vary, but on a mature process you might expect something like 80%+. Yield is influenced by the area of a single chip: bigger chips are more likely to be faulty.
![[Pasted image 20230115141631.png]]
Testing determines which chips work and which don't.

## Components for Testing
### Linear Feedback Shift Registers
A type of shift register that generates pseudo random sequences. Useful in circuit testing as a method of generating test patterns, but also for **signature analysis**. 