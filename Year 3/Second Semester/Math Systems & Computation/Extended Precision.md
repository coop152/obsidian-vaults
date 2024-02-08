When performing normal FP arithmetic, we have a precision $p$. Usually this is the precision that the hardware computes in, and we call it the **working precision**. The content that follows explores the ways to extend the accuracy of our results beyond working precision.
We will investigate two strategies:
1. Using hardware/working precision $p$ to obtain more accurate results in the form of an *unevaluated sum* of multiple values in precision $p$
2. Using software to compute in a precision higher than $p$.

# Mixed precision
There are two ways that mixed precision arithmetic may be implemented:
1. Mixed precision operations - an adder, multiplier, etc. that produces an answer using multiple precisions (either internally or in the input/output)
2. Mixed precision algorithms - algorithms that utilize operations of different precision at different steps

Arithmetic operators with the same input/output precisions are called **homogeneous operators**, and are by far the most common form. Some processors implement operators with different input/output precisions, called **nonhomogeneous operators**. 
IEEE 754 only requires nonhomogeneous variants as of recent revisions, but hardware (in general) hasn't caught up, leaving many processors with only homogeneous operators.

## Example operator: FMA (Fused Multiply-Add)
This is an instruction that appears in most modern CPUs. Given three precision-$p$ numbers $a$, $b$, and $c$, FMA computes $RN(a\times b+c)$.
By definition, FMA contains only a single rounding. Therefore, the result of $a \times b$ is not rounded before the addition, and introduces no rounding error. For this to be true, it must be held exactly in a wider internal format of $2p$ bits, which is then added to $c$ and finally rounded back to $p$ bits.

## Mixed precision algorithms
Consider an algorithm that adds $2^{-24}$ a hundred million times to $1$, using binary32 arithmetic.
![](Pasted%20image%2020240207151038.png)
This basic algorithm uses no mixed precision; all variables are `float`/`binary32`. The result of this computation, when using exact arithmetic, is approximately 6.96. This code, however, returns 1.
The problem is that `addend` is so small that adding it to 1 just rounds back down to 1 (in `binary32`). This happens on every iteration, and sum never increases.

We can fix this problem using mixed precision. We introduce a higher precision (`binary64`) for the addition step, which stops $1 + 2^{-24}$ from being rounded down:
![](Pasted%20image%2020240207151452.png)
This code produces the answer $6.96046...$, which is correct. The value is converted to a `float` on line 13, so this algorithm still produces a `binary32` result, but unlike the first example it produces a *correct* one.
Note that while the technique used above gives a correct answer, it can also impact performance if binary64 arithmetic is slower than binary32 on your platform.
Instead of increasing precision in a part of your program, you may also *reduce* precision in some parts. If your computer has faster low-precision arithmetic, and the reduced precision doesn't significantly impact your results, then you may selectively drop the precision of numbers to improve performance. 
From these two examples, you can see how mixed precision algorithms can be used to both increase accuracy and improve performance. 
# Error-Free Transformations
We can also extend the precision of our arithmetic without changing the actual FP precision by using alternative algorithms that avoid information loss. For example:
## 2Sum and Fast2Sum
Given two FP numbers $a$ and $b$ with precision $p$, we can obtain a sum ($s$) and an error value ($t$) such that $s = RN(a+b)$ and $s+t=a+b$. 
Both of these outputs are precision $p$ FP numbers. Think of $s$ as the actual result, while $t$ is the rounding error. When we have a tuple $(s, t)$ that represents one quantity like this, we say that it is an **unevaluated sum** of two FP values.
Note that:
- This only works for the RN (round to nearest, ties go to even) rounding mode. Other rounding modes cannot assure that the result is error-free.
- Computing $s+t$ will achieve nothing. We know that $RN(s + t) = s$, which is the whole point of $t$ existing. They are only useful as separate values.
### Fast2Sum
![](Pasted%20image%2020240207154030.png)
Fast2Sum performs addition between two numbers and returns the result, as well as the rounding error. This algorithm requires that the arguments are sorted by magnitude; it won't give an exact result otherwise (although it does give a good approximation if you violate this requirement).
`s` is simply the result of adding the two arguments as normal, rounding error included.
`b'` is the value of input `b` plus error.
`t` performs $b - (b + \text{error})$, so the `b`s cancel out and we are left with the error (negated, so that adding it to `s` would remove it).

