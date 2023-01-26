## Rules for Analysing Algorithms
- We perform the analysis concerning a **computational model** (that is, not a specific device)
- We will usually use a generic single-processor **random-access machine** (RAM)
    - All memory **equally expensive** to access
    - Instructions executed one after another **(no concurrent operations)**
    - All reasonable instructions take **unit time**
	     - Except, of course, function calls
    - Constant word size
        - Unless we are explicitly manipulating bits

## Input Size
Time and Space Complexity
- This is generally a function of the input size
- How we characterise input size depends on:
    - Sorting: number of input items
    - Multiplication: total number of bits
    - Graph algorithms: number of nodes and edges

## Definitions
$$\begin{align*}
f(n) \in O(g(n)) \text{ if there exist positive constants } c \text{ and } n_0 \text{ such that } \\ 
0 \leq f(x) \leq cg(n) \text{ for all } n \geq n_0.
\end{align*}$$
$$\begin{align*}
f(n) \in \Omega(g(n)) \text{ if there exist positive constants } c \text{ and } n_0 \text{ such that } \\ 
0 \leq cg(n) \leq f(x) \text{ for all } n \geq n_0.
\end{align*}$$
$$\begin{align*}
f(n) \in \Theta(g(n)) \text{ if there exist positive constants } c_1, c_2 \text{ and } n_0 \text{ such that } \\ 
0 \leq c_1g(n) \leq f(x) \leq c_2g(n) \text{ for all } n \geq n_0.
\end{align*}$$
Strictly speaking, the big-Os are sets of functions and a function is "in" them, not equal to them. However, $f(n) \in O(g(n))$ is commonly written as $f(n) = O(g(n))$ for simplicity.

- $O(g(n))$ - Worst case/Upper bound. If an algorithm is $O(g(n))$, then its complexity is at worst proportional to $g(n)$.
- $\Theta(g(n))$ - Average case. If an algorithm is $\Theta(g(n))$, then its complexity is exactly proportional to $g(n)$ in the average case.
- $\Omega(g(n))$ - Best case/Lower bound. If an algorithm is $\Omega(g(n))$, then its complexity is at best proportional to $g(n)$.

## Example
Q: Use the formal definition of $\Theta$ to show that $\frac{1}{2}n^2 -3n = \Theta(n^2)$.
A: 