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
Generally the desired solution is a global optima, not a local one.

# Golden Search
**Golden search** is a simple, general-purpose, single-variable optimisation technique.
Say we have this objective function:
![](Pasted%20image%2020240429160101.png)
![](Pasted%20image%2020240429160631.png)
We want to maximise $\delta(x)$, and we have no constraints. This is a **single-variable unconstrained optimisation problem**.

Golden search requires some initial guesses for the objective function's variable:
- $x_l$, a lower bound on the interval which contains the optimum
- $x_u$, an upper bound on the interval containing the optimum

Finding these bounds can be a challenge for certain problems, but in this problem we say that valid inputs are from 0 to 600, so it is easy:
- $x_l = 0.0$
- $x_u = 600.0$

Next we pick two points ($x_1$ and $x_2$) in the bounded interval:
![](Pasted%20image%2020240429160913.png)
![](Pasted%20image%2020240429161112.png)
Where $r$ in these equations is the **golden ratio**:
![](Pasted%20image%2020240429160940.png)

![](Pasted%20image%2020240429161232.png)
![](Pasted%20image%2020240429161301.png)On iteration 9:
![](Pasted%20image%2020240429161320.png)
This algorithm is fast and efficient, and it converges rapidly because the interval is reduced by the golden ratio at every iteration. It also reuses old results alot, which is good when evaluating the objective function is time consuming (e.g. if it requires solving a PDE with high accuracy).
It is a closed method, however, as we need to give the initial interval that contains the solutions. Sometimes this is difficult.

# Newton's Method
![](Pasted%20image%2020240429161641.png)
![](Pasted%20image%2020240429161909.png)However, this method only works if the initial guesses are within a certain acceptable range of the optima:
![](Pasted%20image%2020240429162012.png)
In this case, it found other optima outside of the area we were interested in:
![](Pasted%20image%2020240429162107.png)
![](Pasted%20image%2020240429162141.png)
Compared to the golden search algorithm, newton's method:
- is open, requiring only a single initial value
- requires care to be taken to ensure the correct stationary point is located
- May converge on a local optimum rather than a global one (meaning you may need multiple initial guesses)
- Is difficult to employ when the derivative and second derivative cannot readily be evaluated
- Is best employed when you know your initial guess is close to an optimum

A useful strategy to satisfy some of the newton method's limitations is to begin with a bounded method far away from the optimum (for example, golden search), then switch to newton's method when getting close to the solution to take advantage of its speed.

# Summary
![](Pasted%20image%2020240429162800.png)
