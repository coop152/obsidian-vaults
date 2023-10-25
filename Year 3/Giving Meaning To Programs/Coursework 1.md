$\xrightarrow{\beta}$ for beta reduction
$\xtwoheadrightarrow{\beta}$ for eventual beta reduction
$\sim_\alpha$ for alpha-equivalence
$\Diamond$ for parallel reduct
$\lambda$ for lambda (no shit)

d)
Base case: For $t=x$ where $x$ is a variable:
$x \xtwoheadrightarrow{\beta} x$            because $\xtwoheadrightarrow{\beta}$ is reflexive
$\Diamond{x} = x$            by bcVar$\Diamond$
$x \sim_\alpha x$             by $\sim_\alpha$ base case
$x \xtwoheadrightarrow{\beta} x \sim_\alpha x$    therefore the result is true for terms that are variables.

Step case Abs: For $t = \lambda w.t'$, where w is a variable and $t'$ is 