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

If you *did* care about the accuracy of the initial sharp incline, but you really wanted the improved performance of a small step size after that, you could employ an **adaptive** step size. For example, solve using $\Delta t=s$