Read section 1.2.5 through to the end of section 1.2. Start reading section 1.3.

# Substitution
Like renaming that maintains $\alpha$-equivalence. Only operates on free occurrences of a variable.
![](Pasted%20image%2020231010133809.png)
Simple: 
- bcVar: If the term is a variable and that variable is the one being replaced, then replace it. Otherwise, do nothing.
- scAbs: If the term is an abstraction, take a fresh variable $w$ and replace the bound variable with it. Rename all occurrences of the old bound variable name with the new fresh variable $w$. Recur on the (renamed) right subterm.
- scApp: If the term is an application, recur on both subterms.

This operation has some important properties:
![](Pasted%20image%2020231010134848.png)
![](Pasted%20image%2020231010134859.png)
Simple:
(a): After substitution $[a/x]$, the set of free variables will gain $a$ and lose $x$ (if it had it).
(b): If $x$ wasn't in the free variables of a term then substituting something in for it will produce an alpha-equivalent result.
(c): You can still combine two renamings even if a substitution is being applied to the inner one, as long as the intermediate and final renaming variables are fresh.
(d): This is just the second step case
(e): You can apply a renaming to a substitution by simply renaming every occurrence of the given variable to the new one, assuming the new variable occurs nowhere in the substitution.
(f): You can change the variable to be substituted to another one by renaming and replacing the term right of the slash, as long as the new variable does not occur anywhere already.

Another important property is that substituting alpha-equivalent terms into alpha-equivalent terms gives us terms that are again alpha-equivalent. That is,
![](Pasted%20image%2020231010140741.png)

Here are some properties that define how multiple substitutions interact:
![](Pasted%20image%2020231010141437.png)
![](Pasted%20image%2020231010141446.png)
Simple:
(a): If you're substituting for the same term twice, just apply the outermost substitution to the innermost substitution's left part.
(b): If you're substituting for two different terms, you can swap the order given that you also apply the formerly outermost substitution to the formerly innermost substitution's left part.

# Beta-Reduction
An operation that "applies" our set of terms in a way similar to a programming language.
![](Pasted%20image%2020231010145027.png)
Simple:
- bcVar: "Apply" an abstraction like a function, by substituting the value to the right of it in for the bound variable.
- scAbs: If you can beta-reduce one term to another then you can do the same if it is in an abstraction.
- scApp: If you can beta reduce one term to another, then you can do the same to both of the terms in an application.

The step cases essentially allow you to apply the base case to terms that are inside abstractions and applications.
A **redex** is a term that matches the bcVar case - that is, a redex is an application with an abstraction on the left and another term on the right.

The symbol for a beta-reduction is $\xrightarrow{\beta}$. This refers specifically to a single step of reduction.
If we wish to indicate that a term beta-reduces to another in multiple steps, we can use this symbol:
![](Pasted%20image%2020231010150008.png)
Which represents the reflexive transitive closure of $\xrightarrow{\beta}$.
You can refer to the single step beta-reduction by "beta-reduces in one step" versus "beta-reduces in many steps" for the reflexive transitive closure.

![](Pasted%20image%2020231010151649.png)
Simple: If you can beta-reduce the term $u$ to $u'$, and $u$ is a subterm of a term $t$, then there must exist a term $t'$ with $u'$ as a subterm that $t$ can beta-reduce to.
![](Pasted%20image%2020231010152212.png)
Simple: If you have a term $t$ that beta-reduces to $t'$, then you can rename a variable in $t$ to something fresh and beta-reduce that to a new term $u$ which is alpha-equivalent to $t'$ after being renamed in the same way.
![](Pasted%20image%2020231010152837.png)Simple: 
(a): The set of free variables of a beta-reduced term is a subset of the free variables of the non-reduced term.
(b): If you have a term $t$ that beta-reduces to $t'$ then you can substitute a variable for some term and reduce the result of that to a term $u'$, which is alpha-equivalent to the result of performing the same substitution on $t'$.
(c): 

In general, this pattern of behaviour is true with beta-reduction:
![](Pasted%20image%2020231010154542.png)
![](Pasted%20image%2020231010154602.png)
By extension:
![](Pasted%20image%2020231011105131.png)
This essentially says that we can interleave beta-reduction and alpha-equivalence to get a relation with reasonable properties. This relation can be written as $\xrightarrow{\beta\alpha}$, and is defined as such:
![](Pasted%20image%2020231011105249.png)
For example, you can say that
![](Pasted%20image%2020231011111358.png)
This is not a valid beta-reduction because the bound variable in the abstraction has not been replaced with a fresh one, but it is valid for this relation.
This relation is also well-behaved for subterms:
![](Pasted%20image%2020231011111547.png)

We also confirm that the properties of beta-reduction all apply to the many step version:
![](Pasted%20image%2020231011111857.png)
Simple:
(a), (b), (c): If a subterm $t$ can (eventually) beta-reduce to $t'$, then the parent term can (eventually) beta-reduce to itself with $t'$ swapped in for $t$.
(d): The free variables of a beta-reduced term are a subset of the original term's.
(e): like (a), (b) and (c) but for substitution (?)

## Imitating functions using $\beta$-reduction
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

Lets use this to perform a more complicated operation. Namely, let's write a term that takes an iterator and returns the iterator with double the number of iterations. We can do this by creating an iterator like the given one, but where the step case is applied twice per iteration. In other words, given a step case $s$ (which is a "function") we want the new step case $\lambda r.s(sr)$, a "function" that takes a value and applies $s$ to it twice. Now, we build a function that takes an iterator, with base and step case bound variables:
$$\lambda n.(\lambda z.\lambda s.??)$$
And we "replace" the new step case into the old iterator by applying the iterator to them:
$$\lambda n.(\lambda z.\lambda s.z(\lambda r.s(sr)))$$
And we have our desired function.