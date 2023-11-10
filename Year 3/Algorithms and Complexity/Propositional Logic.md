# DPLL
Algorithm for solving SAT.
![](Pasted%20image%2020231110171826.png)
resolve (at some literal $l$):
if a clause contains $l$, remove the clause from the set of clauses
if a clause contains $\bar{l}$, remove the (inverse of the) literal from the clause

DPLL:
If there are no clauses the set is satisfiable.
If there is an empty clause the set is unsatisfiable.
While there is a clause with only one literal $l$:
	resolve($l$)
	if that removed all clauses, the set is satisfiable
	if that created an empty clause, the set is unsatisfiable
there are no unit clauses left but there are still clauses: select the first literal of the first clause as $l$
fork and try DPLL on the set with unit clauses $l$ and $\bar{l}$ added
If either was satisfiable then the set is satisfiable, otherwise it is unsatisfiable.

# SAT special cases
## Horn
A clause is **Horn** if at most one of the literals are negative.
e.g. $\bar{p_1} \lor \bar{p_2} \lor p_3$ or $\bar{p_1} \lor \bar{p_2}$ 
**Horn-SAT** is the same as SAT but every clause is Horn. It is PTIME.
Horn-DPLL is a modification that solves Horn-SAT:
![](Pasted%20image%2020231110173023.png)
If there is an empty clause the set is unsatisfiable
while there are unit clauses $l$
	resolve($l$)
	if there is an empty clause the set is unsatisfiable
the set is satisfiable (any remaining clauses are Horn, so can be satisfied by making the remaining variables false)

## Krom
A clause is Krom if it contains at most two literals.
KROM-SAT is the same as 2-SAT.
KROM-UNSAT is the problem of deciding if a set of Krom clauses is unsatisfiable.

Theorem: KROM-UNSAT is in NLOGSPACE.
![](Pasted%20image%2020231110173739.png)
![](Pasted%20image%2020231110174741.png)
Simple:
We have a set $\Gamma$ of Krom clauses.
Assume their are no unit clauses, empty clauses or clauses that are falsum.
Put these clauses in the form of implications. ($l \rightarrow m$, which is equivalent to $\bar{m} \rightarrow \bar{l}$).
Define an order $\geq$. $l \geq m$ iff there is a "path" from $l$ to $m$ where an implication is taken as an edge in a directed graph. $\geq$ is a **pre-order** (reflexive and transitive).
We also define $\sim$ as $l \sim m$ if $l \geq m$ and $m \geq l$.

We can prove that $\Gamma$ is satisfiable iff there exists no literal $l$ such that $l \sim \bar{l}$.

It is obvious that $\Gamma$ is unsatisfiable if there exists a literal $l$ such that $l \sim \bar{l}$, because 