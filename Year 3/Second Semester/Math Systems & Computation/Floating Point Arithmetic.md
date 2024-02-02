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
- How to *correctly round* FP data
- Some recommended functions, e.g. *exp, log, power, tan, cos,* etc.

## Fused multiply-add
IEEE 754 defines an operation that performs these two basic arithmetic operations in one: $$\circ(x\times y+z)$$
This is called the *fused multiply-add* (FMA).
Note that there is only one rounding step. This differentiates FMA from performing a separate multiplication followed by an addition:
$$\circ(\circ(x \times y)+z)$$
This single rounding improves the accuracy of the operation, and can also result in a speed-up. Compilers will usually take advantage of this instruction whenever possible.

## Standard data types
![](Pasted%20image%2020240202114847.png)
The standard defines 4 binary data types: binary128, binary64, binary32 and binary16.
- These formats are signed, with the MSB being the sign bit $s$. 0 is positive, 1 is negative.
- The significand is logically 1 bit longer than it appears in the binary format; as they are normalised, there is always a 1 at the start which is omitted to save space.
- The value can be calculated with this expression: $(-1)^s \times m \times 2^{e-p+1}$
- binary16 is intended in the standard for memory operations and not computation, but some hardware devices do perform computation on it anyway.
- binary128 is rare in hardware, but sometimes used in software.
- binary64 and binary32 are by far the most common in hardware. binary32 is commonly known as `float` and binary64 as `double` in programming languages.

# Intel extended precision FP
![](Pasted%20image%2020240202120502.png)
Intel's x86 instruction set provides an 80-bit FP data type, starting with the 8087 math co-processor (1980s). It is based on the extended precision specification in the IEEE 754 standard, but not exactly the same.
The format is similarly encoded to binary32/64, but the MSB of the significand is not implicit, meaning all normalised numbers will contain a 1 in the 63rd place.
The registers in the FPU are 80 bits wide to accommodate this format, and generally any lower precision formats will be increased in size while they are stored in these registers. They need to be rounded back down when converted back to a regular FP number, which incurs a performance penalty from the required rounding, so there is the option to disable the extended precision format.
This format can be an issue when writing multi-platform code: compilers may automatically use these extended precision registers on x86 platforms, giving different results from other platforms that only support regular 64 or 32 bit FP numbers.
# Others + summary
![](Pasted%20image%2020240202121637.png)
Apart from those previously mentioned, there are another 2 FP data types that are intended for machine learning, bfloat16 and TensorFloat-32. They are both relatively small, which is beneficial for performance in machine learning, and have proportionally larger exponents compared to the usual formats, giving them bigger range but reduced precision.
![](Pasted%20image%2020240202121855.png)
($f_\min$ is the smallest positive normal value, and $s_\min$ is the smallest positive subnormal value.)
# Rounding
When performing operations that take FP inputs or produce FP outputs, it is likely that we produce outputs in higher precision. Almost always, we need to map this higher precision value back into a smaller FP arithmetic.
Operations that map a value into a target FP arithmetic are called *rounding*. Rounding is performed as part of every FP operation, as well as during conversion between different data types.
As mentioned before, we refer to rounding with $\circ (x\text{ op }y)$, where $\text{op}$ is an operation and $\circ$ is one of $\{RN, RZ, RU, RD\}$.
In FP arithmetic, overflows (including those caused by rounding) automatically evaluate to infinity. This is unlike in fixed-point arithmetic, where *saturation* will be performed after an overflow to return a maximal value. FP's automatic infinity is suitable in most cases.

IEEE 754 specified the following rounding modes:
- Round toward $-\infty$ (RD(x)): Returns the greatest FP value in the target precision that is less than $x$. (i.e. rounds down to the nearest value)
- Round toward $\infty$ (RU(x)): Returns the smallest FP value in the target precision that is greater than $x$. (i.e. rounds up to the nearest value)
- Round toward zero (RZ(x)): Returns the closest FP value in the target precision that is not greater in magnitude than $x$. (i.e. Rounds down for positive numbers, rounds up for negative numbers)
- Round to nearest, ties to even (RN(x)): Returns the closest FP value, and if both nearby values are equally close, returns the one with an even significand. This is usually the default mode in most hardware.

Here are some example cases:
![](Pasted%20image%2020240202123814.png)
($x_m$ is the true value halfway between the two neighbouring values; it cannot be represented in the FP arithmetic.)
(a): If $x$ is negative, and is closer to the more negative neighbour, then RN and RD will round down while RZ and RU will round up.
(b): If $x$ is positive, and is closer to the more positive neighbour, then RN and RU will round up while RZ and RD will round down.
(c): If $x$ is negative, and is closer to the less negative neighbour, then RD will round down while RN, RZ and RU will round up.
(d): If $x$ is positive, and is closer to the less positive neighbour, then RU will round up while RN, RZ and RD will round down.

## Types of rounding
There are two commonly mentioned types of rounding:
- *Correct rounding* - When some FP operation or function produces values as if the function was computed in infinite precision and range and then rounded, it is said to be a *correctly rounded* FP operation for a given rounding mode.
- *Faithful rounding*/*faithful arithmetic* - When some FP operation or function produces *either* of the two FP values surrounding the exact result, regardless of the rounding mode, the arithmetic is said to be *faithful*.
- Any arithmetic that provides correctly rounded results is faithful.

## Table Maker's dilemma
Having correct rounding for an arithmetic is a much more strict requirement than having a faithful arithmetic. IEEE 754 only requires that *addition/subtraction, multiplication, division, square root, fused multiply-add and conversions* are correctly rounded: More complex functions (e.g. exp, log, sin, cos) are *recommended* and not required due to how expensive they are to implement, especially with correct rounding.
The problem is that, in general, outputs of elementary functions can produce non-terminating decimal outputs which might require infinitely many digits to decide the correct rounding.

## Properties of rounding
- If a number is already a number in the target precision, then $\circ(x) = x$ for all rounding modes.
- Rounding is *monotonic* - that is, if we have two real numbers $x$ and $y$ such that $x \leq y$ then it is always the case that $\circ(x) \leq \circ(y)$. The same is true if $x\geq y$. 
- RN is an *unbiased* rounding scheme, whereas RZ, RD and RU are *biased*, or *directed*.
- RU cannot round to $-\infty$, and RD cannot round to $+\infty$.
- RN and RZ are *symmetric* with regards to zero.
- $RU(x) = -RD(-x)$ and $RD(x) = -RU(-x)$.
- If RN is breaking a tie where, for example, $x$ is halfway between the largest FP value and infinity, RN rounds to infinity.