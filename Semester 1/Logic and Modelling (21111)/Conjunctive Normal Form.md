$\newcommand{\impl}{\rightarrow} \newcommand{\equi}{\leftrightarrow}$
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
There is a problem, however, with the regular transformation; the equivalence ($\equi$) will exponentially expand in size, turning a relatively small formula into a huge set of clauses.
![](Pasted%20image%2020230123142831.png)
To resolve this issue, we can introduce names for subformulae.
First, add a new formula to the set stating that the new variable $n$ is equivalent to a subformula.
$$p_1 \equi (p_2 \equi (p_3 \equi (p_4 \equi (p_5 \equi p_6))))$$$$n \equi (p_5 \equi p_6)$$
And repeat this until the equation has been completely collapsed.
$$p_1 \equi (p_2 \equi (p_3 \equi (p_4 \equi (p_5 \equi p_6)))) $$$$ p_1 \equi (p_2 \equi n_3) $$$$ n_3 \equi (p_3 \equi n_4) $$$$ n_4 \equi (p_4 \equi n_5) $$$$ n_5 \equi (p_5 \equi p_6)$$
Converting this new set of formulae only creates 4 copies of $p_6$, compared to 32 with the original formula. Note that these new formulae are not equivalent to the original formula, they are only equisatisfiable, which is all that is required for the problem we are trying to solve.

## Example of DCNF Transformation
1. Draw a table header like this, and add your initial formula:

| Name  | Subformula                                                                            | Definition | Clauses |
| ----- | ------------------------------------------------------------------------------------- | ---------- | ------- |
| $n_1$ | $¬((p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r))$ |            |         |

2. Break the formula down, one connective at a time. For this example, remove the outermost negation:

| Name  | Subformula                                                                            | Definition                 | Clauses |
| ----- | ------------------------------------------------------------------------------------- | -------------------------- | ------- |
| $n_1$ | $¬((p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r))$ | $n_1 \equi ¬n_2$ |         |
| $n_2$ | $(p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r)$    |                            |         |

Observe that a new definition has been added to the previous row, then the next rows filled in with the values of the new variables. Think of the equivalence ($\equi$) here as an equals, saying that $n_1 = ¬n_2$.

3. Repeat this until the formula is completely decomposed, stopping the decomposition of each subformulae one step before you get unit clauses:

| Name  | Subformula                                                    | Definition                  | Clauses |
| ----- | ------------------------------------------------------------- | --------------------------- | ------- |
| $n_1$ | $¬((p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r))$ | $n_1 \equi ¬n_2$            |         |
| $n_2$ | $(p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r)$    | $n_2 \equi (n_3 \impl n_4)$ |         |
| $n_3$ | $(p \impl q) \land (p \land q \impl r)$                       | $n_3 \equi (n_5 \land n_6)$ |         |
| $n_4$ | $p \impl ¬r$                                                  | $n_4 \equi (p \impl ¬r)$    |         |
| $n_5$ | $p \impl q$                                                   | $n_5 \equi (p \impl q)$     |         |
| $n_6$ | $p \land q \impl r$                                           | $n_6 \equi (n_7 \impl r)$   |         |
| $n_7$ | $p \land q$                                                   | $n_7 \equi (p \land q)$     |         | 

4. Now transform each of the definitions into CNF, and put the result in the Clauses column:

| Name  | Subformula                                                    | Definition                  | Clauses                                                            |
| ----- | ------------------------------------------------------------- | --------------------------- | ------------------------------------------------------------------ |
| $n_1$ | $¬((p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r))$ | $n_1 \equi ¬n_2$            | $¬n_1 \lor ¬n_2$ <br> $n_2 \lor n_1$                               |
| $n_2$ | $(p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r)$    | $n_2 \equi (n_3 \impl n_4)$ | $¬n_2 \lor ¬n_3 \lor n_4$ <br> $n_3 \lor n_2$ <br> $¬n_4 \lor n_2$ |
| $n_3$ | $(p \impl q) \land (p \land q \impl r)$                       | $n_3 \equi (n_5 \land n_6)$ | etc..                                                              |
| $n_4$ | $p \impl ¬r$                                                  | $n_4 \equi (p \impl ¬r)$    | etc..                                                              |
| $n_5$ | $p \impl q$                                                   | $n_5 \equi (p \impl q)$     | etc..                                                              |
| $n_6$ | $p \land q \impl r$                                           | $n_6 \equi (n_7 \impl r)$   | etc..                                                              |
| $n_7$ | $p \land q$                                                   | $n_7 \equi (p \land q)$     | etc..                                                              |

5. Done. You now have the starting formula in DCNF. 

## Optimised Definitional Clausal Normal Form (ODCNF)
This transformation can be optimised further using polarity.
The method is the same as before, but with 3 added rules:
- If the subformula occurs positively in the formula in which it was defined, its definition shall be of the form $n_i \impl A$.
- If the subformula occurs negatively in the formula in which it was defined, its definition shall be of the form $A \impl n_i$.
- If the subformula occurs neutrally in the formula in which it was defined, its definition shall be of the form $n_i \equi A$ (that is, the same as before).
If this is done with the previous formula, the result looks like this:

| Name        | Subformula                                                    | Definition                  | Clauses |
| ----------- | ------------------------------------------------------------- | --------------------------- | ------- |
| $n_1$ $(+)$ | $¬((p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r))$ | $n_1 \impl ¬n_2$            |         |
| $n_2$ $(-)$ | $(p \impl q) \land (p \land q \impl r) \impl (p \impl ¬r)$    | $(n_3 \impl n_4) \impl n_2$ |         |
| $n_3$ $(+)$ | $(p \impl q) \land (p \land q \impl r)$                       | $n_3 \impl (n_5 \land n_6)$ | etc..   |
| $n_4$ $(-)$ | $p \impl ¬r$                                                  | $(p \impl ¬r) \impl n_4$    | etc..   |
| $n_5$ $(+)$ | $p \impl q$                                                   | $n_5 \impl (p \impl q)$     | etc..   |
| $n_6$ $(+)$ | $p \land q \impl r$                                           | $n_6 \impl (n_7 \impl r)$   | etc..   |
| $n_7$ $(-)$ | $p \land q$                                                   | $(p \land q) \impl n_7$     | etc..   |

(Don't write the positive/negative notes in the name column, write them somewhere unobtrusive like the margin)