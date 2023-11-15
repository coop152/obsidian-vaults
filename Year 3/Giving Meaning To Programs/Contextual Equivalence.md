By showing strong normalisation, we seem to have good evidence that terms in the simply typed lambda-calculus are well behaved. Now we turn to proving that they "behave like functions", which will include a formal definition of what we mean by "behave like". However we do define the "behaviour" of a lambda-term, clearly it should be independent of the precise "implementation details" of how exactly the term is written.
In previous sections we gave some examples of terms that are equivalent in behaviour but are *not* alpha-beta-equivalent. For example:
![](Pasted%20image%2020231113111519.png)
Now we will specify exactly what we mean by "equivalent terms", and which behaviour we are concerned with. Intuitively, two programs should be considered equivalent if we could replace one with the other **in a larger program** without changing the result. In real programming terms, they would pass all the same tests, their documentation would be the same, and so on.
To formalise this we first need to decide what we consider a 'result'. Since function typed terms can be equivalent in behaviour while having non-trivial internal differences, we don't want to consider those to be results of computation. Therefore, we consider terms of type $\iota$ to be acceptable 'results': to see if they are equivalent it is sufficient to compared them in some environment using alpha-beta-equivalence. This idea is the motivation behind Definition 30, but before we can assert that we need to do some preparation.
We need to define the "larger programs" into which a term can be put. A **context** is like a lambda-term but with exactly one 'hole' in it. Substituting into this 'hole' is simpler than substituting for a variable because we do not allow abstraction over the 'hole'.
![](Pasted%20image%2020231113112413.png)
![](Pasted%20image%2020231113112455.png)
Note that substitution into a context is **not** capture avoiding. It behaves like a simple copy-and-paste of a term into a larger program. For this reason we need to restrict which terms we expect to behave like functions to only consider terms with no free variables. We call terms with no free variables **closed terms**. If we didn't restrict them like this, then the context could wrongly capture one of the free variables in $t$.
This restriction isn't very severe, because if we can find a good notion of equivalence for terms with no free variables, we can extend it to terms *with* free variables by declaring them to be equivalent if the corresponding closed terms where we abstracted over those free variables are equivalent.
By deciding to only concern ourselves with closed terms, we can simplify the notion of contexts. This is because for closed terms, there is little difference between substitution into a context and applying a term to an argument. This is true because for these terms there is also little difference between ordinary and capture-avoiding substitution.
![](Pasted%20image%2020231113114927.png)
(I think theres a typo here and that $C_y$ should be $T^C_y$.)
Simple: If you have a context $C$ that doesn't contain $y$, and a term $T^C_y$ that is the same as $C$ but with $y$ in the place of $\square$, performing a substitution into $C$ is alpha-equivalent to performing a capture-avoiding substitution into $T^C_y$. 

This is one way in which the more advanced features of the simply typed lambda calculus actually make it easier to study. If we were studying a language with no notion of 'functions' which allow us to represent a context as a term, we would have to treat contexts as a completely separate notion. This corresponds to the fact that in a language with 'functions' we can wrap up tests as a piece of code, whereas if the language lacks these features, tests which embed the code to be tested in a larger program would have to be constructed by external scripts, which would be more unwieldy.
Recalling that the results of computation are terms of type $\iota$, we need to define the notion of a context which we can use to get a 'test result' from a term of a given type $\tau$. We want these terms to have a 'hole' in the such that if we replace the 'hole' with a term of type $\sigma$, the resulting term has type $\iota$. It is also useful to consider the general idea of a context which transforms a term of type $\sigma$ into one of some type $\tau$. As usual, we have to allow for free variables to appear in our terms and so we have to include a type environment.
![](Pasted%20image%2020231113134158.png)
Simple: $C$ is a $(\Gamma,\sigma,\tau)$-context if, for every term $t$ with type $\sigma$, substituting $t$ into $C$ produces a term that is typed $\tau$.
Note that because we have no built-in data types, $\Gamma$ cannot be empty. The only way to get a term of type $\iota$ is to assume we have a variable of that type in $\Gamma$. For an example of why this is, see:
![](Pasted%20image%2020231113135202.png)
![](Pasted%20image%2020231113135329.png)
![](Pasted%20image%2020231113135550.png)
Notice that we only need 'input data' of type $\iota$ or $\iota \rightarrow \iota$, but never a higher order function type which takes a function as an argument. This will always be the case: just as we only want to consider tests which output a concrete piece of data (i.e. of type $\iota$), we only want to put concrete data in to test our terms.
Now we are ready to define the key notion of equivalence, which corresponds to the intuition that two programs are equivalent if one can be used in the place of the other in any larger program without affecting the observable input.
![](Pasted%20image%2020231113135923.png)
![](Pasted%20image%2020231113135931.png)
Simple: If you have two closed terms $t$ and $t'$ with the same type $\tau$, then they are contextually equivalent if they both give the same result in all type environments $\Gamma$ and $(\Gamma,\tau,\iota)$-contexts.
We can see that $(\Gamma, \tau, \iota)$-contexts are important and we look at what we can say about how they might be built.
Contextual equivalence is well motivated, but it is hard to work with because of the quantification over all contexts. Once one has defined contextural equivalence for a language, one is there interested in finding more concrete ways to describe the same relation. 

