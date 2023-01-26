## Rules for Analysing Algorithms
- We perform the analysis concerning a **computational model** (that is, not a specific device)
- We will usually use a generic single-processor **random-access machine** (RAM)
    - All memory **equally expensive** to access
    - Instructions executed one after another **(no concurrent operations)**
    - All reasonable instructions take **unit time**
	     - Except, of course, function calls
    - Constant word size
        - Unless we are explicitly manipulating bits

## Input Size
Time and Space Complexity
- This is generally a function of the input size
    - E.g., sorting, multiplication
- How we characterize input size depends:
    - Sorting: number of input items
    - Multiplication: total number of bits
    - Graph algorithms: number of nodes and edges