### 2Sum
![](Pasted%20image%2020240207154729.png)
2Sum performs the same operation as Fast2Sum, but is more robust in that it doesn't require the arguments to be sorted. It has twice the steps as Fast2Sum, but steps 4 and 5 are parallelisable.
`s` is again simply the result of adding the two arguments, rounding error included.
`a'` is the value of input `a`, which may be with or without error depending on which argument introduced the value that was rounded out of the sum.
`b'` is the value of input `b`, which is similar to `a'`.
If `a` was the input that was rounded then `a'` will not equal `a`, and similarly for `b`. Only one of these can be equal to their corresponding input. Consequently:
`e_a` is the error in a, and `e_b` is the error in b. Only one will be non-zero.
So by adding them to make `t`, we get whichever was non-zero (because clearly $x + 0 = x$.)

## 2MultFMA
![](Pasted%20image%2020240207155653.png)
This is another error-free algorithm that is roughly analogous to 2Sum but for multiplication. That is, it outputs the product of 2 numbers as well as the exact error in the result.
This algorithm requires the FMA instruction, as can be seen in the second step. The way it works is clear; it calculates the rounded result, then uses FMA to subtract that rounded result from the exact result in order to obtain the error.
Note that there are some cases for very small $a$ and $b$ where $t \neq a\times b-s$, due to underflows.

## Multi-word arithmetic
Now what do you do with these unevaluated sums? There exist algorithms for performing the usual arithmetic operations on numbers represented as unevaluated sums. This allows us to take the outputs of the previously shown algorithms and perform extra precise arithmetic using the given errors.

# Summation algorithms
A very good use for error-free transformation functions is in algorithms that make heavy use of summation. This is a common occurrence in scientific computing and elsewhere, for example:
- Vector products
- Matrix-Vector products
- Matrix-Matrix products
- Means
- Variances
- Evaluating polynomials
- ODE and PDE solvers
- Weight updates in machine learning

Summations involving large amounts of numbers can suffer from **accuracy** loss (not precision!) if the magnitude of the sum greatly exceeds that of the values being added to it, as seen in the prior example program. This can lead to algorithms producing incorrect solutions. There are multiple solutions, which vary in efficacy depending on the data being summed:
## Recursive summation
![](Pasted%20image%2020240207161501.png)
This is the regular approach to summing a series of values, where the values are simply accumulated into a sum variable as normal.
Each iteration introduces new error of up to $u=2^{-p}$, from the rounding in the addition operation.
We can improve the accuracy of this basic solution by ordering our input values. If we sort in decreasing order, then the sum will progressively get bigger while the quantities being added to it get progressively smaller - this large gap in size can result in more rounding errors.
Inversely, if we sort in increasing order then we will be adding progressively larger values to a progressively larger sum. This usually reduces the amount of shifting needed to align the significands, and therefore reduces the amount (and magnitude) of rounding errors.
## Compensated summation
![](Pasted%20image%2020240207163248.png)
This algorithm uses Fast2Sum to incorporate the error from each addition into the next one. 
By reducing the amount of error that is discarded, a more accurate result is achieved.
## Cascaded summation
![](Pasted%20image%2020240207163745.png)
This algorithm also uses Fast2Sum, but instead of reintroducing the error from each addition into the next, it keeps a second sum of every error combined, which it adds to the rounded sum at the end. The accuracy of this method is similar to compensated summation, and which is faster will depend on the dataset.
## Example: Harmonic series
We illustrate the summation algorithms with an example: Calculating the summation of the harmonic series.
![](Pasted%20image%2020240207161900.png)
Using real numbers this series is *divergent* (it doesn't converge on a final value), but it is known to converge when limited precision arithmetics are involved.
We write a program to calculate the truncated series for some steps $N$. That is, we compute ![](Pasted%20image%2020240207162226.png)
### Using recursive summation
![](Pasted%20image%2020240207162408.png)
Note that limited-precision arithmetics will always fail to compute the harmonic series, no matter the precision. However, we will only be comparing limited-precision solutions to other limited-precision solutions.

When running this simple implementation, we get these results:
![](Pasted%20image%2020240207162645.png)
There is a not insignificant difference between the result for floats and doubles. We take the double result as our benchmark and try to improve the accuracy of the float result, starting with:
### Using recursive summation w/ reversed order
![](Pasted%20image%2020240207163001.png)
We use our previous observation and start by summing the smallest items first. Just this simple change significantly improves accuracy:
![](Pasted%20image%2020240207163122.png)
### Using compensated summation
![](Pasted%20image%2020240207163624.png)
By implementing compensated summation, we reduce our error even more:
![](Pasted%20image%2020240207163655.png)
### Using cascaded summation
![](Pasted%20image%2020240207163928.png)
By implementing cascaded summation, we also achieve good results:
![](Pasted%20image%2020240207163955.png)
In this case they are not as good as compensated summation, but this is up to this specific problem. In some problems cascaded summation would be better than compensated summation.