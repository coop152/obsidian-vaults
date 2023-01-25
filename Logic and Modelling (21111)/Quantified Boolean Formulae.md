## Notation
Like propositional logic, but with 2 new operators:
- $\forall{p}F$ - universal quantifier (for all $p$, $F$)
- $\exists{p}F$ - existential quantifier (there exists $p$ such that $F$)
These operators have the same precedence as negation ($¬$).

Let $I = \{p \mapsto 1, q \mapsto 0, r \mapsto 1\}$.
$I^p_0$ is I with $p$ set to $0$. That is, $I^p_0 = \{p \mapsto 0, q \mapsto 0, r \mapsto 1\}$.
$I(\forall pF) = 1$ if and only if $I_p^0(F) = 1$ and $I_p^1(F)= 1$.
$I(\exists pF) = 1$ if and only if $I_p^0(F) = 1$ or $I_p^1(F)= 1$.
