## Classes of Problems
- **P**: Problems solvable in polynomial time (e.g. $O(n^c)$)
- **NP**: problems that are **verifiable** in polynomial time, or are solvable in polynomial time **by a non-deterministic machine**. **P** is a subset of **NP**.
- **NP-Hard**: Problems that are at least as hard as every problem in **NP**.
- **NP-Complete**: Problems that are in both **NP** and **NP-Hard**.

## Reductions
You have two problems $A$ and $B$, and you know how to solve $B$.
(this shit doesnt make sense)

## Encodings
#### Decision Problems
Decision problems are problems with a yes or no output, e.g.
- Is element $e$ in list $L$? `SEARCH(e, L)`
- Is there a path from nodes $u$ to $v$ in a directed graph $G$, with at most $k$ edges? `PATH(G, u, v, k)`
- Does a boolean combinatorial circuit C have a set of inputs that make its output 1? `CIRCUIT-SAT(C)`

The other kind of problem is an **optimisation problem**, where you try to find a value, for example Travelling Salesman.

