Recall everything from previous units about unsigned integers, signed integers, etc.
# Signed Integers
The most obvious way to represent a signed integer is to designate a bit as the **sign bit**, with one state representing a positive number and the other representing a negative. This representation was soon abandoned as it is quite cumbersome:
- Enables both positive and negative zero, which are distinct values
- Requires more complicated adders, as the sign of the result is not clear until the addition has been performed.

The actual representation used in most hardware is Two's Complement. A leading 0 represents a positive number, and a leading 1 represents a negative; The representation is otherwise the same as an unsigned number, but with the value of the MSB inverted. This solution is the well suited for hardware.
![](Pasted%20image%2020240201121220.png)
While an unsigned binary number has range $0$ to $2^n-1$, a two's complement number has range $-2^{n-1}$ to $2^{n-1}-1$. So the positive range is halved.
All negative numbers have a 1 in the most significant bit, which is still known as the **sign bit**. Hardware need only check this bit to determine if a number is negative or positive.
It is true in two's complement that $\bar{x} + 1 = -x$. This also makes negating a number very simple in hardware; simply invert the bits and then add one.
Some care needs to be taken when transforming a signed number in this format to a different size. When making it bigger you need to perform **sign extension**, where you insert 1's in the new most significant bits. 
# Fixed Point Arithmetic
How do we represent real ($\mathbb{R}$) numbers using bits?
The most straightforward solution it to extend our current number representation in the opposite direction: We grow to more significant digits using increasing powers of two, so we can grow to the less significant fractional digits using decreasing powers of two:
![](Pasted%20image%2020240201122153.png)
In doing this, we have essentially shifted our smallest unit of counting. Before, our LSB represented the quantity of 1, and that was the smallest difference we could have. Now, our LSB represents the quantity of $2^{-8}$. So this representation can represent any multiple of $2^{-8}$.
This representation has some benefits:
- All integer arithmetic operations can be applied to fixed point numbers, with no changes
- The ability to reuse integer arithmetic hardware makes it very fast and efficient

However, it has very important drawbacks which prevent it from being used in the general case:
- Range and precision are directly coupled
- Increasing the fractional precision directly results in reduced integer range (assuming the same number of bits)

There is a standard fixed point representation for C, which allows processors with no FPU to perform calculations on real values. Multiple representations are provided for various purposes, for example:
- Accum (signed, 16.15 bits)
- Fract (signed, 1.31 bits, where the only integer bit is the sign bit)