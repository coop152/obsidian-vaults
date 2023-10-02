# Some recap I needed when reading the notes:
- **Injective Function/Injection** - A function is injective if every output is distinct to some input. That is, for every possible value in the range there is **at most one** value in the domain that is mapped to it (or maybe none).
![](Pasted%20image%2020230928112004.png)
- **Surjective Function/Surjection** - A function is surjective if it can output every possible value in the range. That is, for every value in the range there is **at least one** value in the domain that is mapped to it (or maybe more).
![](Pasted%20image%2020230928112313.png)
- **Bijective Function/Bijection** - A function is bijective if it is both injective and surjective. That is, for every possible value in the range there is **exactly one** value that maps to it, no more and no less. ($\exists{!x}$ means "there exists exactly one instance of $x$")
![](Pasted%20image%2020230928113240.png)
![](Pasted%20image%2020230928113336.png)

# Variables
We assume that we have an infinite set of variables $\text{Vars}$, so that we can always produce a new variable which hasn't been used yet. To show this precisely, we use the fact that for an infinite set we can find an injective function from the set to itself that is not surjective. Create a function that takes a variable and returns a new, as-of-yet unused one:
![](Pasted%20image%2020230928114024.png)
The actual implementation of this function is unimportant.

# Definitions
## Lambda-terms
![](Pasted%20image%2020230928114454.png)
Simple:
- Case bcVar: Every variable is a $\lambda$-term.
- Case scAbs: You can add $\lambda{x}.$ to the outside of any $\lambda$-term to make a new $\lambda$-term. This is called **$\lambda$-abstraction**.
- Case scApp: Two $\lambda$-terms beside each other (in brackets) make a new $\lambda$-term. This is called **application**.
## Subterms
![](Pasted%20image%2020230928115012.png)
Simple:
- Case bcVar: A variable is a subterm of itself.
- Case scAbs: The subterms of a $\lambda$-extraction are itself, the rightmost $\lambda$-term, and the subterms of the rightmost $\lambda$-term.
- Case scApp: The subterms of an application are itself, both lambda-terms in it and their subterms.

Also, a **proper subterm** is a subterm that is not equal (in the same fashion as a proper subset).
## Bound Variables
![](Pasted%20image%2020230928120446.png)
Simple: The set of bound variables is the set of variables that are beside the $\lambda$ in a $\lambda$-abstraction.
## Free Variables
![](Pasted%20image%2020230928131436.png)
Simple: The set of free variables is the set of variables that are not bound. Note that a single variable can be both bound and free, for example: $(\lambda{x}.xy)x$
In this example $x$ is bound because it is given in the $\lambda$-abstraction, but it is also free because there is an occurrence outside of the $\lambda$-abstraction.
## Total Variables
![](Pasted%20image%2020230928131444.png)
Simple: The set of variables is the set of variables in the term. (lol)
## Variable Renaming
![](Pasted%20image%2020230928131455.png)
Simple:
- **bcVarren**: If the variable equals $x$, replace it with $z$
- **scAbsren**: Recur on both subterms
- **scAbsren**: Recur on both subterms

In other words, do find and replace.
![](Pasted%20image%2020231002105153.png)
Simple:
(a): Renaming a variable to itself has no effect on the term.
(b): Renaming from $x$ to $y$ and then from $y$ to $z$ is the same as renaming from $x$ to $z$ GIVEN THAT $y$ is not already present in the term.
(c): Renaming from $x$ to $y$ and then back to $x$ has no effect on the term GIVEN THAT $y$ is not already present in the term.
(d): The order in which you replace two variables does not matter GIVEN THAT you do not rename the same variable twice or rename the result of the other renaming.
(e): Renaming has no effect on the term if the variable being renamed is not present in the term.

Some more about renaming and free variables:
![](Pasted%20image%2020231002110242.png)![](Pasted%20image%2020231002110250.png)
Simple: (**Note that $y$ is a fresh variable in all of these. This is very important**)
(a): If you replace a variable with a fresh one in a term then the set of variables no longer contains the old variable, and if the old variable was indeed present in the term it now contains the fresh variable. Note that the old variable is *never present* after this operation.
(b): If a free variable is inside a $\lambda$-abstraction and you rename that variable to one that is bound in that scope, then it is no longer in the set of free variables for that term.
(c): If you rename a variable that isn't in the term then the set of free variables does not change. If you rename a variable that *is* in a term into a fresh variable, then the set of free variables loses the old variable and gains the fresh one.

## Alpha-equivalence
![](Pasted%20image%2020231002112755.png)
![](Pasted%20image%2020231002112806.png)
![](Pasted%20image%2020231002112816.png)
Simplified: Two terms are $\alpha$-equivalent if they differ only in the naming of their bound variables. We do not need to remember the proposition, but for understanding's sake:
Two terms are $\sim_\exists$ if they are identical in structure, and for each pair of $\lambda$-abstractions **there exists some** fresh variable $w$ you can swap with the associated bound variable and end up with identical terms.
Two terms are $\sim_\forall$ if they are identical in structure, and for each pair of $\lambda$-abstractions you can swap **all possible** fresh variables $w$ with the associated bound variable and end up with identical terms.
We can use either of these two relations, due to the unusual requirements of $\alpha$-equivalence.
This is a more informal (but still mathsy) description of $\alpha$-equivalence.
![](Pasted%20image%2020231002114508.png)
