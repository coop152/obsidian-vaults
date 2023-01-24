## Signed Formula
A Signed formula is an expression $A = b$, where $A$ is a formula and $b$ a boolean value (1 or 0).
-   A signed formula $A = b$ is true in an interpretation $I$ (denoted as $I \models A = b$) if $I(A) = b$.
-   If $A=b$ is true in $I$, we also say that $I$ is a model of $A=b$, or that I satisfies $A = b$.
-   A signed formula is satisfiable if it has a model.

## Tableaux
A tree-like structure with signed formulae at the nodes.
When written in text form, the syntax is:
$A=a|B=b$ means "formula A evaluates to a, or formula B evaluates to b"
$A = a,B = b$ means "formula A evaluates to a AND formula B evaluates to b"
The rules for evaluating these signed formulae
In tree form, "OR" is represented as branching while "AND" is represented as multiple formulae in a list at a node. e.g:
![](Pasted%20image%2020230124144844.png)
