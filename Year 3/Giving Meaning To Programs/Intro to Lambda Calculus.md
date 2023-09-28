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
