Read section 1.2.5 through to the end of section 1.2. Start reading section 1.3.

# Substitution
Like renaming that maintains $\alpha$-equivalence. Only operates on free occurrences of a variable.
![](Pasted%20image%2020231010133809.png)
Simple: 
- bcVar: If the term is a variable and that variable is the one being replaced, then replace it. Otherwise, do nothing.
- scAbs: If the term is an abstraction, take a fresh variable $w$ and replace the left subterm with it. Rename all occurrences of the old bound variable name with the new fresh variable $w$. Recur on the right subterm.
- scApp: If the term is an application, recur on both subterms.