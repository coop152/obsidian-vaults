The simply typed lambda-calculus is an unrealistic programming language; while it does have nice properties such as strong normalization that make it theoretically useful, it is notably lacking features. Most important among these is **recursion**.
Instead, we think of the simply typed lambda-calculus as a template which can be modified to make many different languages. Most importantly, it embodies the fundamental operation of substitution and how it interacts with typing, which is central to any functional language. We will define a new language PCF by adding and removing some aspects of the simply typed lambda-calculus. Before we get to adding recursion, we will consider a few other changes.
# The changes we want
## Built in data
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
The typing rule for `rec` is:
![](Pasted%20image%2020231120163431.png)
and it's reduction rule is:
![](Pasted%20image%2020231120163446.png)
We will bracket `rec` as if it was a term being applied to another term. `rec` will never be an allowed name for a term, and neither will any of the other built-in names, for obvious reasons.
Now we can assemble a formal definition of PCF.
# Defining the Language PCF
## Terms, types and beta-reduction
We start along familiar lines by defining a collection of preterms that might not typecheck, defining typing rules that describe the set of terms, and then giving a definition of beta-reduction that explains how these terms compute.
![](Pasted%20image%2020231121123644.png)
Note that the notion of alpha-equivalence can be extended to PCF terms, but you don't need to know the details.
We define a "PCF term" using the following type rules:
![](Pasted%20image%2020231121124155.png)
We may also extend the notion of capture-avoiding substitution to PCF recursively, but we don't bother. We just assume it works as expected.
Now we define the rules for beta-reduction:
![](Pasted%20image%2020231121130005.png)
The reflexive transitive closure of $\xrightarrow{\beta}$ has the same symbol as usual. Remember that even though we are using the same name, beta-reduction actually behaves quite differently from previous languages. For example, this term:
$$\lambda x:\sigma .((\lambda y:\delta.y)x)$$
Can be beta-reduced in the simply typed lambda-calculus but not in PCF, because beta-reduction for PCF does not allow beta reducing subterms of an abstraction.
While a beta-reduction being valid in the simply typed lambda-calculus does not mean it is valid in PCF, the opposite *is* true; that is, if a term $t$ beta-reduces to $t'$ in PCF then it must also beta-reduce in the simply-typed lambda-calculus.
![](Pasted%20image%2020231121140949.png)
## Irreducible terms
Classifying the set of irreducible terms is significantly easier in PCF than with the other versions of the lambda-calculus.
There are three outcomes for any reduction of some term of a type $\tau$ **with an empty type environment**:
- We reach a valid irreducible term. For example, if the term is type `nat` then we reach some valid natural number (0 with or without some successors). For a function type, we reach an abstraction that we can apply to other terms in the way we expect from the type.
- We never finish computing the result. This possibility is simply an unavoidable consequence of having a useful programming language.
- We get "stuck" at some term with the correct type, but it isn't in the correct form. In particular, consider the case where we have a term of function type but we don't achieve an abstraction.

Thankfully, this last case is only theoretical and cannot occur in PCF. If we get a result, we are guaranteed that it is in the expected useful form.
But what about when the type environment is non-empty? In that case we may construct terms with free variables, and such variables (being irreducible) can block further reductions. For example, if we apply a variable (say of type $\rho \rightarrow \sigma$) to another term, then the resulting term is irreducible because we can't apply any of the beta-reduction rules. Therefore, we only characterize irreducible terms which can be typed in the empty type environment.
![](Pasted%20image%2020231121143535.png)
This helps make sense of the beta-reduction rules a little better. If we have an expression that contains `pred`, for example, we want to eventually apply the base case for `pred` which will explain the computational meaning of `pred`. This proposition shows that (as long as the computation does not run forever) we can always get a valid form for the inner term that `pred` can work with. This same logic applies to all of the base cases of beta-reduction.
## Properties of reduction
We revisit some of the properties that were shown for the simply typed lambda-calculus.
Showing confluence is trivial for PCF because it is completely deterministic:
![](Pasted%20image%2020231121145804.png)
Beta-reduction still doesn't affect the type of a term:
![](Pasted%20image%2020231121145914.png)
We do not formally define alpha-equivalence for PCF terms, but it is worth remarking that all of the properties you would expect do hold. For example, if $t \sim_\alpha t'$ and $t$ beta-reduces to some irreducible term $u$, then there is an irreducible term $u'$ such that $t'$ reduces to $u'$ and $u \sim_\alpha u'$.
We just assume these properties are true if they are needed; the methods used to prove them for PCF are extremely similar to those for the simply typed lambda-calculus.
## Non-termination
By introducing recursion, we have also introduced non-terminating programs.
![](Pasted%20image%2020231121150315.png)
![](Pasted%20image%2020231121150323.png)
This tells us something about what our denotational semantics can be like. For instance, we might have hoped that we could have $[\![\text{nat}]\!] = \mathbb{N}$. But if we instantiate the term from the last example where $\sigma = \text{nat}$ we would have a term $\text{rec} \lambda x:\text{nat}.x$ which is clearly of type `nat` but is also clearly different from outputting any particular natural number.
We need a denotation of every type to contain a special value $\bot$ which represents "never observing an output". This is conceptually different from observing some kind of error value; you would eventually actually observe an error value, but you will never actually observe $\bot$, because the term will be getting evaluated forever.
You might think of $\bot$ as meaning "no information". This is because PCF programs do **not** necessarily run forever if one of their inputs runs forever.
![](Pasted%20image%2020231121151015.png)
![](Pasted%20image%2020231121151347.png)
The situation for function types is more complicated. We know there must be a $\bot$ type for every type, but recursively defined functions can terminate on some inputs and run forever on others.
![](Pasted%20image%2020231121151816.png)
![](Pasted%20image%2020231121151822.png)
This function is more informative than the function which doesn't halt for any input, but less informative than a function that always halts (say, the identity function on `nat`). On the other hand, it is also observably different from the function which always returns $\bar{s}\bar{0}$ because they return clearly different values when evaluated at $\bar{0}$. Clearly the information ordering of complicated types is not easily found by intuition. To define the denotational semantics of PCF we need a mathematical theory which describes what sort of ordered structures these are, and comes with a 'function space' construction which builds the correct ordered structure for us at higher type. This theory is described later on.
![](Pasted%20image%2020231121153001.png)
(just read the fucking notes, god)
## Section 3.3
![](Pasted%20image%2020231121153213.png)
