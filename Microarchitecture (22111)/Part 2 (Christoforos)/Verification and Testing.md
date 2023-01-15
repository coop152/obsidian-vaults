Absolutely vital to VLSI (Very Large Scale Integration).
**VLSI Verification**: Done before silicon development, to fix issues with the design. These may be bugs, or other quality issues. This includes multiple kinds of verification, such as IP verification, RTL verification, timing verification, etc. **SoC Verification** is a similar but more board term.
**VLSI Testing/Validation**: Done after the silicon is developed, to validate the quality of the resulting silicon. If a problem is found during testing, the only solution is to recycle the silicon and try again, which is very costly.

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
