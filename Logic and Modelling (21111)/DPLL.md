$\newcommand{\impl}{\rightarrow} \newcommand{\equi}{\leftrightarrow}$
DPLL is a specialised [Satisfiability Checking](Satisfiability%20Checking.md) formula for CNF formulae.
Consider this regular formula: $¬((p \impl q) \land (p \land q \impl r ) \impl (p \impl r))$
It breaks down into these clauses:
-   $¬p \lor q$
-   $¬p \lor ¬q \lor r$
-   $p$
-   $¬r$

For this set of clauses to be satisfiable, it is clear:
- $p$: $p$ must be true
- $¬r$: $r$ must be false

Therefore any model for this formula will be of the form $\{p \mapsto 1, r \mapsto 0, q \mapsto ?\}$.

## Unit Propagation
Let $S$ be a set of clauses.
If $S$ contains a unit clause $L$:
1. Remove the unit clause (the set now has one less clause in it)
2. Remove every clause that contains $L$
3. In every clause that contains $¬L$, remove $¬L$. (the set will still contain the same number of clauses; if this operation caused the whole clause to disappear, then an empty clause has been created)
#### Example
Initially:
-   $¬p \lor q$
-   $¬p \lor ¬q \lor r$
-   $p$
-   $¬r$

Propagate $p$:
-   $q$
-   $¬q \lor r$
-   $¬r$

Propagate $¬r$:
-   $q$
-   $¬q$

Propagate $q$:
-   $\square$

Results in a set of empty clauses; unsatisfiable.
## DPLL Algorithm
```python
procedure DPLL(S)
input: set of clauses S
output: satisfiable or unsatisfiable
parameters: function select_literal

begin
	S := propagate(S)
	if S is empty then return satisfiable
	if S contains ▢ then return unsatisfiable

	L := select_literal(S)
	if DPLL(S ⋃ {L}) = satisfiable
		then return satisfiable
	else
		return DPLL(S ⋃ {¬L})
end
```
The steps of the algorithm are as follows:
1. Perform unit propagation until you can't anymore. In the graph method, each unit propagation is represented as a branch.
2. If the result is an empty set, the formula is **satisfiable**. If the result contains any empty clauses, the formula is **unsatisfiable**.
3. If the result is neither empty nor contains an empty clause, select a remaining literal and add it as a new clause, making it true. Perform DPLL on this new set. In the graph method, this is represented as a branch.
4. If this doesn't result in a satisfiable set of clauses, then add the inverse of the literal instead and perform DPLL on this.
5. If both of these are unsatisfiable, then the formula is unsatisfiable.

#### Example
![](Pasted%20image%2020230124122007.png)
Initially:
- $¬p \lor ¬q$
- $¬p \lor q$
- $p \lor ¬q$

No unit to propagate, add a literal. Chosen literal is $p$:
- $¬p \lor ¬q$
- $¬p \lor q$
- $p \lor ¬q$
- $p$

Unit propagation of $p$:
- $¬q$
- $q$

Unit propagation of $q$:
- $\square$

Branch is unsatisfiable. Go back and try the inverse of the chosen literal, which is $¬p$:

- $¬p \lor ¬q$
- $¬p \lor q$
- $p \lor ¬q$
- $¬p$

Unit propagation of $¬p$:

- $¬q$

Unit propagation of $¬q$:

