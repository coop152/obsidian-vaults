The Middle-End is the final part of the front-end, where the user's input (now in the form of a parse tree) is converted into the intermediate representation.
At this point, it has been determined (by syntax analysis) that the input is a valid sentence in the source language. However, the program is still not guaranteed to be error-free. What is wrong with this code?
![](Pasted%20image%2020230308121527.png)
## Context sensitive questions
To generate code, the compiler needs to answer questions such as:
- Is `x` a scalar, an array, a function? Is `x` event declared in this context?
- Are there names used which are not declared? Declared but not used?
- What declaration of `x` does each use refer to?
- Is the expression `x - 2 * y` type-consistent?
- In `a[i][j][k]`, is a actually a 3 dimensional array?
- Does `z` need to be allocated? How is this done? Is the value always valid for every usage?
- What does the `15` in `f = 15` mean? Is it an integer? A float?
- How many arguments does `foo()` take? Is it called with the right number?

None of these questions can be answered by a context-free grammar.
This phase is called **semantic analysis**. The answers to these questions depend on the values of identifiers, not the syntax of the program. We need non-local information, and potentially some computation, to check these properties.
Semantic analysis **does not** use format methods like parsing (using context-sensitive grammars and such is called **attribute grammars**) because it is too complicated. Ad hoc techniques which are unsuited for parsing are very effective for **semantic analysis**.
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