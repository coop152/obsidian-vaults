Syntax Analysis is a part of the front-end which takes a series of tokens as input (that is, the output of lexical analysis) and derives meaningful constructs from it.

## Expressing Syntax
You cannot sufficiently express every language construct using regular expressions. For example, a very important one is **balanced brackets**; you cannot write a regex to match balanced brackets (e.g. `(())` and `()()` but not `())(` ). Instead, we utilise **context-free grammars** which allows us to define a so-called **context-free syntax**.
![](Pasted%20image%2020230303134732.png)
## Derivations and Parse Trees
Derivation: a sequence of derivation steps. At each step, we choose a non-terminal symbol to replace; different choices can lead to different derivations.
There are two kinds of derivation which we are interested in:
Leftmost derivation: at each step, replace the leftmost non-terminal.
Rightmost derivation: at each step, replace the rightmost non-terminal.

A parse tree is a graphical representation oof a derivation that filters out the choice regarding the replacement order.
![](Pasted%20image%2020230303134943.png)
#### Derivations and Precedence
Consider the leftmost and rightmost derivations plus the parse tree for this sequence on this grammar:
`x - 2 * y`
![](Pasted%20image%2020230303135443.png)
You may notice that there are two different leftmost and two different rightmost derivations. Because of this, there are also different parse trees. Assuming a standard way of traversing the parse tree, these two different derivations will evaluate to $x - (2 * y)$ and $(x - 2) * y$. This demonstrates a problem with this grammar; it is **ambiguous**.
An ambiguous grammar is a grammar that produces more than one parse tree, leftmost derivation or rightmost derivation for some sentence. Only one needs to be true.
## Parsing Techniques
- Top-down parsers:
	- Construct the top node of the tree and grow towards the leaves, trying to match the input in pre-order (depth-first).
	- Pick some production and try to match the input; if you fail, backtrack.
	- Essentially, we find some leftmost derivation for the input string (which we scan left to right)
	- Some grammars are backtrack-free (known as predictive parsing).
- Bottom-up parsers
	- Construct the tree for an input string, beginning at the leaves and working up towards the top/root.
	- Bottom-up parsing, using left to right scan of the input, tries to construct a rightmost derivation in reverse.
	- Can handle a large class of grammars - used more commonly than top-down in modern compilers.