We can now find if a problem is decidable and therefore computationally solvable in theory, but there may be some problems that take so long to solve that they aren't feasible. In this section we look at how to analyse the time complexity of a Turing machine, and decide if it is computationally intractable.

# Measuring Complexity
Take the language $A=\{0^k1^k|k \leq 0\}$. Clearly this language is decidable, but how long will a single-tape Turing machine take to decide $A$? Let us examine the single-tape machine $M_1$ for $A$:
![](Pasted%20image%2020231026110827.png)
Note that we are describing the machine using quite low level descriptions, importantly including the movement of the head.
Note that when analysing the complexity of a turing machine we are only concerned with the length of the input *string*, and not the structure it represents (if any). 
![](Pasted%20image%2020231026111638.png)
Like usual, we are more concerned with **asymptotic analysis**. That is, we usually disregard every term in the complexity other than the highest order, because that term dominates the others on large inputs. In other words, it's the usual **big-O notation**.
![](Pasted%20image%2020231026111851.png)
![](Pasted%20image%2020231026112015.png)
Also note that the base of a logarithm does not need to be expressed in a big-O complexity. Because $$\log_bn = \log_2n/\log_2b,$$
The base is equivalent to a constant coefficient and can therefore be ignored like any other.