(At this point i stopped verbatim copying the notes for no reason. Just read the notes. Only snippets of definitions from now on.)

![](Pasted%20image%2020231113140850.png)
# The Function Model
The interpretation of a term in some model is also known as its **denotation**.
## The idea
For the moment we are only interested in interpreting typeable terms that do not contain free variables. That means we don’t have to worry about interpreting terms that consist of just a variable.
We will write ![](Pasted%20image%2020231113141535.png) for the interpretation of a type. We assume that the interpretation of a function type is ![](Pasted%20image%2020231113141559.png), where ![](Pasted%20image%2020231113141626.png) is the set of all functions from $A$ to B. 
![](Pasted%20image%2020231113141931.png)
## Interpreting types
We interpret the types as sets of items. For the base type $\iota$, we can interpret it as any set we like, for example booleans or natural numbers. We may also want to think about the meaning of a term with respect to any set, which is the same as considering a set whose elements we leave unspecified.
![](Pasted%20image%2020231113142159.png)
![](Pasted%20image%2020231113142211.png)
Simple: The denotation of the base type relative to some set is just that set. The denotation of a function type recurs in the obvious way.
![](Pasted%20image%2020231113142625.png)
## Valuations
It is convenient to be able to refer to the elements which appear in the denotation of some type. We take the union of the denotations of all the types to assemble all these elements:
![](Pasted%20image%2020231113145705.png)
![](Pasted%20image%2020231113150147.png)
![](Pasted%20image%2020231113150715.png)
Simple: the support of a type environment is the set of variables that the type environment assigns a function type to (in other words, every variable mentioned in the type environment apart from those of base type). (???)
An $X$-valuation is a function that maps variables in the type environment to some value relative $X$.
If the set $X$ is obvious from context (not the contexts defined above, just the word context) then we just call these valuations for $\Gamma$.
Note that we may weaken $\Gamma$ and obtain a type environment where valuations do not change. For type environment $\Gamma$:
![](Pasted%20image%2020231113152528.png)
and the interpretation of $x$ still exists in the same set, so X-valuations for $\Gamma,x:\tau$ are also X-valuations for $\Gamma,x:\sigma,x:\tau$.
![](Pasted%20image%2020231113152706.png)

We need a way to move from a valuation for one type environment to a valuation for another. For a type environment $\Gamma$, a valuation $\phi$ for $\Gamma$ and a variable $y$ we would like to be able to perform two tasks:
- If the variable $y$ is already in the support of $\Gamma$, change the value it is assigned.
- If the variable $y$ is not already in the support of $\Gamma$, add it to the source set for $\phi$.

We notate both of these by writing this:
![](Pasted%20image%2020231113153306.png)
Which means that the mappings given by $\phi$ stay the same for all variables except $y$, which is mapped to $a$. Formally:
![](Pasted%20image%2020231113153344.png)
![](Pasted%20image%2020231113153429.png)
## Interpreting terms
We define the denotations of terms by mapping them to elements of the denotations of their types. As previously indicated, we need to do this relative to a suitable type environment. 
![](Pasted%20image%2020231115101555.png)
![](Pasted%20image%2020231115101602.png)
Simple: We write the $\Gamma$-denotation of a term in some type environment $\Gamma$ similarly to the denotation of a type, but we also attach the relevant valuation. 
When applying the operation in order to find the final result of a term, we follow these rules:
- bcVar: If the term is a variable $x$ then replace the $\Gamma$-denotation with the value for $x$ given in $\phi$.
- scAbs: If the term is an abstraction $\lambda x:\sigma.u$, then replace the whole term with $u$, add the type $x:\sigma$ to the type environment, and add $x$ to the valuation with an unknown value of type $\sigma$. (e.g. $[\![ \Gamma, \lambda x:\sigma.u ]\!]^{X}_\phi$ goes to $[\![ \Gamma x:\sigma, u ]\!]^{X}_{\phi[x \mapsto a]}$, where $a \in [\![ \sigma ]\!]^X$.)
- scApp: If the term is an application $rs$, split the $\Gamma$-denotation into two gamma denotations for each subterm. (e.g. $[\![ \Gamma, rs ]\!]^{X}_\phi$ goes to $[\![ \Gamma, r ]\!]^{X}_\phi[\![ \Gamma, s ]\!]^{X}_\phi$).

If the term $t$ can be typed with the empty type environment then we don't require a valuation and we write $$[\![t]\!]^X.$$
![](Pasted%20image%2020231115103426.png)
The book has a very long example with a free variable.k
