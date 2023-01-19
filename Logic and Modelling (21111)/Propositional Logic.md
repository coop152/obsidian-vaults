## Semantics
An interpretation $I$ assigns truth values to propositional variables.
$$I:P \to \set{1, 0}$$
In this example, $1, 0$ are truth values.
If $I(p) = 1$ then $p$ is true in $I$.
If $I(p) = 0$ then $p$ is false in $I$.

$I \models A$ if $I(A) = 1$. ("$I$ is a model of $A$", "$I$ satisfies $A$")
$I \not\models A$ if $I(A) = 0$. ("$I$ is not a model of $A$", "$I$ doesn't satisfy $A$")
If $A$ is true in at least one interpretation, it is **satisfiable**.
If A is true in all interpretations, it is **valid**/**a tautology**.
If A is false in all interpretations, it is **unsatisfiable**.
A formula $A$ **entails** a formula $B$ ($A \models B$) if every model of $A$ is a model of $B$.
(for example, if $A = a \land b$ and $B = (a \land b) \lor c$, then $A \models B$, because any true interpretation of A will also be a true interpretation of B.)
Two formulae are **equivalent** ($A \equiv B$) if they have the exact same set of models.
