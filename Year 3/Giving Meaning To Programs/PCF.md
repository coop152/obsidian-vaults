The simply typed lambda-calculus is an unrealistic programming language; while it does have nice properties such as strong normalization that make it theoretically useful, it is notably lacking features. Most important among these is **recursion**.
Instead, we think of the simply typed lambda-calculus as a template which can be modified to make many different languages. Most importantly, it embodies the fundamental operation of substitution and how it interacts with typing, which is central to any functional language. We will define a new language PCF by adding and removing some aspects of the simply typed lambda-calculus. Before we get to adding recursion, we will consider a few other changes.
# Built in data
The simply typed lambda-calculus lacks any built in datatypes. This is nice theoretically because it makes proofs easier and more general, but it is extremely obstructive when writing programs. In PCF, instead of the base type $\iota$ we have a concrete base type "nat" which models the natural numbers. 
To write numbers we add two constants $\bar{0}$ and $\bar{s}$ which represent 0 and the successor function. We write a natural number $n$ like ![](Pasted%20image%2020231120151940.png)
Because we aim to use recursion, we need to be able to determine if a number is $\bar{0}$ or a successor of something, and if it is then we want to be able to find the number it succeeds. For this we add the built-in operations `ifz` and `pred` to the language.
`ifz` takes three `nat` arguments. If the first argument is $\bar{0}$ then it returns the first argument, and returns the third argument otherwise.
`pred` is the predecessor function (which outputs $\bar{0}$ if given $\bar{0}$).
The typing rules for these are as follows:
![](Pasted%20image%2020231120152309.png)
Note that we need to use brackets with the successor due to the bracketing conventions for application. This is laborious and ugly, so we write ![](Pasted%20image%2020231120152616.png) for a term where successor is applied $n$ times.
`ifz` is bracketed as normal for a three-argument function. However, we often add extra brackets for clarity when using `ifz`. For example, $\text{ifz}\ npq$ can be written $\text{ifz}\ n[p][q]$ or $\text{ifz}\ (n)[p][q]$.
Let's see what beta-reduction is like for these built-ins:
![](Pasted%20image%2020231120152908.png)
We also want to facilitate beta-reduction of sub-terms in these built-ins, so that we can always evaluate a result:
![](Pasted%20image%2020231120153126.png)
These new details actually allow us to simplify many matters. The added concreteness completely removes all of the required complexity that we needed in defining contextual equivalence for the simply typed lambda-calculus. Because our new language has a concrete base type with actual elements, there is no need for type environments and the careful machinery that surrounds it.
## Restricting beta-reduction
The second change is to restrict beta-reduction. Our previous definitions were as general as possible, but in a real programming language we expect program execution to be deterministic - that is, there is a defined **evaluation strategy** which is taken in the same way every time. In PCF we choose the "leftmost, outermost" strategy where a subterm can only reduce if:
1. It is by one of the rules given above for succ/pred/ifz
2. The subterm is on the left hand side of an application

This means that arguments of applications are banned from beta-reducing, as well as the bodies of abstractions. In other words, we remove these parts in red from the definition of beta reduction:
![](Pasted%20image%2020231120153915.png)
Again, this simplifies a lot. Confluence is now trivial because there is never any choice in how to reduce a term, and the irreducible terms are easy to classify. It does raise the question of whether there are some odd terms that can only be shown equal to ordinary elements using the removed rules - save this thought for later.
## (Re-)introducing recursion
The most important change is to introduce recursion, which allows us to write complicated programs that we cannot in the simply typed lambda-calculus. Specifically, terms that may run forever if there is no solution. We saw a form of recursion in the untyped lambda-calculus, but it relied on self referential terms which were untypable and conceptually hard to reason about. We seek a way to define recursion that has a sensible type.
Let's consider a regular mathematical recursive function:
![](Pasted%20image%2020231120154944.png)
This function from naturals to naturals, as simple as it is, cannot be defined with the simply typed lambda-calculus.
Much of the body of the function can already be translated into a PCF term using the features described previously, but the recursion itself seems to rely on our ability to name a function and then refer to it with this definition. Adding named declarations would significantly complicate our language; we want to define the function above without relying on creating some "global" name for $f$. While we investigate this, let's use the temporary notation
![](Pasted%20image%2020231120155203.png)
to represent $f$. 
We want to express this function in the expected form $\lambda n:nat.$ so that we can apply it to a single argument via beta-reduction. Let's look ahead and see what we want a beta reduction to produce:
![](Pasted%20image%2020231120160047.png)
But this contains a free $f$. Let's try again, but replace $f$ with the function definition instead of leaving it free:
![](Pasted%20image%2020231120160353.png)
Let's use this expression to think about how we want the recursive definition to look after it is rewritten into the form of an abstraction:
![](Pasted%20image%2020231120160644.png)
![](Pasted%20image%2020231120160717.png)
When written like this, it looks like the body of a recursive expression is best thought of as a *higher order function*, which takes a function as an input and uses it to interpret the recursive call. This at least lets us come up with a sensible notation for recursion. We write the recursive function above as:
![](Pasted%20image%2020231120160915.png)
Then we create a beta-reduction rule to express the fact that the function to use in the recursive call is *the function itself*. In our running example we want the rule to behave such that
![](Pasted%20image%2020231120161021.png)
So the conclusion of this discussion is that a recursive term should be an abstraction, where the abstracted variable is the term we will use to interpret the recursive call. The special part is that the function supplies *itself* as the term to be used in the recursive call.
Let's deduce the types involved. The body, being an abstraction, must have a function type, and the recursive function itself must be a suitable input. That is, to use recursion to specify an element of type $\tau$, we need to supply a term $t$ of type $\tau \rightarrow \tau$, and then the desired term of type $\tau$ is $\text{rec}\ t$. Then the beta-reduction rule for $\text{rec}\ t$ can safely state that $\text{rec}\ t \xrightarrow{\beta} t\text{rec}\ t$.