Syntax Analysis is a part of the front-end which takes a series of tokens as input (that is, the output of lexical analysis) and derives meaningful constructs from it.

## Expressing Syntax
You cannot sufficiently express every language construct using regular expressions. For example, a very important one is **balanced brackets**; you cannot write a regex to match balanced brackets (e.g. `(())` and `()()` but not `())(` ). Instead, we utilise **context-free grammars** which allows us to define a so-called **context-free syntax**.
![](Pasted%20image%2020230303134732.png)
## Derivations and Parse Trees
Derivation: a sequence of derivation steps. At each step, we choose a non-terminal symbol to replace; different choices can lead to different derivations.
There are two kinds of derivation which we are interested in:
Leftmost derivation: at each step, replace the leftmost non-terminal.
Rightmost derivation: at each step, replace the rightmost non-terminal.

A parse tree is a graphical representation of a derivation that filters out the choice regarding the replacement order.
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
## Top-down Parsing
1. Begin with the starting symbol of the grammar. This is the root of the parse tree.
2. Repeat these steps until you fully match the input string:
	1. Find the leftmost non-terminal symbol in the current sentence (sentential form)
	2. Select a production rule with A on its left-hand-side and substitute with the right-hand-side
	3. Match any terminal symbols to the input
![](Pasted%20image%2020230303140600.png)
One of the factors that can lead to non-termination like in the right example is **left-recursive grammars**.
A grammar is left-recursive if it has some non-terminal symbol $A$ such that there is a derivation $A \Rightarrow Aa$ for some string $a$. Left-recursive grammars can cause a top-down parser to go into an infinite loop.
To eliminate left-recursion, in many cases it is sufficient to replace $A \rightarrow Aa|b$ with $A \rightarrow bA'$ and $A' \rightarrow aA'|\epsilon$.
![](Pasted%20image%2020230303141441.png)

So we can produce a top-down parser, but if it picks the wrong production rule it still has to backtrack. Really, we want a distinct way of choosing the correct production rule for any given rule. **Idea**:
#### Lookahead
Look ahead in the input and use upcoming information to pick the correct rule.
- How much lookahead is needed? In general, an arbitrarily large amount. Fortunately, most programming languages fall into subclasses of context-free grammars that can be parsed with a limited amount of lookahead.
The **LL(1) property** says that if $A \rightarrow B$ and $A \rightarrow C$ both appear in the grammar, we need to make sure that any first terminal symbol produced by B is different from any first terminal symbol produced by C. This would allow the parser to make a correct choice with a lookahead of exactly one symbol.
![](Pasted%20image%2020230303142450.png)

## Bottom-up Parsing
Given a grammar $G$, construct a parse tree for some sentence by starting from the leaves and working to the root (i.e. work from the input sentence back toward the start symbol S).
![](Pasted%20image%2020230303142817.png)
What are we actually trying to find here?
- A substring $B$ that matches the right side of a production that occurs as one step in the rightmost derivation. Informally, this substring is called a **handle**.
- If a grammar is unambiguous, then every right-sentential form has a unique handle. So if we know these handles, we have a derivation!
![](Pasted%20image%2020230303143323.png)
![](Pasted%20image%2020230303143355.png)
![](Pasted%20image%2020230303143524.png)
