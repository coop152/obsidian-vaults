# Explicit Methods
All of the previously shown methods for solving ODEs are **explicit methods**. This includes Euler's method, the Midpoint method, and Heun's method.
They all use information from the beginning of some interval to predict the value at the end of the interval. They are efficient and easy to implement, and are well suited to initial value problems (but not so much to boundary value problems.) The accuracy of these methods is controlled by the step size.

But do all ODEs respond well to these methods?
Consider this example ODE:
![](Pasted%20image%2020240321124450.png)
If we solve with step size $\Delta t=0.0001$, we get accurate results:
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