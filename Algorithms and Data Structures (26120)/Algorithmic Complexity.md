## Rules for Analysing Algorithms
- We perform the analysis concerning a **computational model** (that is, not a specific device)
- We will usually use a generic uniprocessor **random-access machine** (RAM)
    - All memory **equally expensive** to access
    - Instructions executed one after another **(no concurrent operations)**
    - All reasonable instructions take **unit time**
	     - Except, of course, function calls
    - Constant word size
        - Unless we are explicitly manipulating bits