# Single variable case
You can find the minimum or maximum of a (single variable) function by checking where the gradient equals zero.
![](Pasted%20image%2020240504173103.png)
![](Pasted%20image%2020240504173112.png)
You can then check the sign of the second derivative to determine if it's a minimum or maximum (or an inflection point).
![](Pasted%20image%2020240504173141.png)
We can optimise using the gradient. For some starting point $x_0$:
1. Evaluate the gradient at $x_0$,
2. Take a step in the opposite direction to the gradient
3. Update starting point, repeat until a zero gradient is found

The size of the step must be adjusted (smaller) as the search continues, so that the actual optimum can be reached; otherwise it would just go back and forth between the two steps surrounding the optimum.
You will most likely not go so far as to actually find gradient=0. You will probably stop searching within some threshold, where $|f'(x)| < \epsilon$.
# Multi-variate case
Consider the continuous 2D function $f(x, y)$.
![](Pasted%20image%2020240504173734.png)
Just like with the 1D case, we take these steps for some starting point $x_0$:
1. Evaluate the gradient at $x_0$
2. Take a step in the direction of the optimum
3. Update starting point, repeat until optimum found

How do we find gradients in higher dimensions?
![](Pasted%20image%2020240504173937.png)
(In plain english: You find the gradient in every dimension, then you can find the gradient and magnitude.)
For example:
![](Pasted%20image%2020240504174043.png)
![](Pasted%20image%2020240504174113.png)
Then move in that direction, and repeat until the optimum is reached.
You must also take care with the angle you calculate; the result of $\tan^{-1}$ needs to be adjusted according to the signs of the inputs. It's like finding the argument of an imaginary number. For example:
![](Pasted%20image%2020240504174653.png)
You have found the optimum when all components of the gradient are zero (or when they are near it, like in the 1D case.)
To tell if this is a maximum or a minimum, we can use the Hessian of the function.
![](Pasted%20image%2020240504174753.png)
This is analogous to finding the second derivative of the 1D function and checking that.
## Method of steepest ascent
The previously described method will look something like this when performed:
![](Pasted%20image%2020240504175117.png)
(though you would have the step size decreasing so that it actually found the optimum.)
The problems with this method are:
- it requires evaluating the gradient at every single step
- you will move away from the optimum at points

With the method of steepest ascent, we instead move along the direction indicated by the gradient until the function stops increasing; only then do we recalculate the gradient. To do this, we need to transform our multivariate function into a univariate function along the direction indicated by the gradient.
Essentially, we want to "take a slice" of the function along the gradient line, like stepping along that line in infinitely small steps and grabbing each value of the function at that point to reduce it to a single variable.

We start as before:
![](Pasted%20image%2020240504175503.png)
Now instead of taking a step in that direction, we create equations for points on this line in terms of a new variable $h$.
![](Pasted%20image%2020240504175639.png)
This gives us this function:
![](Pasted%20image%2020240504175736.png)
We then find the maximum of this function using any method, from Golden Search to Newton's method, or using the method discussed for 1D functions at the start. in this case we can just differentiate because its an easy function:
![](Pasted%20image%2020240504175844.png)
These coordinates are the maximum point along the gradient line in the original function; we set our points to this and then repeat.
If we can find the maximum $h$ for $g(h)$ in a single step (like in the previous example, with an analytical solution) then this method is called **optimal gradient ascent/descent**.
In summary:
![](Pasted%20image%2020240504180234.png)