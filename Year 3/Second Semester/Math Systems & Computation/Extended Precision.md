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