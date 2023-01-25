## Notation
Like propositional logic, but with 2 new operators:
- $\forall{p}F$ - universal quantifier (for all $p$, $F$)
- $\exists{p}F$ - existential quantifier (there exists $p$ such that $F$)
These operators have the same precedence as negation ($¬$).

Let $I = \{p \mapsto 1, q \mapsto 0, r \mapsto 1\}$.
$I^p_0$ is I with $p$ set to $0$. That is, $I^p_0 = \{p \mapsto 0, q \mapsto 0, r \mapsto 1\}$.
$I(\forall pF) = 1$ if and only if $I_p^0(F) = 1$ and $I_p^1(F)= 1$.
$I(\exists pF) = 1$ if and only if $I_p^0(F) = 1$ or $I_p^1(F)= 1$.

## Example
Consider the QBF $\forall p \exists q (p \leftrightarrow q)$.
a. Evaluate against the interpretation $\{p \mapsto 1, q \mapsto 0\}$.
$I_{10} \models \forall p \exists q (p \leftrightarrow q)$
$\Rightarrow I_{10} \models \exists q (p \leftrightarrow q) \text{ AND } I_{00} \models \exists q (p \leftrightarrow q)$
$\Rightarrow (I_{10} \models p \leftrightarrow q \text{ OR } I_{11} \models p \leftrightarrow q) \text{ AND } (I_{00} \models p \leftrightarrow q \text{ OR } I_{01} \models p \leftrightarrow q)$
$\Rightarrow ( \bot \text{ OR } \top ) \text{ AND } ( \top \text{ OR } \bot )$
$\Rightarrow \top$
Therefore, the interpretation models the QBF.
b. Find all models.
$I_{**} \models \forall p \exists q (p \leftrightarrow q)$
$\Rightarrow I_{1*} \models \exists q (p \leftrightarrow q) \text{ AND } I_{0*} \models \exists q (p \leftrightarrow q)$
$\Rightarrow (I_{10} \models p \leftrightarrow q \text{ OR } I_{11} \models p \leftrightarrow q) \text{ AND } (I_{00} \models p \leftrightarrow q \text{ OR } I_{01} \models p \leftrightarrow q)$