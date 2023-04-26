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

## Abstract and Concrete Inputs
In the descriptions of problems, our inputs are **abstractly defined**. When making an algorithm, we need a **concrete** representation of our inputs, i.e. and actual representation that could be applied in a computer program.
We use a standard encoding of integers and lists to a binary representation so we can serialise anything and encode it. As long as any encoding we use is **polynomially related** to these standard encodings, then our results will hold.

## Language of a Decision Problem
With our standard encodings, we can assume that all inputs are binary strings. 
With this, an alternative interpretation of a decision problem is therefore the set of binary strings where the answer is 1. This is the **language** of the decision problem.
The same name is often used to refer to a problem and its language:
![](Pasted%20image%2020230426110908.png)
