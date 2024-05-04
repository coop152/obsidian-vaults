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
You must also take care with the angle you calculate; the result of $\tan^{-1}$ needs to be adjusted according to the signs of the inputs. It's like finding the argument of an imaginary number; have to add 