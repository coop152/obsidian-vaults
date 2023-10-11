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
(d): This is just the second step case
(e): You can apply a renaming to a substitution by simply renaming every occurrence of the given variable to the new one, assuming the new variable occurs nowhere in the substitution.
(f): You can change the variable to be substituted to another one by renaming and replacing the term right of the slash, as long as the new variable does not occur anywhere already.

Another important property is that substituting alpha-equivalent terms into alpha-equivalent terms gives us terms that are again alpha-equivalent. That is,
![](Pasted%20image%2020231010140741.png)

Here are some properties that define how multiple substitutions interact:
![](Pasted%20image%2020231010141437.png)
![](Pasted%20image%2020231010141446.png)
Simple:
(a): If you're substituting for the same term twice, just apply the outermost substitution to the innermost substitution's left part.
(b): If you're substituting for two different terms, you can swap the order given that you also apply the formerly outermost substitution to the formerly innermost substitution's left part.

# Beta-Reduction
An operation that "applies" our set of terms in a way similar to a programming language.
![](Pasted%20image%2020231010145027.png)
Simple:
- bcVar: "Apply" an abstraction like a function, by substituting the value to the right of it in for the bound variable.
- scAbs: If you can beta-reduce one term to another then you can do the same if it is in an abstraction.
- scApp: If you can beta reduce one term to another, then you can do the same to both of the terms in an application.

The step cases essentially allow you to apply the base case to terms that are inside abstractions and applications.
A **redex** is a term that matches the bcVar case - that is, a redex is an application with an abstraction on the left and another term on the right.

The symbol for a beta-reduction is $\xrightarrow{\beta}$. This refers specifically to a single step of reduction.
If we wish to indicate that a term beta-reduces to another in multiple steps, we can use this symbol:
![](Pasted%20image%2020231010150008.png)
Which represents the reflexive transitive closure of $\xrightarrow{\beta}$.
You can refer to the single step beta-reduction by "beta-reduces in one step" versus "beta-reduces in many steps" for the reflexive transitive closure.

![](Pasted%20image%2020231010151649.png)
Simple: If you can beta-reduce the term $u$ to $u'$, and $u$ is a subterm of a term $t$, then there must exist a term $t'$ with $u'$ as a subterm that $t$ can beta-reduce to.
![](Pasted%20image%2020231010152212.png)
Simple: If you have a term $t$ that beta-reduces to $t'$, then you can rename a variable in $t$ to something fresh and beta-reduce that to a new term $u$ which is alpha-equivalent to $t'$ after being renamed in the same way.
![](Pasted%20image%2020231010152837.png)Simple: 
(a): The set of free variables of a beta-reduced term is a subset of the free variables of the non-reduced term.
(b): If you have a term $t$ that beta-reduces to $t'$ then you can substitute a variable for some term and reduce the result of that to a term $u'$, which is alpha-equivalent to the result of performing the same substitution on $t'$.
(c): Same as (b), but to any depth of beta-reduction.

In general, this pattern of behaviour is true with beta-reduction:
![](Pasted%20image%2020231010154542.png)
![](Pasted%20image%2020231010154602.png)
By extension:
![](Pasted%20image%2020231011105131.png)
This essentially says that we can interleave beta-reduction and alpha-equivalence to get a relation with reasonable properties. This relation can be written as $\xrightarrow{\beta\alpha}$, and is defined as such:
![](Pasted%20image%2020231011105249.png)
For example, you can say that
![](Pasted%20image%2020231011111358.png)
This is not a valid beta-reduction because the bound variable in the abstraction has not been replaced with a fresh one, but it is valid for this relation.
This relation is also well-behaved for subterms:
![](Pasted%20image%2020231011111547.png)

We also confirm that the properties of beta-reduction all apply to the many step version:
![](Pasted%20image%2020231011111857.png)
Simple:
(a), (b), (c): If a subterm $t$ can (eventually) beta-reduce to $t'$, then the parent term can (eventually) beta-reduce to itself with $t'$ swapped in for $t$.
(d): The free variables of a beta-reduced term are a subset of the original term's.
(e): like (a), (b) and (c) but for substitution (?)

