# Condition
The **condition** of a problem represents how sensitive the problem is to perturbations in the data. If small changes in the data lead to relatively large changes in the solution, then a problem is **ill-conditioned**.
Condition is concerned with the sensitivity of *the problem itself*, and is not related to any one method of solving the problem.
For example, consider the problem of finding the roots of this cubic equation:
![](Pasted%20image%2020240214161450.png)
The solution is to identify the linear factor $(x-1)$ and divide through:
![](Pasted%20image%2020240214161618.png)
Notice that this result features a double root.
Let's try making small changes to the coefficients, in this instance changing $a$ from 1 to 0.99 and 1.01.
![](Pasted%20image%2020240214161845.png)
The results have become drastically different with only a small error in the input. For $a = 0.99$ we lost the double root and ended up with three, and for $a = 1.01$ we now get only a single real root and two complex ones. This is clearly an **ill-conditioned problem**. 

## Wilkinson's Polynomial
Wilkinson's polynomial allows us to assess the sensitivity of polynomial roots to small coefficient variations.
![](Pasted%20image%2020240214162440.png)We can apply these to the previous example to get a more concrete showing of it's ill-conditioned-ness.
![](Pasted%20image%2020240214162548.png)
The single root number is low, meaning that the single root by itself is well-conditioned (as can be seen by how little it is affected by the perturbations in the graph).
In contrast, the double root number is high, showing that it is ill-conditioned, as we noticed.

## Condition Number
The problem of conditionality can be expressed as:
For a function $f(x)$ and a variable $x$ (with approximation $\bar{x}$), find the discrepancy between $f(x)$ and $f(\bar{x})$:
![](Pasted%20image%2020240214162933.png)
Unfortunately, in most cases $x$ is not known. However, if $x - \bar{x}$ (the error) is small, and $f(x)$ is continuous and differentiable, we can evaluate the problem using a Taylor series.
![](Pasted%20image%2020240214163212.png)This useful value is called the **condition number**, and it quantifies the condition of a problem.
![](Pasted%20image%2020240214163444.png)
There are three cases:
- $C=1$: The function's relative error is identical to the relative error in the observed variable. I.e, the output of the function will have the same amount of (relative) error as the input.
- $C > 1$: The function will **amplify** the relative error of inputs, producing outputs with more (relative) error than the inputs. (This is bad.)
- $C < 1$: The function will **attenuate** the relative error of inputs, producing outputs with less (relative) error than the inputs. (This is good.)

# Stability
The **stability** of a *method* for solving a problem is concerned with the sensitivity of the method to rounding errors in the solution process. A method that guarantees a solution as accurate as the input data allows is said to be **stable**, and otherwise it is **unstable**.

Stability differs from condition in that condition is related to the sensitivity of the problem itself, while stability is related to the sensitivity of the method with which you solve the problem.

For example, the quadratic equation is known to be unstable in certain conditions. We use it to solve this polynomial:
![](Pasted%20image%2020240214164305.png)
If we use double precision FP to solve, we get:
![](Pasted%20image%2020240214164320.png)
But if we use 4 sig fig to solve, we get:
![](Pasted%20image%2020240214164342.png)
One result is quite similar, but the other is very different. This demonstrates the instability of the quadratic formula.
Why? Well,
![](Pasted%20image%2020240214164613.png)
## Subtractive Cancellation
Subtractive cancellation refers to the round-off error introduced when subtracting two FP numbers of a similar magnitude. This is a problem unique to subtraction - it doesn't occur when adding the same numbers.
If we compare this step of the previous calculation in 4 s.f. versus double precision:
![](Pasted%20image%2020240214165142.png)
Subtractive cancellation is the reason that the 4 s.f. calculation is affected so heavily by this rounding error.