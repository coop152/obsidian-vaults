
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

## Most common uses of compiler technology
- Translating a high-level program to object code. By far the most popular usage.
	- May be to binary executable code, or hardware synthesis (e.g. verilog)
- Optimisations for specific computer architectures
- Automatic parallelisation or vectorisation
- Performance instrumentation
- Interpreters, e.g. Python, Ruby, sh...
- Software development tools, e.g. debugging aids
- Security, e.g. checking that code is "safe" like the JVM using compiler analysis
- Text formatting
- Just In Time compilation
- etc. etc.

## Structure of a compiler
At the highest level of abstraction:
![](Pasted%20image%2020230216154401.png)
A compiler simply takes source code and outputs object code, along with some diagnostics if necessary.
On a conceptual level, it's like this:
![](Pasted%20image%2020230216154501.png)
the **Front-end** is the part that performs **analysis** on the source code.
- Recognises illegal programs and reports errors.
- "understands" the input program and converts its semantics into an intermediate representation (IR).
- Produces the IR, which forms the input of the **Back-end**.
- Can be very automated.
the **Back-end** is the part that performs the **synthesis** into the target language.
- Chooses the instructions to implement for each IR operation.
- Translates the IR into the target code.
- Must conform to the system interfaces.
- Not particularly good at being automated.
Typically the front-end is $O(n)$, while the back-end is **NP-complete**.
The benefit of this conceptual split is:
![](Pasted%20image%2020230216154934.png)
You can get $m \cdot n$ compilers using just $m+n$ components!
The language specific knowledge goes in the front-end, and the target specific knowledge goes in the back-end. This separation does not come for free, however.
The structure in terms of operations is as follows:
![](Pasted%20image%2020230216155110.png)
Note that the order of operations in the back-end is not as clear-cut as the order of the front-end. It may vary.

## Lexical Analysis
The very first step, applied directly to the source code.
- Read in the characters in the source program and group them into **words**.
- Isolate these words and identify the category that the words fall into.
- The output of lexical analysis is a series of **tokens**, which are pairs of the form `<type, lexeme>` or `<token_class, attribute>`. For example, `a=b+c` becomes `<id, a>, <=,>, <id, b>, <+,>, <id, c>`.
- Needs to keep record of each identifier (id) attribute; generates a **symbol table**.
- Also eliminates meaningless text, e.g. optional whitespace
- Speed is important; there are specialised tools for creating scanners, such as `flex`.

## Syntactical Analysis
- Imposes a hierarchical structure on the token stream.
- This structure is usually expressed by recursive rules.
- Context-free grammars formalise these recursive rules and guide syntax analysis.
For example:
![](Pasted%20image%2020230216160005.png)
- When the syntax has been determined using a parse tree or some other method, it will then be converted into a more minimal form that omits the details of how it was derived, such as an Abstract Syntax Tree (AST).
- ASTs are a form of IR.
![](Pasted%20image%2020230216160229.png)

## Semantic Analysis
- Collects the context information and checks for semantic errors, then annotates the nodes of the tree with results.
- For example:
	- Type checking: report an error if an operator has been applied to operands of an incompatible type
	- Check that referenced variables have been declared
	- Check that the names of variables are valid

## Intermediate code generation
- Translate language-specific constructs in the AST into more generic constructs.
- Defines the level of "generality": it should be straightforward to generate the target code from the intermediate representation chosen.
- An example form of IR (3-address code):
```
tmp1 = 4
tmp2 = tmp1 * a
tmp3 = tmp2 * c
tmp4 = b * b
tmp5 = tmp4 - tmp3
```

## Code Optimisation
The goal is to improve the intermediate code before code generation, thus improving performance. These optimisations can range from trivial (e.g. constant folding) to highly sophisticated (e.g. in-lining). For example, why have this instruction:
```
tmp1 = 4
tmp2 = tmp1 * a
```
when you could have:
```
tmp2 = 4 * a
```
Modern compilers perform such a wide range of optimisations that one could argue the actual structure of a compiler is:
![](Pasted%20image%2020230216161203.png)

## Code Generation
Map the IR into a linear list of instructions for the target machine in a symbolic form.
- Instruction selection: match the patterns to decide which instruction(s) shall represent each IR operation.
- Register allocation: Deduce how to place values in registers when they are in use, even though there are a limited number. This is an NP-Complete problem.
- Instruction scheduling: Take advantage of multiple functional units. This is also an Np-Complete problem.
Some specific properties of the target may be used to optimise the code further.
Finally, the object code is generated alongside other information the OS needs.


## LLVM
An open source toolchain of modular compiler components that can be used to implement compilers for any language.