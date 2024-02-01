The main advantages of floating-point over fixed-point arithmetic are:
- The range of representable values is wider - under and overflow are much less common
- The accuracy of representation is relative to the number being represented rather than absolute. This is achieved by increasing the smallest quantity as the numbers being represented get larger.
- The behaviour is well-defined by the IEEE 754 standard, unlike fixed-point representations which vary.
- It's more widely used, which makes error analysis of it more widespread.

The main disadvantage is that it's more expensive to implement.

# Notational Remarks
Floating Point operations (herein shortened to FP) are referred to with $\circ(x\text{ op }b)$, where $\circ \in \{RN, RZ, RU, RD\}$ and $op \in \{+,-,\times,\sqrt{}\}$. This is to differentiate them from the exact operations they parallel, which are represented as $x\text{ op }y$ like normal.
The circle values are the *rounding modes*, which indicate how the operation will round numbers.

# FP representation
A set of FP numbers $\mathcal{F} \subset \mathbb{R}$ is a subset of the real numbers which is (partially) characterised by:
- It's radix/base $\beta$, where $\beta \geq 2$ (though nowadays it always equals 2)
- a precision $p \geq 2$
- extremal exponents $e_\min$ and $e_\max$.

Usually $e_\min < e_\max$ and $e_\min = 1-e_\max$.
Elements of $\mathcal{F}$ have the form $m \times \beta^{e-p+1}$, where $0 \leq |m| \leq \beta^p - 1$ and $e_\min \leq e \leq e_\max$.
$m$ is the integer *mantissa* (or *significand*), and $e$ is the integer *exponent*. 
$p$ represents the precision of the resulting FP number by determining the number of values that $\beta$ can take on. 
![](Pasted%20image%2020240201125547.png)
This part is meant to be pre-binary representation but its just confusing without it:
$p - 1$ is the number of bits dedicated to the mantissa. Observe this representation, where 
$\beta = 2$
$p = 3$
$e_\min = -2$
$e_\max = 3$

| E   | E   | E   | M   | M   |
| --- | --- | --- | --- | --- |
| 0   | 1    | 1    | 0    | 0    |
E and M are the bit representations. That is, E = 011 and M = 00.
$M$ is stored as if it always had a 1 in the MSB that was cut off. Therefore, you always add this "invisible" 1 to get the true value of $m$. Therefore:
$m = 2^{p-1} + M$
The exponent as-stored (E) is "biased": We need a signed exponent to represent both small and large numbers, but representing it using two's complement makes comparisons difficult (for reasons not yet explained). So, we "bias" the exponent, subtracting