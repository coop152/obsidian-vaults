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
- scPAbs$\alpha$: Two typed abstractions are alpha-equivalent if the type of the bound variable is the same and the subterms are alpha-equivalent.
- scPApp$\alpha$: Two applications are alpha-equivalent if the left subterms are alpha equivalent and the right subterms are alpha-equivalent (same as untyped.)

We can easily adjust capture-avoiding substitution and beta-reduction, noting that the base case of beta-reduction becomes:
![](Pasted%20image%2020231023142017.png)
All of the results for the untyped lambda calculus apply to to this new system.

## Type environments
Consider this example:
![](Pasted%20image%2020231023142332.png)
This example shows that we build our typed expressions by assigning types to the free variables of subterms. To do this we need to remember the assumptions we made about the type of those free variables based on the lambda-abstractions they are in. We call this set of assumptions a *type environment*. A formal definition will come later, but first we consider how these assumptions work with shadowing.
![](Pasted%20image%2020231023142603.png)
![](Pasted%20image%2020231023142612.png)
The steps of this example show that we can deal with assumptions properly in the presence of shadowing using a stack-like structure, where the most recent assumption about a variable name is used and then forgotten when it goes out of the scope that defined it.
We do need to consider shadowing, or else our type system would allow typeable terms that are alpha-equivalent to untypeable terms. There are some bizarre workarounds that one can do to avoid shadowing, but they all either defeat the point of defining a typed lambda calculus or make the lambda calculus behave not at all like a real programming language.
We define our type environment which can accommodate shadowing as such:
![](Pasted%20image%2020231023143238.png)
Simple:
- bcTE: There is an empty type environment where no assumptions about the type of free variables are made.
- scTE: Given some type environment $\Gamma$ (Gamma), we can make another type environment by adding a mapping from a variable to a type at the end.

Vitally, equality for type environments is **NOT** given by syntactic equality.
The definition of a type environment is quite simple, but determining if two type environments should be considered equal is much more complicated. We should be allowed to swap the order of some of our assumptions, but we cannot do it freely; it is clear that when we have several assumptions for the same variable due to shadowing, the order of the assumptions is important. We want our definition of equality to follow these principles:
- Typing assumptions for distinct variables may be swapped freely.
- Type assumptions for the same variable may not be swapped without changing the type environment and therefore breaking equality.

To define equality on type environments we define an equivalence relation $\approx$. (recap: an equivalence relation is a relation that is reflexive, transitive, and symmetric.) Let's begin by giving an intermediate relation:
For distinct variables $x, y$, and type environments $\Gamma, \Delta$:
![](Pasted%20image%2020231023144744.png)
Note that this relation is symmetric.
We now define the equality relation in terms of this relation:
![](Pasted%20image%2020231023145059.png)
Because the intermediate relation is symmetric, and we are taking the reflexive transitive closure, this definition of equality is a valid equivalence relation.
Consider this example:
![](Pasted%20image%2020231023145210.png)
Note that there is another obvious operation for type environments: If $\Gamma$ and $\Delta$ are type environments then $\Gamma, \Delta$ is a type environment. This operation is well behaved with respect to equality:
![](Pasted%20image%2020231023145426.png)
We also find the following useful when manipulating type environments:
![](Pasted%20image%2020231023145519.png)
![](Pasted%20image%2020231023145530.png)
Simple:
- (a): The concatenation of two type environments has some assumption for $x$ as the most recent, if and only if:
	- The second type environment in the concatenation has that assumption as the most recent for $x$, OR
	- The first type environment has that assumption as the most recent for $x$ and there is no assumption about $x$ in the second type environment that shadows it.
- (b): If some type environment $\Delta$ is equal to some type environment $\Gamma$, then the most recent assumption made for variable $x$ in $\Gamma$ must also be true for $\Delta$.

