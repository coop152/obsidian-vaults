# Logical Predicates
We want to prove that our type system works well with beta-reduction, in order to show that those terms which were problematic in the untyped lambda calculus are no longer a problem in the typed lambda calculus. By showing that this language has stronger properties, we hope to make it easier to reason about.
To this end, we define a theoretical device known as a **logical predicate**. Informally, a logical predicate is a way of defining some property across all types in a uniform way. These properties which vary uniformly over types are precisely the ones which the type system gives us a reasoning principle for.
## Uniformity across types
In this case we model the properties we are interested in by defining the set of things which have the property. For now, think of a logical predicate as a set of terms, though we will need to add to this idea to make it really work.
Because we've decided to show how a property varies using the type of the terms involved, it makes sense to define not just a single set of "terms which satisfy the property", but instead to make many sets for each type $\tau$, containing only terms of that type. This makes it easier to state what we mean by "varying uniformly" with the type.
The general idea is that whenever our language gives a way for two terms to interact, there should be a well behaved property that the interaction preserves. In the simply typed lambda calculus the only interaction we have is for one term to be applied to the other. Therefore we need a way of saying that, if two terms hold some property and one is applied to the other, then the resulting term also holds this property. In fact we want an even more uniform notion: we concern ourselves with properties that hold for some function type *if and only if* every application involving that term will preserve the property. 
Above we described the idea of sets of terms as a way of modelling properties, but this is not actually enough and we need to add a bit of extra information to the terms involved. This is because of free variables, which prevent us from finding the type of a term without an attached type environment. We could try restricting ourselves to terms that contain no free variables, but then we would have no examples at the base type $\iota$. Even for other types we would be making things impossibly difficult because the subterms of our terms with no free variables may themselves contain free variables. Therefore, we attach a type environment to each term in order to keep the reasoning simple.
A logical predicate $\mathit{R}$, at a given type $\tau$, is a "set" (strictly, a selection) of pairs of form
$$(\Gamma,t)$$
such that:
- $\Gamma$ is a type environment, and
- $t$ is a term of type $\tau$ such that $\Gamma \vdash t:\tau$ is derivable.

We denote the logical predicate $\mathit{R}$ at some type $\tau$ as $\mathit{R}_\tau$.
This notation poses a minor problem in that commas are now being used in pairs but also to combine type environments. We solve this by adopting the convention of dropping the comma between type environments in this context.
We now forge a connection between these selections at different types by demanding that for all types $\sigma$ and $\tau$, the selection of pairs at type $\sigma \rightarrow \tau$ is completely determined by the selections at type $\sigma$ and $\tau$. We do this by requiring that $$\mathit{R}_{\sigma \rightarrow \tau}$$
consists of all pairs $(\Gamma, f)$ with  $\Gamma \vdash \sigma \rightarrow \tau$ such that when $f$ is applied to a term from $\mathit{R}_\sigma$, it produces a term in $\mathit{R}_\tau$. We just need to figure out how to include the type environments in an appropriate manner.
We want to build a condition which says that
$$(\Gamma, f) \in \mathit{R}_{\sigma \rightarrow \tau}$$
if and only if for all $(\Delta,u) \in \mathit{R}_\sigma$ you have
$$(?, fu) \in \mathit{R}_\tau.$$
We have to ensure that the type environment $?$ is sufficient to type the application of $f$ to $u$. That is, we must be able to derive
$$? \vdash f: \sigma \rightarrow \tau$$
as well as
$$? \vdash u: \sigma.$$
We know that $\Gamma$ contains sufficient information to derive the type of $f$, so $?$ must contain the information given in $\Gamma$. We also need the information in $\Delta$ to derive the type of $\sigma$ for $u$, but we can't just add $\Delta$ after $\Gamma$ because while we will definitely be able to derive the type of $u$ correctly, the correct typing of $f$ might be "overwritten" by $\Delta$.
For this reason, we cannot quantify over all type environments $\Delta$ - we have to add a technical condition:
## Compatibility of type environments
![](Pasted%20image%2020231106150100.png)
In other words, the type environment $\Delta$ is **compatible** with $\Gamma$ if adding $\Delta$ onto the end of $\Gamma$ does not "overwrite" any of the assigned typings of $\Gamma$.
Note in particular that if $\Gamma$ is a type environment and the variable $z$ does not occur in it, then the type environment $z:\sigma$ is compatible with $\Gamma$ for every type $\sigma$.
We check that this definition behaves as intended. That is, if we can derive a type for some term $t$ in $\Gamma$ then we can derive the same type from $\Gamma\Delta$ where $\Delta$ is compatible with $\Gamma$.
![](Pasted%20image%2020231106151004.png)
## Defining the logical predicate
With this, we are in a position to give the formal definition of a logical predicate.
![](Pasted%20image%2020231106151129.png)
![](Pasted%20image%2020231106151143.png)
(I think this definition is wrong. in the step case it ought to say $\Gamma \vdash t:\sigma \rightarrow \tau$)
So, a logical predicate at the base type is defined as a suitable selection of pairs with terms of the base type. Using the recursive structure given, we can determine what happens at higher types.
Working with this recursive definition is not easy, and if you want to work with a logical predicate you are generally trying to characterise elements at higher types. Later on we provide a particular logical predicate such that $(\Gamma, t)$ being an element of some type implies that term $t$ has a property we care about.
The definition of a logical predicate means that knowing what happens at base type should allow us to determine if particular pairs are also elements at other types (for example $\iota \rightarrow \iota$). Here is an example:

