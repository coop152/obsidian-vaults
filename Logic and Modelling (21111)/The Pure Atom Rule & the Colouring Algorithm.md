We introduce a partial order $<$ on truth values such that $0 < 1$. 
As a result of this, the following things are true:
- The connectives $\land$ and $\lor$ are monotonic on all of their arguments.
- The negation ($¬$) is anti-monotonic.
- The implication ($\rightarrow$) is monotonic on the second argument, but anti-monotonic on the first.
- The equivalence $\leftrightarrow$ is neither monotonic nor anti-monotonic.

To refer to certain subformulae in a given formula, we use the syntax
$$A|_{1.2.3}$$
where $A$ is the formula and $1.2.3$ is a series of shortenings of scope; this example represents the third child of the second child of the first child of A.

We then introduce the concept of **polarity**. At any position in the formula the function $pol(A, \pi)$ gives a value in the set $\{1, 0, -1\}$, called *positive*, *neutral* and *negative* respectively. 


This algorithm solves the [Satisfiability Checking](Satisfiability%20Checking.md) problem.