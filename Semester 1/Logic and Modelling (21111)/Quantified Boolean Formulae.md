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

## Position and Polarity
In the formulas $\exists{p}F$ and $\forall{p}F$, F is at the first position.
$\forall$ and $\exists$ do not change the polarity of their subformulae.
![](Pasted%20image%2020230125151351.png)

## Bound and Free Variables
If a variable given in a quantifier is in the "scope" of that quantifier, then it is bound. If not, then it is "free". e.g. in $p \rightarrow \exists{q}(¬q \lor p)$, $q$ is bound and $p$ is free.
If a formula contains no free variables, it is a "closed formula".
If a formula is closed, then it can only be valid or unsatisfiable. This is because the output of the formula doesn't depend on the interpretation; it can only be true for all inputs or false for all inputs.

## Renaming bound variables
It is more clear for bound variables that share a name with unbound variables to be differently named; the value of the free variable $p$ will not influence the value of the bound value $p$.

**Rectification** is the process of renaming every bound variable, so that a formula cannot contain any naming conflicts between bound and free variables. A **rectified** formula is required for **prenexing**:

## Prenexing
Prenex form is a form of QBF where all of the quantifiers are on the outside. e.g: $\forall{p}\exists{q}(p \land q \land r)$.
The **quantifier-free** formula on the inside can be any propositional formula.
If the quantifier-free formula on the inside of a prenex form QBF is in CNF, then the whole QBF is said to be in CNF. Therefore, to use algorithms that require a formula in CNF, a QBF must be rectified, put into prenex form and then it's internal quantifier-free formula put into CNF.

To put a QBF into prenex form, apply these rules:
![](Pasted%20image%2020230125161008.png)
(Remember, these only apply to formulas that have been rectified!)

#### Example
![](Pasted%20image%2020230125162439.png)
1. Took the $\exists{q}$ out from the left of the implication, switching the quantifier in the process
2. Took the $\forall{r}$ out from the $¬$, switching the quantifier in the process
3. Took the $\lor p$ inside the $\exists{r}$
4. Took the $\exists{r}$ out of the top $\rightarrow$.

## Algorithms
See [QBF Splitting](Splitting.md#QBF%20Splitting), [QBF DPLL](DPLL.md#QBF%20DPLL), and [QBF OBDDs](Binary%20Decision%20Data%20Structures.md#QBF%20OBDDs).