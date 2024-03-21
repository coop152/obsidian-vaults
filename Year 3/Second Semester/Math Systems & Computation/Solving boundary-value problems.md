When finding numerical solutions to ODEs, we start with a set of auxiliary conditions that enable us to evaluate the integration constants. These have previously been given at the same value of the independent variable - these are **initial value problems**.
There is a second (and very important) class of ODE that is produced when auxiliary conditions are specified at *different* values of the independent variables. These are known as **boundary value problems**. (Why "boundary"? Because the auxiliary conditions provided are usually extreme values that lie at the "boundary" of the interesting data.)
# Example: Heat conductance equation
The way that heat balances out along a long, thin bar can be described by this second order ODE:
![](Pasted%20image%2020240307162359.png)
Where:
- $T$ is the temperature at location $x$
- $k$ is the heat transfer coefficient which parameterizes the heat dissipation to the surrounding air, which is of temperature $T_a$.

![](Pasted%20image%2020240307162404.png)
This is a **steady-state problem** - it does not vary with time. We want to solve for these conditions:
- $T_a = 20 \degree C$
- $T_0=40 \degree C$
- $T_L = 200 \degree C$
- $k = 0.01 m^{-2}$
- The bar is 10m long ($L=10\text{m}$)

## Analytical solution
![](Pasted%20image%2020240307162833.png)
![](Pasted%20image%2020240307162839.png)
(you don't need to know how to do this.)
The complexity of this analytical solution, even for such a simple ODE, demonstrates why these numerical methods are so useful.
## Numerical solution w/ finite difference
We compute the solution for $T(x)$ at discrete locations along the bar, separated by distance $\Delta x$.
![](Pasted%20image%2020240307163216.png)
We know these conditions:
- The temperature is 40$\degree C$ at the start of the bar (when $x=0$, $T(x)=40 \degree C$)
- The temperature is 200$\degree C$ at the end of the bar (when $x = L = 10, T(x)=200\degree C$)

An initial value technique won't work here, as our conditions aren't defined at a single location. We will use a finite difference approximation. Specifically, we use the central difference approximation of the second derivative:
![](Pasted%20image%2020240307164102.png)
Substitute into the head conductance equation to get, for some location $x_i$:
![](Pasted%20image%2020240307164130.png)
We write this equation down many times to describe the temperature at multiple locations along the bar, as well as doing some rearranging:
![](Pasted%20image%2020240307164332.png)
![](Pasted%20image%2020240307164340.png)
The boundary conditions are used here; any values of $T(x)$ that are known can be taken as constants, and moved to the right-hand side of the equation. Now we can solve these equations simultaneously; we write them in matrix form (which is why we rearranged them):
![](Pasted%20image%2020240307164534.png)
And we solve it. This system is **tri-diagonal**, which means we can solve it with an efficient method known as the **Thomas algorithm**. By doing this, we get results very close to the analytical solution, even with such a large distance between points:
![](Pasted%20image%2020240307164745.png)
