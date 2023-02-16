## What is a compiler?
A compiler (in a general sense) is a program that accepts program text in a certain language as input and produces program text in another language as output.
For example:
- C++ ==> Assembly
- C++ ==> C
- High Performance Fortran ==> Fortran
- $\LaTeX$ ==> PDF
- $\LaTeX$ ==> HTML

A compiler must:
- Preserve the meaning of the program being compiled
- "Improve" the source code in some way. Could be:
	- Speed (of compiled code)
	- Size (of compiled code)
	- Feedback (information provided to the user)
	- Debugging (target can be easy related to the source code)
	- Compile-time efficiency (fast compiles)
	- Not all of these may be possible at the same time.
A *good* compiler will:
- Generate correct code
- Generate fast code
- Conform closely to the specification of the input language
- Cope with any size of input program w.r.t text input size, program complexity, etc.
- Compilation time should scale linearly with the size of the source
- The diagnostic messages should be helpful
- The optimisations should be consistent
- It should work well with a debugger
