When performing normal FP arithmetic, we have a precision $p$. Usually this is the precision that the hardware computes in, and we call it the **working precision**. The content that follows explores the ways to extend the accuracy of our results beyond working precision.
We will investigate two strategies:
1. Using hardware/working precision $p$ to obtain more accurate results in the form of an *unevaluated sum* of multiple values in precision $p$
2. Using software to compute in a precision higher than $p$.

# Mixed precision
There are two ways that mixed precision arithmetic may be implemented:
1. Mixed precision operations - an adder, multiplier, etc. that produces an answer using multiple precisions (either internally or in the input/output)
2. Mixed precision algorithms - algorithms that utilize operations of different precision at different steps

Arithmetic operators with the same input/output precisions are called **homogeneous operators**, and are by far the most common form. Some processors implement operators with different input/output precisions, called **nonhomogeneous operators**. 