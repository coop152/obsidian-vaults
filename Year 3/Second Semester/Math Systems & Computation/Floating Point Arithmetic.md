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
## Binary representation
(This part is totally original from the slides, cause they are not very helpful)
Observe this binary representation, where 
$\beta = 2$
$p = 3$
$e_\min = -2$
$e_\max = 3$

| E   | E   | E   | M   | M   |
| --- | --- | --- | --- | --- |
| 0   | 1    | 1    | 0    | 0    |
Notice that $p - 1$ is the number of bits dedicated to the mantissa. Why not $p$? This is explained in short order.

$E$ and $M$ are the bit representations of $e$ and $m$, respectively. That is, $E = 011$ and $M = 00$. These representations are not equal to the values they represent - you need to do some conversion before using them.

$M$ is stored as if it has an invisible 1 in the MSB. To get $m$, you add this "invisible" 1. In this example $m = 100$, aka M with a 1 prepended.
In numeric terms, you calculate $m$ from $M$ with this equation:
$m = 2^{p-1} + M$

The exponent as-stored (E) is "biased": We need a signed exponent to represent both small and large numbers, but representing it using two's complement makes comparisons difficult (for reasons not yet explained). Since we are assuming that $e_\min = -e_\max + 1$, we can add the maximum exponent to $e$ to get a number in the range 1 to $2e_\max$. This allows us to store the exponent as an unsigned number which can be easily compared, but also easily retrieve the actual value by just subtracting $e_\max$.
Therefore, you can calculate $e$ from $E$ with this equation:
$e = E - e_\max$
In this example, $e = 0$.
Now you can calculate the final value. But why is this the formula?
$$m \times \beta^{e-p+1}$$
This could be read instead as
$$m \times \beta^{-p+1} \times \beta^{e}.$$
The exponent in the middle serves to shift $m$ such that the hidden 1 is the only integer digit, and everything else is fractional. In this example where $m = 100$:
$$m \times \beta^{-p+1} = 0b100 \times 2^{-2} = 0b1.00$$
Or, for example, if $m = 101$:
$$m \times \beta^{-p+1} = 0b101 \times 2^{-2} = 0b1.01$$
This makes the parallels to decimal numbers in normal form much clearer. Now that we have this shifted mantissa, we multiply by base^exponent:
$$0b1.01 \times 2^{0} = 0b1.00$$
so the number represented is 1.25.

Our representation also has some special cases:
- `00000` is reserved for the number zero. 
- `00001` and `00011` are subnormal numbers, because the exponent is zero and a number is subnormal if $e=e_\min$.
- `00100` and `11011` are regular, normalised numbers.
- `11100` (number where $e = e_\max$ and $M = 0$) represents positive infinity.
- `11101` and `11111` (number where $e = e_\max$ and $M \neq 0$) represent NaN.

This is a small number system, so we can write down every possible number. Here is a table:
![](Pasted%20image%2020240201150451.png)
If we plot these values on a line, we can visualise how the format performs in precision and range:
![](Pasted%20image%2020240201150558.png)
Notable observations include:
- The gaps between numbers doubles at every power of 2, except 0.25 (which is because of the subnormals)
- At it's most precise, the representation has gaps of 0.0625 in-between numbers. This can be calculated with this equation: $2^{e_\min}\times 2^{1-p}$ 
- In general, the gap in front of any FP number x is equal to $2^e \times 2^{1-p}$ (where $e$ is that number's exponent). This is commonly called a *unit of least precision (ulp)*, written as $ulp(x)$.

Sometimes, you may measure relative error in ulps. In this representation, the number 4 is 8ulps away from 1; this is seen on the graph as 8 gaps between them (of varying size).
People sometimes call $2^{1-p}$ the *machine epsilon* of an FP representation, denoted as $\epsilon$.

# IEEE 754 Standard
IEEE 754 is a standard for FP representation and arithmetic. It specifies, among other things:
- 4 binary FP formats, three for computation and one for data interchange
- Some decimal FP formats (not relevant to this course)
- The required operations of *add, subtract, multiply, divide, square root, compare* and some others
- Floating-point exceptions and how they should be handled, particularly the splitting of NaN values into two categories called *quiet* and *signalled* NaNs
- Rounding modes for FP arithmetic
- How to 