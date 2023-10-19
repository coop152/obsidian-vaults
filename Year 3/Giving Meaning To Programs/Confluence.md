# Diverging Computations
We have shown how to use the lambda-calculus to write simple functional programs, where the result is the irreducible term reached after many beta-reductions. However, we have not actually proved this rewriting process is well-behaved enough to truly think of lambda-terms like mathematical functions.
The first problem to worry about is whether the "functions" we write will always reduce to a single value, or if a term might have many distinct normal forms. When we look at the rule for beta-reducing an application, we see that there is a choice on what to do next. For example:
![](Pasted%20image%2020231018112723.png)
There are two different redexes that qualify for the first rule here, and which one you reduce is up to choice:
![](Pasted%20image%2020231018112857.png)
Therefore, we have that the given lambda-term beta-alpha-reduces to both of these:
![](Pasted%20image%2020231018112954.png)
![](Pasted%20image%2020231018113002.png)
This is a disturbing result if we wish to model functions, although this specific case is harmless because we can bring the two branches back together again:
![](Pasted%20image%2020231018113112.png)
(Side note: alpha-equivalence is reflexive, so it is okay to write beta-alpha-reduces instead of beta-reduces even if we know it is a beta-reduction.)
Lets extend this graph to include every possible beta-alpha-reduction of the original term:
![](Pasted%20image%2020231018113349.png)
We can simplify this graph significantly by having each node represent a class of alpha-equivalent terms, of which there are many in the previous graph:
![](Pasted%20image%2020231018113455.png)
## The diamond property
From these graphs, we might wonder if it always the case that if we have
![](Pasted%20image%2020231018113553.png)
Then there must be a $t'$ such that
![](Pasted%20image%2020231018113611.png).
This is a property that could apply to any relation, though it has particular importance for certain relations in computer science.
![](Pasted%20image%2020231018113721.png)
So, does beta-reduction have the diamond property? Worryingly, the answer is no.
Consider this example:
![](Pasted%20image%2020231018113833.png)
In this case it is clear that with just one more beta-reduction we could make either of those options into $(fu)u$, so this isn't cause for alarm.
![](Pasted%20image%2020231018114228.png)
This example shows that we might be forced to do any number of beta-reductions before we can get the same result from two diverging branches. The best we can hope for is that beta-reduction satisfies the following (weaker) property, which will still be good enough for our purposes.
![](Pasted%20image%2020231018114740.png)
Note that this new property "confluence" is essentially the diamond property but on the reflexive transitive closure of $R$. In other words, it's like the diamond property but it accepts cases where the branches "eventually" meet again, instead of only cases where they meet again immediately. In terms of beta-reduction, we can picture it the same as the diamond property but using beta-reduction in multiple steps:
![](Pasted%20image%2020231018115057.png)
In reality, the situation is slightly more complicated; We only achieve what you might call "confluence up to alpha-equivalence" for beta-reduction. 
![](Pasted%20image%2020231018115258.png)
So, we can show confluence but only to the point that the final terms achieved are alpha equivalent.
# Parallel Reduct
How would we prove confluence (or rather, this specific version of it)? We start by thinking about our counter-example to the diamond property: In that example, the reason beta-reduction could not bring the derivations together is because one of our branches copied a term that could have been reduced while another reduced it before copying. After this first step, the first version can't "catch up" with the other because it can only reduce one of the copies in a single step.
You might conjecture that this is "the only thing that can go wrong". To solve this, you might define a new relation which acts as a "parallel reduction", which can reduce as many redexes in a single step as it wants. This leads to very complicated proofs, however, so instead we define the parallel reduct **operation**:
![](Pasted%20image%2020231018120127.png)
Think of the diamond as an operator or a function that takes a lambda-term as input and produces another lambda-term. It behaves similarly to carrying out a number of beta-reduction steps at once.
The rules in plain English are:
- bcVar: The parallel reduct of a variable is itself.
- scAbs: The parallel reduct ignores the bound variable of an abstraction and goes into the right subterm.
- scApp1: If the term is a redex, then perform the beta-reduction step case of removing the bound variable and substituting the right subterm in for the bound variable. Also, apply the operator to both subterms involved.
- scApp2: If the term isn't a redex, apply parallel reduct to both of the subterms.

You can write $\Diamond^n t$ to mean the result of applying the parallel reduct operation $n$ times on the term $t$.
Here is an example application of the operation:
![](Pasted%20image%2020231018120749.png)

We must now prove that parallel reduct is both relevant to beta-reduction, and that it behaves nicely.
Before comparing parallel reduct to beta-reduction, we need to make sure it interacts well with free variables and capture avoiding substitution.

First we prove this proposition with a straightforward proof by induction:
![](Pasted%20image%2020231018124420.png)
This shows that it is well-behaved w.r.t. free variables.
In order to show it is well-behaved w.r.t. alpha-equivalence, we first show this property about renaming:
![](Pasted%20image%2020231018124706.png)
I.e. parallel reduct over renaming maintains alpha equivalence.
From here we prove this:
![](Pasted%20image%2020231019111000.png)
I.e. parallel reduct maintains alpha-equivalence between terms.
Next we want to show that parallel reduct is actually what we think it is; that is, for any term you can get the result of a parallel reduct by instead beta-reducing a number of times (that is, up to alpha-equivalence).
![](Pasted%20image%2020231019111352.png)
Combining these previous two results, we get this new result:
![](Pasted%20image%2020231019111444.png)
That is, for any two alpha-equivalent terms $t$ and $u$, the results of parallel reduct on these two terms are alpha equivalent.
We can extend this to iterations of the parallel reduct (i.e. multiple applications):
![](Pasted%20image%2020231019111817.png)
In other words, any number of parallel reducts on a term will still result in a term you could have gotten with beta-reduction (up to alpha-equivalence). The second part asserts that this is true also for intermediate parallel reducts.

Next we show that parallel reduct is *monotone* with respect to beta-reduction (in multiple steps).
For redexes:
![](Pasted%20image%2020231019112227.png)
I.e. 