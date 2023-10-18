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
