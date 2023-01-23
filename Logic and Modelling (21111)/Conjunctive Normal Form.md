A formula is in Conjunctive Normal Form (CNF) if it is a conjunction of disjunctions, for example:
$$A = (p \lor q \lor r) \land (¬p \lor ¬q) \land q$$
Each group of disjunctions is called a **clause**. There are multiple types of clauses:
- Regular clause: A series of disjunctions, $n > 1$. e.g. $p \lor q \lor r$.
- Empty clause: An empty series of disjunctions, $n = 0$. Represented as $\square$. **This clause is false in every interpretation.**
- Unit clause: A "disjunction" with one literal, $n = 1$.
- Horn clause: A clause with at most one positive literal, e.g. $¬p \lor ¬q \lor r$. Also includes completely negative clauses.

An interpretation $I$ satisfies a formula in CNF if and only if it satisfies every clause in it.
An interpretation $I$ satisfies a clause if and only if it satisfies at least one literal in the clause.
An empty set of clauses **is always satisfiable**. Not to be confused with a set of empty clauses, which will always be unsatisfiable due to containing nothing but unsatisfiable empty clauses.

## The CNF Transformation
![](Pasted%20image%2020230123142303.png)
Using these formulae, any propositional formula can be translated into CNF. Why would you do this? Because it allows us to change the problem we are trying to solve; CNF reduces the problem of satisfiability *on a formula* down to the problem of satisfiability *on a set of clauses*, which is easier.

## Definitional CNF Transformation
There is a problem, however, with the regular transformation; the equivalence ($\leftrightarrow$) will exponentially expand in size, turning a relatively small formula into a huge set of clauses.
![](Pasted%20image%2020230123142831.png)
To resolve this issue, we can introduce names for subformulae.
First, add a new formula to the set stating that the new variable $n$ is equivalent to a subformula.
$$p_1 \leftrightarrow (p_2 \leftrightarrow (p_3 \leftrightarrow (p_4 \leftrightarrow (p_5 \leftrightarrow p_6))))$$$$n \leftrightarrow (p_5 \leftrightarrow p_6)$$
And repeat this until the equation has been completely collapsed.
$$p_1 \leftrightarrow (p_2 \leftrightarrow (p_3 \leftrightarrow (p_4 \leftrightarrow (p_5 \leftrightarrow p_6)))) $$$$ p_1 \leftrightarrow (p_2 \leftrightarrow n_3) $$$$ n_3 \leftrightarrow (p_3 \leftrightarrow n_4) $$$$ n_4 \leftrightarrow (p_4 \leftrightarrow n_5) $$$$ n_5 \leftrightarrow (p_5 \leftrightarrow p_6)$$
Converting this new set of formulae only creates 4 copies of $p_6$, compared to 32 with the original formula. Note that these new formulae are not equivalent to the original formula, they are only equisatisfiable, which is all that is required for the problem we are trying to solve.

## Finding Satisfiability using DCNF
1. Draw a table header like this, and add your initial formula:

| Name  | Subformula                                                                            | Definition | Clauses |
| ----- | ------------------------------------------------------------------------------------- | ---------- | ------- |
| $n_1$ | $¬((p \rightarrow q) \land (p \land q \rightarrow r) \rightarrow (p \rightarrow ¬r))$ |            |         |
2. Break the formula down, one connective at a time. For this example, remove the outermost negation:

| Name  | Subformula                                                                            | Definition                 | Clauses |
| ----- | ------------------------------------------------------------------------------------- | -------------------------- | ------- |
| $n_1$ | $¬((p \rightarrow q) \land (p \land q \rightarrow r) \rightarrow (p \rightarrow ¬r))$ | $n_1 \leftrightarrow ¬n_2$ |         |
| $n_2$ | $(p \rightarrow q) \land (p \land q \rightarrow r) \rightarrow (p \rightarrow ¬r)$    |                            |         |
Observe that a new definition has been added to the previous row upon filling in this row's formula. Think of the equivalence ($\leftrightarrow$) here as an equals, saying that $n_1 = ¬n_2$.

3. Repeat this until the formula is completely decomposed:

| Name  | Subformula                                                                            | Definition                 | Clauses |
| ----- | ------------------------------------------------------------------------------------- | -------------------------- | ------- |
| $n_1$ | $¬((p \rightarrow q) \land (p \land q \rightarrow r) \rightarrow (p \rightarrow ¬r))$ | $n_1 \leftrightarrow ¬n_2$ |         |
| $n_2$ | $(p \rightarrow q) \land (p \land q \rightarrow r) \rightarrow (p \rightarrow ¬r)$    |                            |         |
| $n_3$ |                                                                                       |                            |         |
