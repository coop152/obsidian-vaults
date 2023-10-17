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
Consider this term:
$$\lambda q.qxyz$$
This is comparable to a function $f(g) = g(x, y, z)$ where x, y, and z are free variables.
Notably, if you pass in a lambda-term $g$ which is a projection, the result will be one of $x$, $y$ or $z$ by itself. In that sense, the term acts like a 3-tuple in which the individual values can be extracted by giving one of the three projection functions. Imagine representing the 3-tuple `(5, 10, 50)` in Python like this:
```python
# free variables
x = 5
y = 10
z = 50
# define tuple
three_tuple = lambda g : g(x, y, z)
# access first item: 5
three_tuple(lambda p, s, r: p)
# access second item: 10
three_tuple(lambda p, s, r: s)
# access third item: 50
three_tuple(lambda p, s, r: r)
```
### If-else
You can think of the previous example in a different way - if the free variables were functions instead of values, then applying the projection serves as a way to choose a branch of computation. Take this simple example that provides something akin to an if-else:
$$\lambda g. gxy$$
Where $x$ and $y$ are complex terms encoding subsequent behaviour. Depending on the projection $g$, either $x$ or $y$ may be left. Using the python analogy:
```python
# subterms
x = lambda x1 : 2 + x1
y = lambda y1 : 2 * y1
# define if-else thing
if_else = lambda g : g(x, y)
# first branch taken: recieve the function x
if_else(lambda p, s: p)
# second branch taken: recieve the function y
if_else(lambda p, s: s)
```
### Iteration
Suppose we have two lambda-terms $s$ and $z$, and we want to apply $s$ to $z$ 5 times. We could write this:
$$s(s(s(s(sz))))$$
Maybe we make this into an "iterate five times" function:
$$\lambda z. \lambda s.s(s(s(s(sz))))$$
This applies the given term $s$ to the given term $z$ 5 times. We might call this an "iterator". We could write a term that takes such an iterator as an argument, and applies some term to another a number of times according to that iterator:
$$\lambda n.(nt)f$$
This will apply the function $f$ to $t$ the number of times indicated by the iterator $n$.
#### Successor
We want a term that, given an iterator, returns an iterator that is one iteration longer. For example, take the iterator $\lambda z. \lambda s. s(sz)$ and make it $\lambda z. \lambda s. s(s(sz))$.
In order to accomplish this, we might try to replace $z$ with $sz$, but this requires some tricks to actually be alpha-equivalent and to not fall apart when performing capture-avoiding substitution. If we take the original iterator and replace the original $s$ and $z$ with new ones, we can safely do this:
$$\lambda n.(\lambda z. \lambda s.n(sz)s)$$

Lets use this to perform a more complicated operation. Namely, let's write a term that takes an iterator and returns the iterator with double the number of iterations. ***CONTINUE FROM HERE***