The **Finite Difference method** is another way of solving ODEs that is also capable of solving **boundary value** problems.
# Forward Difference Approximation
We have a function $f(x)$, and we want to estimate the derivative $f'(x)$. From the Taylor series, we know that:
![](Pasted%20image%2020240307143059.png)
If we subtract $f(x)$ and then divide by $h$:
![](Pasted%20image%2020240307143149.png)
And then we truncate the infinite series to get:
![](Pasted%20image%2020240307145701.png)
Which gives us an equation for estimating the derivative of a function (with some error $R_n$) based on the value at the current step and the next.
![](Pasted%20image%2020240307145827.png)
This is conceptually similar to Euler's method, as you can see in the graphical representation.
# Backward Difference Approximation
Essentially the same as forward difference approximation, but backwards. We use the Taylor series again, but this time with a negative step ($-h$):
![](Pasted%20image%2020240307150007.png)
We again subtract by $f(x)$ and divide by $h$:
![](Pasted%20image%2020240307150036.png)
And truncate to get:
![](Pasted%20image%2020240307150045.png)
Giving us two equations which allow us to approximate the derivative of an equation based on a value at any point, going backwards or forwards.
![](Pasted%20image%2020240307150242.png)
# Central Difference Approximation
The previous two techniques are useful in theory, but not actually very accurate. Central Difference Approximation combines the two to work over two timesteps, thus increasing accuracy.
To find the central difference, we subtract the backward difference from the forward difference, giving the difference from the step before $x_i$ to the step after $x_i$:
![](Pasted%20image%2020240307150452.png)
![](Pasted%20image%2020240307150457.png)
Notice that some terms cancel out; this is good for accuracy, as it means that when we truncate, we lose less information and therefore gain less error.
We truncate as usual, though thanks to the cancelling terms we have only truncated third order terms and higher:
![](Pasted%20image%2020240307150643.png)
The forward and backward difference methods are **first order accurate**, while the central difference approximation is **second order accurate**.
![](Pasted%20image%2020240307150734.png)
# Comparison
![](Pasted%20image%2020240307150821.png)