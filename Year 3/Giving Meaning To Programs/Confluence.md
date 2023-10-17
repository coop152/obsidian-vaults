# Preamble - Pretending $\lambda$-abstractions are functions
We can already model functions using beta-reduction. For example, here is the application of the identity function to another function:
![](Pasted%20image%2020231017141839.png)
Here is a slightly more complicated example, with a function that applies the given value to some free variable g twice:
![](Pasted%20image%2020231017141900.png)
But what about multiple arguments? We have no concept of "pairs" in our definition of the $\lambda$-calculus. Instead, we use currying (like in a functional programming language). For example, we want to create this:
![](Pasted%20image%2020231017142150.png)
That being a lambda term that takes a function $f$ with two arguments, a function $g$ with one argument, and some other thing $x$. We can use a form like this:
$$\lambda f.\lambda g.\lambda x.(fx)(gx)$$
First you apply $f$, which gives you:
$$\lambda g.\lambda x.((\lambda w_1\lambda w_2.??)x)(gx)$$
(Where ?? represents the unknown body of some arbitrary function)
Then you apply $g$:
$$\lambda x.((\lambda w_1\lambda w_2.??)x)((\lambda w_3.??)x)$$
Lets say we substitute $x$ for the free variable $x$:
$$((\lambda w_1\lambda w_2.??)x)((\lambda w_3.??)x)$$
We have achieved the desired meaning. That is, we have $f$ (whatever it is) applied to $x$ and the result of applying $g$ to $x$. Lets say $f$ adds it's arguments, and $g$ doubles it's arguments - the final result of $f(x, gx)$ would be $x + 2*x$. Let's test this on our lambda term (Arithmetic expressions in a $\lambda$-term are not valid, this is just for visualisation purposes):
$$((\lambda w_1\lambda w_2.w_1 + w_2)x)((\lambda w_3.2*w_3)x)$$
$$(\lambda w_2.x + w_2)(2*x)$$
$$x + (2*x)$$
Our curried "function" behaves as expected.
(Pretend each of these formulae had a $\xrightarrow{\beta\alpha}$ between them. They were beta reductions, but playing loose with the variable names.)

## Non-trivial behaviour
### Projections
A projection function simply returns an argument. For example:
$$\lambda x.\lambda y.\lambda z.x$$
Just returns the first argument and ignores the other 2.
### Returning multiple results
