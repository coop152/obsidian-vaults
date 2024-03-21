Partial differential equations are differential equations that involve two or more independent variables. For example:
![](Pasted%20image%2020240321111128.png)
(The $\partial$ symbol is the same as $d$.)
The order of a PDE is decided by its highest order derivative, and it is linear if the unknown functions and derivatives are linear.
Solving PDEs analytically is difficult, and writing down a formula that describes a PDE solution is typically hard for anything more than a trivial example (i.e. for any meaningful engineering problem.) 
PDEs do show up often in science and engineering, as they can be used to describe problems bounded in space and time.

The general form of a PDE with two real variables is:
![](Pasted%20image%2020240321111705.png)
(Where $a$ through $g$ are just coefficients. I don't know why they have "arguments".)

You can characterise a second order system using its auxiliary quadratic equation, specifically using its roots ($b^2 -4ac$):
![](Pasted%20image%2020240321111842.png)
# Analytical solutions for PDEs
Consider a more accurate version of the previously shown heat conduction equation, which takes into account time $t$ as well as position $x$:
![](Pasted%20image%2020240321112042.png)
Given this system state at $t = 0$:
![](Pasted%20image%2020240321112120.png)
We have that
- $T(x,t=0) = 500$ (The entire bar is 500$\degree C$ at the initial time)
- $T(x=0,t) = 0$ (The left wall is always 0$\degree C$)
- $T(x=L,t) = 0$ (The right wall is always 0$\degree C$)

How can this be solved analytically?

First we define two single-variable functions $X(x)$ and $Z(t)$ to replace $T(x, t)$, such that $T(x,t) = X(x)Z(t)$:
![](Pasted%20image%2020240321112420.png)
This allows us to separate our variables:
![](Pasted%20image%2020240321112437.png)
We assume that for these two sides of the equation to be equal they must both equal some constant, which we denote as $-P^2$:
![](Pasted%20image%2020240321112535.png)
We take the left-hand equation first, and rearrange into this form:
![](Pasted%20image%2020240321112647.png)
By forming the auxiliary quadratic equation ($m^2 + P^2 = 0$), we can find the roots
![](Pasted%20image%2020240321112721.png)
Which tells us that the solution is of the form
![](Pasted%20image%2020240321112752.png)
Now we take the right-hand equation:
![](Pasted%20image%2020240321112830.png)
If we separate the variables we can integrate straightforwardly:
![](Pasted%20image%2020240321113010.png)
![](Pasted%20image%2020240321113032.png)
![](Pasted%20image%2020240321113016.png)
With these two solutions we can undo our "splitting" of $T(x, t)$ and obtain a solution:
![](Pasted%20image%2020240321113205.png)
(Note that the unknown constant D was absorbed into A and B.)
Then you spend ages applying the boundary conditions to find the values of the constants:
![](Pasted%20image%2020240321113427.png)
![](Pasted%20image%2020240321113513.png)
(Reminder that you don't need to actually know how this works.)
Combining this gives the analytical solution:
![](Pasted%20image%2020240321113603.png)
![](Pasted%20image%2020240321113613.png)
Clearly, this is too much work.
# Numerical solutions for PDEs
We can develop finite difference approximations for PDEs in a similar manner to ODEs.
The first order derivatives obtained via the central difference method can be expressed as:
![](Pasted%20image%2020240321113959.png)
And the second order derivatives, also via the central difference method:
![](Pasted%20image%2020240321114104.png)
(Where $h$ and $k$ are the steps in $x$ and $y$ respectively.)

To solve a PDE using these approximations (by hand):
1. Create a graphical representation of the solution domain