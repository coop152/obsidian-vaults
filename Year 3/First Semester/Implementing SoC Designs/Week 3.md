# Verification and Debugging
First, you should test the simple things:
- Does it reset?
	- Is the initial state achieved?
	- Are there unknowns in the circuit?
- Do the straightforward operations work properly?

When writing the later tests you should be sure:
- You test every statement
- You try to break it

## What to look for
Start with simple sequences:
- Is the protocol being honoured?
- Are state machines behaving as expected? (Needs to be observable to check internal state)
- Have you observed every transition in the state diagram? (use test coverage)
- …for all of the possible triggers?
- Does it terminate properly?

For example, a processor design:
- Feed the instruction bus with NOP instructions
- Does it reset and start fetching instructions?
- Is the fetch-decode-execute sequence running successfully?
- Does it handle slow memory properly? (e.g. can it wait for memory)

Then, check the output is actually correct:
- Can compare data values with independently generated test results
- Some data faults will be readily apparent, such as out-of-range values, the wrong number of operations being performed, graphics being obviously wrong (if you can view them)
- For certain designs, self-testing may be possible (e.g. a processor can be tested by running software on it, which will probably crash if there is an error)

## Designs for tests
Here are some suggested ways to design verification tests:
- Individual, deliberately targeted tests
	- Manually create test cases so that (at least) one case of every circumstance is exercised
	- Effective, but expensive (in time taken manually writing them)
- Algorithmically generated
	- for example, iterate over all possible cases of inputs, or over regular input patterns
	- Relatively easy to do, requires some design effort
- Random patterns ("Monte Carlo")
	- Can give high (but incomplete) coverage of common cases
	- Will probably miss specific input combinations
	- Cheap to produce