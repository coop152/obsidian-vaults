# Differential Equations
Differential equations are equations involving a derivative. For example, you might want to model the speed at which a person falls. You could do this with Newton's second law, but that equation doesn't actually contain velocity:
![](Pasted%20image%2020240227154101.png)
But acceleration is the derivative of velocity. If we replace it like so:
![](Pasted%20image%2020240227154211.png)
We get a differential equation. by solving this, we can model the speed of a falling object in terms of its mass and the force applied to it.
Taking the example further, let's expand the force term into gravity and drag:
![](Pasted%20image%2020240227154409.png)
If we solve this equation for v, we get a model. In this simple case, there is an analytical solution:
![](Pasted%20image%2020240227154452.png)
# Ordinary Differential Equations (ODEs)
An **Ordinary Differential Equation** is a differential equation with a single independent variable. For example:
![](Pasted%20image%2020240227154535.png)
(Where x is the independent variable.)
ODEs are described in terms of their **order**, which is the power of the highest derivative term. The previous example was a first order ODE, and here is a second order ODE:
![](Pasted%20image%2020240227154646.png)

# Linear and Nonlinear
A linear function is (in the strict definition) a function such that:
![](Pasted%20image%2020240227154747.png)
In the informal sense, it's a function that looks like a line; if two points are equidistant in their x then they are also equidistant in their y.
Inversely, a nonlinear function is any function that isn't linear. Examples include exponential functions, trigonometric functions, etc.
Our previously devised falling-man model is a linear function. For contrast, here is a more advanced model that is nonlinear:
![](Pasted%20image%2020240227155129.png)
# Solving ODEs
When we solve an ODE, we endeavour to find a function that satisfies the relationship between the dependent and independent variables. In other words, if we have the ODE
![](Pasted%20image%2020240227155235.png)
Then we want to find some function $y$ which differentiates to give $f(x)$. This is essentially the same problem as integration:
![](Pasted%20image%2020240227155433.png)
## Initial/Boundary conditions
Similarly to integration, there is a family of solutions to any given differential equation. To find a specific solution, we have to use known information. Consider the example:
![](Pasted%20image%2020240227161023.png)
We can solve this analytically by integrating, but the solution is:
![](Pasted%20image%2020240227161038.png)
With an unknown constant. We can calculate C using some known value - these known values are called *initial conditions* and *boundary conditions*.
For example, if we know that at x=0, y=20, we can solve for C and get that C=20.
![](Pasted%20image%2020240227161542.png)
## Initial value problems
If all of the auxilliary information is provided at the same point (for example, t=0 where t is the independent variable) then the problem is said to be an *initial value problem* (also known as *propagation problem*). We find the solution by marching forward in time from this starting point. Sometimes, we may be able to find an analytical solution to this kind of problem.
## Analytical solutions
As previously seen, solving an ODE that has an analytical solution is trivial. Let's investigate this example:
![](Pasted%20image%2020240227162923.png)
From this description we glean this information:
- When $t = 0$, $T = 100$. This is our initial condition.
- We are trying to model temperature change. This allows us to choose an appropriate equation, in this case **Newton's law of cooling**:
![](Pasted%20image%2020240227163123.png)
- After 10 minutes, the temperature drops from the initial 100 to 60.

We start by solving our chosen equation normally:
![](Pasted%20image%2020240227163410.png)
Now we can evaluate it at our initial condition to find the value of our unknown constant $A$:
![](Pasted%20image%2020240227163438.png)
Now we can calculate our decay rate, using the fact that temperature drops from 100 to 60 after 10 minutes:
![](Pasted%20image%2020240227163555.png)
Giving us our solution. Now we can answer the question: How long before closing time should the soup be finished in order to be 20 by closing time?
Find t when T=20:
![](Pasted%20image%2020240227163705.png)
And our answer is 37.4 minutes.
This is the solution plotted:
![](Pasted%20image%2020240227163923.png)
## Numeric solutions
For complicated ODEs and large systems of ODEs, finding an analytical solution is often prohibitive or even impossible. 