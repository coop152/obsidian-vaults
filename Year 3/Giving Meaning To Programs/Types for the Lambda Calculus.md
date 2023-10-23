The lambda-calculus is a mathematical formalism inspired by how we evaluate functions, with the purpose of being used to reason about functions.
The previously discussed "self-application" paradox term breaks this intuition, and this is because our untyped definition lacked an important aspect of a function; the source and target. A function application only makes sense if the input is suitable for that function, which the untyped lambda calculus did not take into account.
In this chapter we define the **simply typed lambda-calculus**.

# Types and Correctly Typed Terms
We define steps in three stages:
- We define what the types in this system are. This part is straightforward.
- We adjust the notion of a term to allow for typing decorations to appear. As a result of this we need to re-evaluate the foundations laid in the previous part to determine how we must deal with these decorated terms. We call these undecorated terms "preterms", because ultimately we are only interested in terms that can be typed. This part is also quite straightforward.
- We need to lay new foundations that ensure the typing mechanism we've created has the desired effect. This part is much more elaborate than the others.

## Defining Types
To add typing information to the lambda-calculus, we first must settle on a collection of types. Greek letters, typically $\sigma$ (sigma) and $\tau$ (tau), are used to easily distinguish types from terms.
For two types $\sigma$ and $\tau$ we will want the type of a "function" from one to the other, which will be denoted as $$\sigma \rightarrow \tau.$$
What base types should we have to construct functions from? In a real programming language we would have built-ins such as booleans, numbers of various sizes, strings, etc. But for the time being we want to keep the type system as simple as possible. For this reason, we will have just **one basic type** called $\iota$ (iota). We do not specify what the elements of this type are; this may seem strange but it ensures there are no built in assumptions in our system.
We start by defining our function types:
![](Pasted%20image%2020231023134628.png)
Simple:
- bc: iota is a type.
- sc: Given two types, there is a type that is a function from one to the other.

There is another bracketing convention to be followed here: In the case of functions, we carry out the $\rightarrow$ operation **from right to left**. For example:
$$\sigma \rightarrow \sigma \rightarrow \sigma$$
has the same meaning as this fully bracketed term:
$$\sigma \rightarrow (\sigma \rightarrow \sigma)$$
In other words, the original function is a function that takes something of type $\sigma$ and returns a function from $\sigma$ to $\sigma$.

We sometimes call types that are not base types "higher types".
It is important to realise that two types are only *equal* if they are syntactically equal.

## Type decorations
Now we need to assign these types to terms.
There are subtleties to consider while defining this; Take the term $\lambda y.y$. This could be typed as the identity term $\iota \rightarrow \iota$, but you could also imagine applying this term to other terms. Because these other terms are not the base type, this typing is too strict for them. Indeed, if we want the identity term to be able to applied to anything then we instead must give it the type $\sigma \rightarrow \sigma$, for every type $\sigma$.
By introducing types we hope to incorporate the idea that terms should only applied to suitable inputs, and that terms only have a single type. To accomplish this we integrate some type information into the term itself, by adding an annotation to the argument of a lambda-abstraction that indicates the type it should have. For example:
![](Pasted%20image%2020231023140148.png)
This is the identity function that only takes a function from the base type to the base type, and returns the same. The overall type of this whole term is
![](Pasted%20image%2020231023140237.png)
Note that many of these annotated terms cannot be assigned a type in the system we will soon introduce; for this reason, they are called **preterms**. This is the definition of the set of preterms:
![](Pasted%20image%2020231023140748.png)
Simple:
- bcPVar: A variable is a preterm.
- scPAbs: An abstraction on some preterm is also a preterm.
- scPApp: An application on two preterms is a preterm.

Before we concern ourselves with assigning types to these preterms, we revisit some of the definitions on the untyped lambda calculus. Most of the ideas presented without types can be easily adjusted to type-annotated terms; For example, subterms, free and bound variables, and renaming are all essentially the same.
We do have to make a change to alpha-equivalence, for when both terms are abstractions:
![](Pasted%20image%2020231023141154.png)
Simple:
- bcPVar$\alpha$: two identical variables are alpha-equivalent (same as untyped.)
- scPAbs$\alpha$: Two typed abstractions are alpha-equivalent if the type of the bound variable is the same