(Not 100% on those but I think that's the point of the propositions, anyway.)

## Typing rules
We can now give a formal definition of the type of a term. The syntax for these definitions resembles the natural deduction system for classical logic from COMP11120.
A statement of the form:
![](Pasted%20image%2020231023151233.png)
This means that if we make the assumptions in the type environment $\Gamma$, then the term $t$ has the type $\iota$.
We define the rules of this typing relation as an inference system of formal logic.
![](Pasted%20image%2020231023151441.png)
Recap on inference:
Consider the rule for applications:
![](Pasted%20image%2020231023151507.png)
The judgements above the line are the **premises** and the statement below the line is the **conclusion**. This rule dictates that, under the premise that $t$ is a function from $\sigma$ to $\tau$ and $u$ is of type $\sigma$, we can conclude that the term $tu$ is of type $\tau$, the output of function $t$. Note that the type environments must be the same in both of the premises to make this conclusion.
Consider the rule for abstractions:
![](Pasted%20image%2020231023152018.png)
If we are assuming that $x$ is of type $\sigma$ in the type environment, and we know that $t$ is of type $\tau$, then we can conclude that the term $\lambda x:\sigma.t$ is of type $\sigma \rightarrow \tau$. We also remove $x$ from the type environment.
When using these rules to find the type of some term, we start at the **bottom** with the fully formed term. We then follow the rules backwards/up the page, breaking the term apart until you have only axioms left.
Here is an example of how to use these rules to type-check a term:
![](Pasted%20image%2020231023152437.png)
The structure we build when we perform these type-checks on the lambda-calculus is called a **formal derivation**. They are also called *proof trees* more generally (for example, when performing the analogous operation in classical logic.) Note that these formal derivations are themselves recursively defined - we can express the rules in word form with a similar format to previous recursive definitions:
![](Pasted%20image%2020231024104144.png)
We can use these to prove some (quite strong) statements about derivations by induction.
At this point, we might wonder what we can already say about terms that can be typed. So far, the only terms we know of in the base type are either variables or the result of applying some function to a base type variable. Are there more terms in the base type? (I think that's what the text is asking, anyway)
## Properties of Derivations
Now we build up some properties of derivations, so that we can show some important properties of our type system (for example, that it is well-behaved w.r.t. alpha-equivalence and beta-reduction.) We also demonstrate that some terms which cause problems in the form of infinite reductions cannot be typed in our system.

### Type environments in derivations
We might wonder if we can add additional assumptions to type environments without affecting our derivations. Because we've allowed shadowing, the situation here is more subtle than the straight-forward weakening rule from the analogous classical logic. We show some relevant results in order to answer this question.

To cope with shadowing we have to worry about type environments that may contain more than one typing for a given variable. There is a more general condition that covers this case:
![](Pasted%20image%2020231024105915.png)
We have to cover shadowing first because it is requires for most of the other statements we want to show.
![](Pasted%20image%2020231024110809.png)
Simple:
(a): Introducing a new typing at the end of two consistent environments maintains their consistency.
(b): If two environments are consistent, then you can derive a typing for some variable if and only if you can derive it in the other.
These propositions are fairly clear from just thinking about consistency. The following propositions are less obvious, and demonstrate a useful property: We indeed can add extra assumptions to a type environment in a manner similar to weakening in classical logic. We have to be careful about how we do it, though!
![](Pasted%20image%2020231024112229.png)
Simple:
(a): Supposedly a low level detail that doesn't really help understanding-wise.
(b): Can't really simplify this one.
(c): If you can derive a typing for a term from a certain type environment, then concatenating this environment onto the end of any other will also yield this typing.
(d): If some variable $y$ isn't in the free variables of some term $t$, then you can assume any type for $y$ in the environment without affecting the typing of $t$.
(e): Similarly, if $y$ isn't a free variable in $t$ then you can remove an assumption about $y$'s type from the environment without affecting the typing of $t$.

This final statement is very helpful; it tells us that we only need make assumptions about the free variables of a term in order to derive a typing for it. So, we can perform a "weakening" operation by adding unnecessary assumptions about the types of bound or unused variables, but we cannot add assumptions about the types of variables that are free in the type-checked term because it could change the outcome. Inversely, we can perform a new "strengthening" operation by *removing* assumptions about the types of bound or unused variables, as long as we do not remove assumptions about variables that are free in the type-checked term, because this could change the outcome.

### Terms as a typing blueprint
When performing a derivation, we end up carrying potentially large amounts of type data that can make proofs regarding types very hard. For example, consider this fragment of a derivation:
![](Pasted%20image%2020231024114004.png)
Reasoning about this derivation using one of the step cases for derivation from earlier seems simple enough. However, notice that a type $\sigma$ is mentioned in one of the sub-proofs, but is **not** mentioned in the conclusion; this type could be huge! This could hugely complicate matters, but fortunately our derivation system is "self-documenting". That is, the information which would appear to have vanished is actually encoded in $t$ ($\sigma$ being it's type), and therefore in $tu$. So, when faced with some derivation $\Gamma \vdash t: \tau$ that we need to base a proof around, we should concern ourselves with the term $t$ and not it's type.
We can begin by noting that if we have a derivation showing some term has a particular type, then we can use the term to say something about the derivation:
![](Pasted%20image%2020231024114907.png)![](Pasted%20image%2020231024114917.png)
Simple:
(a): If a derivation shows that some variable $x$ has type $\tau$, then we know it was an axiom rule derivation and that the type environment contained $x:\tau$ as the most recent typing for that variable.
(b): If a derivation shows that some 