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

Sometimes big-O complexities can appear in arithmetic expressions, for example $f(n) = O(n^2) + O(n)$. In that case, the higher order term will cancel out the lower order one like usual and make the expression equivalent to $f(n) = O(n^2)$. Other cases include:
- $f(n) = 2^{O(n)}$, which can't be reduced but has a straight forward meaning ($2^{cn}$ for some $c$)
- $f(n) = 2^{O(\log n)}$, which can be transformed into $f(n) = n^{O(1)}$. This is called a **polynomial bound**.

We often talk of **polynomial bounds** like $f(n) = n^{O(1)}$, and **exponential bounds** like $f(n) = 2^{n^{O(1)}}$.

There is also **small-o notation**, which is equivalent to big-O notation but inclusive.
![](Pasted%20image%2020231026113332.png)
Also note that for a Turing machine, a linear time complexity is **impossible**.

Back to our analysis of $M_1$:
We consider its fours stages separately.
- Stage 1: The machine scans the entire tape to verify it is in the correct form, which takes $n$ steps. It then moves back to the left, ready for the next stage, which takes another $n$ steps and brings the total for this stage to $2n$ steps. In big-O, this is $O(n)$.
- Stage 2 & 3: A single iteration of Step 3 requires $n$ steps as it goes from one side of the tape to the other. As part of Stage 2, this will happen at most $(n/2)$ times because every sweep eliminates two characters. Therefore, the total time taken by stages 2 and 3 is $(n/2)n = O(n^2)$.
- Stage 4: The machine scans the entire tape to determine if there are any un-eliminated characters, which takes $n$ steps. This is $O(n)$.

In total, the running time is $O(n) + O(n^2) + O(n)$, or $O(n^2)$. This concludes our analysis of this machine.

Let's introduce some more notation for classifying languages according to their complexity:
![](Pasted%20image%2020231026114531.png)