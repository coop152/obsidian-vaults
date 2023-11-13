There are certain problems in NP whose individual complexity is related to that of the entire class. If a polynomial time algorithm exists for any of these problems, all problems in NP would be polynomial time solvable. These problems are called **NP-Complete**. The phenomenon of NP-completeness is important for both theoretical and practical reasons.
Theoretically speaking, if you wanted to prove that P equals NP then you would only need to find a polynomial time algorithm for an NP-complete problem. 
Practically speaking, knowing that a problem is NP-Complete allows us to know that it most likely isn't solvable in polynomial time, even if we can't mathematically prove that it isn't.
# The satisfiability problem
A boolean formula is an expression involving boolean variables and operations. For example:
![](Pasted%20image%2020231110141003.png)
A boolean formula is **satisfiable** if some assignment of 0s and 1s to the variables makes it evaluate to 1. The given example is satisfiable because the assignment x = 0, y = 1, z = 0 makes it evaluate to 1. We say that this assignment **satisfies** $\phi$. The **satisfiability problem** is to test whether a boolean formula is satisfiable.
![](Pasted%20image%2020231110141135.png)
Skipping over the proof for now, we know it to be true that
![](Pasted%20image%2020231110141207.png)
That is, **SAT is NP-Complete**.
To prove this, we must develop a new method.
# Polynomial Time Reducibility
We have a concept of reducing a problem to another. When problem $A$ reduces to problem $B$, a solution to $B$ can be used to solve $A$. We can infer some properties of both $A$ and $B$'s complexities from this, if we first define a new version of reducibility that takes efficiency into account.
When problem $A$ is *efficiently* reducible to problem $B$, an *efficient* solution to $B$ can be used to solve $A$ *efficiently*.
![](Pasted%20image%2020231110141806.png)
![](Pasted%20image%2020231110141842.png)
![](Pasted%20image%2020231110142355.png)
As with the regular reduction, a polynomial time reduction of $A$ to $B$ provides a way to take a membership test for $A$ and use it to test for membership in $B$. To test if $w \in A$, we use $f$ to map $w$ to $f(w)$ and then test if $f(w) \in B$.
If a language is polynomial time reducible to a language that is already known to have a polynomial time solution, then we have a polynomial time solution to the original language:
![](Pasted%20image%2020231110142450.png)

## 3SAT
In order to demonstrate a read example of a polynomial time reduction, we introduce **3SAT**, a special case of the satisfiability problem whereby all formulas are in **3-cnf form**.
![](Pasted%20image%2020231110142936.png)
A formula is in 3-cnf form if it is in Conjunctive Normal Form and each clause has three literals in it:
![](Pasted%20image%2020231110142902.png)
For an assignment to satisfy a cnf formula (of any clause size), each clause must contain at least one literal that evaluates to 1. The following theorem presents a polynomial time reduction from 3SAT to the **CLIQUE** problem.
![](Pasted%20image%2020231110143108.png)
![](Pasted%20image%2020231110143659.png)
![](Pasted%20image%2020231110143708.png)
![](Pasted%20image%2020231110143716.png)
So we have shown that if CLIQUE is solvable in polynomial time, so is 3SAT. This connection may seem quite remarkable at first, because these problems are superficially quite different. Regardless, polynomial time reducibility allows us to link their complexities. Now we turn to a definition that will allow us to similarly link the complexities of an entire class of problems.
![](Pasted%20image%2020231110143942.png)
![](Pasted%20image%2020231110144032.png)
![](Pasted%20image%2020231110144259.png)
# The Cook-Levin Theorem
Once we have one NP-Complete problem, we can obtain others by polynomial time reduction from it. However, how do we establish the first NP-complete problem? The following theorem proves that SAT is NP-complete:

![](Pasted%20image%2020231110144520.png)
![](Pasted%20image%2020231110144736.png)
![](Pasted%20image%2020231110144831.png)
LMAO this proof is 5 pages long, i'll pass