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
# Analytical solutions
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
# Numeric solutions
For complicated ODEs and large systems of ODEs, finding an analytical solution is often prohibitive or even impossible. In this case, we reformulate the problem of solving them into an arithmetic problem. 
## Euler's Method
Euler's method says that we can *predict* the value of $T(x_{i+1})$ based on the value of $T(x_i)$ using its derivative:
![](Pasted%20image%2020240227165524.png)
![](Pasted%20image%2020240227165649.png)
We can repeatedly apply Euler's method, finding the value of T for each t (according to our step size.)
![](Pasted%20image%2020240227165847.png)
This is an **estimate**; each application of Euler's method creates a small amount of error, which accumulates as steps are taken.
We can improve our estimate by reducing our step size, at the cost of more computation. The results will be worse for functions with a large rate of change.
Here is an actual result of applying Euler's method to the Cook's dillema:
![](Pasted%20image%2020240227170552.png)
You can see that even a step size of 60 seconds is reasonably accurate to the true solution, going a bit low as the target is approached, but a step size of 1 second gives a very good approximation.

## Mid-point method
One of the issues that limits the accuracy of the Euler's method is that it uses the gradient at the beginning of the timestep to estimate the entire interval. If the rate of change is high, the gradient may have changed significantly by the next timestep, creating lots of error.
![](Pasted%20image%2020240227212030.png)
In the midpoint method, we use the gradient *at the midpoint* instead, which better captures how the slope changes within the interval.
![](Pasted%20image%2020240227212325.png)
To perform this method:
1. Use Euler's rule to predict $y$ at the midpoint
$$y_{i+0.5} = f(x_i,y_i)\frac{h}{2}$$
2. Predict the slope at the midpoint based on that
$$y^{'}_{i+0.5} = f(x_{i+0.5}, y_{i+0.5})$$
3. Use the slope to extrapolate to $x_{i+1}$
$$y_{i+1} = y_i + f(x_{i+0.5}, y_{i+0.5})h$$

The results of this method are much closer to an analytical solution.
![](Pasted%20image%2020240227215906.png)
## Heun's Method
Another method with similarly improved accuracy is Heun's method. Instead of estimating a midpoint and then finding the slope there like the midpoint method, it finds the slope at the current step and the next so that it can take an average of them.
To perform this method:
![](Pasted%20image%2020240227223329.png)
![](Pasted%20image%2020240227223341.png)
The results of Heun's method are similarly closer to the analytical solution.
![](Pasted%20image%2020240227224047.png)
# Order accuracy
Euler's method is **1st order accurate**: If the function being solved is linear, then the result of Euler's method will be numerically identical to the analytical solution. If the order is higher then it is only an approximation.
The midpoint method and Heun's method are both **2nd order accurate**: If the function being solved is quadratic or lesser, then the result of these methods will be numerically identical to the analytical solution, else it is only an approximation.
For example, here is a second order ODE:
![](Pasted%20image%2020240227224419.png)
and here are the results of the three methods:
![](Pasted%20image%2020240227224432.png)
Midpoint and Heun are exact solutions, while Euler veers off.

# Runge-Kutta methods
The midpoint and Heun methods are part of a wider class of ODE solving methods called the *Runge-Kutta* (RK) methods. These methods are capable of achieving the accuracy of a Taylor series approach, without requiring the calculation of higher derivative terms.
These methods fit the general form:
![](Pasted%20image%2020240228124012.png)
Where $\phi$ is the *increment function*, representing the slope over the interval $h$. For example, in the midpoint method $\phi(x_i,y_i,h) = f(x_{i+0.5}, y_{i+0.5})$.
An nth order RK method takes the form:
![](Pasted%20image%2020240228124225.png)
(I don't think this really matters that much.)
## Ralston's Method (RK-2)
Ralston's method is a popular 2nd order RK method which achieves the minimum possible truncation error for a second order RK method. It is defined as:
![](Pasted%20image%2020240228124412.png)
While it is the same order as the Midpoint method and Heun's method, it produces more accurate results:
![](Pasted%20image%2020240228124646.png)
## 4th order method (RK-4)
Higher order RK methods can provide accurate results for higher order ODEs. A 4th order method can provide exact results for ODEs with quartic solutions and below.
This is a commonly used 4th order RK method, sometimes known as the **classical 4th order RK method**:
![](Pasted%20image%2020240228151523.png)
Here are the results it produces, compared to Euler and the 2nd order methods:
![](Pasted%20image%2020240228152020.png)
