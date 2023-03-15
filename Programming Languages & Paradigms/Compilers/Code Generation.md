We are now done with the front/middle-end and have reached the back-end, and we have some (partially) optimised IR that needs to be converted to the final target code. The broad strokes of this issue are:
- We need to select the right instructions in the target language - this is a pattern matching problem
- We need to perform further optimisations, this time according to the specific capabilities of the target platform

The concrete steps involved in the back-end are:
- Instruction selection: map the intermediate representation into target code
- Register allocation: Decide which values will reside in which registers (only applies to an assembly/machine code target)
- Instruction scheduling: Reorder operations to hide latency (e.g. in pipelined and superscalar systems)

We could solve all of these independently, but they all end up being inter-related.

## Instruction Selection
This step is essentially pattern matching; we need to match certain IR patterns to snippets of target code. It is easy to make locally optimal choices (i.e. just choose the most optimal instructions for the current pattern) but making a globally optimal program is an NP-complete problem. Note that in this step we assume we have enough registers to represent all values.
For ongoing examples, assume a RISC-like target language defined like this:
![](Pasted%20image%2020230315121204.png)
#### Arithmetic expressions
We can adopt a simple tree walk scheme, and emit code in post-order:
![](Pasted%20image%2020230315121346.png)
This (extremely simple) method simply represents each variable as a load from memory, and each operator as the corresponding operation. Any literals are also loaded into a register. This method demonstrates some issues that must be handled when generating code for arithmetic expressions:
- What about values that have already been loaded into registers?
	- We need to modify the IDENTIFIER case.
- Why evaluate the left subtree first and not the right?
	- You should evaluate the most demanding (i.e. requiring the most registers) subtree first (this is the Sethi-Ullman labelling scheme)
- You might need a second pass to minimise register usage and improve performance.
- The compiler could also be taking advantage of commutativity and associativity for integers to improve code.
- You might also be able to reduce multiplications down to shifts and adds, depending on the values in the expressions. For example:
	- $325x = 256x+64x+4x+x$ 
	- On some systems, integer multiplication may be significantly slower than other operations.

#### Array references
The compiler needs to decide on a memory storage scheme for N-dimensional array elements:
- Row-major order: store as a sequence of consecutive rows (most common)
	- `A[1, 1], A[1, 2], A[1, 3], A[2, 1], A[2, 2], A[2, 3]`
	- Derive an element's place with the formula $\text{baseAddr} + w\cdot(i\cdot(m+1)+j)$ where n, m are the array sizes `[n][m]`, i, j are the desired locations `[i][j]` and w is the size of an element.
- Column-major order: store as a sequence of consecutive columns (FORTRAN does it this way):
	- `A[1, 1], A[2, 1], A[3, 1], A[1, 2], A[2, 2], A[3, 2]`
	- Derive an element's place with the formula $\text{baseAddr} + w\cdot(j\cdot(m+1)+i)$ where n, m are the array sizes `[n][m]`, i, j are the desired locations `[i][j]` and w is the size of an element.

#### Control Flow
- `if expr then stmt1 else stmt2`
	1. Evaluate the expression to true or false
	2. If true, fall through to the `then` block, otherwise branch to the else
	3. If false, branch to the else, otherwise fall through to the `then` block
- While loop, for loop, do loop (e.g. `while (expr) do stmt`)
	1. Evaluate expr
	2. If false, branch beyond the end of the loop, if true fall through to stmt
	3. At the end of stmt, re-evaluate expr
	4. If true, branch to the start of the loop, if false, fall through
- Complex boolean expressions can also be evaluated using treewalk, but it requires more. For example:
	- Short-circuiting. the expression `x != 0 && y / x > 1` **requires** short circuiting to prevent a divide by zero. May also apply to higher-level things, e.g. `while (file.notEmpty() && file.readByte() != "\0")` requires short circuiting to prevent the program from trying to read past the end of a file.
#### Procedures/Functions/Methods
Procedures are fundamental to building complex software, but they are a high-level abstraction that has no analogue in low-level hardware. The ocmpiler needs to generate code that emulates the behaviour of procedures. For each call, the calling method must arrange some memory in preparation for the call (**pre-call**), the called function must set up its own memory for variables and such (**prologue**), the called function must discard its memory after it's done (**epilogue**) and the caller must then clean up the memory it set up for calling (**post-return**).
![](Pasted%20image%2020230315125237.png)
This is a costly abstraction which the compiler will attempt to avoid if at all possible. For example, the compiler may **inline** some functions, meaning it will insert the function directly into the caller, avoiding all of this calling overhead. This results in code duplication, and the compiler must decide somehow if the trade-off between code size and speed is worth it.