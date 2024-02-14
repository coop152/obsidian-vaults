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

## Using the Taylor series
We can use the **Taylor series** to estimate this error analytically. Taking our known value as $f(\bar{x})$, we write:
![](Pasted%20image%2020240214153033.png)
We make it finite by truncating terms of second order and above, which gives us this equation:
![](Pasted%20image%2020240214153551.png)
So now we have a formula for finding *an estimate of* the error in $f$ given any input, as long as we can calculate $f$'s derivative.
Applying this to the previous example where $f(x) = x^4$, $\bar{x} = 3.5$ and $\Delta \bar{x} = 0.01$:
![](Pasted%20image%2020240214154328.png)
This estimated error is very close to the error calculated with the previous method. You can see why when you see how close the Taylor series approximation is to the actual function:
![](Pasted%20image%2020240214154828.png)

# Propagation of Error
We can use a similar technique involving the multi-variable Taylor series to assess how error propagates in functions of multiple independent variables. We take this two-variable Taylor series:
![](Pasted%20image%2020240214155145.png)
And truncate the terms of second order or higher:
![](Pasted%20image%2020240214155200.png)
Essentially, the same as before but with both variables and using the relevant partial derivatives.
This allows us to start investigating the binary operators. Let's start with addition:
Using the formula, we can get an absolute error:
![](Pasted%20image%2020240214155356.png)
This confirms the intuitive assumption that the error resulting from an (error-free) addition is the sum of the errors in the inputs.
We can get the relative error, using this result:
![](Pasted%20image%2020240214155532.png)
In terms of both the absolute and relative errors of the inputs.

Doing this for all of the operators, we get these results:
![](Pasted%20image%2020240214155716.png)
Of interest is:
- The relative error of multiplication and division is additive, compared to the weighted average-like behaviour of addition/subtraction.
- The absolute error of division has $v^2$ in the denominator; be careful of dividing by very small numbers, as the error may rack up quickly.

# Underflow & Overflow
If a number is larger than the largest representable FP number then the result will overflow, and if a number is smaller than the smallest representable FP number then it will underflow.
It's important to remember that this doesn't just apply to input and output data - intermediate values in a calculation can also experience this.
For a simple example, consider the problem of finding the length of a triangle's hypotenuse given the length of the other sides:
![](Pasted%20image%2020240214160241.png)
Suppose that $x$ and $y$ equal $10^{200}$. The max representable FP number is $1.79769\times 10^{308}$, but when we calculate $x^2$ and $y^2$ we get $10^{400}$ - they overflow in the middle of the calculation.
We can solve this problem by reformulating the problem:
![](Pasted%20image%2020240214160645.png)
(This also solves another case wherein $x^2$ and $y^2$ underflow, causing $z$ to evaluate to 0.)