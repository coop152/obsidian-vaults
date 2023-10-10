Read section 1.2.5 through to the end of section 1.2. Start reading section 1.3.

# Substitution
Like renaming that maintains $\alpha$-equivalence. Only operates on free occurrences of a variable.
![](Pasted%20image%2020231010133809.png)
Simple: 
- bcVar: If the term is a variable and that variable is the one being replaced, then replace it. Otherwise, do nothing.
- scAbs: If the term is an abstraction, take a fresh variable $w$ and replace the bound variable with it. Rename all occurrences of the old bound variable name with the new fresh variable $w$. Recur on the (renamed) right subterm.
- scApp: If the term is an application, recur on both subterms.

This operation has some important properties:
![](Pasted%20image%2020231010134848.png)
![](Pasted%20image%2020231010134859.png)
Simple:
(a): After substitution $[a/x]$, the set of free variables will gain $a$ and lose $x$ (if it had it).
(b): If $x$ wasn't in the free variables of a term then substituting something in for it will produce an alpha-equivalent result.
(c): You can still combine two renamings even if a substitution is being applied to the inner one, as long as the intermediate and final renaming variables are fresh.
(d): 