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
