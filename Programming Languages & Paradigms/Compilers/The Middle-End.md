The Middle-End is the final part of the front-end, where the user's input (now in the form of a parse tree) is converted into the intermediate representation.
At this point, it has been determined (by syntax analysis) that the input is a valid sentence in the source language. However, the program is still not guaranteed to be error-free. What is wrong with this code?
![](Pasted%20image%2020230308121527.png)
## Context sensitive questions
To generate code, the compiler needs to answer questions such as:
- Is `x` a scalar, an array, a function? Is `x` available in this context?
- Are there names used which are not declared? Declared but not used?
- What declaration of `x` does each use refer to?
- Is the expression `x - 2 * y` type-consistent?
- In `a[i][j][k]`, is a actually a 3 dimensional array?
- Does `z` need to be allocated? How is this done? Is the value always valid for every usage?
- What does the `15` in `f = 15` mean? Is it an integer? A float?
- How many arguments does `foo()` take? Is it called with the right number?

None of these questions can be answered by a context-free grammar.
This phase is called **semantic analysis**. The answers to these questions depend on the values of identifiers, not the syntax of the program. We need non-local information, and potentially some computation, to check these properties.
Semantic analysis **does not** use formal methods like parsing (using context-sensitive grammars and such for semantic analysis is called **attribute grammars**) because it is too complicated. Ad hoc techniques which are unsuited for parsing are very effective for semantic analysis.
## Type Systems
The **type** of a value is a set of properties associated with it. The set of all types in a programming language is called a **type system**.
**Type checking** refers to assigning/inferring types for expressions and checking that they are used in legal contexts.
The components of a type system include:
- Base/Built-in types, such as `int`, `char`, `String`
- Rules that allow the construction of new types (e.g. construct an int with a literal like `23`, construct a float with a literal like `12.3`, construct an Object using the `new` keyword)
- Rules that determine if two types can be used equivalently (e.g. can an `int` and a `short` be added? What about an `int` and a `float`?)
![](Pasted%20image%2020230308122226.png)

Type checking can be done at compile time (statically checked) or at run time (dynamically checked), depending on the design of the language.
If the language requires the programmer to declare a variable before using it, inference is not difficult (e.g. `int x;`). However, this becomes a real challenge for languages that don't require declarations.
## Symbol Tables
A symbol table is a data structure which acts as a central repository of facts. In a compiler there could be one symbol table or multiple.
The symbol table is first created during lexical analysis, and is retained across compilation (and even after, as it can be useful for debugging).
Using a symbol table, one can check basic semantic questions **during lexical analysis** by associating a snippet of code with each production rule and executing it every time the parser reduces that production. For example:
- Code that checks if a variable is declared prior to use (For a production rule like $\text{Factor} \rightarrow \text{id}$).
- Code that checks that each operator and its operands are type-compatible (For a production rule like $\text{Term} \rightarrow \text{Term} * \text{Factor}$)

This type of evaluation can be integrated nicely into LR(1) parsing.
But what is in the symbol table?
- Variable names
- Declared constants
- Procedure/Function names
- Literal constants
- Source text labels
- Compiler-generated temporary values

In practise, many different tables will exist. They will be accessed frequently; efficiency of access is critical!
#### Organising the symbol table
How do we represent the symbol table in order to maximise efficiency?
- Linear List: A simple approach, and has no fixed size, but is inefficient; lookup is $O(n)$.
- Binary Tree: An unbalanced tree would behave similarly to a linked list (and it is not entirely unlikely that symbols will be entered in a sorted order). If the tree was balanced, perhaps by using a dynamically rebalancing type of tree, lookups could be $O(log_2n)$. Better than a linear list, but still inefficient.
- Hash table: The solution used by modern compilers. Use a function to map names/identifiers into integers; this is potentially $O(1)$, but it requires:
	- An inexpensive hash function that behaves well (e.g. maps uniformly)
	- A policy to handle collisions

## Other symbol table issues
- Choosing a good hash function is of paramount importance. This problem has had a lot of work done on it; for example, see universal hash function, Knuth's multiplicative function, etc... The theory is very important in this case!
- Lexical scoping is a problem in most languages:
	- Many languages introduce independent name scopes. For example, 
		- C has global, static, local and block scopes
		- Pascal can have nested procedure declarations
		- C++, Java, and other OOP languages have class inheritance, nested classes, packages, namespaces, etc...
	- At any given point, which declaration of `y` is currently being used?
	- Multiple symbol tables must be allocated and initialised for each scope.
	- It is by far the easiest to just have a single global namescope, but it isn't the most efficient; if you define a variable one time at the start of the program and never use it again, it will remain in the symbol table.

