## Master Method
Used to trivially solve these forms of recurrences: $T(n) = aT(n/b) + f(n)$.
![](Pasted%20image%2020230127110109.png)
In other words, using the example $T(n) = 9T(\frac{n}{3}) + n$:
- Identify $a$, $b$ and $f(x)$ in the given recurrence. $a$ is the coefficient of the RHS $T(n)$, $b$ is the denominator inside $T(n)$ and $f(n)$ is the rest of the recurrence not including the $T(n)$. In this example, $a = 9$, $b = 3$ and $f(n) = n$.
- Choose the correct case based on $f(n)$. In this example $n^{log_ba} = n^2$. $f(n) = n$ which is less than $n^2$, so take the first case where $\epsilon = 1$. Therefore, $T(n) = \Theta(n^2)$.

## Iteration Method
Simple process but difficult working:
1. Expand the recursive calls until you find a pattern
2. Re-arrange the repeating pattern as a summation
3. Evaluate the summation, by setting $k = n$.
For example, with $T(n)$