Problem: If we have some input data with an unknown amount of error, can we predict the error in some computation $x \circ y$?
We can find the errors for the inputs $x$ and $y$ easily. Given the two true values $x$ and $y$, there are two approximate representations $\bar{x}$ and $\bar{y}$ with some absolute and relative error:
![](Pasted%20image%2020240214151352.png)
But how can we find the absolute and relative errors for:
- $x + y$
- $x - y$
- $x \times y$
- $x \div y$?

For the purposes of this investigation, we pretend that these operations are **error-free**. This way, we can specifically analyse the error that is contributed by the data.
# Estimating Error
Because errors are often unknown, it is very useful to be able to compute an estimate of the effects error has had on some computation.
The task, given a function $f(x)$, is to predict the effect of an error ($\Delta x$) in the independent variable $\bar{x}$ when evaluating $f(\bar{x})$.
For example, consider $f(x) = x^4$. For input $\bar{x} = 3.5$, we can estimate the effect of an error of $\Delta x = 0.01$:
![](Pasted%20image%2020240214152241.png)
We start by directly calculating the result of the function on $\bar{x}$, assuming there is no error and $\bar{x} = x$. Then we calculate the result for $\bar{x} + \Delta x$ and $\bar{x} - \Delta x$, getting the results for the two error extremes. 
These two extremes serve as bounds for $f(\bar{x})$. Any value of $\bar{x}$ plus $\Delta x$ error will be between these values.
In this example, the effect of the error on $f(\bar{x})$ is quite large. 

We can use the **Taylor series** to estimate this error analytically.
![](Pasted%20image%2020240214153033.png)