The previous part of the course covered methods to solve systems of equations, allowing us to mathematically model a complex system and then get results from it.
Optimisation is when we take one of these mathematical models and, when applied to some scenario, determine the best possible solution. 
For example, you may find a mathematical model for an aircraft wing, then optimise the physical parameters to minimise weight and maximise strength. Or, you may model the prediction error in a neural network, and then optimise the network's parameters to minimise it.

# Formulating an optimisation problem
The **objective function** $f(x)$ of a problem is a function that characterises the problem's behaviour. The optimisation task is to find the value of $x$ which maximises/minimises $f(x)$.
On top of this you have constraints, which only need to be met but not optimised:
- Inequality constraints $p_i(x) \leq a_i$: Declares that some characteristic of the model is less than (or equal to) some constant
- Equality constraints $q_j(x) = b_j$: Declares that some characteristic of the model is equal to some constant

# Linear Programming
Linear programming is a simple solution for optimising a system of linear equations. For example:
![](Pasted%20image%2020240429153841.png)
First we construct our objective function. We want to maximise profit, so we define profit:
![](Pasted%20image%2020240429154033.png)
Then we determine any constraints. In this problem we have limited time on each of the machines:
![](Pasted%20image%2020240429154109.png)
This is a **linear optimisation problem**, as the objective function and constraints are linear functions in $x$ and $y$. We can efficiently solve them using linear programming methods, such as **simplex**.
## Simplex
Quick recap:
![](Pasted%20image%2020240429154513.png)
![](Pasted%20image%2020240429154627.png)![](Pasted%20image%2020240429154707.png)
![](Pasted%20image%2020240429154733.png)
Then repeat until the objective function has no negative terms.
![](Pasted%20image%2020240429154848.png)
Simplex is an efficient algorithm for solving linear optimisation problems.
Unfortunately, most interesting problems cannot be defined using linear equations. Different kinds of objective functions require different algorithms:
![](Pasted%20image%2020240429155137.png)

# Optima
Optimisation methods need to be mindful of local vs global optima:
![](Pasted%20image%2020240429155336.png)
Generally the desired solution is the global optima.