i give up on this topic
# Strong Normalization
One of the concerns we had with the untyped lambda-calculus was that a computation might continue forever by becoming circular. This is one of the ways that we are prevented from reasoning about terms as if they were functions: a function always has a well defined output for every input. We know from the Halting problem that a sufficiently powerful programming language must contain programs that might continue forever, but we show in this section that our type system is so strong that it has eliminated non-termination completely.
You could consider this a both a good and a bad thing. Bad because while we've made reasoning about programs easier, we've made another language that isn't useful in practise; Good because we have a solid foundation with good properties on top of which we can carefully add features, instead of randomly finding them as a consequence of mind-bending hacks. Either way, it is clearly valuable to prove that the simply typed lambda-calculus really does contain only terminating programs.
We prove this claim by constructing a suitable logical predicate. Along the way we will introduce another powerful technique, which we then extend to **logical relations** to allow us to prove further results.
## Terminating reductions
First we clarify the idea of a program that always halts. We call terms that cannot keep reducing forever **strongly normalizing**. In other words, no matter how we reduce such a term, a sequence of reduction steps starting from that term finishes after a finite number of reductions.
![](Pasted%20image%2020231107122302.png)
Note that for some terms $t$ and $t'$ there may be various lengths of reduction sequences. For example, the term
$$(\lambda x.xx)\lambda x.xx$$
beta-reduces to itself, so we can construct a reduction sequence of any length where all terms are equal to the given term. This includes a reduction sequence of length 0, because eventual beta reduction is reflexive.
Using this idea, we define the notion that we are interested in.
![](Pasted%20image%2020231107122700.png)
In other words, if we drew a graph indicating all of the possible reductions for a strongly normalizing term then all the branches would end after a finite number of steps. This is a comforting property from the point of view of a compiler writer - it means that to calculate the "final answer" of a strongly normalizing term we don't need to be particularly careful about which branch of computation we go down, because every branch will eventually resolve. In this section we will show that the simply typed lambda-calculus demonstrates this property for all terms.
Similarly to previous proofs, we first check that this notion works well with alpha equivalence:
![](Pasted%20image%2020231107123146.png)
What examples of strongly normalizing terms can we give at this stage? At the very least, every variable of base type is strongly normalizing because there are no reductions to take. We can say no more with our current progress defining strong normalization. We can, at least, show some useful properties of strong normalization to make it easier to work with.
![](Pasted%20image%2020231107125435.png)
## A logical predicate for strong normalization
Our strategy to prove that all typeable terms must be strongly normalizing is to build a logical predicate $S$ which obviously implies strong normalization.
We start by defining the logical predicate for the base type. We set
![](Pasted%20image%2020231107130055.png)
What about higher types? Assume that $\Gamma \vdash x:\sigma \rightarrow \tau$ is derivable. We need to check the given condition, so let's assume we have $(\Delta, u) \in S_\sigma$ such that $\Delta$ is compatible with $\Gamma$. Now we need to confirm that $(\Gamma\Delta, xu)$ is in $S_\tau$.

(just watch the videos this is baffling)

# Irreducible terms and computation of values
Since every typeable term in the simply typed lambda-calculus is strongly normalising, we know that every term evaluates to an irreducible term that is unique up to alpha-equivalence. We can consider this term the result of our computation, or as the value that was encoded in the original term.
When we try to understand the behaviour of terms in the coming sections, we would like to test them with terms that produce a result/term of type $\iota$, and we would like to only test them with data that is 'concrete'. We begin by defining the types that we expect to employ.
![](Pasted%20image%2020231109105003.png)
In other words, a type is first order if it is composed entirely of $\iota$. That includes types that are themselves composed entirely of $\iota$ (i.e. are first order types), even if they are included by another name (e.g. $\tau$).
![](Pasted%20image%2020231109105026.png)
We give a name to these irreducible terms that are either variables or applications.
![](Pasted%20image%2020231109123317.png)
In other words:
A term is $\Gamma$-concrete for a first order type environment $\Gamma$ if it is a free variable given a type by $\Gamma$, or if it is an application of many such variables.
Conversely, If a first order type environment assigns some term a first order type $\tau$ then either $t$ reduces to a $\Gamma$-concrete term or it reduces to an abstraction that returns an irreducible term.

$\Gamma$-concrete terms are alpha-equivalent only to themselves, so in the first case of Corollary 2.22 our term reduces to a unique irreducible term. If our term is of base type, then the irreducible term it reduces to cannot be an abstraction and it must be $\Gamma$-concrete. Therefore in that situation there is a unique value we can think of as a result of the calculation represented by the term.
