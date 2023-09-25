## Levin-Cook Theorem
We need to prove that SAT is NP-Complete in order to prove that anything else is. This is because (the easy method of) proving a problem is NP-Complete requires showing that the problem can be made by reducing another NP-Complete problem. Therefore, we need at least one NP-Complete problem to be proven another way.
#### The Idea
We need to show for all $A \in NP$ that $A \leq_p SAT$.  But how can we build a reduction when we know nothing about $A$?
Assuming $A$ can be decided by a Non-deterministic Turing Machine $M$ in time $n^k$:
- Create a reduction $f$ that will map inputs $w$ of $A$ to a formula $\phi_{M, w}$. $w \in A$ if and only if $\phi_{M, w}$ is satisfiable.
- Intuitively, you can say that $\phi_{M, w}$ simulates $M$ on input $w$.
- In other words, we build a formula $\phi_{M, w}$ that is only satisfiable when machine $M$ accepts $w$.

