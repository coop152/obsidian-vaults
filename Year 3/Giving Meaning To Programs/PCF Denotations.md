# Posets
Posets are sets that are partially ordered. A group of posets that we are concerned with are the *flat types*, for example $\mathbb{N}_\bot$ (the flat natural numbers). These sets are equal to the set indicated in their name but feature another element $\bot$ for which every element is greater:
![](Pasted%20image%2020231204134215.png)
We are also concerned with the function types that stem from these. The logic we use to construct these is that we are ordering elements by how "informative" they are: in a base type like natural numbers, no number is more informative than another but every number is certainly more informative than no result at all ($\bot$). For functions, we say that a function is more informative than another if if provides exactly the same results as that function **and then some**. For example:
![](Pasted%20image%2020231204134448.png)
![](Pasted%20image%2020231204134508.png)
Each of these functions is more informative than the last in the family because it returns one more result on top of the results of the previous. You cannot compare the informativity of these functions to another different function (say, one that always returns 1), only to themselves.
We can also observe an interesting property about this family of functions in particular:
![](Pasted%20image%2020231204135252.png)
This is interesting, because each of these functions $f_n$ is 'finite' by themselves (in the sense that they only return informative output for a finite number of inputs) but taken together they 'approximate' $f_\infty$. We will elaborate on this idea, but first we need to investigate partial orders more closely.
## Properties of partial orders
If we want to precisely define the notion of approximation then a particularly important idea is that of the "least upper bound" of a set of elements. First we define some properties that we expect of a least upper bound.
![](Pasted%20image%2020231204140156.png)
This is just the basic definition of a least upper bound.
![](Pasted%20image%2020231204140442.png)
(a): The least upper bound of a subset is less than or equal to the least upper bound of the whole set.
(b): If for every element in one subset $S$ there is something greater in another subset $S'$, then the least upper bound of $S$ is at most that of $S'$.
We introduce a notation for the least upper bound of a subset $S$:
$$\bigvee S$$
![](Pasted%20image%2020231204141141.png)
(a): The least upper bound of the union of many subsets is the same as the least upper bound of the individual least upper bounds.
(b): If the union of some group of subsets is equal to the union of another group of subsets, then the least upper bounds of the unions (and therefore the groups) are equal also.
## Posets of functions
To interpret function types we have to think about how we may put a partial order on a set of functions, and which functions we should consider. We only want to allow functions that are well-behaved w.r.t the partial order, so we impose two conditions:
First, we only consider **order-preserving** functions. This models the crucial idea that we can't possibly get more information from a computation that hasn't finished. We can wait for information to come in, but if it never comes then we can't supply information that would have resulted from it. In a more explicit sense, if a function expects a natural number and receives $\bot$ then it cannot possibly determine that the given number "won't terminate" - that's the halting problem. All it can do it wait indefinitely for a result, which would mean it also "returns" $\bot$. 
![](Pasted%20image%2020231204142418.png)
If our input and output sets are flat sets such as $\mathbb{N}_\bot$ then the condition is simply that
![](Pasted%20image%2020231204142833.png)
For example, our function $f_n$ is order-preserving because no matter which function in that family you choose, $\bot$ simply maps to $\bot$.
You can think of this condition as demanding that if we give some information to $f$ (in the form of $p$) and then give it at least that much or more (in the form of $p'$) then it must give at least as much information back for $p'$ as it did for $p$.
Another simple example of an order-preserving function is every constant function. That is, for posets $P$ and $Q$, where $q$ is some constant in $Q$:
![](Pasted%20image%2020231204143441.png)
is order-preserving.
![](Pasted%20image%2020231204143537.png)
Clearly the identity function on a poset is order preserving because it maps $\bot$ to $\bot$. There is a proof for the second part, but it is clear either way.
For two posets $(P, \leq)$ and $(Q,\leq)$ we write
$$P \Rightarrow_\leq Q$$
For the set of all order-preserving functions from $P$ to $Q$. We define a partial order on this set as follows:
![](Pasted%20image%2020231204144115.png)
In other words, a function $g$ is greater or equal to a function $f$ (in the pointwise order) if it gives a greater or equal result for all possible inputs.
![](Pasted%20image%2020231204145034.png)
Previously we had only defined $\bot$ for the base type, but we need to define it for *all* types: We can make any type reduce forever if we apply rec to its identity function, meaning any type can give no information. Therefore, we also use $\bot$ as the least element of any such poset:
![](Pasted%20image%2020231204150739.png)
# Directed-complete Partial Orders (DCPO)
So far we have only looked at posets with finite 'height'. You might assume that all of the posets we are concerned with are like this, but unfortunately not.
We intent to interpret `nat` using the flat natural numbers $\mathbb{N}_\bot$ and then use a function space construction to interpret higher types. The posets we obtain in this manner contain infinite chains, for example:
![](Pasted%20image%2020231204151238.png)
For the previously described $f_n$. We want to consider $f_n$ as approximating $f_\infty$, which is the identity on $\mathbb{N}_\bot$. All of these functions appear in our interpretation of `nat` $\rightarrow$ `nat`, so if we wanted to interpret a function of even higher type (e.g. one that takes or returns this) then we need to decide what elements of that function space should do with these infinite approximations.
(Some shit i don't really understand)
![](Pasted%20image%2020231204151841.png)
In other words, if we pick a finite number of elements in $S$ then there is another element that is greater than or equal to all of them.
To talk about the infinite objects approximated by finite computations, we have to restrict ourselves to special posets, namely those where we can always form a least upper bound to model the infinite object that they describe.
![](Pasted%20image%2020231204152141.png)
![](Pasted%20image%2020231204152424.png)
We can establish that the partial orders suggested for the base type are dcpos: The only directed sets in these flat posets have at most two elements (For example, $\{\bot, n\}$ for all $n$ in $\mathbb{N}_\bot$), and therefore a greatest element and a least upper bound.
![](Pasted%20image%2020231204152753.png)
In particular, $\mathbb{N}_\bot$ is a dcpo.
## Suitable functions and function spaces
To ensure that we're able to interpret recursion we need to demand an addition property for recursive functions: they have to be well-behaved with respect to the least upper bounds of directed sets.
![](Pasted%20image%2020231204153247.png)
![](Pasted%20image%2020231204153749.png)
In other words, a function between dcpos is Scott-continuous if applying the function to the least upper bound of some (directed) subset gives the same result as the least upper bound of applying it to every function.
You can also write this equality as
![](Pasted%20image%2020231204154056.png)
(Dont really get the rest of the stuff here)
![](Pasted%20image%2020231205114115.png)
The second condition we impose on functions we use in out interpretation is that they must be Scott-continuous.
If $D$ and $E$ are dcpos we write $D \Rightarrow E$ for the set of all Scott-continuous functions from $D$ to $E$.
![](Pasted%20image%2020231205114756.png)
![](Pasted%20image%2020231205114909.png)
![](Pasted%20image%2020231205114916.png)
## Fixed Points
To interpret the `rec` operator, we have to ensure there is suitable infrastructure in our model.