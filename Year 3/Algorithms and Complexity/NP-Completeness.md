There are certain problems in NP whose individual complexity is related to that of the entire class. If a polynomial time algorithm exists for any of these problems, all problems in NP would be polynomial time solvable. These problems are called **NP-Complete**. The phenomenon of NP-completeness is important for both theoretical and practical reasons.
Theoretically speaking, if you wanted to prove that P equals NP then you would only need to find a polynomial time algorithm for an NP-complete problem. 
Practically speaking, knowing that a problem is NP-Complete allows us to know that it most likely isn't solvable in polynomial time, even if we can't mathematically prove that it isn't.
## The satisfiability problem
A boolean formula is an expression involving boolean variables and operations. For example:
![](Pasted%20image%2020231110141003.png)
A boolean formula is **satisfiable** if some assignment of 0s and 1s to the variables makes it evaluate to 1. The given example is satisfiable because the assignment x = 0, y = 1, z = 0 makes it evaluate to 1. We say that this assignment **satisfies** $\phi$. The **satisfiability problem** is to test whether a boolean formula is satisfiable.
![](Pasted%20image%2020231110141135.png)
Skipping over the proof for now, we know it to be true that
![](Pasted%20image%2020231110141207.png)
That is, **SAT is NP-Complete**.
To prove this, we must develop a new method.
## Polynomial Time Reducibility
We have a concept of reducing a problem to another. When problem $A$ reduces to problem $B$, a solution to $B$ can be used to solve $A$. We can infer some properties of both $A$ and $B$'s complexities from this, if we first define a new version of reducibility that takes efficiency into account.
When problem $A$ is *efficiently* reducible to problem $B$, an *efficient* solution to $B$ can be used to solve $A$ *efficiently*.
![](Pasted%20image%2020231110141806.png)
![](Pasted%20image%2020231110141842.png)
