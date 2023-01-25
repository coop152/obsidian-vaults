We introduce a partial order $<$ on truth values such that $0 < 1$. 
As a result of this, the following things are true:
- The connectives $\land$ and $\lor$ are monotonic on all of their arguments.
- The negation ($¬$) is anti-monotonic.
- The implication ($\rightarrow$) is monotonic on the second argument, but anti-monotonic on the first.
- The equivalence $\leftrightarrow$ is neither monotonic nor anti-monotonic.

To refer to certain subformulae in a given formula, we use the syntax
$$A|_{1.2.3}$$
where $A$ is the formula and $1.2.3$ is a series of shortenings of scope; this example represents the third child of the second child of the first child of A.

We then introduce the concept of **polarity**. At any position in the formula the function $pol(A, \pi)$ gives a value in the set $\{1, 0, -1\}$, called *positive*, *neutral* and *negative* respectively. Combined with the partial order and monotonicity introduced earlier, a new algorithm for satisfiability checking arises.

## The Colouring Algorithm
Begin at the outermost element of the formula, with a polarity of $1$. Now, progress through each of the operators using these rules derived from their monotonicity:
- If you reach the connectives $\land$ and $\lor$, continue on each path and keep the same polarity.
- If you reach the negation $¬$, continue down the path and swap the polarity.
- If you reach the implication $\rightarrow$, split as if you had encountered the connectives but keep the polarity of the right subformula and swap the polarity of the left.
- If you reach the equivalence $\leftrightarrow$, split as if you had encountered the connectives but set the polarity of both subformulae to $0$. Everything following this will have a polarity of $0$, regardless of the future operators.
### Example
![](Pasted%20image%2020230123123612.png)
![](Pasted%20image%2020230123123004.png)
The important part is the polarity of the variables at the ends of the tree. These values are used in:
## The Pure Atom Rule
(Reminder: an atom is a single propositional variable)
For the purposes of satisfiability checking, these rules are true:
- If an atom occurs only positively, then it can be replaced with $\top$.
- If an atom occurs only negatively, then it can be replaced with $\bot$.
0 counts as negative or positive; if an atom occurs neutrally then it can still be pure.

This algorithm will not always be able to fully reduce a formula to a truth value; it is more like an optimisation that can reduce the time needed to run other satisfiability checking algorithms. That said, it will sometimes be able to completely reduce a formula, such as in this example:
![](Pasted%20image%2020230123125959.png)
