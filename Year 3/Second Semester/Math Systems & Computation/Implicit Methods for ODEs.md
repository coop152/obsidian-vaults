# Explicit Methods and Stability
All of the previously shown methods for solving ODEs are **explicit methods**. This includes Euler's method, the Midpoint method, and Heun's method.
They all use information from the beginning of some interval to predict the value at the end of the interval. They are efficient and easy to implement, and are well suited to initial value problems (but not so much to boundary value problems.) The accuracy of these methods is controlled by the step size.

But do all ODEs respond well to these methods?
Consider this example ODE:
![](Pasted%20image%2020240321124450.png)
If we solve with step size $\Delta t=0.0001$ (and initial condition $y(t_0 = 0) = 0$), we get accurate results:
![](Pasted%20image%2020240321124528.png)
But if we increase our step size to 0.01, the results are very, very poor:
![](Pasted%20image%2020240321124742.png)
(Take note that the analytical solution hasnt changed, and the y-axis has gained an incredibly large scale.)
Worse than becoming inaccurate, it's become **unstable**.
Why does this happen? If we examine the analytical solution:
![](Pasted%20image%2020240321124944.png)
We see it is made up of three parts:
![](Pasted%20image%2020240321124956.png)
This is why the analytical solution (and the Euler with small step size) has a small, sharp incline at the start followed by a slow, gradual rise.

Let's extract the quickly decaying term into its own ODE.
![](Pasted%20image%2020240321130155.png)
By integrating with initial condition $y(t=0)=y_0$ we get this:
![](Pasted%20image%2020240321130314.png)
Which will decay to zero.
Solving using Euler's method instead gives the solution:
![](Pasted%20image%2020240321130553.png)
To be accurate to the analytical solution, we know that this must decay to zero. A requirement for this is that $|1 - 1000h| < 1$, so that each value is closer to zero than the last. Thus, we have a limitation on what we can use as our step size in this problem:
![](Pasted%20image%2020240321130837.png)

Testing at this bound shows a stable result, though not necessarily a useful one:
![](Pasted%20image%2020240321130950.png)
Explicit methods require strict conditions on the step size in order to get stable results, though they often require an even smaller step size to get acceptable accuracy.
The result of step size 0.002 oscillates about the true solution, but anything lesser will gradually tend towards it:
![](Pasted%20image%2020240321131218.png)
If you weren't interested in the accuracy of the initial sharp incline, then a large step size such as 0.001 might be useful.

You might have the idea to use an **adaptive step size**, which changes at certain points while solving. For example:
![](Pasted%20image%2020240321132258.png)
This seems like it could be the perfect solution, which allows a large performant step size while avoiding the quickly changing section of the solution that caused it to become unstable.
However, this will not work and it will still become unstable.
In the previous example, let's use the largest possible stable step size at the start but increase it ever so slightly once we get past the initial sharp incline:
![](Pasted%20image%2020240321132437.png)
Even just going 0.00001 over the boundary will still ruin the solution.
The stability of an explicit method's solution is not related to the region being solved for - it is decided by the ODE itself.

In summary, explicit methods:
- Require small step sizes for accuracy
- Can be unstable even with extremely small step sizes
- Can work poorly for **stiff ODEs**; that is, systems where there is a combination of rapidly and slowly varying components in the solution.
- Are stable or not depending only on the ODE and not the solution dynamics in the specific region being solved for. You cannot use a timestep outside the required limits at any point in your solution.

# Implicit Methods
Implicit methods offer a solution to these stability and accuracy problems.
Instead of updating our solution based on information at the start of the timestep (like with an explicit method), we use information at the **end** of the timestep:
![](Pasted%20image%2020240321133222.png)
![](Pasted%20image%2020240321133227.png)
This specific method is known as the **implicit Euler method**, and the resemblance with the regular Euler method is clear.
(It's called implicit because the unknown appears on both sides of the equation.)

## Implicit Euler method
![](Pasted%20image%2020240321133222.png)
Lets try solving the previously troublesome ODE using this new method.
![](Pasted%20image%2020240321133446.png)
Inserting the ODE into the implicit Euler equation gives:
![](Pasted%20image%2020240321133533.png)
Because this ODE is linear in $y$, we can easily rearrange this to give:
![](Pasted%20image%2020240321133642.png)
And just solve the arithmetic on the right (every value there is known).
If we do this with a step size of 0.002 (the previous boundary for a stable solution), we get a notably better result:
![](Pasted%20image%2020240321133819.png)
But this time we can take the step size much larger, with no instability. For step size 0.2:
![](Pasted%20image%2020240321133851.png)
The sharp incline at the start isn't represented properly, but the gradual increase is well represented and more importantly the solution is stable.

Let's investigate the quickly decaying solution term, as we did with the explicit Euler method:
![](Pasted%20image%2020240321134009.png)
Integrating with initial condition $y=y_0$ at $t=0$:
![](Pasted%20image%2020240321134028.png)
Now solving with the **implicit** Euler's method, we get:
![](Pasted%20image%2020240321134043.png)
We know that the solution should decay to zero, thus a stable result requires that $|1+1000h| \geq 1$. This is **always true**, so the implicit method is **unconditionally stable** in this case.

In summary, implicit methods:
- Use implicit information, information that is unknown at the current solution step.
- Can be unconditionally stable for certain problems, and in particular are useful for **stiff ODEs** (ODEs with solutions containing both fast and slow changing components)
- Allow greater accuracy *and* larger timesteps
- Have very simple update equations when the ODE is linear in the dependent variable (as with the given example)...
- ...but can be more complicated when the ODE is nonlinear. For example:
![](Pasted%20image%2020240321134417.png)
