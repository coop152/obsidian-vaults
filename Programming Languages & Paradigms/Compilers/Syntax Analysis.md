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
