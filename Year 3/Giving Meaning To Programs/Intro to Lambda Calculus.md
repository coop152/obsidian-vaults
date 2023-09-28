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
![](Pasted%20image%2020230928114454.png)
Simple:
- Case bcVar: Every variable is a $\lambda$-term.
- Case scAbs: You can add $\lambda{x}.$ to the outside of any $\lambda$-term to make a new $\lambda$-term. This is called **$\lambda$-abstraction**.
- Case scApp: Two $\lambda$-terms beside each other (in brackets) make a new $\lambda$-term. This is called **application**.

![](Pasted%20image%2020230928115012.png)
Simple:
- Case bcVar: A variable is a subterm of itself.
- Case scAbs: The subterms of a $\lambda$-extraction are itself, the rightmost $\lambda$-term, and the subterms of the rightmost $\lambda$-term.
- Case scApp: The subterms of an application are itself, both lambda-terms in it and their subterms.

Also, a **proper subterm** is a subterm that is not equal (in the same fashion as a proper subset).

![](Pasted%20image%2020230928120446.png)
![](Pasted%20image%2020230928131436.png)
![](Pasted%20image%2020230928131444.png)
![](Pasted%20image%2020230928131455.png)
