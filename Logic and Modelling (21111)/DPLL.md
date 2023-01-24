$\newcommand{\impl}{\rightarrow} \newcommand{\equi}{\leftrightarrow}$
DPLL is a specialised [Satisfiability Checking](Satisfiability%20Checking.md) formula for CNF formulae.
Consider this regular formula: $¬((p \impl q) \land (p \land q \impl r ) \impl (p \impl r))$
This breaks down into these clauses:
-   $¬p \lor q$
-   $¬p \lor ¬q \lor r$
-   $p$
-   $¬r$