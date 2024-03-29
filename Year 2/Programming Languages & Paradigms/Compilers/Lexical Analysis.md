This step of compilation reads in a sequence of characters and produces a sequence of tokens. This is where the given source code is broken down into chunks of syntax. 
## Definitions
- Vocabulary: a finite set of symbols.
- String: a finite sequence of symbols from the vocabulary.
- Language: a set of strings over a fixed vocabulary.
- Grammar: a finite way of describing a language.
- Context-free grammar: A way of expressing a grammar. Some grammar $G$ is given as $G = (S, N, T, P)$ where
	- $S$ = starting symbol
	- $N$ = set of non-terminal symbols
	- $T$ = set of terminal symbols
	- $P$ = set of production rules
- A language is the set of all terminal productions of some CFG $G$. That is, every possible sequence that satisfies the production rules $P$ and ends with some terminal $\in T$.
## CFG Example
A grammar to produce arithmetic expressions:
$S = \text{Expr}$
$N = \{\text{Expr}, \text{Term}, \text{Factor}\}$
$T = \{+, *, (, ), x\}$
$P = \{$
	$\text{Expr} \rightarrow \text{Term}|\text{Expr}+\text{Term},$
	$\text{Term} \rightarrow \text{Factor}|\text{Term}*\text{Factor},$
	$\text{Factor} \rightarrow (\text{Expr})|x$
$\}$
An example of a possible string from this grammar (shortening the non-terminal names to their initials):
$E \Rightarrow E+T$
$\Rightarrow T+T$
$\Rightarrow F+T$
$\Rightarrow x+T$
$\Rightarrow x+T*F$
$\Rightarrow x+F*F$
$\Rightarrow x+x*F$
$\Rightarrow x+x*x$
## Motivation
Why study lexical analysis? 
- To avoid writing lexical analysers by hand
- To simplify the specification and implementation.
- To understand the techniques and technologies.

We want to know how to specify lexical patterns. Some parts of this are easy (e.g. defining operators, comments, whitespace, etc.) but some are significantly harder such as identifiers (letter followed by alphanumeric characters), numbers (integer vs floating point, multiple base literals, etc.).

## Regular Expressions
Regex patterns form a regular language. A regular language is a formula that describes a possibly infinite set of strings. For example:
![](Pasted%20image%2020230221144602.png)
## Building a Lexical Analyser
You could write a lexical analyser that checks case by case, like this:
![](Pasted%20image%2020230221144802.png)
This has the potential to be efficient, but it requires a lot of work and will be difficult to modify.
Instead, consider how regular expressions are evaluated:
![](Pasted%20image%2020230221145048.png)
These are the actions that take place in a lexical analyser. A string is evaluated against a set of states and transitions, being identified as some sort of token if it reaches a terminal state.
Using this concept, lexical analysis can be automated.

## Automating lexical analysis
Continuing on with the transition diagram concept, we implement a transition table: a 2D table with a column for each input symbol and a row for each state. Each row gives a set of states that can be reached from that state, and each entry gives the state that follows on from the given state when the given character is read.
![](Pasted%20image%2020230221145500.png)
With this, creating a lexical analyser is as simple as this:
![](Pasted%20image%2020230221145534.png)
With this approach, the first step of creating a lexical analyser is to create a generalised transition diagram representing the desired syntax. These transition diagrams are **finite automatons**, meaning they can be either:
- Deterministic (DFA): For every state and symbol combination, there is only one transition to another state
- Non-Deterministic (NFA): for every state and symbol combination, there may be more than one possible transition. for example, consider the regex `(a|b)*abb`:
![](Pasted%20image%2020230221145924.png)

In summary:
- Write down a regular expression that expresses the input language.
- Convert the regular expression to an NFA (using Thompson's construction).
- Convert the NFA to a DFA (using subset construction).
- Minimise the DFA (using Hopcroft's algorithm).
There exist programs that (pretty much) do this for you, such as `lex` and `flex`; these algorithms are well-known and well understood.

## RE to NFA
By following these rules, a regular expression can be translated to an NFA:
![](Pasted%20image%2020230221151051.png)
![](Pasted%20image%2020230221151137.png)
## NFA to DFA
![](Pasted%20image%2020230221151349.png)
Simple definitions:
- $\text{move}(s_i, a)$: the states that you can reach from state $s_i$ when the input symbol is $a$.
- $\epsilon\text{-closure}(s_i)$: the states that you can reach from state $s_i$ without needing another input symbol (i.e. where the transition symbol is $\epsilon$).

![](Pasted%20image%2020230221151713.png)
![](Pasted%20image%2020230221151930.png)
## DFA Minimisation
![](Pasted%20image%2020230221152236.png)
![](Pasted%20image%2020230221164453.png)
![](Pasted%20image%2020230221152455.png)
![](Pasted%20image%2020230221152645.png)
## Building fast scanners
Using a table to encode the transitions is easy to understand and code, but it involves lots of memory operations, and other operations in general. Instead, you can represent the transition logic directly in your code:
![](Pasted%20image%2020230221165847.png)
This code:
- Has fewer operations
- Avoids memory interaction (which is especially important for large tables)
- Has a lot of added complexity, which may make the code ugly and/or difficult to understand. However this is still generated automatically, so it doesn't really matter that much
## Practical considerations
Poor language design may complicate lexical analysis. For example:
```PL/I
if if then then = else else else = then
```
Limitations of lexical analysis have actually served to positively influence language design, removing many cases of confusing and awkward ambiguity like were present in older languages.

## Flex & Lex
Flex is a tool for generating lexical analysers. It takes 3 inputs:
- Regular expressions
- Pairs of regular expressions and C code
- auxiliary C code.
When the input is compiled it generates a C source file as output (called `lex.yy.c`). This file contains a routine `yylex()`. After compiling the C file, the executable will isolate tokens from the input according to the regular expressions and, for each token, execute the code associated with it. The array `char yytext[]` contains the representation of a token.
![](Pasted%20image%2020230221173217.png)
