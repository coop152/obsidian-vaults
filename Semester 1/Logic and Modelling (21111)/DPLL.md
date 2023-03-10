$\newcommand{\impl}{\rightarrow} \newcommand{\equi}{\leftrightarrow}$
DPLL is a SAT algorithm - a satisfiability checking algorithm for sets of clauses instead of whole formulae.
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

Empty clause - branch is unsatisfiable. Go back and try the inverse of the chosen literal, which is $¬p$:

- $¬p \lor ¬q$
- $¬p \lor q$
- $p \lor ¬q$
- $¬p$

Unit propagation of $¬p$:

- $¬q$

Unit propagation of $¬q$:
(empty set)

Empty set - branch is satisfiable. Therefore, formula is satisfiable. The model of this formula is $\{ p \mapsto 0, q \mapsto 0 \}$.

## Optimisations
#### Tautological clauses
If a clause is of the form $p \lor ¬p \lor C$ (i.e. it contains a literal and its negation) then it can be removed, as this clause will always be satisfied.
#### Pure literal elimination
Recall the pure atom rule; if an atom occurs only positively in the formula, then it can be replaced by $\top$, or by $\bot$ if it only occurs negatively.
However, in a CNF clause it can be observed that clauses containing $T$ will always be removed. Therefore, if a literal occurs only positively in the set you can remove every clause containing that literal. 
#### Horn clause propagation
Reminder: A clause is a Horn clause if it contains at most one positive literal, e.g. $¬p_1 \lor ¬p_2 \lor p_3$.
????????

## k-SAT
The previous material has been solving the SAT problem; that is, clauses of any length. There is another set of problems known as k-SAT, where the length of each clause is limited. Their properties are as such:
- SAT (as shown previously) is NP-complete.
- 3-SAT (SAT where every clause is 3 literals long) is also NP-complete.
- 2-SAT (where every clause is 3 literals long) is decidable in **linear time**.
A set of clauses can be easily reduced to use smaller clauses using a method similar to that of *naming* from DCNF:
Initial formula:
- $L_1 \lor L_2 \lor L_3 \lor L_4$

3-SAT:
- $L_1 \lor L_2 \lor ¬n$
- $n \lor L_3 \lor L_4$

## Random Probabilistic Stuff
Don't Care

## QBF DPLL
This algorithm requires a rectified, prenexed, CNF QBF.
As before, it functions as a combination of splitting and unit propagation, though this one is similar to QBF Splitting.
![](Pasted%20image%2020230125170629.png)
#### Unit propagation for QBFs
If your set of clauses $S$ contains a unit clause with literal $L$ like $p$ or $¬p$, then
- If the set of quantifiers contains $\exists{p}$, or $p$ isn't in your quantifiers
	- Remove all clauses that contain the literal
	- If any clauses contain the inverse of the literal, remove it from that clause
- If the set of quantifiers contains $\forall{p}$ ,then replace $S$ by $\{\square\}$.
If neither of these new conditions are satisfied, then unit propagation works like regular DPLL.

#### Optimisations
Let $Q$ be the set of quantifiers and $S$ be the set of clauses.
**Pure Literal Rule**: If a literal $L$ (variable $p$) is pure in $S$ (that is, it's inversion doesn't appear), these rules apply:
- If $\exists{p}$ is in Q then remove every clause containing $L$.
- If $\forall{p}$ is in Q then remove $L$ from every clause containing it. (not the inversion, like it is in unit propagation!)
**Universal Literal Deletion**: You can entirely remove a clause $C$ from $S$ if these conditions are all satisfied:
- $C$ is non-tautological
- One of the variables in $C$ is present universally ($\forall$) in $Q$
- All of the other variables in $C$ are present existentially ($\exists$) in $Q$, and before the universal variable.
Example:
![](Pasted%20image%2020230125184301.png)
