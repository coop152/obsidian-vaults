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

The rules for evaluating these signed formulae are as follows:
$$\begin{align*} (A_1 \wedge \dots \wedge A_n) = 0 &\rightsquigarrow A_1 = 0 | \dots | A_n = 0 \\ (A_1 \wedge \dots \wedge A_n) = 1 &\rightsquigarrow A_1 = 1 , \dots , A_n = 1 \\ \\ (A_1 \vee \dots \vee A_n) = 0 &\rightsquigarrow A_1 = 0 , \dots , A_n = 0 \\ (A_1 \vee \dots \vee A_n) = 1 &\rightsquigarrow A_1 = 1 | \dots | A_n = 1 \\ \\ (A_1 \rightarrow A_2) = 0 &\rightsquigarrow A_1 = 1, A_2 = 0 \\ (A_1 \rightarrow A_2) = 1 &\rightsquigarrow A_1 = 0 | A_2 = 1 \\ \\ (\neg A_1) = 0 &\rightsquigarrow A_1 = 1 \\ (\neg A_1) = 1 &\rightsquigarrow A_1 = 0 \\ \\ (A_1 \leftrightarrow A_2) = 0 &\rightsquigarrow A_1 = 0, A_2 = 1 | A_1 = 1, A_2 = 0 \\ (A_1 \leftrightarrow A_2) = 1 &\rightsquigarrow A_1 = 0, A_2 = 0 | A_1 = 1, A_2 = 1 \end{align*}$$
A branch is said to be "closed" if it fulfils any of these conditions:
- It contains both $p = 1$ and $p = 0$ for some variable $p$
- It contains $\top = 0$
- It contains $\bot = 1$

In tree form, "OR" is represented as branching while "AND" is represented as multiple formulae in a list at a node. e.g:
![](Pasted%20image%2020230124144844.png)
#### Properties
- To prove satisfiability, construct the tableau $A = 1$ and show that it has a complete open branch.
- To prove validity, construct the tableau $A = 0$ and show that it is closed.
- To prove equivalence, construct the tableau $(A \leftrightarrow B) = 0$ and show that it is closed.
- To find every model of $A$, construct a fully expanded tableau for $A = 1$.
