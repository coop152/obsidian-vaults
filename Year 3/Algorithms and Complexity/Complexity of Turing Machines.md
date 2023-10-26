We can now find if a problem is decidable and therefore computationally solvable in theory, but there may be some problems that take so long to solve that they aren't feasible. In this section we look at how to analyse the time complexity of a Turing machine, and decide if it is computationally intractable.

# Measuring Complexity
Take the language $A=\{0^k1^k|k \leq 0\}$. Clearly this language is decidable, but how long will a single-tape Turing machine take to decide $A$? Let us examine the single-tape machine $M_1$ for $A$:
![](Pasted%20image%2020231026110827.png)
Note that we are describing the machine using quite low level descriptions, importantly including the movement of the head.