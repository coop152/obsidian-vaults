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