## Intermediate Representation
Once the code has been checked for (lexical, syntactical, and semantic) errors, it needs to be converted into a convenient intermediate representation. This representation can be used to:
- Facilitate retargeting (compile for different target architectures)
- Enable machine-independent code optimisations and/or aggressive code generation strategies.

An IR should be easy to generate and manipulate, while having a high level of abstraction from any one target architecture and a minimal size. These decisions on how the IR is designed have a major impact on the speed and effectiveness of a compiler.

#### Types of IR
- Graphical (structural): such as a tree
	- Close to the source code and the parse tree
	- Node and edge structures tend to be large
- Linear: pseudo-code for some abstract machine.
	- The format of these kinds of IR may vary significantly
	- Close to the target code
- Hybrid: some combination of the above

There may also be an auxiliary IR that captures properties of the code instead of the whole code; these are usually graphical.
There is no universally correct choice for the type of IR to use; it depends on the goals of the compiler.

#### Parse Tree to Abstract Syntax Tree
Why not just use the parse tree? It contains a lot of unnecessary information:
![](Pasted%20image%2020230308131038.png)
Instead, let's convert it to an Abstract Syntax Tree (AST):
1. Traverse the tree in post-order (that is, traverse the node's left child, then its right child, then the node itself.)
2. Remove any non-terminal symbols

For the previous example:
![](Pasted%20image%2020230308131424.png)

## Abstract Syntax Trees (ASTs)
An AST is a parse tree with the non-terminal symbols removed. It is a near source-level representation; the source code can be easily generated from an AST by performing an in-order traversal, printing each node as it is visited.

## Three-address code
An umbrella term used to describe many different linear representations, where each statement is a single operator with at most three operands. For example:
![](Pasted%20image%2020230308132151.png)
This kind of IR is compact, makes intermediate values explicit, and resembles the instruction set of many kinds of machine.

## Other N-address codes
**Two address code** is more compact. In general, it allows statements of the form `x = x OP y` with a single operator and at most two operands. This form resembles certain instruction sets better than three-address does, but it less representative for some others.
![](Pasted%20image%2020230308132451.png)
**One address code** is more compact still, and resembles the instruction set of a stack machine. Is useful for environments where space is at a premium (has been used to construct bytecode interpreters for Java).
![](Pasted%20image%2020230308132609.png)
## Auxiliary Graph Representations
Can be useful for analysis.
- **Control-Flow Graph (CFG)**: models the way that the code transfers control as a result of conditional or loop statements.
	- Node: a single basic block (=a maximal straight line of code)
	- Edge: transfer of control between basic blocks.
	- (Captures loops, if statements, case, goto, ...).
- **Data Dependence Graph**: encodes the flow of data.
	- Node: program statement
	- Edge: connects two nodes if one uses the result of the other
	- Useful in examining the legality of program transformations
- **Call Graph**: shows dependencies between procedures.
	- Useful for inter-procedural analysis.

## High-level Code Optimisations
Once the IR is in place, the compiler may try to apply some transformations in order to optimise the program. The goal is to improve the performance of the program in some way (e.g. reduce runtime, code size, power consumption, etc).
Issues include:
- **Legality**: The meaning of the program must be preserved.
- **Benefit**: Must improve performance on average or common cases.
- **Compile-time cost is justifiable**: The improvement in runtime performance is good enough to justify any worsening at compile-time (i.e. longer compile times)

Finding an appropriate sequence of transofmrations is a major challenge: modern optimisers are structure into a series of passes (i.e. each optimisation is applied on top of the last).
These transformations may improve the program at the source level (e.g. algorithm specifics), the IR level (e.g. machine independent transformations) or the target level (machine-dependent transformations).
Typical transformations include:
- Discover and propagate constant values 
- Remove unreachable and/or redundant code
- Replace a certain kind of computation with an equivalent but more efficient form

#### Classifications
![](Pasted%20image%2020230308133804.png)

#### Some transformations
![](Pasted%20image%2020230308133930.png)
![](Pasted%20image%2020230308134143.png)
#### Loop Unrolling
![](Pasted%20image%2020230308134314.png)