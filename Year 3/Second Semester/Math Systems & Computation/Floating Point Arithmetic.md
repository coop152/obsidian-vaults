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
![](Pasted%20image%2020240201125717.png)
